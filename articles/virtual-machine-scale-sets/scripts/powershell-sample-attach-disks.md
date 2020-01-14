---
title: Примеры Azure PowerShell. Присоединение и использование дисков данных
description: При помощи этого скрипта создается масштабируемый набор виртуальных машин Azure, а также присоединяются и подготавливаются диски данных с помощью PowerShell.
services: virtual-machine-scale-sets
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machine-scale-sets
ms.devlang: na
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/27/2018
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: f7098808837b9fd0206323fca03da4c98096d3c5
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/25/2019
ms.locfileid: "75350958"
---
# <a name="attach-and-use-data-disks-with-a-virtual-machine-scale-set-with-powershell"></a>Присоединение и использование дисков данных с масштабируемым набором виртуальных машин с помощью PowerShell
При помощи этого скрипта создается масштабируемый набор виртуальных машин, а также присоединяются и подготавливаются диски данных.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [updated-for-az.md](../../../includes/updated-for-az.md)]

## <a name="sample-script"></a>Пример скрипта

[!code-powershell[main](../../../powershell_scripts/virtual-machine-scale-sets/use-data-disks/use-data-disks.ps1 "Create a virtual machine scale set with data disks")]

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
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Удаляет группу ресурсов и все ресурсы, содержащиеся в ней. |

## <a name="next-steps"></a>Дальнейшие действия
Дополнительные сведения о модуле Azure PowerShell см. в [документации по Azure PowerShell](/powershell/azure/overview).

См. дополнительные примеры сценариев PowerShell для [масштабируемого набора виртуальных машин Azure](../powershell-samples.md).
