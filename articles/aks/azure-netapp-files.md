---
title: Интеграция файлов NetApp Azure с сервисом Azure Kubernetes
description: Узнайте, как интегрировать файлы Azure NetApp с сервисом Azure Kubernetes
services: container-service
author: zr-msft
ms.topic: article
ms.date: 09/26/2019
ms.author: zarhoads
ms.openlocfilehash: 1c4996df66d475c63110e3d2797f55598fd85b8d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78273752"
---
# <a name="integrate-azure-netapp-files-with-azure-kubernetes-service"></a>Интеграция файлов NetApp Azure с сервисом Azure Kubernetes

[Azure NetApp Files][anf] — это высокопроизводительная служба хранения файлов корпоративного класса, работая в Azure. В этой статье показано, как интегрировать файлы Azure NetApp с azure Kubernetes Service (AKS).

## <a name="before-you-begin"></a>Перед началом
В этой статье предполагается, что у вас есть кластер AKS. Если вам нужен кластер AKS, обратитесь к этому краткому руководству по работе с AKS [с помощью Azure CLI][aks-quickstart-cli] или [портала Azure][aks-quickstart-portal].

> [!IMPORTANT]
> Кластер AKS также должен находиться [в регионе, поддерживающем файлы NetApp Azure.][anf-regions]

Вам также нужна версия Azure CLI 2.0.59 или более поздняя установка и настройка. Чтобы узнать версию, выполните команду  `az --version`. Если вам необходимо выполнить установку или обновление, см. статью  [Установка Azure CLI][install-azure-cli].

### <a name="limitations"></a>Ограничения

При использовании файлов NetApp Azure:

* Файлы Сети Azure доступны только [в некоторых регионах Azure.][anf-regions]
* Прежде чем использовать файлы NetApp Azure, вам должен быть предоставлен доступ к сервису Azure NetApp Files. Чтобы подать заявку на получение доступа, вы можете использовать форму отправки файлов [ожидания Azure NetApp Files.][anf-waitlist] Вы не можете получить доступ к сервису Azure NetApp Files до тех пор, пока не получите официальное подтверждение от команды Azure NetApp Files.
* Служба файлов Azure NetApp должна быть создана в той же виртуальной сети, что и кластер AKS.
* После первоначального развертывания кластера AKS поддерживается только статическое обеспечение файлов NetApp Azure.
* Чтобы использовать динамическое обеспечение с azure NetApp Files, установите и назначайте версию [NetApp Trident](https://netapp-trident.readthedocs.io/) 19.07 или позже.

## <a name="configure-azure-netapp-files"></a>Настройка файлов NetApp Azure

> [!IMPORTANT]
> Прежде чем зарегистрировать поставщика ресурсов *Microsoft.NetApp,* необходимо заполнить форму отправки файлов [ожидания Azure NetApp для][anf-waitlist] подписки. Вы не можете зарегистрировать предоставить ресурс до тех пор, пока не получите официальное подтверждение от команды Azure NetApp Files.

Зарегистрируйтесь в провайдере ресурсов *Microsoft.NetApp:*

```azurecli
az provider register --namespace Microsoft.NetApp --wait
```

> [!NOTE]
> Это может занять некоторое время.

При создании учетной записи Azure NetApp для использования в AKS необходимо создать учетную запись в группе ресурсов **узлов.** Сначала получите имя группы ресурсов, выполнив команду [az aks show][az-aks-show] и добавив параметр запроса `--query nodeResourceGroup`. Следующий пример получает группу ресурсов узлов для кластера AKS под названием *myAKSCluster* в группе ресурсов *myResourceGroup:*

```azurecli-interactive
az aks show --resource-group myResourceGroup --name myAKSCluster --query nodeResourceGroup -o tsv
```

```output
MC_myResourceGroup_myAKSCluster_eastus
```

Создание учетной записи Файлов NetApp Azure в группе ресурсов **узлов** и в том же регионе, что и кластер AKS, с помощью [учетной записи az netappfiles.][az-netappfiles-account-create] Следующий пример создает учетную запись под названием *myaccount1* в *группе ресурсов MC_myResourceGroup_myAKSCluster_eastus* и регионе *Востока:*

```azurecli
az netappfiles account create \
    --resource-group MC_myResourceGroup_myAKSCluster_eastus \
    --location eastus \
    --account-name myaccount1
```

Создание нового пула емкости с помощью [бассейна az netappfiles создать.][az-netappfiles-pool-create] Следующий пример создает новый пул емкости под названием *mypool1* с 4 ТБ в размере и *премиум* уровень обслуживания:

```azurecli
az netappfiles pool create \
    --resource-group MC_myResourceGroup_myAKSCluster_eastus \
    --location eastus \
    --account-name myaccount1 \
    --pool-name mypool1 \
    --size 4 \
    --service-level Premium
```

Создайте подсеть для [делегирования в Azure NetApp Files][anf-delegate-subnet] с помощью [поднетной сети az network vnet.][az-network-vnet-subnet-create] *Эта подсеть должна находиться в той же виртуальной сети, что и кластер AKS.*

```azurecli
RESOURCE_GROUP=MC_myResourceGroup_myAKSCluster_eastus
VNET_NAME=$(az network vnet list --resource-group $RESOURCE_GROUP --query [].name -o tsv)
VNET_ID=$(az network vnet show --resource-group $RESOURCE_GROUP --name $VNET_NAME --query "id" -o tsv)
SUBNET_NAME=MyNetAppSubnet
az network vnet subnet create \
    --resource-group $RESOURCE_GROUP \
    --vnet-name $VNET_NAME \
    --name $SUBNET_NAME \
    --delegations "Microsoft.NetApp/volumes" \
    --address-prefixes 10.0.0.0/28
```

Создайте объем, используя [объем az netappfiles.][az-netappfiles-volume-create]

```azurecli
RESOURCE_GROUP=MC_myResourceGroup_myAKSCluster_eastus
LOCATION=eastus
ANF_ACCOUNT_NAME=myaccount1
POOL_NAME=mypool1
SERVICE_LEVEL=Premium
VNET_NAME=$(az network vnet list --resource-group $RESOURCE_GROUP --query [].name -o tsv)
VNET_ID=$(az network vnet show --resource-group $RESOURCE_GROUP --name $VNET_NAME --query "id" -o tsv)
SUBNET_NAME=MyNetAppSubnet
SUBNET_ID=$(az network vnet subnet show --resource-group $RESOURCE_GROUP --vnet-name $VNET_NAME --name $SUBNET_NAME --query "id" -o tsv)
VOLUME_SIZE_GiB=100 # 100 GiB
UNIQUE_FILE_PATH="myfilepath2" # Please note that creation token needs to be unique within all ANF Accounts

az netappfiles volume create \
    --resource-group $RESOURCE_GROUP \
    --location $LOCATION \
    --account-name $ANF_ACCOUNT_NAME \
    --pool-name $POOL_NAME \
    --name "myvol1" \
    --service-level $SERVICE_LEVEL \
    --vnet $VNET_ID \
    --subnet $SUBNET_ID \
    --usage-threshold $VOLUME_SIZE_GiB \
    --creation-token $UNIQUE_FILE_PATH \
    --protocol-types "NFSv3"
```

## <a name="create-the-persistentvolume"></a>Создание устойчивого объема

Перечислите детали вашего тома, используя [объемный показ az netappfiles][az-netappfiles-volume-show]

```azurecli
az netappfiles volume show --resource-group $RESOURCE_GROUP --account-name $ANF_ACCOUNT_NAME --pool-name $POOL_NAME --volume-name "myvol1"
```

```output
{
  ...
  "creationToken": "myfilepath2",
  ...
  "mountTargets": [
    {
      ...
      "ipAddress": "10.0.0.4",
      ...
    }
  ],
  ...
}
```

Создайте `pv-nfs.yaml` определяющий объем. Заменить `path` *creationToken* `server` и *ipAddress* из предыдущей команды. Пример:

```yaml
---
apiVersion: v1
kind: PersistentVolume
metadata:
  name: pv-nfs
spec:
  capacity:
    storage: 100Gi
  accessModes:
    - ReadWriteMany
  nfs:
    server: 10.0.0.4
    path: /myfilepath2
```

Обновление *сервера* и *путь* к значениям вашего NFS (сетевая файловая система) объем вы создали на предыдущем этапе. Создайте PersistentVolume с помощью команды [kubectl:][kubectl-apply]

```console
kubectl apply -f pv-nfs.yaml
```

Проверить *состояние* persistentVolume *доступно* с помощью [команды kubectl описать:][kubectl-describe]

```console
kubectl describe pv pv-nfs
```

## <a name="create-the-persistentvolumeclaim"></a>Создание persistentVolumeClaim

Создайте `pvc-nfs.yaml` определяющий объем. Пример:

```yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: pvc-nfs
spec:
  accessModes:
    - ReadWriteMany
  storageClassName: ""
  resources:
    requests:
      storage: 1Gi
```

Создайте PersistentVolumeClaim с помощью команды [kubectl:][kubectl-apply]

```console
kubectl apply -f pvc-nfs.yaml
```

Проверить *состояние* persistentVolumeClaim *связанс* с помощью [команды kubectl описать:][kubectl-describe]

```console
kubectl describe pvc pvc-nfs
```

## <a name="mount-with-a-pod"></a>Гора с стручком

Создайте `nginx-nfs.yaml` определяющую капсулу, используюую PersistentVolumeClaim. Пример:

```yaml
kind: Pod
apiVersion: v1
metadata:
  name: nginx-nfs
spec:
  containers:
  - image: nginx
    name: nginx-nfs
    command:
    - "/bin/sh"
    - "-c"
    - while true; do echo $(date) >> /mnt/azure/outfile; sleep 1; done
    volumeMounts:
    - name: disk01
      mountPath: /mnt/azure
  volumes:
  - name: disk01
    persistentVolumeClaim:
      claimName: pvc-nfs
```

Создайте стручок с [командой kubectl применить:][kubectl-apply]

```console
kubectl apply -f nginx-nfs.yaml
```

Проверить стручок *Работает* с помощью [kubectl описать][kubectl-describe] команду:

```console
kubectl describe pod nginx-nfs
```

Проверьте ваш объем был установлен в стручок с помощью [kubectl exec][kubectl-exec] для подключения к стручок затем, `df -h` чтобы проверить, если объем установлен.

```console
$ kubectl exec -it nginx-nfs -- bash
```

```output
root@nginx-nfs:/# df -h
Filesystem             Size  Used Avail Use% Mounted on
...
10.0.0.4:/myfilepath2  100T  384K  100T   1% /mnt/azure
...
```

## <a name="next-steps"></a>Дальнейшие действия

Для получения дополнительной информации о файлах NetApp Azure смотрите [файлы NetApp Azure.][anf] Для получения дополнительной информации об использовании NFS с AKS, [см.][aks-nfs]


[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[aks-nfs]: azure-nfs-volume.md
[anf]: ../azure-netapp-files/azure-netapp-files-introduction.md
[anf-delegate-subnet]: ../azure-netapp-files/azure-netapp-files-delegate-subnet.md
[anf-quickstart]: ../azure-netapp-files/
[anf-regions]: https://azure.microsoft.com/global-infrastructure/services/?products=netapp&regions=all
[anf-waitlist]: https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR8cq17Xv9yVBtRCSlcD_gdVUNUpUWEpLNERIM1NOVzA5MzczQ0dQR1ZTSS4u
[az-aks-show]: /cli/azure/aks#az-aks-show
[az-netappfiles-account-create]: /cli/azure/netappfiles/account?view=azure-cli-latest#az-netappfiles-account-create
[az-netappfiles-pool-create]: /cli/azure/netappfiles/pool?view=azure-cli-latest#az-netappfiles-pool-create
[az-netappfiles-volume-create]: /cli/azure/netappfiles/volume?view=azure-cli-latest#az-netappfiles-volume-create
[az-netappfiles-volume-show]: /cli/azure/netappfiles/volume?view=azure-cli-latest#az-netappfiles-volume-show
[az-network-vnet-subnet-create]: /cli/azure/network/vnet/subnet?view=azure-cli-latest#az-network-vnet-subnet-create
[install-azure-cli]: /cli/azure/install-azure-cli
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-describe]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#describe
[kubectl-exec]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#exec
