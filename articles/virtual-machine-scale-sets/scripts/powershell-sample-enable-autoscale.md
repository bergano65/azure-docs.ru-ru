---
title: Примеры для Azure PowerShell. Включение автомасштабирования на основе узла
description: С помощью этого скрипта создается масштабируемый набор виртуальных машин под управлением Windows Server 2016. При этом используются метрики на основе узла для автоматического масштабирования в соответствии с изменением нагрузки на ЦП.
author: ju-shim
ms.author: jushiman
ms.topic: sample
ms.service: virtual-machine-scale-sets
ms.subservice: autoscale
ms.date: 03/27/2018
ms.custom: avverma
ms.openlocfilehash: 4e9c050c82a08572042f75e7979fc338b8133d0f
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/12/2020
ms.locfileid: "83125066"
---
# <a name="automatically-scale-a-virtual-machine-scale-set-with-powershell"></a>Автоматическое масштабирование масштабируемых наборов виртуальных машин с помощью PowerShell
С помощью этого скрипта создается масштабируемый набор виртуальных машин под управлением Windows Server 2016. При этом используются метрики на основе узла для автоматического масштабирования в соответствии с изменением нагрузки на ЦП.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [updated-for-az.md](../../../includes/updated-for-az.md)]

## <a name="sample-script"></a>Пример скрипта


[!code-powershell[main](../../../powershell_scripts/virtual-machine-scale-sets/auto-scale-host-metrics/auto-scale-host-metrics.ps1 "Automatically scale a virtual machine scale set")]

## <a name="clean-up-deployment"></a>Очистка развертывания
Выполните следующую команду, чтобы удалить группу ресурсов, масштабируемый набор и все связанные с ними ресурсы.

```powershell
Remove-AzResourceGroup -Name myResourceGroup
```

## <a name="script-explanation"></a>Описание скрипта
Чтобы создать развертывание, скрипт использует следующие команды. Для каждого элемента в таблице приведены ссылки на документацию по команде.

| Get-Help | Примечания |
|---|---|
| [New-AzVmss](/powershell/module/az.compute/new-azvmss) | Создание масштабируемого набора виртуальных машин и всех вспомогательных ресурсов, включая виртуальную сеть, подсистему балансировки нагрузки и правила NAT. |
| [Get-AzVmss](/powershell/module/az.compute/get-azvmss) | Получение информации о масштабируемом наборе виртуальных машин. |
| [Add-AzVmssExtension](/powershell/module/az.compute/add-azvmssextension) | Добавление расширения виртуальной машины для настраиваемого скрипта установки базового веб-приложения. |
| [Update-AzVmss](/powershell/module/az.compute/update-azvmss) | Обновление модели масштабируемого набора виртуальных машин для применения расширения виртуальной машины. |
| [Get-AzPublicIpAddress](/powershell/module/az.network/get-azpublicipaddress) | Позволяет получить сведения об общедоступном IP-адресе, который использует подсистема балансировки нагрузки. |
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Удаляет группу ресурсов и все ресурсы, содержащиеся в ней. |

## <a name="next-steps"></a>Дальнейшие действия
Дополнительные сведения о модуле Azure PowerShell см. в [документации по Azure PowerShell](/powershell/azure/overview).

См. дополнительные примеры сценариев PowerShell для [масштабируемого набора виртуальных машин Azure](../powershell-samples.md).
