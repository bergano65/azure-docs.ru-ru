---
title: Включить и отключить серийную консоль Azure (ru) Документы Майкрософт
description: Как включить и отключить службу серийной консоли Azure
services: virtual-machines
documentationcenter: ''
author: asinn826
manager: borisb
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm
ms.workload: infrastructure-services
ms.date: 8/20/2019
ms.author: alsin
ms.openlocfilehash: e09e08f8ba36cf576bc27551254225adee3bb0fd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75451306"
---
# <a name="enable-and-disable-the-azure-serial-console"></a>Включить и отключить серийную консоль Azure

Как и любой другой ресурс, серийная консоль Azure может быть включена и отключена. Serial Console включена по умолчанию для всех подписок в глобальной Azure. В настоящее время отключение Serial Console отключит сервис для всей подписки. Отключение или повторное включение Серийной Консоли для подписки требует доступа на уровне участников или выше по подписке.

Вы также можете отключить серийную консоль для отдельного VM или виртуального масштаба машины, установленного экземпляром, отключив диагностику ботинок. Вам потребуется доступ к уровню вкладчика или выше как в наборе VM/virtual machine, так и в учетной записи хранения загрузочной диагностики.

## <a name="vm-level-disable"></a>Отключение на уровне виртуальной машины
Серийная консоль может быть отключена для определенного VM или виртуального масштаба машины, установленного путем отключения настройки диагностики загрузки. Выключите диагностику загрузки с портала Azure, чтобы отключить серийную консоль для VM или набора виртуальной машины. Если вы используете серийную консоль в виртуальном наборе масштабов машины, убедитесь, что вы обновите экземпляры виртуального набора масштабов машины до последней модели.


## <a name="subscription-level-enabledisable"></a>Включение/отключить на уровне подписки

> [!NOTE]
> Убедитесь, что перед запуском этой команды вы находитесь в нужном облаке (Azure Public Cloud, Azure US Government Cloud). Вы можете `az cloud list` проверить и установить облако с `az cloud set -n <Name of cloud>`.

### <a name="azure-cli"></a>Azure CLI

Серийная консоль может быть отключена и повторно включена для всей подписки, используя следующие команды в Azure CLI (вы можете использовать кнопку "Попробуй" для запуска экземпляра облачной оболочки Azure, в которой можно запускать команды):

Чтобы отключить серийную консоль для подписки, используйте следующие команды:
```azurecli-interactive
subscriptionId=$(az account show --output=json | jq -r .id)

az resource invoke-action --action disableConsole --ids "/subscriptions/$subscriptionId/providers/Microsoft.SerialConsole/consoleServices/default" --api-version="2018-05-01"
```

Для включения серийной консоли для подписки используйте следующие команды:
```azurecli-interactive
subscriptionId=$(az account show --output=json | jq -r .id)

az resource invoke-action --action enableConsole --ids "/subscriptions/$subscriptionId/providers/Microsoft.SerialConsole/consoleServices/default" --api-version="2018-05-01"
```

Чтобы получить текущий статус включенной/отключенной последовательной консоли для подписки, используйте следующие команды:
```azurecli-interactive
subscriptionId=$(az account show --output=json | jq -r .id)

az resource show --ids "/subscriptions/$subscriptionId/providers/Microsoft.SerialConsole/consoleServices/default" --output=json --api-version="2018-05-01" | jq .properties
```

### <a name="powershell"></a>PowerShell

Серийная консоль также может быть включена и отключена с помощью PowerShell.

Чтобы отключить серийную консоль для подписки, используйте следующие команды:
```azurepowershell-interactive
$subscription=(Get-AzContext).Subscription.Id

Invoke-AzResourceAction -Action disableConsole -ResourceId /subscriptions/$subscription/providers/Microsoft.SerialConsole/consoleServices/default -ApiVersion 2018-05-01
```

Для включения серийной консоли для подписки используйте следующие команды:
```azurepowershell-interactive
$subscription=(Get-AzContext).Subscription.Id

Invoke-AzResourceAction -Action enableConsole -ResourceId /subscriptions/$subscription/providers/Microsoft.SerialConsole/consoleServices/default -ApiVersion 2018-05-01
```

## <a name="next-steps"></a>Дальнейшие действия
* Узнайте больше о [серийной консоли Azure для Linux VMs](./serial-console-linux.md)
* Подробнее о [серийной консоли Azure для Windows VMs](./serial-console-windows.md)
* Узнайте о [вариантах управления питанием в серийной консоли Azure](./serial-console-power-options.md)