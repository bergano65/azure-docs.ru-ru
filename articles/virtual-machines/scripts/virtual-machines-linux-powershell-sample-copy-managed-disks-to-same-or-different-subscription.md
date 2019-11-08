---
title: Пример сценария Azure PowerShell. Копирование (перемещение) управляемых дисков в ту же или другую подписку | Документация Майкрософт
description: Пример сценария Azure PowerShell для копирования (перемещения) управляемых дисков в ту же или другую подписку.
services: virtual-machines-linux
documentationcenter: storage
author: ramankumarlive
manager: kavithag
editor: tysonn
tags: azure-service-management
ms.assetid: ''
ms.service: virtual-machines-linux
ms.topic: sample
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 06/06/2017
ms.author: ramankum
ms.openlocfilehash: babf7bf9de46e1657f94d19fe13bb0e328831ce0
ms.sourcegitcommit: 827248fa609243839aac3ff01ff40200c8c46966
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/07/2019
ms.locfileid: "73748889"
---
# <a name="copy-managed-disks-in-the-same-subscription-or-different-subscription-with-powershell"></a>Копирование управляемых дисков в ту же или другую подписку с помощью PowerShell

Этот сценарий создает копию управляемого диска в той же или в другой подписке. Новый диск создается в том же регионе, что и родительский управляемый диск.

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

 

## <a name="sample-script"></a>Пример скрипта

[!code-powershell[main](../../../powershell_scripts/virtual-machine/copy-managed-disks-to-same-or-different-subscription/copy-managed-disks-to-same-or-different-subscription.ps1 "Copy managed disk")]

## <a name="script-explanation"></a>Описание скрипта

Этот сценарий использует приведенные ниже команды для создания управляемого диска в целевой подписке с помощью идентификатора исходного управляемого диска. Для каждой команды в таблице приведены ссылки на соответствующую документацию.

| Команда | Примечания |
|---|---|
| [New-AzDiskConfig](https://docs.microsoft.com/powershell/module/az.compute/New-AzDiskConfig) | Создает конфигурацию диска, которая используется для создания диска. Она содержит идентификатор ресурса родительского диска и расположение, которое совпадает с расположением родительского диска.  |
| [New-AzDisk](https://docs.microsoft.com/powershell/module/az.compute/New-AzDisk) | Создает диск с помощью конфигурации диска, имени диска и имени группы ресурсов, которые передаются в качестве параметров. |

## <a name="next-steps"></a>Дополнительная информация

Дополнительные сведения о модуле Azure PowerShell см. в [документации по Azure PowerShell](/powershell/azure/overview).

Дополнительные примеры сценариев PowerShell для виртуальных машин представлены в [документации по виртуальным машинам Azure под управлением Linux](../linux/powershell-samples.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).