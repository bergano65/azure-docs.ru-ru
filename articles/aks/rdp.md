---
title: RDP в блокальные узлы Windows Server Службы Azure (AKS)
description: Узнайте, как создать соединение RDP с кластером Azure Kubernetes (AKS) кластера Windows Server для устранения неполадок и задач обслуживания.
services: container-service
ms.topic: article
ms.date: 06/04/2019
ms.openlocfilehash: 897504aa9902d0feaf4245c719d3a4a3c6fd2241
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77594487"
---
# <a name="connect-with-rdp-to-azure-kubernetes-service-aks-cluster-windows-server-nodes-for-maintenance-or-troubleshooting"></a>Подключитесь к блоку Службы Windows Server с RDP в кластере Azure Kubernetes (AKS) для технического обслуживания или устранения неполадок

На протяжении всего жизненного цикла кластера Azure Kubernetes Service (AKS) может потребоваться доступ к узелу AKS Windows Server. Этот доступ используется для обслуживания, сбора журналов или других операций по устранению неполадок. Вы можете получить доступ к узлам AKS Windows Server с помощью RDP. Кроме того, если вы хотите использовать SSH для доступа к узлам AKS Windows Server и у вас есть доступ к той же паре ключей, которая использовалась при создании кластера, вы можете следовать шагам в [SSH в кластерные узлы Azure Kubernetes Service (AKS).][ssh-steps] В целях безопасности узлы Службы Azure Kubernetes (AKS) недоступны через Интернет.

Поддержка узлов Windows Server в настоящее время находится в предварительном просмотре в AKS.

В этой статье показано, как создать соединение RDP с узлами AKS с помощью своих частных IP-адресов.

## <a name="before-you-begin"></a>Перед началом

В этой статье предполагается, что у вас есть существующий кластер AKS с узлами Windows Server. Если вам нужен кластер AKS, смотрите статью о [создании кластера AKS с контейнером Windows с помощью Azure CLI.][aks-windows-cli] Вам нужно имя пользователя администратора Windows и пароль для узла Windows Server, который вы хотите устранить. Вам также нужен клиент RDP, такой как [Microsoft Remote Desktop.][rdp-mac]

Вам также нужна версия Azure CLI 2.0.61 или более поздняя установка и настройка. Чтобы узнать версию, выполните команду  `az --version`. Если вам необходимо выполнить установку или обновление, см. статью  [Установка Azure CLI][install-azure-cli].

## <a name="deploy-a-virtual-machine-to-the-same-subnet-as-your-cluster"></a>Развертывание виртуальной машины в той же подсети, что и кластер

Узлы Windows Server кластера AKS не имеют внешних IP-адресов. Чтобы сделать соединение RDP, можно развернуть виртуальную машину с общедоступным IP-адресом в той же подсети, что и узлы Windows Server.

Следующий пример создает виртуальную машину под названием *myVM* в группе ресурсов *myResourceGroup.*

Во-первых, получите подсеть, используемую пулом узлов Windows Server. Чтобы получить идентификатор подсети, вам нужно название подсети. Чтобы получить название подсети, вам нужно имя vnet. Получите имя Vnet, задав запрос кластера для его списка сетей. Для запроса кластера необходимо его имя. Вы можете получить все это, запустив следующие в оболочке облака Azure:

```azurecli-interactive
CLUSTER_RG=$(az aks show -g myResourceGroup -n myAKSCluster --query nodeResourceGroup -o tsv)
VNET_NAME=$(az network vnet list -g $CLUSTER_RG --query [0].name -o tsv)
SUBNET_NAME=$(az network vnet subnet list -g $CLUSTER_RG --vnet-name $VNET_NAME --query [0].name -o tsv)
SUBNET_ID=$(az network vnet subnet show -g $CLUSTER_RG --vnet-name $VNET_NAME --name $SUBNET_NAME --query id -o tsv)
```

Теперь, когда у вас есть SUBNET_ID, запустите следующую команду в том же окне облака Azure, чтобы создать VM:

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

Следующий пример вывода показывает, что VM был успешно создан и отображает общедоступный IP-адрес виртуальной машины.

```console
13.62.204.18
```

Запись общедоступного IP-адреса виртуальной машины. Этот адрес будет использоваться на более позднем этапе.

## <a name="allow-access-to-the-virtual-machine"></a>Разрешить доступ к виртуальной машине

Подсети пула узлов AKS защищены НСК (группы сетевой безопасности) по умолчанию. Чтобы получить доступ к виртуальной машине, вам придется ввести доступ в NSG.

> [!NOTE]
> НСГ контролируются службой АКС. Любые изменения, которые вы внесете в NSG, будут перезаписаны в любое время на плоскости управления.
>

Во-первых, получить группу ресурсов и ng имя nsg, чтобы добавить правило:

```azurecli-interactive
CLUSTER_RG=$(az aks show -g myResourceGroup -n myAKSCluster --query nodeResourceGroup -o tsv)
NSG_NAME=$(az network nsg list -g $CLUSTER_RG --query [].name -o tsv)
```

Затем создайте правило NSG:

```azurecli-interactive
az network nsg rule create --name tempRDPAccess --resource-group $CLUSTER_RG --nsg-name $NSG_NAME --priority 100 --destination-port-range 3389 --protocol Tcp --description "Temporary RDP access to Windows nodes"
```

## <a name="get-the-node-address"></a>Получить адрес узла

Управлять кластером Kubernetes можно при помощи [kubectl][kubectl], клиента командной строки Kubernetes. Если вы используете Azure Cloud Shell, `kubectl` уже установлен. Чтобы установить `kubectl` локально, используйте команду [az aks install-cli][az-aks-install-cli]:
    
```azurecli-interactive
az aks install-cli
```

Чтобы настроить `kubectl` на подключение к кластеру Kubernetes, выполните команду [az aks get-credentials][az-aks-get-credentials]. Эта команда скачивает учетные данные и настраивает интерфейс командной строки Kubernetes для их использования.

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

Перечислите внутренний IP-адрес узлов Windows Server с помощью [команды kubectl:][kubectl-get]

```console
kubectl get nodes -o wide
```

В следующем выводе примера показаны внутренние IP-адреса всех узлов кластера, включая узлы Windows Server.

```console
$ kubectl get nodes -o wide
NAME                                STATUS   ROLES   AGE   VERSION   INTERNAL-IP   EXTERNAL-IP   OS-IMAGE                    KERNEL-VERSION      CONTAINER-RUNTIME
aks-nodepool1-42485177-vmss000000   Ready    agent   18h   v1.12.7   10.240.0.4    <none>        Ubuntu 16.04.6 LTS          4.15.0-1040-azure   docker://3.0.4
aksnpwin000000                      Ready    agent   13h   v1.12.7   10.240.0.67   <none>        Windows Server Datacenter   10.0.17763.437
```

Запишите внутренний IP-адрес узла Windows Server, который вы хотите устранить. Этот адрес будет использоваться на более позднем этапе.

## <a name="connect-to-the-virtual-machine-and-node"></a>Подключение к виртуальной машине и узлам

Подключайтесь к общедоступному IP-адресу виртуальной машины, созданной ранее, с помощью клиента RDP, такого как [Microsoft Remote Desktop.][rdp-mac]

![Изображение подключения к виртуальной машине с помощью клиента RDP](media/rdp/vm-rdp.png)

После подключения к виртуальной машине подключитесь к *внутреннему IP-адресу* узла Windows Server, который необходимо устранить с помощью клиента RDP внутри виртуальной машины.

![Изображение подключения к узлам Сервера Windows с помощью клиента RDP](media/rdp/node-rdp.png)

Теперь вы подключены к узлам Windows Server.

![Изображение окна cmd в узло сервера Windows Server](media/rdp/node-session.png)

Теперь вы можете запускать любые команды по устранению неполадок в *окне cmd.* Поскольку узлы Windows Server используют ядро Windows Server Core, при подключении к узлу Сервера Windows -10 не существует полного графического интерфейса или других инструментов графического интерфейса.

## <a name="remove-rdp-access"></a>Удаление доступа К RDP

После этого выйдите из RDP-соединения в узла Windows Server, а затем выйдите из сеанса RDP в виртуальную машину. После выхода из обоих сеансов RDP удалите виртуальную машину с командой [удаления az vm:][az-vm-delete]

```azurecli-interactive
az vm delete --resource-group myResourceGroup --name myVM
```

И правило NSG:

```azurecli-interactive
CLUSTER_RG=$(az aks show -g myResourceGroup -n myAKSCluster --query nodeResourceGroup -o tsv)
NSG_NAME=$(az network nsg list -g $CLUSTER_RG --query [].name -o tsv)
```

```azurecli-interactive
az network nsg rule delete --resource-group $CLUSTER_RG --nsg-name $NSG_NAME --name tempRDPAccess
```

## <a name="next-steps"></a>Дальнейшие действия

Если вам нужны дополнительные данные по устранению неполадок, можно [просмотреть журналы узлов Kubernetes][view-master-logs] или [Azure Monitor.][azure-monitor-containers]

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
