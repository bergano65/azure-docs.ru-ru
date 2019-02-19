---
title: Примеры Azure PowerShell. Установка приложений | Документация Майкрософт
description: Примеры сценариев Azure PowerShell.
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
ms.openlocfilehash: a2887eea0e4a43e21c776f7d25a1ee55971d80a1
ms.sourcegitcommit: 943af92555ba640288464c11d84e01da948db5c0
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/09/2019
ms.locfileid: "55981610"
---
# <a name="install-applications-into-a-virtual-machine-scale-set-with-powershell"></a>Установка приложений в масштабируемый набор виртуальных машин с помощью PowerShell
Этот скрипт создает виртуальную машину Azure под управлением Windows Server 2016 и использует расширение пользовательских скриптов для установки базовых веб-приложений. После выполнения скрипта можно получить доступ к веб-приложению с помощью веб-браузера.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [updated-for-az-vm.md](../../../includes/updated-for-az-vm.md)]

## <a name="sample-script"></a>Пример скрипта

[!code-powershell[main](../../../powershell_scripts/virtual-machine-scale-sets/install-apps/install-apps.ps1 "Install apps into a scale set")]

## <a name="clean-up-deployment"></a>Очистка развертывания
Выполните следующую команду, чтобы удалить группу ресурсов, масштабируемый набор и все связанные с ними ресурсы.

```powershell
Remove-AzResourceGroup -Name myResourceGroup
```

## <a name="script-explanation"></a>Описание скрипта
Чтобы создать развертывание, скрипт использует следующие команды. Для каждого элемента в таблице приведены ссылки на документацию по команде.

| Команда | Примечания |
|---|---|
| [New-AzVmss](/powershell/module/az.compute/new-azvmss) | Создание масштабируемого набора виртуальных машин и всех вспомогательных ресурсов, включая виртуальную сеть, подсистему балансировки нагрузки и правила NAT. |
| [Get-AzVmss](/powershell/module/az.compute/get-azvmss) | Получение информации о масштабируемом наборе виртуальных машин. |
| [Add-AzVmssExtension](/powershell/module/az.compute/add-azvmssextension) | Добавление расширения виртуальной машины для настраиваемого скрипта установки базового веб-приложения. |
| [Update-AzVmss](/powershell/module/az.compute/update-azvmss) | Обновление модели масштабируемого набора виртуальных машин для применения расширения виртуальной машины. |
| [Get-AzPublicIpAddress](/powershell/module/az.network/get-azpublicipaddress) | Позволяет получить сведения об общедоступном IP-адресе, который использует подсистема балансировки нагрузки. |
|  [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Удаляет группу ресурсов и все ресурсы, содержащиеся в ней. |

## <a name="next-steps"></a>Дополнительная информация
Дополнительные сведения о модуле Azure PowerShell см. в [документации по Azure PowerShell](/powershell/azure/overview).

См. дополнительные примеры сценариев PowerShell для [масштабируемого набора виртуальных машин Azure](../powershell-samples.md).
