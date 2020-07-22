---
title: Настройка предпочтительного варианта маршрутизации для виртуальной машины — Azure CLI
description: Узнайте, как создать виртуальную машину с общедоступным IP-адресом и выбором предпочтительного варианта маршрутизации с помощью интерфейса командной строки (CLI) Azure.
services: virtual-network
documentationcenter: na
author: KumudD
manager: mtillman
ms.service: virtual-network
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/18/2020
ms.author: mnayak
ms.openlocfilehash: d5ea44e7059ae01204dbafd454c187e10f85e4e8
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: ru-RU
ms.lasthandoff: 07/02/2020
ms.locfileid: "84707554"
---
# <a name="configure-routing-preference-for-a-vm-using-azure-cli"></a>Настройка предпочтительного варианта маршрутизации для виртуальной машины с помощью Azure CLI

В этой статье показано, как настроить предпочтительный вариант маршрутизации для виртуальной машины. Связанный с Интернетом трафик от виртуальной машины будет направляться через сеть поставщика услуг Интернета при выборе параметра **Интернет** в качестве предпочтительного варианта маршрутизации. Маршрутизация по умолчанию осуществляется через глобальную сеть Майкрософт.

В этой статье показано, как создать виртуальную машину с общедоступным IP-адресом, настроенным для маршрутизации трафика через общедоступный Интернет с помощью Azure CLI.

> [!IMPORTANT]
> Выбор предпочтительного варианта маршрутизации сейчас предоставляется в общедоступной предварительной версии.
> Эта предварительная версия предоставляется без соглашения об уровне обслуживания и не рекомендована для использования рабочей среде. Некоторые функции могут не поддерживаться или их возможности могут быть ограничены. Дополнительные сведения см. в статье [Дополнительные условия использования предварительных выпусков Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="register-the-feature-for-your-subscription"></a>Регистрация функции для подписки
Функция выбора предпочтительного варианта маршрутизации в настоящее время доступна в предварительной версии. Зарегистрируйте функцию для своей подписки следующим образом:
```azurecli
az feature register --namespace Microsoft.Network --name AllowRoutingPreferenceFeature
```
## <a name="create-a-resource-group"></a>Создание группы ресурсов
1. При использовании Cloud Shell перейдите к шагу 2. Откройте сеанс командной строки и войдите в Azure с помощью команды `az login`.
2. Создайте группу ресурсов с помощью команды [az group create](/cli/azure/group#az-group-create). В следующем примере создается группа ресурсов в регионе Azure "Восточная часть США".

    ```azurecli
    az group create --name myResourceGroup --location eastus
    ```

## <a name="create-a-public-ip-address"></a>Создание общедоступного IP-адреса
Чтобы получить доступ к виртуальным машинам из Интернета, необходимо создать общедоступный IP-адрес. Создайте общедоступный IP-адрес с помощью команды [az network public-ip create](/cli/azure/network/public-ip). В следующем примере создается общедоступный IP-адрес с предпочтительным вариантом маршрутизации *Интернет* в регионе *Восточная часть США*.

```azurecli
az network public-ip create \
--name MyRoutingPrefIP \
--resource-group MyResourceGroup \
--location eastus \
--ip-tags 'RoutingPreference=Internet' \
--sku STANDARD \
--allocation-method static \
--version IPv4
```

## <a name="create-network-resources"></a>Создание сетевых ресурсов

Перед развертыванием виртуальной машины необходимо создать вспомогательные сетевые ресурсы — группу безопасности сети, виртуальную сеть и виртуальный сетевой адаптер.

### <a name="create-a-network-security-group"></a>Создание группы безопасности сети

Создайте группу безопасности сети для правил, которые будут управлять входящим и исходящим обменом данными в виртуальной сети с помощью [az network nsg create](https://docs.microsoft.com/cli/azure/network/nsg?view=azure-cli-latest#az-network-nsg-create)

```azurecli
az network nsg create \
--name myNSG  \
--resource-group MyResourceGroup \
--location eastus
```

### <a name="create-a-virtual-network"></a>Создание виртуальной сети

Создайте виртуальную сеть с помощью команды [az network vnet create](https://docs.microsoft.com/cli/azure/network/vnet?view=azure-cli-latest#az-network-vnet-create). В следующем примере создается виртуальная сеть *myVNET* с подсетью *mySubNet*.

```azurecli
# Create a virtual network
az network vnet create \
--name myVNET \
--resource-group MyResourceGroup \
--location eastus

# Create a subnet
az network vnet subnet create \
--name mySubNet \
--resource-group MyResourceGroup \
--vnet-name myVNET \
--address-prefixes "10.0.0.0/24" \
--network-security-group myNSG
```

### <a name="create-a-nic"></a>Создание сетевой карты

Создайте виртуальную сетевую карту для виртуальной машины с помощью команды [az network nic create](https://docs.microsoft.com/cli/azure/network/nic?view=azure-cli-latest#az-network-nic-create). В следующем примере создается виртуальная сетевая карта, которая будет подключена к виртуальной машине.

```azurecli-interactive
# Create a NIC
az network nic create \
--name mynic  \
--resource-group MyResourceGroup \
--network-security-group myNSG  \
--vnet-name myVNET \
--subnet mySubNet  \
--private-ip-address-version IPv4 \
--public-ip-address MyRoutingPrefIP
```

## <a name="create-a-virtual-machine"></a>Создание виртуальной машины

Создайте виртуальную машину с помощью команды [az vm create](https://docs.microsoft.com/cli/azure/vm?view=azure-cli-latest#az-vm-create). В приведенном ниже примере создается виртуальная машина windows server 2019 и обязательные компоненты виртуальной сети, если их еще нет:

```azurecli
az vm create \
--name myVM \
--resource-group MyResourceGroup \
--nics mynic \
--size Standard_A2 \
--image MicrosoftWindowsServer:WindowsServer:2019-Datacenter:latest \
--admin-username myUserName
```

## <a name="clean-up-resources"></a>Очистка ресурсов

Чтобы удалить ненужную группу ресурсов и все содержащиеся в ней ресурсы, выполните команду [az group delete](/cli/azure/group#az-group-delete).

```azurecli
az group delete --name myResourceGroup --yes
```

## <a name="next-steps"></a>Дальнейшие действия

- Дополнительные сведения о [предпочтительном варианте маршрутизации в общедоступных IP-адресах](routing-preference-overview.md).
- См. дополнительные сведения об [общедоступных IP-адресах в Azure](virtual-network-ip-addresses-overview-arm.md#public-ip-addresses).
- Дополнительные сведения о [параметрах общедоступного IP-адреса](virtual-network-public-ip-address.md#create-a-public-ip-address).
