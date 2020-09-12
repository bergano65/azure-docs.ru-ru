---
title: Примеры для PowerShell
description: Список примеров PowerShell для виртуальных машин
author: cynthn
ms.service: virtual-machines
ms.topic: how-to
ms.workload: infrastructure
ms.date: 03/01/2019
ms.author: cynthn
ms.openlocfilehash: b1a154e562db9f8fdd74300bb8eb3d6839cb2ef4
ms.sourcegitcommit: 5ed504a9ddfbd69d4f2d256ec431e634eb38813e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/02/2020
ms.locfileid: "89321928"
---
# <a name="azure-vm-powershell-samples-for-creating-and-managing-linux-vms"></a>Примеры Azure VM PowerShell для создания виртуальных машин Linux и управления ими

Ниже приведена таблица с ссылками на примеры сценариев PowerShell, которые позволяют создавать виртуальные машины Linux и управлять ими.

| Скрипт | Описание |
|---|---|
|**Создание виртуальных машин**||
| [Создание полностью настроенной виртуальной машины](./../scripts/virtual-machines-linux-powershell-sample-create-vm.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Создает группу ресурсов, виртуальную машину и все связанные ресурсы.|
| [Создание виртуальной машины с поддержкой Docker](./../scripts/virtual-machines-linux-powershell-sample-create-docker-host.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Создает виртуальную машину, настраивает ее в качестве узла Docker и запускает контейнер NGINX. |
| [Создание виртуальной машины и выполнение скрипта настройки](./../scripts/virtual-machines-linux-powershell-sample-create-vm-nginx.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Создает виртуальную машину и использует расширение пользовательских скриптов Azure для установки NGINX. |
| [Создание виртуальной машины с установленным экземпляром WordPress](./../scripts/virtual-machines-linux-powershell-sample-create-vm-wordpress.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Создает виртуальную машину и использует расширение пользовательских скриптов Azure для установки WordPress. |
| [Создание виртуальной машины с помощью существующего управляемого диска ОС с использованием CLI](./../scripts/virtual-machines-powershell-sample-create-vm-from-managed-os-disks.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Создает виртуальную машину путем подключения имеющегося управляемого диска как диска ОС. |
| [Создание виртуальной машины из моментального снимка с помощью PowerShell](./../scripts/virtual-machines-linux-powershell-sample-create-vm-from-snapshot.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Создает виртуальную машину из моментального снимка, сначала создав из него управляемый диск, а затем подключив этот диск как диск ОС. |
|**Управление хранилищем**||
| [Создание управляемого диска на основе VHD-файла в учетной записи хранения в той же или другой подписке](../scripts/virtual-machines-powershell-sample-create-managed-disk-from-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Создает управляемый диск из специализированного VHD в качестве диска ОС или из VHD данных в качестве диска данных в той же или в другой подписке.  |
| [Create a managed disk from a snapshot with PowerShell](../scripts/virtual-machines-powershell-sample-create-managed-disk-from-snapshot.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) (Создание управляемого диска из моментального снимка с помощью PowerShell) | Создание управляемого диска из моментального снимка. |
| [Экспорт моментального снимка в виде VHD в учетную запись хранения](../scripts/virtual-machines-powershell-sample-copy-snapshot-to-storage-account.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Экспортирует управляемый моментальный снимок в качестве VHD в учетную запись хранения в другом регионе. |
| [Экспорт VHD управляемого диска в учетную запись хранения](../scripts/virtual-machines-powershell-sample-copy-managed-disks-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Экспортирует базовый VHD управляемого диска в учетную запись хранения в другом регионе. |
| [Create a snapshot from a VHD to create multiple identical managed disks in small amount of time with PowerShell](../scripts/virtual-machines-powershell-sample-create-snapshot-from-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) (Создание моментального снимка на основе VHD для быстрого создания нескольких идентичных управляемых дисков с помощью PowerShell) | Создает моментальный снимок на основе VHD и использует его для быстрого создания нескольких идентичных управляемых дисков.  |
| [Копирование снимка экрана в ту же или другую подписку](../scripts/virtual-machines-powershell-sample-copy-snapshot-to-same-or-different-subscription.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Копирует снимок экрана в ту же или в другую подписку, расположенную в том же регионе, что и родительский снимок экрана. |
|**Мониторинг виртуальных машин**||
| [Мониторинг виртуальной машины с помощью журналов Azure Monitor](./../scripts/virtual-machines-linux-powershell-sample-create-vm-oms.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Создает виртуальную машину, устанавливает агент Log Analytics и регистрирует виртуальную машину в рабочей области Log Analytics.  |
| [Копирование управляемого диска в ту же или другую подписку](../scripts/virtual-machines-linux-powershell-sample-copy-managed-disks-to-same-or-different-subscription.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Копирует управляемый диск в ту же или другую подписку, расположенную в том же регионе, что и родительский управляемый диск.
| [Сбор сведений обо всех виртуальных машинах в подписке с использованием PowerShell](../scripts/virtual-machines-powershell-sample-collect-vm-details.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Отвечает за создание CSV-файла, содержащего имя виртуальной машины, имя группы ресурсов, частный IP-адрес, сведения о регионе, виртуальной сети, подсети и типе ОС, а также общедоступный IP-адрес виртуальных машин в указанной подписке.
| | |
