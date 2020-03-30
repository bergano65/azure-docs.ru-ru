---
title: Используйте CLI для развертывания VMs Azure Spot (Предварительный просмотр)
description: Узнайте, как использовать CLI для развертывания VMs Azure Spot для экономии затрат.
services: virtual-machines-linux
documentationcenter: ''
author: cynthn
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/11/2020
ms.author: cynthn
ms.openlocfilehash: 110e935671ab1d640b2ff3dc26c203b262e999fe
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77163098"
---
# <a name="preview-deploy-spot-vms-using-the-azure-cli"></a>Предварительный просмотр: Развертывание Spot VMs с помощью Azure CLI

Использование [VMs Azure Spot](spot-vms.md) позволяет использовать наши неиспользованные мощности при значительной экономии средств. В любой момент времени, когда Azure требуется обратной мощности, инфраструктура Azure будет выселять Spot VMs. Таким образом, Spot VMs отлично подходят для рабочих нагрузок, которые могут обрабатывать перерывы, такие как задания по обработке пакетов, среды для разработки разработчиков/тестов, большие вычислительные нагрузки и многое другое.

Цены на Spot VMs является переменной, на основе региона и SKU. Для получения дополнительной информации [Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) см. [Windows](https://azure.microsoft.com/pricing/details/virtual-machines/windows/) 

У вас есть возможность установить максимальную цену, которую вы готовы платить, в час, для VM. Максимальная цена на Spot VM может быть установлена в долларах США (USD), используя до 5 десятичных мест. Например, значение `0.98765`будет максимальной ценой $0.98765 USD в час. Если вы установите максимальную цену, чтобы быть, `-1`VM не будет выселен в зависимости от цены. Цена для VM будет текущая цена для Spot или цена для стандартного VM, который всегда меньше, до тех пор, пока есть емкость и квота доступна. Для получения дополнительной информации об установлении максимальной цены, см [Spot VMs - Цены](spot-vms.md#pricing).

Процесс создания VM с помощью Spot с помощью Azure CLI такой же, как описано в [статье quickstart](/azure/virtual-machines/linux/quick-create-cli). Просто добавьте параметр '--priority Spot' `-1`и предоставьте максимальную цену или .

> [!IMPORTANT]
> Точечные экземпляры в настоящее время находятся в общедоступном предварительном просмотре.
> Эта предварительная версия не рекомендуется для производственных нагрузок. Некоторые функции могут не поддерживаться или их возможности могут быть ограничены. Дополнительные сведения см. в статье [Дополнительные условия использования предварительных выпусков Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
>



## <a name="install-azure-cli"></a>Установка Azure CLI

Для создания Spot VMs необходимо запустить версию Azure CLI 2.0.74 или позже. Чтобы узнать версию, выполните команду **az --version**. Если вам необходимо выполнить установку или обновление, см. статью [Установка Azure CLI](/cli/azure/install-azure-cli). 

Затем войдите в Azure с помощью команды [az login](/cli/azure/reference-index#az-login).

```azurecli
az login
```

## <a name="create-a-spot-vm"></a>Создание Spot VM

Этот пример показывает, как развернуть Linux Spot VM, который не будет выселен на основе цены. 

```azurecli
az group create -n mySpotGroup -l eastus
az vm create \
    --resource-group mySpotGroup \
    --name myVM \
    --image UbuntuLTS \
    --admin-username azureuser \
    --generate-ssh-keys \
    --priority Spot \
    --max-price -1
```

После создания VM можно запросить максимальную цену выставления счетов для всех вс-бар в группе ресурсов.

```azurecli
az vm list \
   -g mySpotGroup \
   --query '[].{Name:name, MaxPrice:billingProfile.maxPrice}' \
   --output table
```

**Дальнейшие действия**

Вы также можете создать Spot VM с помощью [Azure PowerShell](../windows/spot-powershell.md) или [шаблона.](spot-template.md)

Если вы столкнулись с ошибкой, просмотрите [коды ошибок](../error-codes-spot.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
