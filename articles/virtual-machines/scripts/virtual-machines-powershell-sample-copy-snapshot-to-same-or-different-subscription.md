---
title: Копирование моментального снимка управляемого диска в подписку (Windows) — PowerShell
description: Пример сценария Azure PowerShell для копирования (перемещения) моментального снимка управляемого диска в ту же или другую подписку.
services: virtual-machines-windows
documentationcenter: storage
author: ramankumarlive
manager: kavithag
tags: azure-service-management
ms.assetid: ''
ms.service: virtual-machines-windows
ms.topic: sample
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 02/28/2019
ms.author: ramankum
ms.openlocfilehash: a2fd6f8b5f2ba0771449178a2d8d3958329e83eb
ms.sourcegitcommit: 3be3537ead3388a6810410dfbfe19fc210f89fec
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/10/2020
ms.locfileid: "89651598"
---
# <a name="copy-snapshot-of-a-managed-disk-in-same-subscription-or-different-subscription-with-powershell-windows"></a>Копирование моментального снимка управляемого диска в ту же или другую подписку с помощью PowerShell (Windows)

Этот сценарий копирует моментальный снимок управляемого диска в ту же или другую подписку. Используйте этот скрипт в следующих сценариях:

1. чтобы перенести моментальный снимок из хранилища класса Premium (Premium_LRS) в стандартное хранилище (Standard_LRS или Standard_ZRS) для сокращения расходов;
1. чтобы перенести моментальный снимок из локально избыточного хранилища (Premium_LRS, Standard_LRS) в хранилище, избыточное между зонами (Standard_ZRS), для повышения его надежности;
1. чтобы переместить моментальный снимок в другую подписку в том же регионе на долгосрочное хранение.

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

 

## <a name="sample-script"></a>Пример скрипта

[!code-powershell[main](../../../powershell_scripts/virtual-machine/copy-snapshot-to-same-or-different-subscription/copy-snapshot-to-same-or-different-subscription.ps1 "Copy snapshot")]

## <a name="script-explanation"></a>Описание скрипта

Этот сценарий использует приведенные ниже команды для создания моментального снимка в целевой подписке с помощью идентификатора исходного моментального снимка. Для каждой команды в таблице приведены ссылки на соответствующую документацию.

| Get-Help | Примечания |
|---|---|
| [New-AzSnapshotConfig](/powershell/module/az.compute/new-azsnapshotconfig) | Создает конфигурацию моментального снимка, используемую для создания моментального снимка. Она содержит идентификатор ресурса родительского моментального снимка и расположение, которое совпадает с расположением родительского моментального снимка.  |
| [New-AzSnapshot](/powershell/module/az.compute/new-azsnapshot) | Создает моментальный снимок с помощью конфигурации моментального снимка, имени моментального снимка и имени группы ресурсов, которые передаются в качестве параметров. |

## <a name="next-steps"></a>Дальнейшие действия

[Создание виртуальной машины на основе моментального снимка](./virtual-machines-windows-powershell-sample-create-vm-from-snapshot.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

Дополнительные сведения о модуле Azure PowerShell см. в [документации по Azure PowerShell](/powershell/azure/).

Дополнительные примеры сценариев PowerShell для виртуальных машин представлены в [документации по виртуальным машинам Azure под управлением Windows](../windows/powershell-samples.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
