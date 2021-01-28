---
title: Создание шлюза NAT — Azure CLI
titlesuffix: Azure Virtual Network NAT
description: В этом кратком руководстве показано, как создать шлюз NAT с помощью Azure CLI.
services: virtual-network
documentationcenter: na
author: asudbring
manager: KumudD
Customer intent: I want to create a NAT gateway for outbound connectivity for my virtual network.
ms.service: virtual-network
ms.subservice: nat
ms.devlang: na
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 02/18/2020
ms.author: allensu
ms.custom: devx-track-azurecli
ms.openlocfilehash: 8d14b8b83fd784956091e738a38d6851d5edacd9
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/28/2021
ms.locfileid: "98927132"
---
# <a name="create-a-nat-gateway-using-azure-cli"></a>Создание шлюза NAT c помощью Azure CLI

В этом руководстве показано, как использовать услугу преобразования сетевых адресов (NAT) в виртуальной сети Azure. Вы узнаете, как создать шлюз NAT для обеспечения исходящего подключения в виртуальных машинах в Azure. 

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- Для работы с этой статьей требуется Azure CLI версии 2.0.71 или более поздней. Если вы используете Azure Cloud Shell, последняя версия уже установлена.

## <a name="create-a-resource-group"></a>Создание группы ресурсов

Создайте группу ресурсов с помощью команды [az group create](/cli/azure/group). Группа ресурсов Azure является логическим контейнером, в котором происходит развертывание ресурсов Azure и управление ими.

В следующем примере создается группа ресурсов с именем **myResourceGroupNAT** в расположении **eastus2**:

```azurecli-interactive
  az group create \
    --name myResourceGroupNAT \
    --location eastus2
```

## <a name="create-the-nat-gateway"></a>Создание шлюза NAT

### <a name="create-a-public-ip-address"></a>Создание общедоступного IP-адреса

Для доступа к Интернету требуется один или несколько общедоступных IP-адресов для шлюза NAT. Создайте общедоступный IP-адрес с именем **myPublicIP** в группе **myResourceGroupNAT** с помощью команды [az network public-ip create](/cli/azure/network/public-ip).

```azurecli-interactive
  az network public-ip create \
    --resource-group myResourceGroupNAT \
    --name myPublicIP \
    --sku standard
```

### <a name="create-a-public-ip-prefix"></a>Создание префикса общедоступного IP-адреса

Со шлюзом NAT можно использовать один или несколько ресурсов общедоступных IP-адресов, префиксов общедоступных IP-адресов или оба варианта. Для демонстрации в этот сценарий будет добавлен ресурс общедоступного IP-префикса.   Выполните команду [az network public-ip prefix create](/cli/azure/network/public-ip/prefix#az-network-public-ip-prefix-create) для создания ресурса общедоступного IP-префикса с именем **myPublicIPprefixs** в группе **myResourceGroupNAT**.

```azurecli-interactive
  az network public-ip prefix create \
    --resource-group myResourceGroupNAT \
    --name myPublicIPprefix \
    --length 31
```

### <a name="create-a-nat-gateway-resource"></a>Создание ресурса шлюза NAT

В этом разделе подробно описано, как создать и настроить следующие компоненты NAT с использованием ресурса Шлюза NAT:
  - пул общедоступных IP-адресов и префикс общедоступного IP-адреса, которые будут использоваться для исходящих потоков, преобразованных ресурсом шлюза NAT;
  - изменение значения времени ожидания простоя с 4 минут по умолчанию на 10 минут.

Создайте глобальный шлюз Azure NAT с помощью команды [az network nat gateway create](/cli/azure/network/nat) с именем **myNATgateway**. В команде используется общедоступный IP-адрес **myPublicIP** и префикс общедоступного IP-адреса **myPublicIPprefix**. В команде также задается значение **10** минут для времени ожидания простоя.

```azurecli-interactive
  az network nat gateway create \
    --resource-group myResourceGroupNAT \
    --name myNATgateway \
    --public-ip-addresses myPublicIP \
    --public-ip-prefixes myPublicIPprefix \
    --idle-timeout 10       
  ```

На этом этапе шлюз NAT функционирует, и все, что осталось сделать — это настроить, в каких подсетях виртуальной сети он должен использоваться.

## <a name="configure-virtual-network"></a>Настройка виртуальной сети

Прежде чем развертывать виртуальную машину и использовать шлюз NAT, необходимо создать виртуальную сеть.

С помощью команды [az network vnet create](/cli/azure/network/vnet) в группе ресурсов **myResourceGroup** создайте виртуальную сеть с именем **myVnet**, содержащую подсеть **mySubnet**.  Диапазон IP-адресов для виртуальной сети — **192.168.0.0/16**. Подсеть в виртуальной сети — **192.168.0.0/24**.

```azurecli-interactive
  az network vnet create \
    --resource-group myResourceGroupNAT \
    --location eastus2 \
    --name myVnet \
    --address-prefix 192.168.0.0/16 \
    --subnet-name mySubnet \
    --subnet-prefix 192.168.0.0/24
```

### <a name="configure-nat-service-for-source-subnet"></a>Настройка NAT для исходной подсети

Мы настроим исходную подсеть **mySubnet** в виртуальной сети **myVnet**, чтобы использовать конкретный ресурс шлюза NAT **myNATgateway** в команде [az network vnet subnet update](/cli/azure/network/vnet/subnet).  Эта команда активирует NAT в указанной подсети.

```azurecli-interactive
  az network vnet subnet update \
    --resource-group myResourceGroupNAT \
    --vnet-name myVnet \
    --name mySubnet \
    --nat-gateway myNATgateway
```

Шлюз NAT теперь используется для всего исходящего трафика для назначений в Интернете.  Нет необходимости настраивать UDR.

## <a name="create-a-vm-to-use-the-nat-service"></a>Создание виртуальной машины для использования NAT

Теперь мы создадим виртуальную машину для использования NAT.  У этой виртуальной машины будет общедоступный IP-адрес для использования на уровне экземпляра, чтобы вы могли получать доступ к ней.  В NAT учитывается направление потоков и заменяется назначение в Интернете по умолчанию в подсети. Общедоступный IP-адрес виртуальной машины не будет использоваться для исходящих соединений.

### <a name="create-public-ip-for-source-vm"></a>Создание общедоступного IP-адреса для исходной виртуальной машины

Мы создадим общедоступный IP-адрес для доступа к виртуальной машине.  Создайте общедоступный IP-адрес с именем **myPublicIPVM** в группе **myResourceGroupNAT** с помощью команды [az network public-ip create](/cli/azure/network/public-ip).

```azurecli-interactive
  az network public-ip create \
    --resource-group myResourceGroupNAT \
    --name myPublicIPVM \
    --sku standard
```

### <a name="create-an-nsg-for-vm"></a>Создание группы безопасности сети для виртуальной машины

Так как стандартные общедоступные IP-адреса безопасны по умолчанию, необходимо создать группу безопасности сети (NSG), чтобы разрешить входящий доступ по протоколу SSH. Чтобы создать ресурс [NSG](/cli/azure/network/nsg#az-network-nsg-create) с именем **myNSG** в **группе myResourceGroupNAT**, используйте команду az network nsg create.

```azurecli-interactive
  az network nsg create \
    --resource-group myResourceGroupNAT \
    --name myNSG 
```

### <a name="expose-ssh-endpoint-on-source-vm"></a>Предоставление конечной точки SSH на исходной виртуальной машине

Мы создадим правило в NSG для SSH-подключения к исходной виртуальной машине. Чтобы создать правило NSG с именем **ssh** в NSG с именем **myNSG** в группе **myResourceGroupNAT**, используйте команду [az network nsg rule create](/cli/azure/network/nsg/rule#az-network-nsg-rule-create).

```azurecli-interactive
  az network nsg rule create \
    --resource-group myResourceGroupNAT \
    --nsg-name myNSG \
    --priority 100 \
    --name ssh \
    --description "SSH access" \
    --access allow \
    --protocol tcp \
    --direction inbound \
    --destination-port-ranges 22
```

### <a name="create-nic-for-vm"></a>Создание сетевой карты для виртуальной машины

Создайте сетевой интерфейс с помощью команды [az network nic create](/cli/azure/network/nic#az-network-nic-create) и свяжите его с общедоступным IP-адресом и NSG. 

```azurecli-interactive
  az network nic create \
    --resource-group myResourceGroupNAT \
    --name myNic \
    --vnet-name myVnet \
    --subnet mySubnet \
    --public-ip-address myPublicIPVM \
    --network-security-group myNSG
```

### <a name="create-vm"></a>Создание виртуальной машины

Создайте виртуальную машину с помощью команды [az vm create](/cli/azure/vm#az-vm-create).  Мы создадим ключи SSH для этой виртуальной машины и сохраним закрытый ключ для последующего использования.

 ```azurecli-interactive
  az vm create \
    --resource-group myResourceGroupNAT \
    --name myVM \
    --nics myNic \
    --image UbuntuLTS \
    --generate-ssh-keys
```

Дождитесь завершения развертывания виртуальной машины и продолжите выполнение остальных шагов.

## <a name="discover-the-ip-address-of-the-vm"></a>Обнаружение IP-адреса виртуальной машины

Сначала необходимо найти IP-адрес виртуальной машины, которую вы создали. Чтобы получить общедоступный IP-адрес виртуальной машины, используйте команду [az network public-ip show](/cli/azure/network/public-ip#az-network-public-ip-show). 

```azurecli-interactive
  az network public-ip show \
    --resource-group myResourceGroupNAT \
    --name myPublicIPVM \
    --query [ipAddress] \
    --output tsv
``` 

>[!IMPORTANT]
>Скопируйте общедоступный IP-адрес и вставьте его в блокнот, чтобы этот адрес можно было использовать для доступа к виртуальной машине.

### <a name="sign-in-to-vm"></a>Вход в виртуальную машину

Учетные данные SSH должны сохраниться в Cloud Shell после предыдущей операции.  Откройте [Azure Cloud Shell](https://shell.azure.com) в браузере. Используйте IP-адрес, полученный на предыдущем шаге, для подключения к виртуальной машине по протоколу SSH.

```bash
ssh <ip-address-destination>
```

Теперь можно использовать NAT.

## <a name="clean-up-resources"></a>Очистка ресурсов

Вы можете удалить ненужную группу ресурсов и все содержащиеся в ней ресурсы, выполнив команду [az group delete](/cli/azure/group#az-group-delete).

```azurecli-interactive 
  az group delete \
    --name myResourceGroupNAT
```

## <a name="next-steps"></a>Дальнейшие действия

В этом учебнике вы создали шлюз NAT и виртуальную машину для его использования. 

Просмотрите метрики в Azure Monitor, чтобы проверить работу NAT. Выполните диагностику проблем, таких как нехватка ресурсов доступных портов SNAT.  Эта проблема устраняется путем добавления ресурсов общедоступного IP-адреса, ресурсов префикса общедоступного IP-адреса, или их сочетания.


- Дополнительные сведения о [NAT виртуальной сети Azure](./nat-overview.md).
- Дополнительные сведения о [ресурсе шлюза NAT](./nat-gateway-resource.md).
- Краткое руководство по развертыванию [ресурса Шлюза NAT с помощью Azure CLI](./quickstart-create-nat-gateway-cli.md).
- Краткое руководство по развертыванию [ресурса Шлюза NAT с помощью Azure PowerShell](./quickstart-create-nat-gateway-powershell.md).
- Краткое руководство по развертыванию [ресурса Шлюза NAT с помощью портала Azure](./quickstart-create-nat-gateway-portal.md).
> [!div class="nextstepaction"]
