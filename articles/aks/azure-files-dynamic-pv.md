---
title: Динамическое создание тома "Файлы" для нескольких групп контейнеров в Службе Azure Kubernetes (AKS)
description: Сведения о том, как динамически создавать постоянный том с файлами Azure для использования с несколькими параллельными pod в Службе Azure Kubernetes (AKS)
services: container-service
author: mlearned
ms.service: container-service
ms.topic: article
ms.date: 09/12/2019
ms.author: mlearned
ms.openlocfilehash: 999e106240a8a1d95c35d098062d474a0b57228d
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/20/2019
ms.locfileid: "74231757"
---
# <a name="dynamically-create-and-use-a-persistent-volume-with-azure-files-in-azure-kubernetes-service-aks"></a>Динамическое создание и использование постоянного тома с файлами Azure в службе Azure Kubernetes (AKS)

Постоянный том — это часть хранилища, которая подготовлена к использованию для модулей pod Kubernetes. Постоянный том может использоваться одним или несколькими модулями и может быть подготовлен динамически или статически. If multiple pods need concurrent access to the same storage volume, you can use Azure Files to connect using the [Server Message Block (SMB) protocol][smb-overview]. В этой статье показано, как в кластере Службы Azure Kubernetes с помощью нескольких модулей pod предоставлять общий доступ к динамическому созданию файлов Azure.

For more information on Kubernetes volumes, see [Storage options for applications in AKS][concepts-storage].

## <a name="before-you-begin"></a>Перед началом работы

В этой статье предполагается, что у вас есть кластер AKS. If you need an AKS cluster, see the AKS quickstart [using the Azure CLI][aks-quickstart-cli] or [using the Azure portal][aks-quickstart-portal].

You also need the Azure CLI version 2.0.59 or later installed and configured. Чтобы узнать версию, выполните команду  `az --version`. If you need to install or upgrade, see [Install Azure CLI][install-azure-cli].

## <a name="create-a-storage-class"></a>Создание класса хранения

Класс хранения используется для определения того, как создается файловый ресурс Azure. A storage account is automatically created in the [node resource group][node-resource-group] for use with the storage class to hold the Azure file shares. Choose of the following [Azure storage redundancy][storage-skus] for *skuName*:

* *Standard_LRS* — локально избыточное хранилище ценовой категории "Стандартный" (LRS);
* *Standard_GRS* — геоизбыточное хранилище ценовой категории "Стандартный" (GRS);
* *Standard_RAGRS* — геоизбыточное хранилище с доступом на чтение ценовой категории "Стандартный" (RA-GRS);
* *Premium_LRS* - premium locally redundant storage (LRS)

> [!NOTE]
> Azure Files support premium storage in AKS clusters that run Kubernetes 1.13 or higher.

For more information on Kubernetes storage classes for Azure Files, see [Kubernetes Storage Classes][kubernetes-storage-classes].

Создайте файл под названием `azure-file-sc.yaml` и скопируйте в него следующий пример манифеста. For more information on *mountOptions*, see the [Mount options][mount-options] section.

```yaml
kind: StorageClass
apiVersion: storage.k8s.io/v1
metadata:
  name: azurefile
provisioner: kubernetes.io/azure-file
mountOptions:
  - dir_mode=0777
  - file_mode=0777
  - uid=1000
  - gid=1000
  - mfsymlinks
  - nobrl
  - cache=none
parameters:
  skuName: Standard_LRS
```

Create the storage class with the [kubectl apply][kubectl-apply] command:

```console
kubectl apply -f azure-file-sc.yaml
```

## <a name="create-a-persistent-volume-claim"></a>Создание заявки на доступ к постоянному тому

Утверждение постоянного тома (PVC) использует объект класса хранения для динамической подготовки файлового ресурса Azure. The following YAML can be used to create a persistent volume claim *5 GB* in size with *ReadWriteMany* access. For more information on access modes, see the [Kubernetes persistent volume][access-modes] documentation.

Теперь создайте файл под названием`azure-file-pvc.yaml` и скопируйте в него следующий код YAML. Убедитесь, что *storageClassName* соответствует классу хранения, созданному на предыдущем шаге.

```yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: azurefile
spec:
  accessModes:
    - ReadWriteMany
  storageClassName: azurefile
  resources:
    requests:
      storage: 5Gi
```

> [!NOTE]
> If using the *Premium_LRS* sku for your storage class, the minimum value for *storage* must be *100Gi*.

Create the persistent volume claim with the [kubectl apply][kubectl-apply] command:

```console
kubectl apply -f azure-file-pvc.yaml
```

После ее выполнения будет создан файловый ресурс. Также будет создан секрет Kubernetes, содержащий сведения о подключении и учетные данные. You can use the [kubectl get][kubectl-get] command to view the status of the PVC:

```console
$ kubectl get pvc azurefile

NAME        STATUS    VOLUME                                     CAPACITY   ACCESS MODES   STORAGECLASS   AGE
azurefile   Bound     pvc-8436e62e-a0d9-11e5-8521-5a8664dc0477   5Gi        RWX            azurefile      5m
```

## <a name="use-the-persistent-volume"></a>Использование постоянного тома

Следующий код YAML создает pod, использующий утверждение постоянного тома *azurefile* для подключения файлового ресурса Azure по пути */mnt/azure*. For Windows Server containers (currently in preview in AKS), specify a *mountPath* using the Windows path convention, such as *'D:'* .

Создайте файл `azure-pvc-files.yaml` и скопируйте в него следующий код YAML. Убедитесь, что *claimName* соответствует утверждению постоянного тома, созданному на предыдущем шаге.

```yaml
kind: Pod
apiVersion: v1
metadata:
  name: mypod
spec:
  containers:
  - name: mypod
    image: nginx:1.15.5
    resources:
      requests:
        cpu: 100m
        memory: 128Mi
      limits:
        cpu: 250m
        memory: 256Mi
    volumeMounts:
    - mountPath: "/mnt/azure"
      name: volume
  volumes:
    - name: volume
      persistentVolumeClaim:
        claimName: azurefile
```

Create the pod with the [kubectl apply][kubectl-apply] command.

```console
kubectl apply -f azure-pvc-files.yaml
```

Теперь у вас есть запущенный pod с общей папкой "Файлы Azure", созданной в каталоге */mnt/azure*. Эта конфигурация может отображаться при проверке вашего модуля через `kubectl describe pod mypod`. Ниже приведен краткий пример выходных данных, где показано, что том подключен к контейнеру:

```
Containers:
  mypod:
    Container ID:   docker://053bc9c0df72232d755aa040bfba8b533fa696b123876108dec400e364d2523e
    Image:          nginx:1.15.5
    Image ID:       docker-pullable://nginx@sha256:d85914d547a6c92faa39ce7058bd7529baacab7e0cd4255442b04577c4d1f424
    State:          Running
      Started:      Fri, 01 Mar 2019 23:56:16 +0000
    Ready:          True
    Mounts:
      /mnt/azure from volume (rw)
      /var/run/secrets/kubernetes.io/serviceaccount from default-token-8rv4z (ro)
[...]
Volumes:
  volume:
    Type:       PersistentVolumeClaim (a reference to a PersistentVolumeClaim in the same namespace)
    ClaimName:  azurefile
    ReadOnly:   false
[...]
```

## <a name="mount-options"></a>Параметры подключения

The default value for *fileMode* and *dirMode* is *0755* for Kubernetes version 1.9.1 and above. If using a cluster with Kuberetes version 1.8.5 or greater and dynamically creating the persistent volume with a storage class, mount options can be specified on the storage class object. В следующем примере задается значение *0777*.

```yaml
kind: StorageClass
apiVersion: storage.k8s.io/v1
metadata:
  name: azurefile
provisioner: kubernetes.io/azure-file
mountOptions:
  - dir_mode=0777
  - file_mode=0777
  - uid=1000
  - gid=1000
  - mfsymlinks
  - nobrl
  - cache=none
parameters:
  skuName: Standard_LRS
```

При использовании кластера версии 1.8.0–1.8.4 контекст безопасности можно указать, задав для *runAsUser* значение *0*. For more information on Pod security context, see [Configure a Security Context][kubernetes-security-context].

## <a name="next-steps"></a>Дальнейшие действия

For associated best practices, see [Best practices for storage and backups in AKS][operator-best-practices-storage].

Узнайте больше о постоянных томах Kubernetes, использующих службу файлов Azure.

> [!div class="nextstepaction"]
> [Подключаемый модуль Kubernetes для службы файлов Azure][kubernetes-files]

<!-- LINKS - external -->
[access-modes]: https://kubernetes.io/docs/concepts/storage/persistent-volumes
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-describe]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#describe
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubernetes-files]: https://github.com/kubernetes/examples/blob/master/staging/volumes/azure_file/README.md
[kubernetes-secret]: https://kubernetes.io/docs/concepts/configuration/secret/
[kubernetes-security-context]: https://kubernetes.io/docs/tasks/configure-pod-container/security-context/
[kubernetes-storage-classes]: https://kubernetes.io/docs/concepts/storage/storage-classes/#azure-file
[kubernetes-volumes]: https://kubernetes.io/docs/concepts/storage/persistent-volumes/
[pv-static]: https://kubernetes.io/docs/concepts/storage/persistent-volumes/#static
[smb-overview]: /windows/desktop/FileIO/microsoft-smb-protocol-and-cifs-protocol-overview

<!-- LINKS - internal -->
[az-group-create]: /cli/azure/group#az-group-create
[az-group-list]: /cli/azure/group#az-group-list
[az-resource-show]: /cli/azure/aks#az-aks-show
[az-storage-account-create]: /cli/azure/storage/account#az-storage-account-create
[az-storage-create]: /cli/azure/storage/account#az-storage-account-create
[az-storage-key-list]: /cli/azure/storage/account/keys#az-storage-account-keys-list
[az-storage-share-create]: /cli/azure/storage/share#az-storage-share-create
[mount-options]: #mount-options
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[install-azure-cli]: /cli/azure/install-azure-cli
[az-aks-show]: /cli/azure/aks#az-aks-show
[storage-skus]: ../storage/common/storage-redundancy.md
[kubernetes-rbac]: concepts-identity.md#role-based-access-controls-rbac
[operator-best-practices-storage]: operator-best-practices-storage.md
[concepts-storage]: concepts-storage.md
[node-resource-group]: faq.md#why-are-two-resource-groups-created-with-aks