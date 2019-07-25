---
title: Динамическое создание тома "Файлы" для нескольких групп контейнеров в Службе Azure Kubernetes (AKS)
description: Сведения о том, как динамически создавать постоянный том с файлами Azure для использования с несколькими параллельными pod в Службе Azure Kubernetes (AKS)
services: container-service
author: mlearned
ms.service: container-service
ms.topic: article
ms.date: 07/08/2019
ms.author: mlearned
ms.openlocfilehash: 580363973afd918351931edfb187a1a8d38d6985
ms.sourcegitcommit: bafb70af41ad1326adf3b7f8db50493e20a64926
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/25/2019
ms.locfileid: "67665972"
---
# <a name="dynamically-create-and-use-a-persistent-volume-with-azure-files-in-azure-kubernetes-service-aks"></a>Динамическое создание и использование постоянного тома с файлами Azure в службе Azure Kubernetes (AKS)

Постоянный том — это часть хранилища, которая подготовлена к использованию для модулей pod Kubernetes. Постоянный том может использоваться одним или несколькими модулями и может быть подготовлен динамически или статически. Если нескольким модулям Pod требуется одновременный доступ к тому же тому хранилища, можно использовать службу файлов Azure для подключения с помощью [протокола SMB][smb-overview]. В этой статье показано, как в кластере Службы Azure Kubernetes с помощью нескольких модулей pod предоставлять общий доступ к динамическому созданию файлов Azure.

Дополнительные сведения о томах Kubernetes см. [в статье параметры хранения для приложений в AKS][concepts-storage].

## <a name="before-you-begin"></a>Перед началом работы

В этой статье предполагается, что у вас есть кластер AKS. Если вам нужен кластер AKS, ознакомьтесь с кратким руководством по AKS, [используя Azure CLI][aks-quickstart-cli] или [с помощью портал Azure][aks-quickstart-portal].

Также требуется Azure CLI версии 2.0.59 или более поздней. Чтобы узнать версию, выполните команду  `az --version`. Если необходимо установить или обновить, см. раздел [install Azure CLI][install-azure-cli].

## <a name="create-a-storage-class"></a>Создание класса хранения

Класс хранения используется для определения того, как создается файловый ресурс Azure. Учетная запись хранения автоматически создается в [группе ресурсов узла][node-resource-group] для использования с классом хранения для хранения файловых ресурсов Azure. Выберите следующую избыточность службы [хранилища Azure][storage-skus] для *skuName*:

* *Standard_LRS* — локально избыточное хранилище ценовой категории "Стандартный" (LRS);
* *Standard_GRS* — геоизбыточное хранилище ценовой категории "Стандартный" (GRS);
* *Standard_RAGRS* — геоизбыточное хранилище с доступом на чтение ценовой категории "Стандартный" (RA-GRS);

> [!NOTE]
> Служба файлов Azure поддерживает хранилище класса Premium в кластерах AKS, которые работают под управлением Kubernetes 1,13 или более поздней версии.

Дополнительные сведения о классах хранилища Kubernetes для службы файлов Azure см. в разделе [классы хранилища Kubernetes][kubernetes-storage-classes].

Создайте файл под названием `azure-file-sc.yaml` и скопируйте в него следующий пример манифеста. Дополнительные сведения о *маунтоптионс*см. в разделе [Параметры подключения][mount-options] .

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
parameters:
  skuName: Standard_LRS
```

Создайте класс хранения с помощью команды [kubectl Apply][kubectl-apply] :

```console
kubectl apply -f azure-file-sc.yaml
```

## <a name="create-a-cluster-role-and-binding"></a>Создание роли кластера и привязки

Чтобы ограничить действия, которые могут быть выполнены, кластеры AKS используют управление доступом на основе ролей (RBAC) Kubernetes. *Роли* определяют разрешения для предоставления, а *привязки* применяют их к желаемым пользователям. Эти назначения могут применяться для определенного пространства имен или в масштабах всего кластера. Дополнительные сведения см. [в разделе Использование авторизации RBAC][kubernetes-rbac].

Чтобы разрешить платформе Azure создавать необходимые ресурсы хранения, создайте *ClusterRole* и *ClusterRoleBinding*. Создайте файл `azure-pvc-roles.yaml` и скопируйте в него следующий код YAML:

```yaml
---
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRole
metadata:
  name: system:azure-cloud-provider
rules:
- apiGroups: ['']
  resources: ['secrets']
  verbs:     ['get','create']
---
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRoleBinding
metadata:
  name: system:azure-cloud-provider
roleRef:
  kind: ClusterRole
  apiGroup: rbac.authorization.k8s.io
  name: system:azure-cloud-provider
subjects:
- kind: ServiceAccount
  name: persistent-volume-binder
  namespace: kube-system
```

Назначьте разрешения с помощью команды [kubectl Apply][kubectl-apply] :

```console
kubectl apply -f azure-pvc-roles.yaml
```

## <a name="create-a-persistent-volume-claim"></a>Создание заявки на доступ к постоянному тому

Утверждение постоянного тома (PVC) использует объект класса хранения для динамической подготовки файлового ресурса Azure. Следующий код YAML можно использовать, чтобы создать утверждение постоянного тома в размере *5 ГБ* с доступом *ReadWriteMany*. Дополнительные сведения о режимах доступа см. в документации по [постоянному тому Kubernetes][access-modes] .

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

Создайте утверждение Постоянного тома с помощью команды [kubectl Apply][kubectl-apply] :

```console
kubectl apply -f azure-file-pvc.yaml
```

После ее выполнения будет создан файловый ресурс. Также будет создан секрет Kubernetes, содержащий сведения о подключении и учетные данные. Для просмотра состояния PVC можно использовать команду [kubectl Get][kubectl-get] :

```console
$ kubectl get pvc azurefile

NAME        STATUS    VOLUME                                     CAPACITY   ACCESS MODES   STORAGECLASS   AGE
azurefile   Bound     pvc-8436e62e-a0d9-11e5-8521-5a8664dc0477   5Gi        RWX            azurefile      5m
```

## <a name="use-the-persistent-volume"></a>Использование постоянного тома

Следующий код YAML создает pod, использующий утверждение постоянного тома *azurefile* для подключения файлового ресурса Azure по пути */mnt/azure*. Для контейнеров Windows Server (в настоящее время на этапе предварительной версии в AKS) укажите *mountPath* с помощью соглашения о пути Windows, например *"d:"* .

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

Создайте модуль Pod с помощью команды [kubectl Apply][kubectl-apply] .

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

Значения по умолчанию *fileMode* and *dirMode* различаются между версиями Kubernetes, как описано в следующей таблице.

| version | value |
| ---- | ---- |
| v1.6.x, v1.7.x | 0777 |
| v1.8.0-v1.8.5 | 0700 |
| v1.8.6 или выше | 0755 |
| v1.9.0 | 0700 |
| v1.9.1 или выше | 0755 |

При динамическом создании постоянного тома с классом хранилища и использовании кластера версии 1.8.5 или выше параметры подключения можно указать в объекте класса хранения. В следующем примере задается значение *0777*.

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
parameters:
  skuName: Standard_LRS
```

При использовании кластера версии 1.8.0–1.8.4 контекст безопасности можно указать, задав для *runAsUser* значение *0*. Дополнительные сведения о контексте безопасности Pod см. в разделе [Настройка контекста безопасности][kubernetes-security-context].

## <a name="next-steps"></a>Следующие шаги

Соответствующие рекомендации см. в разделе рекомендации [по хранению и резервному копированию в AKS][operator-best-practices-storage].

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