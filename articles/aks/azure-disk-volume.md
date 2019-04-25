---
title: Создание тома "Статический" для групп контейнеров в Службе Azure Kubernetes (AKS)
description: Сведения о том, как вручную создавать том с дисками Azure для использования с группой контейнеров в Службе Azure Kubernetes (AKS)
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: article
ms.date: 03/01/2019
ms.author: iainfou
ms.openlocfilehash: 02a863a4ddf59fb36c5f2ae7f3092896d2e1d860
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/23/2019
ms.locfileid: "60467533"
---
# <a name="manually-create-and-use-a-volume-with-azure-disks-in-azure-kubernetes-service-aks"></a>Создание вручную и использование тома с дисками в Службе Azure Kubernetes (AKS)

Контейнерные приложения часто требуются для обращения к данным и их хранения во внешнем томе данных. Если отдельной группе контейнеров (pod) необходим доступ к хранилищу, можно использовать диски Azure, чтобы предоставить собственный том для использования приложения. В этой статье показано, как вручную создать диск Azure и присоединить его к pod в AKS.

> [!NOTE]
> Диск Azure можно подключить только к одному pod за один раз. Если потребуется предоставить общий доступ для постоянного тома, используемого несколькими модулями pod, можно применить [Файлы Azure][azure-files-volume].

Дополнительные сведения о томах Kubernetes см. в разделе [параметры хранилища для приложений в AKS][concepts-storage].

## <a name="before-you-begin"></a>Перед началом работы

В этой статье предполагается, что у вас есть кластер AKS. Если вам нужен кластер AKS, обратитесь к этому краткому руководству по работе с AKS [с помощью Azure CLI][aks-quickstart-cli] или [портала Azure][aks-quickstart-portal].

Вам также понадобится Azure CLI версии 2.0.59 или более поздней версии установлен и настроен. Чтобы узнать версию, выполните команду  `az --version`. Если вам необходимо выполнить установку или обновление, см. статью  [Установка Azure CLI][install-azure-cli].

## <a name="create-an-azure-disk"></a>Создание диска Azure

При создании диска Azure для использования с AKS можно создать ресурс диска в группе ресурсов **узла**. Такой подход позволит кластеру AKS получить доступ к ресурсу диска и управлять им. Если вместо этого вы создадите диск в отдельной группе ресурсов, необходимо предоставить для субъекта-службы Azure Kubernetes (AKS) кластера роль `Contributor` в группе ресурсов диска.

В рамках этой статьи создайте диск в группе ресурсов узла. Сначала получите имя группы ресурсов, выполнив команду [az aks show][az-aks-show] и добавив параметр запроса `--query nodeResourceGroup`. В следующем примере возвращается группа ресурсов узла для имени кластера AKS *myAKSCluster* в группе ресурсов *myResourceGroup*.

```azurecli-interactive
$ az aks show --resource-group myResourceGroup --name myAKSCluster --query nodeResourceGroup -o tsv

MC_myResourceGroup_myAKSCluster_eastus
```

Создайте диск, выполнив команду [az disk create][az-disk-create]. Укажите имя группы ресурсов узла, полученное в предыдущей команде, а затем имя ресурса диска, например *myAKSDisk*. В следующем примере создается *20*Гиб диска и идентификатор выходных данных после ее создания диска:

```azurecli-interactive
az disk create \
  --resource-group MC_myResourceGroup_myAKSCluster_eastus \
  --name myAKSDisk  \
  --size-gb 20 \
  --query id --output tsv
```

> [!NOTE]
> Диски Azure выставляются в зависимости от SKU для определенного размера. Эти номера SKU варьируются от 32GiB S4 или P4 дисков до 32TiB для дисков S80 или P80 (в предварительной версии). Число операций ввода-вывода в секунду и пропускная способность управляемого диска (цен. категория "Премиум") зависят как от номера SKU, так и от размера экземпляров узлов в кластере AKS. Дополнительные сведения см. на странице [Цены на управляемые диски][managed-disk-pricing-performance].

Идентификатор ресурса диска отображается после успешного завершения команды, как показано в следующем примере выходных данных. Этот идентификатор диска используется для подключения диска на следующем шаге.

```console
/subscriptions/<subscriptionID>/resourceGroups/MC_myAKSCluster_myAKSCluster_eastus/providers/Microsoft.Compute/disks/myAKSDisk
```

## <a name="mount-disk-as-volume"></a>Подключите диска в качестве тома

Чтобы установить диск Azure в pod, настройте том в спецификации контейнера. Создайте файл `azure-disk-pod.yaml` со следующим содержимым. Обновите `diskName`, указав имя диска, созданного на предыдущем шаге, и `diskURI`, указав идентификатор диска, содержащийся в выходных данных команды создания диска. При необходимости обновите `mountPath`. Это путь, по которому диск Azure подключен в pod.

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: mypod
spec:
  containers:
  - image: nginx:1.15.5
    name: mypod
    resources:
      requests:
        cpu: 100m
        memory: 128Mi
      limits:
        cpu: 250m
        memory: 256Mi
    volumeMounts:
      - name: azure
        mountPath: /mnt/azure
  volumes:
      - name: azure
        azureDisk:
          kind: Managed
          diskName: myAKSDisk
          diskURI: /subscriptions/<subscriptionID>/resourceGroups/MC_myAKSCluster_myAKSCluster_eastus/providers/Microsoft.Compute/disks/myAKSDisk
```

Создайте pod с помощью команды `kubectl`.

```console
kubectl apply -f azure-disk-pod.yaml
```

Теперь у вас есть запущенная группа pod с подключенным диском Azure в `/mnt/azure`. Вы можете использовать `kubectl describe pod mypod` для проверки успешного подключения диска. Ниже приведен краткий пример выходных данных, где показано, что том подключен к контейнеру:

```
[...]
Volumes:
  azure:
    Type:         AzureDisk (an Azure Data Disk mount on the host and bind mount to the pod)
    DiskName:     myAKSDisk
    DiskURI:      /subscriptions/<subscriptionID/resourceGroups/MC_myResourceGroupAKS_myAKSCluster_eastus/providers/Microsoft.Compute/disks/myAKSDisk
    Kind:         Managed
    FSType:       ext4
    CachingMode:  ReadWrite
    ReadOnly:     false
  default-token-z5sd7:
    Type:        Secret (a volume populated by a Secret)
    SecretName:  default-token-z5sd7
    Optional:    false
[...]
Events:
  Type    Reason                 Age   From                               Message
  ----    ------                 ----  ----                               -------
  Normal  Scheduled              1m    default-scheduler                  Successfully assigned mypod to aks-nodepool1-79590246-0
  Normal  SuccessfulMountVolume  1m    kubelet, aks-nodepool1-79590246-0  MountVolume.SetUp succeeded for volume "default-token-z5sd7"
  Normal  SuccessfulMountVolume  41s   kubelet, aks-nodepool1-79590246-0  MountVolume.SetUp succeeded for volume "azure"
[...]
```

## <a name="next-steps"></a>Дальнейшие действия

Связанные практические рекомендации, см. в разделе [советы и рекомендации для хранилища и резервных копий в AKS][operator-best-practices-storage].

Дополнительные сведения о взаимодействии кластеров AKS с дисками Azure см. в [этом разделе на сайте GitHub][kubernetes-disks].

<!-- LINKS - external -->
[kubernetes-disks]: https://github.com/kubernetes/examples/blob/master/staging/volumes/azure_disk/README.md
[kubernetes-volumes]: https://kubernetes.io/docs/concepts/storage/volumes/
[managed-disk-pricing-performance]: https://azure.microsoft.com/pricing/details/managed-disks/

<!-- LINKS - internal -->
[az-disk-list]: /cli/azure/disk#az-disk-list
[az-disk-create]: /cli/azure/disk#az-disk-create
[az-group-list]: /cli/azure/group#az-group-list
[az-resource-show]: /cli/azure/resource#az-resource-show
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[az-aks-show]: /cli/azure/aks#az-aks-show
[install-azure-cli]: /cli/azure/install-azure-cli
[azure-files-volume]: azure-files-volume.md
[operator-best-practices-storage]: operator-best-practices-storage.md
[concepts-storage]: concepts-storage.md
