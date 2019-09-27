---
title: Интеграция Azure NetApp Files со службой Kubernetes Azure
description: Узнайте, как интегрировать Azure NetApp Files со службой Kubernetes Azure
services: container-service
author: zr-msft
ms.service: container-service
ms.topic: article
ms.date: 09/26/2019
ms.author: zarhoads
ms.openlocfilehash: 84192a831e3b1f24e20eb07a6c8695516c28970f
ms.sourcegitcommit: e9936171586b8d04b67457789ae7d530ec8deebe
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/27/2019
ms.locfileid: "71329335"
---
# <a name="integrate-azure-netapp-files-with-azure-kubernetes-service"></a>Интеграция Azure NetApp Files со службой Kubernetes Azure

[Azure NetApp Files][anf] — это высокопроизводительная служба хранилища файлов в масштабе корпоративного класса, работающая в Azure. В этой статье показано, как интегрировать Azure NetApp Files со службой Azure Kubernetes (AKS).

## <a name="before-you-begin"></a>Перед началом работы
В этой статье предполагается, что у вас есть кластер AKS. Если вам нужен кластер AKS, ознакомьтесь с кратким руководством по AKS, [используя Azure CLI][aks-quickstart-cli] или [с помощью портал Azure][aks-quickstart-portal].

> [!IMPORTANT]
> Кластер AKS также должен находиться [в регионе, поддерживающем Azure NetApp Files][anf-regions].

Также требуется Azure CLI версии 2.0.59 или более поздней. Чтобы узнать версию, выполните команду  `az --version`. Если необходимо установить или обновить, см. раздел [install Azure CLI][install-azure-cli].

### <a name="limitations"></a>Ограничения

При использовании Azure NetApp Files применяются следующие ограничения.

* Azure NetApp Files доступен только [в выбранных регионах Azure][anf-regions].
* Прежде чем можно будет использовать Azure NetApp Files, необходимо предоставить доступ к службе Azure NetApp Files. Для применения к Access можно использовать [форму Azure NetApp Files отправки ваитлист][anf-waitlist]. Вы не можете получить доступ к службе Azure NetApp Files, пока не получите официальное электронное письмо с подтверждением от команды Azure NetApp Files.
* Служба Azure NetApp Files должна быть создана в той же виртуальной сети, что и кластер AKS.
* В AKS поддерживается только статическая подготовка для Azure NetApp Files.

## <a name="configure-azure-netapp-files"></a>Настройка Azure NetApp Files

> [!IMPORTANT]
> Прежде чем можно будет зарегистрировать поставщик ресурсов *Microsoft. NetApp* , необходимо заполнить [форму отправки Azure NetApp Files ваитлист][anf-waitlist] для своей подписки. Вы не можете зарегистрировать ресурс, пока не получите официальное электронное письмо с подтверждением от команды Azure NetApp Files.

Зарегистрируйте поставщик ресурсов *Microsoft. NetApp* :

```azure-cli
az provider register --namespace Microsoft.NetApp --wait
```

> [!NOTE]
> Это может занять некоторое время.

При создании учетной записи Azure NetApp для использования с AKS необходимо создать учетную запись в группе ресурсов **узла** . Сначала получите имя группы ресурсов с помощью команды [AZ AKS показывать][az-aks-show] и добавьте параметр запроса `--query nodeResourceGroup`. В следующем примере выполняется получение группы ресурсов узла для кластера AKS с именем *myAKSCluster* в группе ресурсов с именем *myResourceGroup*:

```azurecli-interactive
$ az aks show --resource-group myResourceGroup --name myAKSCluster --query nodeResourceGroup -o tsv

MC_myResourceGroup_myAKSCluster_eastus
```

Создайте учетную запись Azure NetApp Files в группе ресурсов **узла** и в том же регионе, что и кластер AKS, выполнив команду [AZ нетаппфилес Account Create][az-netappfiles-account-create]. В следующем примере создается учетная запись с именем *myaccount1* в группе ресурсов *MC_myResourceGroup_myAKSCluster_eastus* и регионе *eastus* :

```azure-cli
az netappfiles account create \
    --resource-group MC_myResourceGroup_myAKSCluster_eastus \
    --location eastus \
    --account-name myaccount1
```

Создайте пул ресурсов с помощью команды [AZ нетаппфилес Pool Create][az-netappfiles-pool-create]. В следующем примере создается новый пул ресурсов с именем *mypool1* с размером 4 ТБ и уровнем обслуживания *Premium* :

```azure-cli
az netappfiles pool create \
    --resource-group MC_myResourceGroup_myAKSCluster_eastus \
    --location eastus \
    --account-name myaccount1 \
    --pool-name mypool1 \
    --size 4 \
    --service-level Premium
```

Создайте подсеть для [делегирования Azure NetApp Files][anf-delegate-subnet] с помощью команды [AZ Network vnet подсети Create][az-network-vnet-subnet-create]. *Эта подсеть должна находиться в той же виртуальной сети, что и кластер AKS.*

```azure-cli
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

Создайте том с помощью команды [AZ нетаппфилес Volume Create][az-netappfiles-volume-create].

```azure-cli
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

## <a name="create-the-persistentvolume"></a>Создание Персистентволуме

Выведите список сведений о томе с помощью команды [AZ нетаппфилес Volume демонстрация][az-netappfiles-volume-show] .
```azure-cli
$ az netappfiles volume show --resource-group $RESOURCE_GROUP --account-name $ANF_ACCOUNT_NAME --pool-name $POOL_NAME --volume-name "myvol1"

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

Создайте `pv-nfs.yaml`, определив Персистентволуме. Замените `path` на *креатионтокен* и `server` с *ipAddress* из предыдущей команды. Пример:

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

Обновите *сервер* и *путь* к значениям тома NFS (сетевой файловой системы), созданного на предыдущем шаге. Создайте Персистентволуме с помощью команды [kubectl Apply][kubectl-apply] :

```console
kubectl apply -f pv-nfs.yaml
```

Убедитесь, что *состояние* персистентволуме *доступно* с помощью команды [kubectl описание][kubectl-describe] :

```console
kubectl describe pv pv-nfs
```

## <a name="create-the-persistentvolumeclaim"></a>Создание Персистентволумеклаим

Создайте `pvc-nfs.yaml`, определив Персистентволуме. Пример:

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

Создайте Персистентволумеклаим с помощью команды [kubectl Apply][kubectl-apply] :

```console
kubectl apply -f pvc-nfs.yaml
```

Убедитесь, что *состояние* персистентволумеклаим *привязано* с помощью команды [kubectl, описанной][kubectl-describe] ниже.

```console
kubectl describe pvc pvc-nfs
```

## <a name="mount-with-a-pod"></a>Подключение с помощью Pod

Создайте `nginx-nfs.yaml`, определив модуль, использующий Персистентволумеклаим. Пример:

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

Создайте модуль Pod с помощью команды [kubectl Apply][kubectl-apply] :

```console
kubectl apply -f nginx-nfs.yaml
```

Убедитесь, что модуль *выполняется* с помощью команды [kubectl, описанной][kubectl-describe] ниже.

```console
kubectl describe pod nginx-nfs
```

Убедитесь, что том подключен к модулю, используя [kubectl Exec][kubectl-exec] для подключения к Pod, затем `df -h`, чтобы проверить, подключен ли том.

```console
$ kubectl exec -it nginx-nfs -- bash

root@nginx-nfs:/# df -h
Filesystem             Size  Used Avail Use% Mounted on
...
10.0.0.4:/myfilepath2  100T  384K  100T   1% /mnt/azure
...
```

## <a name="next-steps"></a>Следующие шаги

Дополнительные сведения о Azure NetApp Files см. в разделе [что такое Azure NetApp Files][anf]. Дополнительные сведения об использовании NFS с AKS см. в статье [Создание и использование NFS-сервера Linux с помощью службы Kubernetes Azure (AKS) вручную][aks-nfs].


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
