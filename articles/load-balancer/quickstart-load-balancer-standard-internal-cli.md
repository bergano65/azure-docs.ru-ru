---
title: Краткое руководство. Создание внутренней подсистемы балансировки нагрузки — Azure CLI
titleSuffix: Azure Load Balancer
description: В этом кратком руководстве показано, как создать внутреннюю подсистему балансировки нагрузки с помощью Azure CLI
services: load-balancer
documentationcenter: na
author: asudbring
manager: KumudD
Customer intent: I want to create a load balancer so that I can load balance internal traffic to VMs.
ms.service: load-balancer
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/19/2020
ms.author: allensu
ms.custom: mvc, devx-track-js, devx-track-azurecli
ms.openlocfilehash: edf893f1f6ba0691da5764420017282d7a8bde84
ms.sourcegitcommit: 61d2b2211f3cc18f1be203c1bc12068fc678b584
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/18/2021
ms.locfileid: "98562817"
---
# <a name="quickstart-create-an-internal-load-balancer-to-load-balance-vms-using-azure-cli"></a>Краткое руководство. Создание внутренней подсистемы балансировки нагрузки с помощью Azure CLI для распределения нагрузки между виртуальными машинами

Начните работу с Azure Load Balancer, создав с помощью Azure CLI внутреннюю подсистему балансировки нагрузки и три виртуальные машины.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)] 

- Для работы с этим кратким руководством требуется Azure CLI версии 2.0.28 или более поздней. Если вы используете Azure Cloud Shell, последняя версия уже установлена.

## <a name="create-a-resource-group"></a>Создание группы ресурсов

Группа ресурсов Azure является логическим контейнером, в котором происходит развертывание ресурсов Azure и управление ими.

Создайте группу ресурсов с помощью команды [az group create](/cli/azure/group#az_group_create):

* с именем **CreateIntLBQS-rg**; 
* в расположении **eastus**.

```azurecli-interactive
  az group create \
    --name CreateIntLBQS-rg \
    --location eastus

```
---

# <a name="standard-sku"></a>[**SKU "Стандартный"**](#tab/option-1-create-load-balancer-standard)

>[!NOTE]
>Для производственных рабочих нагрузок рекомендуется использовать подсистему балансировки нагрузки ценовой категории "Стандартный". Дополнительные сведения о доступных ценовых категориях см. в статье **[Номера SKU для Azure Load Balancer](skus.md)** .

В этом разделе показано, как создать подсистему балансировки нагрузки, которая распределяет нагрузку между виртуальными машинами. 

При создании внутренней подсистемы балансировки нагрузки виртуальная сеть настраивается в качестве сети для подсистемы балансировки нагрузки. 

На следующей схеме показаны ресурсы, созданные при работе с этим кратким руководством:

:::image type="content" source="./media/quickstart-load-balancer-standard-internal-portal/resources-diagram-internal.png" alt-text="Ресурсы подсистемы балансировки нагрузки ценовой категории &quot;Стандартный&quot;, созданные для работы с этим кратким руководством." border="false":::

## <a name="configure-virtual-network---standard"></a>Настройка виртуальной сети, категория "Стандартный"

Прежде чем развертывать виртуальные машины и подсистему балансировки нагрузки, создайте вспомогательные ресурсы виртуальной сети.

### <a name="create-a-virtual-network"></a>Создание виртуальной сети

Создайте виртуальную сеть с помощью команды [az network vnet create](/cli/azure/network/vnet#az-network-vnet-create):

* с именем **myVNet**;
* с префиксом подсети **10.1.0.0/16**;
* с именем подсети **myBackendSubnet**;
* с префиксом подсети **10.1.0.0/24**;
* в группе ресурсов **CreateIntLBQS-rg**;
* расположении **eastus**.

```azurecli-interactive
  az network vnet create \
    --resource-group CreateIntLBQS-rg \
    --location eastus \
    --name myVNet \
    --address-prefixes 10.1.0.0/16 \
    --subnet-name myBackendSubnet \
    --subnet-prefixes 10.1.0.0/24
```

### <a name="create-a-public-ip-address"></a>Создание общедоступного IP-адреса

Чтобы создать общедоступный IP-адрес узла-бастиона, воспользуйтесь командой [az network public-ip create](/cli/azure/network/public-ip#az-network-public-ip-create):

* создайте стандартный избыточный между зонами общедоступный IP-адрес с именем **myPublicIP**;
* в группе ресурсов **CreateIntLBQS-rg**.

```azurecli-interactive
az network public-ip create \
    --resource-group CreateIntLBQS-rg  \
    --name myBastionIP \
    --sku Standard
```
### <a name="create-a-bastion-subnet"></a>Создание подсети бастиона

Используйте команду [az network vnet subnet create](/cli/azure/network/vnet/subnet#az-network-vnet-subnet-create), чтобы создать подсеть бастиона:

* с именем **AzureBastionSubnet**;
* с префиксом подсети **10.1.1.0/24**.
* в виртуальной сети **myVNet**;
* в группе ресурсов **CreateIntLBQS-rg**;

```azurecli-interactive
az network vnet subnet create \
    --resource-group CreateIntLBQS-rg  \
    --name AzureBastionSubnet \
    --vnet-name myVNet \
    --address-prefixes 10.1.1.0/24
```

### <a name="create-bastion-host"></a>Создание узла-бастиона

Используйте команду [az network bastion create](/cli/azure/network/bastion#az-network-bastion-create) для создания узла-бастиона:

* с именем **myBastionHost**;
* в группе ресурсов **CreateIntLBQS-rg**.
* связанного с общедоступным IP-адресом **myBastionIP**;
* связанного с виртуальной сетью **myVNet**;
* в расположении **eastus**.

```azurecli-interactive
az network bastion create \
    --resource-group CreateIntLBQS-rg  \
    --name myBastionHost \
    --public-ip-address myBastionIP \
    --vnet-name myVNet \
    --location eastus
```

Развертывание узла-бастиона может занять несколько минут.


### <a name="create-a-network-security-group"></a>Создание группы безопасности сети

Для подсистемы балансировки нагрузки уровня "Стандартный" у серверных виртуальных машин должны быть сетевые интерфейсы, связанные с группой безопасности сети. 

Создайте группу безопасности сети с помощью команды [az network nsg create](/cli/azure/network/nsg#az-network-nsg-create):

* с именем **myNSG**;
* в группе ресурсов **CreateIntLBQS-rg**.

```azurecli-interactive
  az network nsg create \
    --resource-group CreateIntLBQS-rg \
    --name myNSG
```

### <a name="create-a-network-security-group-rule"></a>Создание правила группы безопасности сети

Создайте правило группы безопасности сети с помощью команды [az network nsg rule create](/cli/azure/network/nsg/rule#az-network-nsg-rule-create), используя такие сведения:

* имя **myNSGRuleHTTP**;
* группа безопасности сети, созданная на предыдущем шаге, **myNSG**;
* в группе ресурсов **CreateIntLBQS-rg**;
* с протоколом **(*)** ;
* направление **Входящий**;
* источник **(*)** ;
* назначение **(*)** ;
* порт назначения **порт 80**;
* доступ **Разрешить**;
* приоритет **200**.

```azurecli-interactive
  az network nsg rule create \
    --resource-group CreateIntLBQS-rg \
    --nsg-name myNSG \
    --name myNSGRuleHTTP \
    --protocol '*' \
    --direction inbound \
    --source-address-prefix '*' \
    --source-port-range '*' \
    --destination-address-prefix '*' \
    --destination-port-range 80 \
    --access allow \
    --priority 200
```

## <a name="create-backend-servers---standard"></a>Создание внутренних серверов, категория "Стандартный"

В этом разделе показано, как создать:

* три сетевых интерфейса для виртуальных машин;
* три виртуальные машины, которые будут использоваться в качестве внутренних серверов для подсистемы балансировки нагрузки.

### <a name="create-network-interfaces-for-the-virtual-machines"></a>Создание сетевых интерфейсов для виртуальных машин

Создайте три сетевых интерфейса с помощью команды [az network nic create](/cli/azure/network/nic#az-network-nic-create), используя следующие сведения:

* имена **myNicVM1**, **myNicVM2** и **myNicVM3**;
* в группе ресурсов **CreateIntLBQS-rg**;
* виртуальная сеть **myVNet**;
* подсеть **myBackendSubnet**;
* группа безопасности сети **myNSG**;

```azurecli-interactive
  array=(myNicVM1 myNicVM2 myNicVM3)
  for vmnic in "${array[@]}"
  do
    az network nic create \
        --resource-group CreateIntLBQS-rg \
        --name $vmnic \
        --vnet-name myVNet \
        --subnet myBackEndSubnet \
        --network-security-group myNSG
  done
```

### <a name="create-virtual-machines"></a>Создание виртуальных машин

Создайте виртуальные машины с помощью команды [az vm create](/cli/azure/vm#az-vm-create), используя следующие сведения:

* имена **myVM1**, **myVM2** и **myVM3**;
* в группе ресурсов **CreateIntLBQS-rg**;
* с подключением к сетевым интерфейсам **myNicVM1**, **myNicVM2** и **myNicVM3**;
* образ виртуальной машины **win2019datacenter**;
* в **зоне 1**, **зоне 2** и **зоне 3**.

```azurecli-interactive
  array=(1 2 3)
  for n in "${array[@]}"
  do
    az vm create \
    --resource-group CreateIntLBQS-rg \
    --name myVM$n \
    --nics myNicVM$n \
    --image win2019datacenter \
    --admin-username azureuser \
    --zone $n \
    --no-wait
  done
```

На развертывание виртуальных машин может потребоваться несколько минут.

## <a name="create-standard-load-balancer"></a>Создание подсистемы балансировки нагрузки (цен. категория "Стандартный")

В этом разделе описано, как создать и настроить следующие компоненты подсистемы балансировки нагрузки:

  * интерфейсный пул IP-адресов, который получает входящий трафик в подсистеме балансировки нагрузки;
  * внутренний пул IP-адресов, на который интерфейсный пул отправляет трафик с балансировкой нагрузки;
  * проверка работоспособности, определяющая работоспособность внутренних экземпляров виртуальной машины;
  * правило подсистемы балансировки нагрузки, определяющее порядок распределения трафика между виртуальными машинами.

### <a name="create-the-load-balancer-resource"></a>Создание ресурса подсистемы балансировки нагрузки

С помощью команды [az network lb create](/cli/azure/network/lb#az-network-lb-create) создайте общедоступную подсистему балансировки нагрузки:

* с именем **myLoadBalancer**,
* пулом переднего плана **myFrontEnd**
* и серверным пулом **myBackEndPool**,
* связанную с виртуальной сетью **myVNet**
* и внутренней подсетью **myBackendSubnet**.

```azurecli-interactive
  az network lb create \
    --resource-group CreateIntLBQS-rg \
    --name myLoadBalancer \
    --sku Standard \
    --vnet-name myVnet \
    --subnet myBackendSubnet \
    --frontend-ip-name myFrontEnd \
    --backend-pool-name myBackEndPool
```

### <a name="create-the-health-probe"></a>Создание зонда работоспособности

При пробе работоспособности выполняется проверка всех экземпляров виртуальной машины, чтобы убедиться, что они могут отправлять сетевой трафик. 

Виртуальная машина с неудачной пробой удаляется из подсистемы балансировки нагрузки и снова добавляется в нее после устранения сбоя.

Создайте пробу работоспособности с помощью команды [az network lb probe create](/cli/azure/network/lb/probe#az-network-lb-probe-create), которая:

* отслеживает работоспособность виртуальных машин;
* имеет имя **myHealthProbe**;
* использует протокол **TCP**;
* отслеживает **порт 80**.

```azurecli-interactive
  az network lb probe create \
    --resource-group CreateIntLBQS-rg \
    --lb-name myLoadBalancer \
    --name myHealthProbe \
    --protocol tcp \
    --port 80
```

### <a name="create-the-load-balancer-rule"></a>Создание правила подсистемы балансировки нагрузки

Правило подсистемы балансировки нагрузки определяет:

* конфигурацию интерфейсных IP-адресов для входящего трафика;
* серверный пул IP-адресов для приема трафика;
* требуемые порты источника и назначения. 

С помощью команды [az network lb rule create](/cli/azure/network/lb/rule#az-network-lb-rule-create) создайте правило подсистемы балансировки нагрузки, которое:

* имеет имя **myHTTPRule**;
* ожидает передачи данных от **порта 80**, используемого интерфейсным пулом **myFrontEnd**;
* перенаправляет трафик, для которого настроена балансировка нагрузки, ко внутреннему пулу адресов **myBackEndPool**, который использует **порт 80**; 
* использует пробу работоспособности **myHealthProbe**;
* использует протокол **TCP**;
* с временем ожидания перед переходом в режим простоя **15 минут**;
* и включенной функцией сброса подключений TCP.

```azurecli-interactive
  az network lb rule create \
    --resource-group CreateIntLBQS-rg \
    --lb-name myLoadBalancer \
    --name myHTTPRule \
    --protocol tcp \
    --frontend-port 80 \
    --backend-port 80 \
    --frontend-ip-name myFrontEnd \
    --backend-pool-name myBackEndPool \
    --probe-name myHealthProbe \
    --idle-timeout 15 \
    --enable-tcp-reset true
```

### <a name="add-virtual-machines-to-load-balancer-backend-pool"></a>Добавление виртуальных машин во внутренний пул подсистемы балансировки нагрузки

Добавьте виртуальные машины во внутренний пул, используя команду [az network nic ip-config address-pool add](/cli/azure/network/nic/ip-config/address-pool#az-network-nic-ip-config-address-pool-add):

* в серверном пуле адресов **myBackEndPool**;
* в группе ресурсов **CreateIntLBQS-rg**;
* с привязкой к сетевым интерфейсам **myNicVM1**, **myNicVM2** и **myNicVM3**;
* связанный с подсистемой балансировки нагрузки **myLoadBalancer**.

```azurecli-interactive
  array=(VM1 VM2 VM3)
  for vm in "${array[@]}"
  do
  az network nic ip-config address-pool add \
   --address-pool myBackendPool \
   --ip-config-name ipconfig1 \
   --nic-name myNic$vm \
   --resource-group CreateIntLBQS-rg \
   --lb-name myLoadBalancer
  done

```

# <a name="basic-sku"></a>[**SKU "Базовый"**](#tab/option-1-create-load-balancer-basic)

>[!NOTE]
>Для производственных рабочих нагрузок рекомендуется использовать подсистему балансировки нагрузки ценовой категории "Стандартный". Дополнительные сведения о доступных ценовых категориях см. в статье **[Номера SKU для Azure Load Balancer](skus.md)** .

В этом разделе показано, как создать подсистему балансировки нагрузки, которая распределяет нагрузку между виртуальными машинами. 

При создании внутренней подсистемы балансировки нагрузки виртуальная сеть настраивается в качестве сети для подсистемы балансировки нагрузки. 

На следующей схеме показаны ресурсы, созданные при работе с этим кратким руководством:

:::image type="content" source="./media/quickstart-load-balancer-standard-internal-portal/resources-diagram-internal-basic.png" alt-text="Ресурсы подсистемы балансировки нагрузки ценовой категории &quot;Базовый&quot;, созданные при работе с этим кратким руководством." border="false":::

## <a name="configure-virtual-network---basic"></a>Настройка виртуальной сети, категория "Базовый"

Прежде чем развертывать виртуальные машины и подсистему балансировки нагрузки, создайте вспомогательные ресурсы виртуальной сети.

### <a name="create-a-virtual-network"></a>Создание виртуальной сети

Создайте виртуальную сеть с помощью команды [az network vnet create](/cli/azure/network/vnet#az-network-vnet-createt):

* с именем **myVNet**;
* с префиксом подсети **10.1.0.0/16**;
* с именем подсети **myBackendSubnet**;
* с префиксом подсети **10.1.0.0/24**;
* в группе ресурсов **CreateIntLBQS-rg**;
* расположении **eastus**.

```azurecli-interactive
  az network vnet create \
    --resource-group CreateIntLBQS-rg \
    --location eastus \
    --name myVNet \
    --address-prefixes 10.1.0.0/16 \
    --subnet-name myBackendSubnet \
    --subnet-prefixes 10.1.0.0/24
```

### <a name="create-a-public-ip-address"></a>Создание общедоступного IP-адреса

Чтобы создать общедоступный IP-адрес узла-бастиона, воспользуйтесь командой [az network public-ip create](/cli/azure/network/public-ip#az-network-public-ip-create):

* создайте стандартный избыточный между зонами общедоступный IP-адрес с именем **myPublicIP**;
* в группе ресурсов **CreateIntLBQS-rg**.

```azurecli-interactive
az network public-ip create \
    --resource-group CreateIntLBQS-rg \
    --name myBastionIP \
    --sku Standard
```
### <a name="create-a-bastion-subnet"></a>Создание подсети бастиона

Используйте команду [az network vnet subnet create](/cli/azure/network/vnet/subnet#az-network-vnet-subnet-create), чтобы создать подсеть бастиона:

* с именем **AzureBastionSubnet**;
* с префиксом подсети **10.1.1.0/24**.
* в виртуальной сети **myVNet**;
* в группе ресурсов **CreateIntLBQS-rg**;

```azurecli-interactive
az network vnet subnet create \
    --resource-group CreateIntLBQS-rg \
    --name AzureBastionSubnet \
    --vnet-name myVNet \
    --address-prefixes 10.1.1.0/24
```

### <a name="create-bastion-host"></a>Создание узла-бастиона

Используйте команду [az network bastion create](/cli/azure/network/bastion#az-network-bastion-create) для создания узла-бастиона:

* с именем **myBastionHost**;
* в группе ресурсов **CreateIntLBQS-rg**.
* связанного с общедоступным IP-адресом **myBastionIP**;
* связанного с виртуальной сетью **myVNet**;
* в расположении **eastus**.

```azurecli-interactive
az network bastion create \
    --resource-group CreateIntLBQS-rg \
    --name myBastionHost \
    --public-ip-address myBastionIP \
    --vnet-name myVNet \
    --location eastus
```

Развертывание узла-бастиона может занять несколько минут.

### <a name="create-a-network-security-group"></a>Создание группы безопасности сети

Для подсистемы балансировки нагрузки уровня "Стандартный" у серверных виртуальных машин должны быть сетевые интерфейсы, связанные с группой безопасности сети. 

Создайте группу безопасности сети с помощью команды [az network nsg create](/cli/azure/network/nsg#az-network-nsg-create):

* с именем **myNSG**;
* в группе ресурсов **CreateIntLBQS-rg**.

```azurecli-interactive
  az network nsg create \
    --resource-group CreateIntLBQS-rg \
    --name myNSG
```

### <a name="create-a-network-security-group-rule"></a>Создание правила группы безопасности сети

Создайте правило группы безопасности сети с помощью команды [az network nsg rule create](/cli/azure/network/nsg/rule#az-network-nsg-rule-create), используя такие сведения:

* имя **myNSGRuleHTTP**;
* группа безопасности сети, созданная на предыдущем шаге, **myNSG**;
* в группе ресурсов **CreateIntLBQS-rg**;
* с протоколом **(*)** ;
* направление **Входящий**;
* источник **(*)** ;
* назначение **(*)** ;
* порт назначения **порт 80**;
* доступ **Разрешить**;
* приоритет **200**.

```azurecli-interactive
  az network nsg rule create \
    --resource-group CreateIntLBQS-rg \
    --nsg-name myNSG \
    --name myNSGRuleHTTP \
    --protocol '*' \
    --direction inbound \
    --source-address-prefix '*' \
    --source-port-range '*' \
    --destination-address-prefix '*' \
    --destination-port-range 80 \
    --access allow \
    --priority 200
```

## <a name="create-backend-servers---basic"></a>Создание внутренних серверов, категория "Базовый"

В этом разделе показано, как создать:

* три сетевых интерфейса для виртуальных машин;
* группу доступности для виртуальных машин;
* три виртуальные машины, которые будут использоваться в качестве внутренних серверов для подсистемы балансировки нагрузки.

### <a name="create-network-interfaces-for-the-virtual-machines"></a>Создание сетевых интерфейсов для виртуальных машин

Создайте три сетевых интерфейса с помощью команды [az network nic create](/cli/azure/network/nic#az-network-nic-create), используя следующие сведения:

* имена **myNicVM1**, **myNicVM2** и **myNicVM3**;
* в группе ресурсов **CreateIntLBQS-rg**;
* виртуальная сеть **myVNet**;
* подсеть **myBackendSubnet**;
* группа безопасности сети **myNSG**;

```azurecli-interactive
  array=(myNicVM1 myNicVM2 myNicVM3)
  for vmnic in "${array[@]}"
  do
    az network nic create \
        --resource-group CreateIntLBQS-rg \
        --name $vmnic \
        --vnet-name myVNet \
        --subnet myBackEndSubnet \
        --network-security-group myNSG
  done
```

### <a name="create-availability-set-for-virtual-machines"></a>Создание группы доступности для виртуальных машин

Создайте группу доступности, используя команду [az vm availability-set create](/cli/azure/vm/availability-set#az-vm-availability-set-create) и следующие сведения:

* имя **myAvailabilitySet**;
* в группе ресурсов **CreateIntLBQS-rg**;
* расположение **eastus**.

```azurecli-interactive
  az vm availability-set create \
    --name myAvailabilitySet \
    --resource-group CreateIntLBQS-rg \
    --location eastus 
    
```

### <a name="create-virtual-machines"></a>Создание виртуальных машин

Создайте виртуальные машины с помощью команды [az vm create](/cli/azure/vm#az-vm-create), используя следующие сведения:

* имена **myVM1**, **myVM2** и **myVM3**;
* в группе ресурсов **CreateIntLBQS-rg**;
* с подключением к сетевым интерфейсам **myNicVM1**, **myNicVM2** и **myNicVM3**;
* образ виртуальной машины **win2019datacenter**;
* в **myAvailabilitySet**.


```azurecli-interactive
  array=(1 2 3)
  for n in "${array[@]}"
  do
    az vm create \
    --resource-group CreateIntLBQS-rg \
    --name myVM$n \
    --nics myNicVM$n \
    --image win2019datacenter \
    --admin-username azureuser \
    --availability-set myAvailabilitySet \
    --no-wait
  done
```
На развертывание виртуальных машин может потребоваться несколько минут.

## <a name="create-basic-load-balancer"></a>Создание подсистемы балансировки нагрузки уровня "Базовый"

В этом разделе описано, как создать и настроить следующие компоненты подсистемы балансировки нагрузки:

  * интерфейсный пул IP-адресов, который получает входящий трафик в подсистеме балансировки нагрузки;
  * внутренний пул IP-адресов, на который интерфейсный пул отправляет трафик с балансировкой нагрузки;
  * проверка работоспособности, определяющая работоспособность внутренних экземпляров виртуальной машины;
  * правило подсистемы балансировки нагрузки, определяющее порядок распределения трафика между виртуальными машинами.

### <a name="create-the-load-balancer-resource"></a>Создание ресурса подсистемы балансировки нагрузки

С помощью команды [az network lb create](/cli/azure/network/lb#az-network-lb-create) создайте общедоступную подсистему балансировки нагрузки:

* с именем **myLoadBalancer**,
* пулом переднего плана **myFrontEnd**
* и серверным пулом **myBackEndPool**,
* связанную с виртуальной сетью **myVNet**
* и внутренней подсетью **myBackendSubnet**.

```azurecli-interactive
  az network lb create \
    --resource-group CreateIntLBQS-rg \
    --name myLoadBalancer \
    --sku Basic \
    --vnet-name myVNet \
    --subnet myBackendSubnet \
    --frontend-ip-name myFrontEnd \
    --backend-pool-name myBackEndPool
```

### <a name="create-the-health-probe"></a>Создание зонда работоспособности

При пробе работоспособности выполняется проверка всех экземпляров виртуальной машины, чтобы убедиться, что они могут отправлять сетевой трафик. 

Виртуальная машина с неудачной пробой удаляется из подсистемы балансировки нагрузки и снова добавляется в нее после устранения сбоя.

Создайте пробу работоспособности с помощью команды [az network lb probe create](/cli/azure/network/lb/probe#az-network-lb-probe-create), которая:

* отслеживает работоспособность виртуальных машин;
* имеет имя **myHealthProbe**;
* использует протокол **TCP**;
* отслеживает **порт 80**.

```azurecli-interactive
  az network lb probe create \
    --resource-group CreateIntLBQS-rg \
    --lb-name myLoadBalancer \
    --name myHealthProbe \
    --protocol tcp \
    --port 80
```

### <a name="create-the-load-balancer-rule"></a>Создание правила подсистемы балансировки нагрузки

Правило подсистемы балансировки нагрузки определяет:

* конфигурацию интерфейсных IP-адресов для входящего трафика;
* серверный пул IP-адресов для приема трафика;
* требуемые порты источника и назначения. 

С помощью команды [az network lb rule create](/cli/azure/network/lb/rule#az-network-lb-rule-create) создайте правило подсистемы балансировки нагрузки, которое:

* имеет имя **myHTTPRule**;
* ожидает передачи данных от **порта 80**, используемого интерфейсным пулом **myFrontEnd**;
* перенаправляет трафик, для которого настроена балансировка нагрузки, ко внутреннему пулу адресов **myBackEndPool**, который использует **порт 80**; 
* использует пробу работоспособности **myHealthProbe**;
* использует протокол **TCP**;
* с временем ожидания перед переходом в режим простоя **15 минут**;

```azurecli-interactive
  az network lb rule create \
    --resource-group CreateIntLBQS-rg \
    --lb-name myLoadBalancer \
    --name myHTTPRule \
    --protocol tcp \
    --frontend-port 80 \
    --backend-port 80 \
    --frontend-ip-name myFrontEnd \
    --backend-pool-name myBackEndPool \
    --probe-name myHealthProbe \
    --idle-timeout 15 
```
### <a name="add-virtual-machines-to-load-balancer-backend-pool"></a>Добавление виртуальных машин во внутренний пул подсистемы балансировки нагрузки

Добавьте виртуальные машины во внутренний пул, используя команду [az network nic ip-config address-pool add](/cli/azure/network/nic/ip-config/address-pool#az-network-nic-ip-config-address-pool-add):

* в серверном пуле адресов **myBackEndPool**;
* в группе ресурсов **CreateIntLBQS-rg**;
* с привязкой к сетевым интерфейсам **myNicVM1**, **myNicVM2** и **myNicVM3**;
* связанный с подсистемой балансировки нагрузки **myLoadBalancer**.

```azurecli-interactive
  array=(VM1 VM2 VM3)
  for vm in "${array[@]}"
  do
  az network nic ip-config address-pool add \
   --address-pool myBackendPool \
   --ip-config-name ipconfig1 \
   --nic-name myNic$vm \
   --resource-group CreateIntLBQS-rg \
   --lb-name myLoadBalancer
  done

```
---

## <a name="test-the-load-balancer"></a>Тестирование подсистемы балансировки нагрузки

### <a name="create-test-virtual-machine"></a>Создание тестовой виртуальной машины

Создайте сетевой интерфейс с помощью команды [az network nic create](/cli/azure/network/nic#az-network-nic-create):

* с именем **myNicTestVM**;
* в группе ресурсов **CreateIntLBQS-rg**;
* виртуальная сеть **myVNet**;
* подсеть **myBackendSubnet**;
* группа безопасности сети **myNSG**;

```azurecli-interactive
  az network nic create \
    --resource-group CreateIntLBQS-rg \
    --name myNicTestVM \
    --vnet-name myVNet \
    --subnet myBackEndSubnet \
    --network-security-group myNSG
```
С помощью команды [az vm create](/cli/azure/vm#az-vm-create) создайте виртуальную машину:

* с именем **myTestVM**;
* в группе ресурсов **CreateIntLBQS-rg**;
* с подключением к сетевому интерфейсу **myNicTestVM**;
* с образом виртуальной машины **Win2019Datacenter**;

```azurecli-interactive
  az vm create \
    --resource-group CreateIntLBQS-rg \
    --name myTestVM \
    --nics myNicTestVM \
    --image Win2019Datacenter \
    --admin-username azureuser \
    --no-wait
```
Развертывание виртуальной машины может занять несколько минут.

## <a name="install-iis"></a>Установка служб IIS

С помощью [az vm extension set](/cli/azure/vm/extension#az_vm_extension_set) можно установить службы IIS на виртуальных машинах и указать для веб-сайта по умолчанию имя компьютера.

```azurecli-interactive
  array=(myVM1 myVM2 myVM3)
    for vm in "${array[@]}"
    do
     az vm extension set \
       --publisher Microsoft.Compute \
       --version 1.8 \
       --name CustomScriptExtension \
       --vm-name $vm \
       --resource-group CreateIntLBQS-rg \
       --settings '{"commandToExecute":"powershell Add-WindowsFeature Web-Server; powershell Add-Content -Path \"C:\\inetpub\\wwwroot\\Default.htm\" -Value $($env:computername)"}'
  done

```

### <a name="test"></a>Тест

1. [Войдите](https://portal.azure.com) на портал Azure.

2. Найдите частный IP-адрес для подсистемы балансировки нагрузки на экране **обзора**. В меню слева щелкните **Все службы**, выберите **Все ресурсы**, а затем — **myLoadBalancer**.

3. Запишите или скопируйте адрес рядом с **частным IP-адресом** на экране **обзора** **myLoadBalancer**.

4. В меню слева щелкните **Все службы**, выберите **Все ресурсы**, а затем в списке ресурсов выберите виртуальную машину **myTestVM**, расположенную в группе ресурсов **CreateIntLBQS-rg**.

5. На странице **Обзор** выберите **Подключиться** и **Бастион**.

6. Введите имя пользователя и пароль, введенные в процессе создания виртуальной машины.

7. На виртуальной машине **myTestVM** откройте браузер **Internet Explorer**.

8. Введите IP-адрес с предыдущего шага в адресную строку браузера. В браузере отобразится страница по умолчанию веб-сервера IIS.

    :::image type="content" source="./media/quickstart-load-balancer-standard-internal-portal/load-balancer-test.png" alt-text="Создание внутренней подсистемы балансировки нагрузки уровня Стандартный" border="true":::
   
Чтобы подсистема балансировки нагрузки распределяла трафик между всеми тремя виртуальными машинами, вы можете настроить стандартную страницу веб-сервера IIS каждой из них, а затем принудительно обновить браузер с клиентского компьютера.

## <a name="clean-up-resources"></a>Очистка ресурсов

Вы можете удалить ненужную группу ресурсов, подсистему балансировки нагрузки и все связанные с ней ресурсы, выполнив команду [az group delete](/cli/azure/group#az-group-delete).

```azurecli-interactive
  az group delete \
    --name CreateIntLBQS-rg
```

## <a name="next-steps"></a>Дальнейшие действия

В этом кратком руководстве:

* создать стандартную или общедоступную подсистему балансировки нагрузки;
* подключить к ней виртуальные машины; 
* настроить правило трафика подсистемы балансировки нагрузки и пробу работоспособности;
* тестировать подсистему балансировки нагрузки.

Чтобы узнать больше об Azure Load Balancer, ознакомьтесь со следующей статьей:
> [!div class="nextstepaction"]
> [Что такое Azure Load Balancer](load-balancer-overview.md)