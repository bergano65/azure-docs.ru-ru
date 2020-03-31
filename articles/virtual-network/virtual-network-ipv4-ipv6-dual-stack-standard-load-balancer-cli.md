---
title: Развертывание iPv6 двойное приложение стек - Стандартный баланс - CLI
titlesuffix: Azure Virtual Network
description: В этой статье показано, как развертывать приложение с двойным стеком IPv6 в виртуальной сети Azure с помощью Azure CLI.
services: virtual-network
documentationcenter: na
author: KumudD
manager: mtillman
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/17/2019
ms.author: kumud
ms.openlocfilehash: fa895a294e26b6c74ab72afa3136feac2b2ec986
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80240249"
---
# <a name="deploy-an-ipv6-dual-stack-application-in-azure-virtual-network---cli-preview"></a>Развертывание приложения с двойным стеком IPv6 в виртуальной сети Azure - CLI (Предварительный просмотр)

В этой статье показано, как развернуть двойной стек (IPv4 - IPv6) приложение с помощью Standard Load Balancer в Azure, который включает в себя двойной стек виртуальной сети с двойным стеком подсети, балансер стандартной нагрузки с двойной (IPv4 iPv6) передние конфигурации, VMs с NICs, которые имеют двойную конфигурацию IP, правила двойной группы безопасности сети и двойные общедоступные IP-адреса.

> [!Important]
> Двойной стек IPv6 для виртуальной сети Azure в настоящее время находится в открытом доступе. Предварительная версия предоставляется без соглашения об уровне обслуживания. Не рекомендуем использовать ее в рабочей среде. Некоторые функции могут не поддерживаться или их возможности могут быть ограничены. См. [дополнительные условия использования для предварительных версий Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Если вместо этого вы решили установить и использовать Azure CLI локально, этот быстрый запуск требует от вас использования версии Azure CLI 2.0.49 или позже. Выполните команду `az --version`, чтобы узнать установленную версию. Сведения об установке или обновлении Azure CLI см. в [этой статье](/cli/azure/install-azure-cli).

## <a name="prerequisites"></a>Предварительные требования
Чтобы использовать функцию виртуальной сети Azv6 для Azure, необходимо настроить подписку с помощью Azure CLI следующим образом:

```azurecli
az feature register --name AllowIPv6VirtualNetwork --namespace Microsoft.Network
az feature register --name AllowIPv6CAOnStandardLB --namespace Microsoft.Network
```

Регистрация функции занимает до 30 минут. Проверить свой регистрационный статус можно, запустив следующую команду Azure CLI:

```azurecli
az feature show --name AllowIPv6VirtualNetwork --namespace Microsoft.Network
az feature show --name AllowIPv6CAOnStandardLB --namespace Microsoft.Network
```

После регистрации выполните следующую команду:

```azurecli
az provider register --namespace Microsoft.Network
```

## <a name="create-a-resource-group"></a>Создание группы ресурсов

Прежде чем создать виртуальную сеть с двойным стеком, необходимо создать группу ресурсов с [созданием группы az.](/cli/azure/group) Следующий пример создает группу ресурсов под названием *DsResourceGroup01* в *восточном* месте:

```azurecli
az group create \
--name DsResourceGroup01 \
--location eastus
```

## <a name="create-ipv4-and-ipv6-public-ip-addresses-for-load-balancer"></a>Создание общедоступных IP-адресов IPv4 и IPv6 для балансировора нагрузки
Чтобы получить доступ к конечным точкам IPv4 и IPv6 в Интернете, вам нужно Ip-адреса IPv4 и IPv6 для балансировора нагрузки. Создайте общедоступный IP-адрес с помощью команды [az network public-ip create](/cli/azure/network/public-ip). Следующий пример создает публичный IP-адрес IPv4 и IPv6 под названием *dsPublicIP_v4* и *dsPublicIP_v6* в группе ресурсов *DsResourceGroup01:*

```azurecli
# Create an IPV4 IP address
az network public-ip create \
--name dsPublicIP_v4  \
--resource-group DsResourceGroup01  \
--location eastus  \
--sku STANDARD  \
--allocation-method static  \
--version IPv4

# Create an IPV6 IP address
az network public-ip create \
--name dsPublicIP_v6  \
--resource-group DsResourceGroup01  \
--location eastus \
--sku STANDARD  \
--allocation-method static  \
--version IPv6

```

## <a name="create-public-ip-addresses-for-vms"></a>Создание общедоступных IP-адресов для vMs

Для удаленного доступа к визам в Интернете необходимо IP-адреса IPv4 для внештатных ими. Создайте общедоступный IP-адрес с помощью команды [az network public-ip create](/cli/azure/network/public-ip).

```azurecli
az network public-ip create \
--name dsVM0_remote_access  \
--resource-group DsResourceGroup01 \
--location eastus  \
--sku Standard  \
--allocation-method static  \
--version IPv4

az network public-ip create \
--name dsVM1_remote_access  \
--resource-group DsResourceGroup01  \
--location eastus  \
--sku Standard  \
--allocation-method static  \
--version IPv4
```

## <a name="create-standard-load-balancer"></a>Создание Load Balancer уровня "Стандартный"

В этом разделе вы настраиваете двойной ip-адрес (IPv4 и IPv6) и пул адресов бэк-энда для балансопарка нагрузки, а затем создаете балансер стандартной нагрузки.

### <a name="create-load-balancer"></a>Создание подсистемы балансировки нагрузки

Создайте Standard Load Balancer с [az network lb создать](https://docs.microsoft.com/cli/azure/network/lb?view=azure-cli-latest) названный **dsLB,** который включает в себя пул переднего энда имени **dsLbFrontEnd_v4**, бэкэнд пул имени **dsLbBackEndPool_v4,** который связан с IPv4 общественный IP-адрес **dsPublicIP_v4,** который вы создали в предыдущем шаге. 

```azurecli
az network lb create \
--name dsLB  \
--resource-group DsResourceGroup01 \
--sku Standard \
--location eastus \
--frontend-ip-name dsLbFrontEnd_v4  \
--public-ip-address dsPublicIP_v4  \
--backend-pool-name dsLbBackEndPool_v4
```

### <a name="create-ipv6-frontend"></a>Создание передней панели IPv6

Создайте IPV6 передний IP с [az сети lb frontend-IP создать.](https://docs.microsoft.com/cli/azure/network/lb/frontend-ip?view=azure-cli-latest#az-network-lb-frontend-ip-create) Следующий пример создает конфигурацию IP-адреса фронтейка под названием *dsLbFrontEnd_v6* и прикрепляет *dsPublicIP_v6* адрес:

```azurepowershell-interactive
az network lb frontend-ip create \
--lb-name dsLB  \
--name dsLbFrontEnd_v6  \
--resource-group DsResourceGroup01  \
--public-ip-address dsPublicIP_v6

```

### <a name="configure-ipv6-back-end-address-pool"></a>Настройка пула адресов IPv6

Создайте пулы адресов IPv6 с [помощью адреса-пула az network lb.](https://docs.microsoft.com/cli/azure/network/lb/address-pool?view=azure-cli-latest#az-network-lb-address-pool-create) Следующий пример создает пул адресов бэк-энда, названный *dsLbBackEndPool_v6* включить в него виртуальные выши с конфигурациями IPv6 NIC:

```azurecli
az network lb address-pool create \
--lb-name dsLB  \
--name dsLbBackEndPool_v6  \
--resource-group DsResourceGroup01
```

### <a name="create-a-health-probe"></a>Создание пробы работоспособности
Создайте зонд здоровья с [az зондом lb сети создать](https://docs.microsoft.com/cli/azure/network/lb/probe?view=azure-cli-latest) для того чтобы контролировать работоспособность фактически машин. 

```azurecli
az network lb probe create -g DsResourceGroup01  --lb-name dsLB -n dsProbe --protocol tcp --port 3389
```

### <a name="create-a-load-balancer-rule"></a>Создание правила балансировщика нагрузки

Правило балансировщика нагрузки позволяет определить распределение трафика между виртуальными машинами. Вы определяете конфигурацию внешнего IP-адреса для входящего трафика и пул внутренних IP-адресов для приема трафика, а также требуемый порт источника и назначения. 

Создайте правило балансировщика нагрузки с помощью команды [az network lb rule create](https://docs.microsoft.com/cli/azure/network/lb/rule?view=azure-cli-latest#az-network-lb-rule-create). Следующий пример создает правила балансоподъемности *нагрузки,* названные dsLBrule_v4 и *dsLBrule_v6* и уравновешивает трафик на *tCP* port *80* с конфигурациями IP-класса IPv4 и IPv6:

```azurecli
az network lb rule create \
--lb-name dsLB  \
--name dsLBrule_v4  \
--resource-group DsResourceGroup01  \
--frontend-ip-name dsLbFrontEnd_v4  \
--protocol Tcp  \
--frontend-port 80  \
--backend-port 80  \
--probe-name dsProbe \
--backend-pool-name dsLbBackEndPool_v4


az network lb rule create \
--lb-name dsLB  \
--name dsLBrule_v6  \
--resource-group DsResourceGroup01 \
--frontend-ip-name dsLbFrontEnd_v6  \
--protocol Tcp  \
--frontend-port 80 \
--backend-port 80  \
--probe-name dsProbe \
--backend-pool-name dsLbBackEndPool_v6

```

## <a name="create-network-resources"></a>Создание сетевых ресурсов
Перед развертыванием некоторых виртуальных технологий необходимо создать вспомогательные сетевые ресурсы - набор доступности, группу сетевой безопасности, виртуальную сеть и виртуальные NICs. 
### <a name="create-an-availability-set"></a>"Создать группу доступности"
Чтобы улучшить доступность приложения, поместите в набор доступных изображений ввей-водоизм.

Создайте группу доступности с помощью команды [az vm availability-set create](https://docs.microsoft.com/cli/azure/vm/availability-set?view=azure-cli-latest). Следующий пример создает набор доступности под названием *dsAVset:*

```azurecli
az vm availability-set create \
--name dsAVset  \
--resource-group DsResourceGroup01  \
--location eastus \
--platform-fault-domain-count 2  \
--platform-update-domain-count 2  
```

### <a name="create-network-security-group"></a>Создание группы безопасности сети

Создайте группу сетевой безопасности для правил, которые будут регулировать входящие и исходящие коммуникации в vNet.

#### <a name="create-a-network-security-group"></a>Создание группы безопасности сети

Создание группы сетевой безопасности с [созданием az network nsg](https://docs.microsoft.com/cli/azure/network/nsg?view=azure-cli-latest#az-network-nsg-create)


```azurecli
az network nsg create \
--name dsNSG1  \
--resource-group DsResourceGroup01  \
--location eastus

```

#### <a name="create-a-network-security-group-rule-for-inbound-and-outbound-connections"></a>Создание правила группы сетевой безопасности для входящих и исходящих соединений

Создайте правило группы сетевой безопасности, позволяющее соединениям RDP через порт 3389, подключение к Интернету через порт 80 и исходящие соединения с [правилом nsg сети az.](https://docs.microsoft.com/cli/azure/network/nsg/rule?view=azure-cli-latest#az-network-nsg-rule-create)

```azurecli
# Create inbound rule for port 3389
az network nsg rule create \
--name allowRdpIn  \
--nsg-name dsNSG1  \
--resource-group DsResourceGroup01  \
--priority 100  \
--description "Allow Remote Desktop In"  \
--access Allow  \
--protocol "*"  \
--direction Inbound  \
--source-address-prefixes "*"  \
--source-port-ranges "*"  \
--destination-address-prefixes "*"  \
--destination-port-ranges 3389

# Create inbound rule for port 80
az network nsg rule create \
--name allowHTTPIn  \
--nsg-name dsNSG1  \
--resource-group DsResourceGroup01  \
--priority 200  \
--description "Allow HTTP In"  \
--access Allow  \
--protocol "*"  \
--direction Inbound  \
--source-address-prefixes "*"  \
--source-port-ranges 80  \
--destination-address-prefixes "*"  \
--destination-port-ranges 80

# Create outbound rule

az network nsg rule create \
--name allowAllOut  \
--nsg-name dsNSG1  \
--resource-group DsResourceGroup01  \
--priority 300  \
--description "Allow All Out"  \
--access Allow  \
--protocol "*"  \
--direction Outbound  \
--source-address-prefixes "*"  \
--source-port-ranges "*"  \
--destination-address-prefixes "*"  \
--destination-port-ranges "*"
```


### <a name="create-a-virtual-network"></a>Создание виртуальной сети

Создайте виртуальную сеть с [az сети Vnet создать](https://docs.microsoft.com/cli/azure/network/vnet?view=azure-cli-latest#az-network-vnet-create). Следующий пример создает виртуальную сеть под названием *dsVNET* с подсетями *dsSubNET_v4* и *dsSubNET_v6:*

```azurecli
# Create the virtual network
az network vnet create \
--name dsVNET \
--resource-group DsResourceGroup01 \
--location eastus  \
--address-prefixes "10.0.0.0/16" "ace:cab:deca::/48"

# Create a single dual stack subnet

az network vnet subnet create \
--name dsSubNET \
--resource-group DsResourceGroup01 \
--vnet-name dsVNET \
--address-prefixes "10.0.0.0/24" "ace:cab:deca:deed::/64" \
--network-security-group dsNSG1
```

### <a name="create-nics"></a>Создание сетевых адаптеров

Создание виртуальных NICs для каждого VM с [az сети nic создать](https://docs.microsoft.com/cli/azure/network/nic?view=azure-cli-latest#az-network-nic-create). Следующий пример создает виртуальный NIC для каждого VM. Каждый NIC имеет две конфигурации IP (1 конфигурация IPv4, 1 конфигурация IPv6). Вы создаете конфигурацию IPV6 с [az сетью nic ip-config create.](https://docs.microsoft.com/cli/azure/network/nic/ip-config?view=azure-cli-latest#az-network-nic-ip-config-create)
 
```azurecli
# Create NICs
az network nic create \
--name dsNIC0  \
--resource-group DsResourceGroup01 \
--network-security-group dsNSG1  \
--vnet-name dsVNET  \
--subnet dsSubNet  \
--private-ip-address-version IPv4 \
--lb-address-pools dsLbBackEndPool_v4  \
--lb-name dsLB  \
--public-ip-address dsVM0_remote_access

az network nic create \
--name dsNIC1 \
--resource-group DsResourceGroup01 \
--network-security-group dsNSG1 \
--vnet-name dsVNET \
--subnet dsSubNet \
--private-ip-address-version IPv4 \
--lb-address-pools dsLbBackEndPool_v4 \
--lb-name dsLB \
--public-ip-address dsVM1_remote_access

# Create IPV6 configurations for each NIC

az network nic ip-config create \
--name dsIp6Config_NIC0  \
--nic-name dsNIC0  \
--resource-group DsResourceGroup01 \
--vnet-name dsVNET \
--subnet dsSubNet \
--private-ip-address-version IPv6 \
--lb-address-pools dsLbBackEndPool_v6 \
--lb-name dsLB

az network nic ip-config create \
--name dsIp6Config_NIC1 \
--nic-name dsNIC1 \
--resource-group DsResourceGroup01 \
--vnet-name dsVNET \
--subnet dsSubNet \
--private-ip-address-version IPv6 \
--lb-address-pools dsLbBackEndPool_v6 \
--lb-name dsLB
```

### <a name="create-virtual-machines"></a>Создание виртуальных машин

Создайте виртуальные машины с помощью команды [az vm create](https://docs.microsoft.com/cli/azure/vm?view=azure-cli-latest#az-vm-create). В приведенном ниже примере создаются две виртуальные машины и обязательные компоненты виртуальной сети, если их еще нет: 

Создайте виртуальную машину *dsVM0* следующим образом:

```azurecli
 az vm create \
--name dsVM0 \
--resource-group DsResourceGroup01 \
--nics dsNIC0 \
--size Standard_A2 \
--availability-set dsAVset \
--image MicrosoftWindowsServer:WindowsServer:2019-Datacenter:latest  
```

Создайте виртуальную машину *dsVM1* следующим образом:

```azurecli
az vm create \
--name dsVM1 \
--resource-group DsResourceGroup01 \
--nics dsNIC1 \
--size Standard_A2 \
--availability-set dsAVset \
--image MicrosoftWindowsServer:WindowsServer:2019-Datacenter:latest 
```

## <a name="view-ipv6-dual-stack-virtual-network-in-azure-portal"></a>Просмотр виртуальной сети IPv6 с двойным стеком на портале Azure
Виртуальную сеть IPv6 с двойным стеком можно просмотреть следующим образом:
1. В панели поиска портала введите *dsVnet*.
2. Когда в результатах поиска появится пункт **myVirtualNetwork**, выберите его. Это запускает **Обзор** страницы двойного стека виртуальной сети под названием *dsVnet*. Виртуальная сеть двойного стека показывает два NICs с конфигурациями IPv4 и IPv6, расположенными в подсетевом сдвойном стеке под названием *dsSubnet.*

  ![Виртуальная сеть IPv6 с двойным стеком в Azure](./media/virtual-network-ipv4-ipv6-dual-stack-powershell/dual-stack-vnet.png)

> [!NOTE]
> Виртуальная сеть IPv6 для Azure доступна на портале Azure только для этого предварительного просмотра.


## <a name="clean-up-resources"></a>Очистка ресурсов

Когда это больше не нужно, можно использовать команду [удаления группы az](/cli/azure/group#az-group-delete) для удаления группы ресурсов, VM и всех связанных ресурсов.

```azurecli
 az group delete --name DsResourceGroup01
```

## <a name="next-steps"></a>Дальнейшие действия

В этой статье вы создали standard Load Balancer с двойной конфигурацией IP-адреса (IPv4 и IPv6). Вы также создали два виртуальных машины, которые включали NICs с двойными конфигурациями IP (IPV4 и IPv6), которые были добавлены в пул задней части баланса нагрузки. Подробнее о поддержке IPv6 в виртуальных сетях Azure читайте в пример [IPv6 для виртуальной сети Azure?](ipv6-overview.md)
