---
title: Примеры сценариев Azure PowerShell.
description: Таблица, содержащая ссылки на примеры скриптов bash, созданных с помощью Azure PowerShell, таких как создание масштабируемого набора и управление им.
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
ms.openlocfilehash: a0a5939d1b64d93d4dbcc40b2639e3f3ac1a33be
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/25/2019
ms.locfileid: "75369139"
---
# <a name="azure-powershell-samples-for-virtual-machine-scale-sets"></a>Примеры сценариев Azure PowerShell для масштабируемых наборов виртуальных машин

В следующей таблице приведены ссылки на сценарии bash, созданные с помощью Azure PowerShell:

| | |
|---|---|
|**Создание масштабируемого набора и управление им**||
| [Создание простого масштабируемого набора виртуальных машин](scripts/powershell-sample-create-simple-scale-set.md?toc=%2fpowershell%2fazure%2ftoc.json) | Создание масштабируемого набора виртуальных машин с минимальной конфигурацией. |
| [Создание полного масштабируемого набора виртуальных машин](scripts/powershell-sample-create-complete-scale-set.md?toc=%2fpowershell%2fazure%2ftoc.json) | Создание масштабируемого набора виртуальных машин и связанных ресурсов с помощью файла конфигурации. |
| [Создание масштабируемого набора на основе пользовательского образа виртуальной машины](scripts/powershell-sample-create-scale-set-from-custom-image.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Создание масштабируемого набора виртуальных машин, использующего пользовательский образ виртуальной машины. |
| [Установка приложений в масштабируемом наборе](scripts/powershell-sample-install-apps.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Чтобы установить простое веб-приложение в масштабируемом наборе, используйте расширение пользовательских скриптов Azure. |
|**Управление хранилищем**||
| [Создание дисков и их подключение к масштабируемому набору](scripts/powershell-sample-attach-disks.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Создание масштабируемого набора виртуальных машин с подключенными дисками данных. |
|**Управление масштабированием и избыточностью**||
| [Включение автомасштабирования на основе узла](scripts/powershell-sample-enable-autoscale.md?toc=%2fpowershell%2fazure%2ftoc.json) | Создание масштабируемого набора виртуальных машин, настроенного для автоматического масштабирования в зависимости от загрузки ЦП. |
| [Создание однозонного масштабируемого набора](scripts/powershell-sample-single-availability-zone-scale-set.md?toc=%2fpowershell%2fazure%2ftoc.json) | Создание масштабируемого набора виртуальных машин, который использует одну зону доступности. |
| [Создание масштабируемого набора, избыточного между зонами](scripts/powershell-sample-zone-redundant-scale-set.md?toc=%2fpowershell%2fazure%2ftoc.json) | Создание масштабируемого набора виртуальных машин, который использует несколько зон доступности. |
| | |


[!INCLUDE [updated-for-az.md](../../includes/updated-for-az.md)]    