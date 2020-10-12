---
title: Создание общедоступного IP-адреса Azure CLI
description: Узнайте, как создать общедоступный IP-адрес с помощью Azure CLI
services: virtual-network
documentationcenter: na
author: blehr
ms.service: virtual-network
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/28/2020
ms.author: blehr
ms.openlocfilehash: 4d0934dd30f9738e2a67d4aff23c96a48aaaa61b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/09/2020
ms.locfileid: "89303606"
---
# <a name="quickstart-create-a-public-ip-address-using-azure-cli"></a>Краткое руководство. Создание общедоступного IP-адреса с помощью Azure CLI

В этой статье показано, как создать ресурс общедоступного IP-адреса с помощью Azure CLI. Дополнительные сведения о том, к каким ресурсам можно связываться, разница между SKU "базовый" и "Стандартный" и другими связанными сведениями см. в разделе [общедоступные IP-адреса](https://docs.microsoft.com/azure/virtual-network/public-ip-addresses).  В этом примере мы рассмотрим только IPv4-адреса. Дополнительные сведения об IPv6-адресах см. в статье [IPv6 для виртуальной сети Azure](https://docs.microsoft.com/azure/virtual-network/ipv6-overview).

## <a name="prerequisites"></a>Предварительные требования

- Локальная установка Azure CLI или Azure Cloud Shell

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)] 

Если вы решили установить и использовать CLI локально, для выполнения инструкций из этого руководства вам потребуется Azure CLI версии 2.0.28 или более поздней версии. Чтобы узнать версию, выполните команду `az --version`. Если вам необходимо выполнить установку или обновление, см. статью [Установка Azure CLI]( /cli/azure/install-azure-cli).

## <a name="create-a-resource-group"></a>Создание группы ресурсов

Группа ресурсов Azure является логическим контейнером, в котором происходит развертывание ресурсов Azure и управление ими.

Создайте группу ресурсов с помощью команды [AZ Group Create](https://docs.microsoft.com/cli/azure/group?view=azure-cli-latest#az-group-create) с именем **myResourceGroup** в расположении **eastus2** .

```azurecli-interactive
  az group create \
    --name myResourceGroup \
    --location eastus2
```
---
# <a name="standard-sku---using-zones"></a>[**SKU "Стандартный" — Использование зон**](#tab/option-create-public-ip-standard-zones)

>[!NOTE]
>Следующая команда работает для API версии 2020-08-01 или более поздней.  Дополнительные сведения о текущей используемой версии API см. в разделе [поставщики и типы ресурсов](https://docs.microsoft.com/azure/azure-resource-manager/management/resource-providers-and-types).

Чтобы создать стандартный общедоступный IP-адрес, избыточный в виде зоны, с именем **мистандардзрпублиЦип** в **myResourceGroup**, используйте команду [AZ Network public-IP Create](https://docs.microsoft.com/cli/azure/network/public-ip?view=azure-cli-latest#az-network-public-ip-create) .

```azurecli-interactive
  az network public-ip create \
    --resource-group myResourceGroup \
    --name myStandardZRPublicIP \
    --sku Standard
    --zone 1,2,3
```
> [!IMPORTANT]
> Для версий API старше 2020-08-01 выполните указанную выше команду, не указывая параметр зоны, чтобы создать IP-адрес, избыточный в виде зоны. 
>

Чтобы создать стандартный общедоступный IP-адрес зональные в зона 2 с именем **мистандардзоналпублиЦип** в **myResourceGroup**, используйте следующую команду:

```azurecli-interactive
  az network public-ip create \
    --resource-group myResourceGroupLB \
    --name myStandardZonalPublicIP \
    --sku Standard \
    --zone 2
```

Обратите внимание, что указанные выше параметры для зон — это допустимые варианты выбора в регионах с [зоны доступности](https://docs.microsoft.com/azure/availability-zones/az-overview?toc=/azure/virtual-network/toc.json#availability-zones).

# <a name="standard-sku---no-zones"></a>[**SKU "Стандартный" — без зон**](#tab/option-create-public-ip-standard)

>[!NOTE]
>Следующая команда работает для API версии 2020-08-01 или более поздней.  Дополнительные сведения о текущей используемой версии API см. в разделе [поставщики и типы ресурсов](https://docs.microsoft.com/azure/azure-resource-manager/management/resource-providers-and-types).

Чтобы создать стандартный общедоступный IP-адрес в качестве ресурса, отличного от зональные, с именем **мистандардпублиЦип** в **myResourceGroup**, используйте команду [AZ Network public-IP Create](https://docs.microsoft.com/cli/azure/network/public-ip?view=azure-cli-latest#az-network-public-ip-create) .

```azurecli-interactive
  az network public-ip create \
    --resource-group myResourceGroup \
    --name myStandardPublicIP \
    --sku Standard
```
Этот выбор допустим во всех регионах и является выбором по умолчанию для стандартных общедоступных IP-адресов в регионах без [зоны доступности](https://docs.microsoft.com/azure/availability-zones/az-overview?toc=/azure/virtual-network/toc.json#availability-zones).

# <a name="basic-sku"></a>[**SKU "Базовый"** ](#tab/option-create-public-ip-basic)

Чтобы создать базовый статический общедоступный IP-адрес с именем **мибасикпублиЦип** в **myResourceGroup**, выполните команду [AZ Network public-IP Create](https://docs.microsoft.com/cli/azure/network/public-ip?view=azure-cli-latest#az-network-public-ip-create) .  Основные общедоступные IP-адреса не имеют концепции зон доступности.

```azurecli-interactive
  az network public-ip create \
    --resource-group myResourceGroup \
    --name myBasicPublicIP \
    --sku Standard
    --allocation-method Static
```
Если IP-адрес приемлем для изменения со временем, можно выбрать **динамическое** назначение IP-адресов, изменив метод выделения на Dynamic.

---

## <a name="additional-information"></a>Дополнительные сведения 

Дополнительные сведения об отдельных переменных, перечисленных выше, см. в разделе [Управление общедоступными IP-адресами](https://docs.microsoft.com/azure/virtual-network/virtual-network-public-ip-address#create-a-public-ip-address).

## <a name="next-steps"></a>Дальнейшие шаги
- Свяжите [общедоступный IP-адрес с виртуальной машиной](https://docs.microsoft.com/azure/virtual-network/associate-public-ip-address-vm#azure-portal).
- См. дополнительные сведения об [общедоступных IP-адресах в Azure](virtual-network-ip-addresses-overview-arm.md#public-ip-addresses).
- См. сведения обо всех [параметрах общедоступных IP-адресов](virtual-network-public-ip-address.md#create-a-public-ip-address).