---
title: RDP в узлы Windows Server кластера Azure Kubernetes Service (AKS)
description: Сведения о создании RDP-подключение с кластером Azure Kubernetes Service (AKS) узлов Windows Server для устранения неполадок и обслуживание задач.
services: container-service
author: tylermsft
ms.service: container-service
ms.topic: article
ms.date: 06/04/2019
ms.author: twhitney
ms.openlocfilehash: 11f6869d4d5a2ee0ef2e986ee8268c7a001ea015
ms.sourcegitcommit: 6932af4f4222786476fdf62e1e0bf09295d723a1
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/05/2019
ms.locfileid: "66688636"
---
# <a name="connect-with-rdp-to-azure-kubernetes-service-aks-cluster-windows-server-nodes-for-maintenance-or-troubleshooting"></a>Подключение с помощью протокола удаленного рабочего СТОЛА для службы Azure Kubernetes (AKS) узлов кластера Windows Server для обслуживания или устранения неполадок

На протяжении жизненного цикла кластера Azure Kubernetes Service (AKS) может потребоваться доступ к узлу AKS Windows Server. Этот доступ используется для обслуживания, сбора журналов или других операций по устранению неполадок. Можно получить доступ к узлов AKS Windows Server, с помощью протокола удаленного рабочего СТОЛА. Кроме того, если вы хотите использовать SSH для доступа к узлам AKS Windows Server и у вас есть доступ к тем же пару ключей, который использовался во время создания кластера, необходимо выполнить действия, описанные в [SSH в узлы кластера Azure Kubernetes Service (AKS)] [ssh-steps]. В целях безопасности узлы Службы Azure Kubernetes (AKS) недоступны через Интернет.

Поддержка узлов Windows Server в настоящее время доступна Предварительная версия в AKS.

В этой статье показано, как создать RDP-подключение с узлом AKS с помощью их частных IP-адресов.

## <a name="before-you-begin"></a>Перед началом работы

В этой статье предполагается, что у вас есть кластер AKS с узла Windows Server. Если вам нужен кластер AKS, см. в статье на [создание кластера AKS с помощью контейнера Windows с помощью Azure CLI][aks-windows-cli]. Требуется имя пользователя администратора Windows и пароль для узла Windows Server, которым нужно устранить проблему. Вам также понадобится клиент RDP например [удаленный рабочий стол Майкрософт][rdp-mac].

Вам также понадобится Azure CLI версии 2.0.61 или более поздней версии установлен и настроен. Чтобы узнать версию, выполните команду  `az --version`. Если вам необходимо выполнить установку или обновление, см. статью  [Установка Azure CLI][install-azure-cli].

## <a name="deploy-a-virtual-machine-to-the-same-subnet-as-your-cluster"></a>Развертывание виртуальной машины в той же подсети, что и кластер

Windows Server узлы кластера AKS отсутствует доступный IP-адресов. Чтобы сделать RDP-подключение, можно развернуть виртуальную машину с общедоступным IP-адрес для той же подсети, что узлы Windows Server.

В следующем примере создается виртуальная машина с именем *myVM* в *myResourceGroup* группы ресурсов.

Во-первых получите подсети, используемой в пуле узлов Windows Server. Чтобы получить идентификатор подсети, необходимо знать имя подсети. Чтобы получить имя подсети, необходимо знать имя виртуальной сети. Получите имя виртуальной сети путем запроса для его список сетей кластера. Чтобы запросить кластер, необходимо его имя. Вы можете получить все это, выполнив в Azure Cloud Shell:

```azurecli-interactive
CLUSTER_RG=$(az aks show -g myResourceGroup -n myAKSCluster --query nodeResourceGroup -o tsv)
VNET_NAME=$(az network vnet list -g $CLUSTER_RG --query [0].name -o tsv)
SUBNET_NAME=$(az network vnet subnet list -g $CLUSTER_RG --vnet-name $VNET_NAME --query [0].name -o tsv)
SUBNET_ID=$(az network vnet subnet show -g $CLUSTER_RG --vnet-name $VNET_NAME --name $SUBNET_NAME --query id -o tsv)
```

Теперь, когда у вас есть SUBNET_ID, выполните следующую команду в окне Azure Cloud Shell для создания виртуальной Машины:

```azurecli-interactive
az vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --image win2019datacenter \
    --admin-username azureuser \
    --admin-password myP@ssw0rd12 \
    --subnet $SUBNET_ID \
    --query publicIpAddress -o tsv
```

В следующем примере выходных данных показано, успешно создана виртуальная машина и общедоступный IP-адрес виртуальной машины.

```console
13.62.204.18
```

Запишите общедоступный IP-адрес виртуальной машины. Этот адрес используется на более позднем этапе.

## <a name="get-the-node-address"></a>Получить адрес узла

Управлять кластером Kubernetes можно при помощи [kubectl][kubectl], клиента командной строки Kubernetes. Если вы используете Azure Cloud Shell, `kubectl` уже установлен. Чтобы установить `kubectl` локально, используйте команду [az aks install-cli][az-aks-install-cli]:
    
```azurecli-interactive
az aks install-cli
```

Чтобы настроить подключение `kubectl` к кластеру Kubernetes, выполните команду [az aks get-credentials][az-aks-get-credentials]. Эта команда скачивает учетные данные и настраивает интерфейс командной строки Kubernetes для их использования.

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

Внутренний IP-адрес узлов Windows Server, с использованием [kubectl get] [ kubectl-get] команды:

```console
kubectl get nodes -o wide
```

В следующем примере выходных данных показаны внутренние IP-адреса всех узлов в кластере, в том числе на узлах Windows Server.

```console
$ kubectl get nodes -o wide
NAME                                STATUS   ROLES   AGE   VERSION   INTERNAL-IP   EXTERNAL-IP   OS-IMAGE                    KERNEL-VERSION      CONTAINER-RUNTIME
aks-nodepool1-42485177-vmss000000   Ready    agent   18h   v1.12.7   10.240.0.4    <none>        Ubuntu 16.04.6 LTS          4.15.0-1040-azure   docker://3.0.4
aksnpwin000000                      Ready    agent   13h   v1.12.7   10.240.0.67   <none>        Windows Server Datacenter   10.0.17763.437
```

Запишите внутренний IP-адрес узла Windows Server, для устранения неполадок. Этот адрес используется на более позднем этапе.

## <a name="connect-to-the-virtual-machine-and-node"></a>Подключитесь к виртуальной машине и узла

Подключение к общедоступный IP-адрес виртуальной машины, созданной ранее с помощью клиент RDP, например [удаленный рабочий стол Майкрософт][rdp-mac].

![Снимок экрана: подключение к виртуальной машине, с помощью клиента удаленного рабочего СТОЛА](media/rdp/vm-rdp.png)

После подключения к виртуальной машине, подключиться к *внутренний IP-адрес* узла Windows Server, нужно устранить проблему с помощью клиента удаленного рабочего СТОЛА из вашей виртуальной машине.

![Снимок экрана: подключение к узлу Windows Server с помощью клиента удаленного рабочего СТОЛА](media/rdp/node-rdp.png)

Теперь вы подключены к узлу Windows Server.

![Изображение окна командной строки в узле Windows Server](media/rdp/node-session.png)

Теперь можно запустить любой команды для устранения неполадок *cmd* окна. Так как узлы Windows Server, используют Windows Server Core, не полным графическим пользовательским Интерфейсом или другие средства графического интерфейса пользователя при подключении к узлу Windows Server по RDP.

## <a name="remove-rdp-access"></a>Удалить доступ по протоколу RDP

Закончив, выйдите из RDP-подключение к узлу Windows Server, а затем выйдите из сеанса удаленного рабочего СТОЛА к виртуальной машине. После выхода из обоих RDP-сеансы, удалить виртуальную машину с [удаление виртуальной машины az] [ az-vm-delete] команды:

```azurecli-interactive
az vm delete --resource-group myResourceGroup --name myVM
```

## <a name="next-steps"></a>Дальнейшие действия

Если вам нужны дополнительные данные об устранении неполадок, вы можете [просмотра журналов главном узле Kubernetes] [ view-master-logs] или [Azure Monitor][azure-monitor-containers].

<!-- EXTERNAL LINKS -->
[kubectl]: https://kubernetes.io/docs/user-guide/kubectl/
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[rdp-mac]: https://aka.ms/rdmac

<!-- INTERNAL LINKS -->
[aks-windows-cli]: windows-container-cli.md
[az-aks-install-cli]: /cli/azure/aks?view=azure-cli-latest#az-aks-install-cli
[az-aks-get-credentials]: /cli/azure/aks?view=azure-cli-latest#az-aks-get-credentials
[az-vm-delete]: /cli/azure/vm#az-vm-delete
[azure-monitor-containers]: ../azure-monitor/insights/container-insights-overview.md
[install-azure-cli]: /cli/azure/install-azure-cli
[ssh-steps]: ssh.md
[view-master-logs]: view-master-logs.md
