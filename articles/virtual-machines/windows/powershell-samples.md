---
title: Примеры PowerShell для виртуальной машины Azure | Документация Майкрософт
description: Примеры PowerShell для виртуальной машины Azure.
services: virtual-machines-windows
documentationcenter: virtual-machines
author: cynthn
manager: gwallace
editor: tysonn
tags: azure-service-management
ms.assetid: ''
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 03/01/2019
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: 9aea63b8366bf974fd89c32105bea707ad72c8a5
ms.sourcegitcommit: dad277fbcfe0ed532b555298c9d6bc01fcaa94e2
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/10/2019
ms.locfileid: "67719983"
---
# <a name="azure-virtual-machine-powershell-samples"></a>Примеры PowerShell для виртуальной машины Azure

Ниже приведена таблица с ссылками на примеры сценариев PowerShell, которые позволяют создавать виртуальные машины Windows и управлять ими.

| | |
|---|---|
|**Создание виртуальных машин**||
| [Быстрое создание виртуальной машины](./../scripts/virtual-machines-windows-powershell-sample-create-vm-quick.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Создает группу ресурсов, виртуальную машину и все связанные ресурсы почти без участия пользователя.|
| [Создание полностью настроенной виртуальной машины](./../scripts/virtual-machines-windows-powershell-sample-create-vm.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Создает группу ресурсов, виртуальную машину и все связанные ресурсы.|
| [Создание высокодоступной виртуальной машины](./../scripts/virtual-machines-windows-powershell-sample-create-nlb-vm.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Создает несколько виртуальных машин с высокодоступной конфигурацией с балансировкой нагрузки.|
| [Создание виртуальной машины с помощью сценария настройки](./../scripts/virtual-machines-windows-powershell-sample-create-vm-iis.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Создает виртуальную машину и использует расширение пользовательских скриптов Azure для установки IIS. |
| [Создание виртуальной машины с помощью DSC](./../scripts/virtual-machines-windows-powershell-sample-create-iis-using-dsc.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Создает виртуальную машину и использует расширение настройки требуемого состояния (DSC) для установки IIS. |
| [Sample script to upload a VHD to Azure and create a new VM](./../scripts/virtual-machines-windows-powershell-upload-generalized-script.md) (Пример скрипта для передачи VHD в Azure и создания виртуальной машины) | Передает локальный файл VHD в Azure, создает образ на основе VHD и виртуальную машину на его основе. |
| [Создание виртуальной машины с помощью существующего управляемого диска ОС с использованием CLI](./../scripts/virtual-machines-windows-powershell-sample-create-vm-from-managed-os-disks.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Создает виртуальную машину путем подключения имеющегося управляемого диска как диска ОС. |
| [Создание виртуальной машины из моментального снимка с помощью PowerShell](./../scripts/virtual-machines-windows-powershell-sample-create-vm-from-snapshot.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Создает виртуальную машину из моментального снимка, сначала создав из него управляемый диск, а затем подключив этот диск как диск ОС. |
|**Управление хранилищем**||
| [Создание управляемого диска на основе VHD-файла в учетной записи хранения в той же или другой подписке](../scripts/virtual-machines-windows-powershell-sample-create-managed-disk-from-vhd.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Создает управляемый диск из специализированного VHD в качестве диска ОС или из VHD данных в качестве диска данных в той же или в другой подписке.  |
| [Create a managed disk from a snapshot with PowerShell](../scripts/virtual-machines-windows-powershell-sample-create-managed-disk-from-snapshot.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) (Создание управляемого диска из моментального снимка с помощью PowerShell) | Создание управляемого диска из моментального снимка. |
| [Копирование управляемого диска в ту же или другую подписку](../scripts/virtual-machines-windows-powershell-sample-copy-managed-disks-to-same-or-different-subscription.md?toc=%2fcli%2fmodule%2ftoc.json) | Копирует управляемый диск в ту же или другую подписку, расположенную в том же регионе, что и родительский управляемый диск.
| [Экспорт моментального снимка в виде VHD в учетную запись хранения](../scripts/virtual-machines-windows-powershell-sample-copy-snapshot-to-storage-account.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Экспортирует управляемый моментальный снимок в качестве VHD в учетную запись хранения в другом регионе. |
| [Экспорт VHD управляемого диска в учетную запись хранения](../scripts/virtual-machines-windows-powershell-sample-copy-managed-disks-vhd.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Экспортирует базовый VHD управляемого диска в учетную запись хранения в другом регионе. |
| [Create a snapshot from a VHD to create multiple identical managed disks in small amount of time with PowerShell](../scripts/virtual-machines-windows-powershell-sample-create-snapshot-from-vhd.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) (Создание моментального снимка на основе VHD для быстрого создания нескольких идентичных управляемых дисков с помощью PowerShell) | Создает моментальный снимок на основе VHD и использует его для быстрого создания нескольких идентичных управляемых дисков.  |
| [Копирование снимка экрана в ту же или другую подписку](../scripts/virtual-machines-windows-powershell-sample-copy-snapshot-to-same-or-different-subscription.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Копирует снимок экрана в ту же или в другую подписку, расположенную в том же регионе, что и родительский снимок экрана. |
|**Защита виртуальных машин**||
| [Шифрование виртуальной машины и ее дисков данных](./../scripts/virtual-machines-windows-powershell-sample-encrypt-vm.md?toc=%2fpowershell%2fazure%2ftoc.json) | Создает Azure Key Vault, ключ шифрования и субъект-службу и шифрует виртуальную машину. |
|**Мониторинг виртуальных машин**||
| [Мониторинг виртуальной Машины с помощью Azure Monitor](./../scripts/virtual-machines-windows-powershell-sample-create-vm-oms.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Создает виртуальную машину, устанавливает агент Azure Log Analytics и регистрирует виртуальную машину в рабочей области Log Analytics.  |
| [Сбор сведений о всех виртуальных машин в подписке с помощью PowerShell](../scripts/virtual-machines-powershell-sample-collect-vm-details.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Создает CSV-файл, содержащий имя, группа ресурсов виртуальной Машины имя, регион, виртуальной сети, подсети, частный IP-адрес, тип операционной системы и общедоступный IP-адрес из виртуальных машин в выбранной подписке.
| | |
