---
title: Включение и отключение последовательной консоли Azure | Документация Майкрософт
description: Включение и отключение службы последовательной консоли Azure
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
ms.openlocfilehash: fa400d875a8f39d54d10820c603e12e97f0cd854
ms.sourcegitcommit: 12d902e78d6617f7e78c062bd9d47564b5ff2208
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/24/2019
ms.locfileid: "74452236"
---
# <a name="enable-and-disable-the-azure-serial-console"></a>Включение и отключение последовательной консоли Azure

Как и любой другой ресурс, последовательная консоль Azure может быть включена и отключена. Последовательная консоль включена по умолчанию для всех подписок в глобальном Azure. В настоящее время отключение последовательной консоли отключит службу для всей подписки. Для отключения или повторного включения последовательной консоли для подписки в подписке требуется доступ на уровне участника или выше.

Вы также можете отключить последовательную консоль для отдельной виртуальной машины или экземпляра масштабируемого набора виртуальных машин, отключив диагностику загрузки. Вам потребуется доступ на уровне участника или выше в масштабируемом наборе виртуальных машин или виртуальных машинах, а также в учетной записи хранения для диагностики загрузки.

## <a name="vm-level-disable"></a>Отключение на уровне виртуальной машины
Последовательную консоль можно отключить для конкретной виртуальной машины или масштабируемого набора виртуальных машин, отключив параметр диагностики загрузки. Отключите диагностику загрузки из портал Azure, чтобы отключить последовательную консоль для ВИРТУАЛЬНОЙ машины или масштабируемого набора виртуальных машин. При использовании последовательной консоли в масштабируемом наборе виртуальных машин убедитесь, что экземпляры масштабируемых наборов виртуальных машин обновлены до последней модели.


## <a name="subscription-level-enabledisable"></a>Включение или отключение уровня подписки

### <a name="azure-cli"></a>Интерфейс командной строки Azure

Серийная консоль можно отключить и повторно включить для всей подписки с помощью следующих команд в Azure CLI (вы можете использовать кнопку "попробовать", чтобы запустить экземпляр Azure Cloud Shell, в котором можно выполнить команды):

Чтобы отключить последовательную консоль для подписки, используйте следующие команды:
```azurecli-interactive
subscriptionId=$(az account show --output=json | jq -r .id)

az resource invoke-action --action disableConsole --ids "/subscriptions/$subscriptionId/providers/Microsoft.SerialConsole/consoleServices/default" --api-version="2018-05-01"
```

Чтобы включить последовательную консоль для подписки, используйте следующие команды:
```azurecli-interactive
subscriptionId=$(az account show --output=json | jq -r .id)

az resource invoke-action --action enableConsole --ids "/subscriptions/$subscriptionId/providers/Microsoft.SerialConsole/consoleServices/default" --api-version="2018-05-01"
```

Чтобы получить текущее состояние включения или выключения последовательной консоли для подписки, используйте следующие команды:
```azurecli-interactive
subscriptionId=$(az account show --output=json | jq -r .id)

az resource show --ids "/subscriptions/$subscriptionId/providers/Microsoft.SerialConsole/consoleServices/default" --output=json --api-version="2018-05-01" | jq .properties
```

> [!NOTE]
> Перед выполнением этой команды убедитесь, что вы в правильном облаке (общедоступное облако Azure, облако для государственных организаций США). Вы можете проверить `az cloud list` и задать в облаке `az cloud set -n <Name of cloud>`.

### <a name="powershell"></a>PowerShell

Серийная консоль также можно включить и отключить с помощью PowerShell.

Чтобы отключить последовательную консоль для подписки, используйте следующие команды:
```azurepowershell-interactive
$subscription=(Get-AzContext).Subscription.Id

Invoke-AzResourceAction -Action disableConsole -ResourceId /subscriptions/$subscription/providers/Microsoft.SerialConsole/consoleServices/default -ApiVersion 2018-05-01
```

Чтобы включить последовательную консоль для подписки, используйте следующие команды:
```azurepowershell-interactive
$subscription=(Get-AzContext).Subscription.Id

Invoke-AzResourceAction -Action enableConsole -ResourceId /subscriptions/$subscription/providers/Microsoft.SerialConsole/consoleServices/default -ApiVersion 2018-05-01
```

## <a name="next-steps"></a>Дополнительная информация
* Дополнительные сведения о [последовательной консоли Azure для виртуальных машин Linux](./serial-console-linux.md)
* Дополнительные сведения о [последовательной консоли Azure для виртуальных машин Windows](./serial-console-windows.md)
* Сведения о [возможностях управления питанием в последовательной консоли Azure](./serial-console-power-options.md)