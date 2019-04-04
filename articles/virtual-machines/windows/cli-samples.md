---
title: Примеры Azure CLI для Windows | Документация Майкрософт
description: Примеры Azure CLI для Windows
services: virtual-machines-windows
documentationcenter: virtual-machines
author: cynthn
manager: jeconnoc
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
ms.openlocfilehash: 8bc151089f76e3f27ababb479f5e893ca9a99365
ms.sourcegitcommit: 9f4eb5a3758f8a1a6a58c33c2806fa2986f702cb
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/03/2019
ms.locfileid: "58905381"
---
# <a name="azure-cli-samples-for-windows-virtual-machines"></a>Примеры Azure CLI для виртуальных машин Windows

В следующей таблице содержатся ссылки на скрипты Bash, созданные с помощью Azure CLI, которые развертывают виртуальные машины Windows.

| | |
|---|---|
|**Создание виртуальных машин**||
| [Создание виртуальной машины](./../scripts/virtual-machines-windows-cli-sample-create-vm-quick-create.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Создает виртуальную машину Windows с минимальной конфигурацией. |
| [Создание полностью настроенной виртуальной машины](./../scripts/virtual-machines-windows-cli-sample-create-vm.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Создает группу ресурсов, виртуальную машину и все связанные ресурсы.|
| [Создание высокодоступных виртуальных машин](./../scripts/virtual-machines-windows-cli-sample-nlb.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Создает несколько виртуальных машин с высокодоступной конфигурацией с балансировкой нагрузки. |
| [Создание виртуальной Машины и запустите сценарий конфигурации](./../scripts/virtual-machines-windows-cli-sample-create-vm-iis.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Создает виртуальную машину и использует расширение пользовательских скриптов Azure для установки IIS. |
| [Создание виртуальной Машины и конфигурации DSC](./../scripts/virtual-machines-windows-cli-sample-create-iis-using-dsc.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Создает виртуальную машину и использует расширение настройки требуемого состояния (DSC) для установки IIS. |
|**Управление хранилищем**||
| [Создание управляемого диска из виртуального жесткого диска](../scripts/virtual-machines-windows-cli-sample-create-managed-disk-from-vhd.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Создание управляемого диска из специализированного VHD в качестве диска операционной системы или из VHD данных в качестве диска данных.  |
| [Создание управляемого диска из моментального снимка](../scripts/virtual-machines-windows-cli-sample-create-managed-disk-from-snapshot.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Создание управляемого диска из моментального снимка. |
| [Копирование управляемого диска в ту же или другую подписку](../scripts/virtual-machines-windows-cli-sample-copy-managed-disks-to-same-or-different-subscription.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Копирование управляемого диска в ту же или в другую подписку, но в том же регионе, что и родительский управляемый диск. 
| [Экспортировать его как виртуальный жесткий ДИСК в учетную запись хранения](../scripts/virtual-machines-windows-cli-sample-copy-snapshot-to-storage-account.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Экспорт управляемого моментального снимка в качестве VHD в учетную запись хранения в другом регионе. |
| [Экспорт виртуального жесткого диска, управляемого диска в учетную запись хранения](../scripts/virtual-machines-windows-cli-sample-copy-managed-disks-vhd.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Экспорт базового VHD управляемого диска в учетную запись хранения в другом регионе. |
| [Копирование моментального снимка, в том же или другую подписку](../scripts/virtual-machines-windows-cli-sample-copy-snapshot-to-same-or-different-subscription.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Копирование моментального снимка в ту же или в другую подписку, но в том же регионе, что и родительский моментальный снимок. |
|**Сети виртуальных машин**||
| [Защита сетевого трафика между виртуальными машинами](./../scripts/virtual-machines-windows-cli-sample-create-vm-nsg.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Создает две виртуальные машины, все связанные ресурсы, а также внешние и внутренние группы безопасности сети (NSG). |
|**Защита виртуальных машин**||
| [Шифрование дисков виртуальной Машины и данных](./../scripts/virtual-machines-windows-cli-sample-encrypt-vm.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Создание Azure Key Vault, ключа шифрования и субъекта-службы. Шифрование виртуальной машины. |
|**Мониторинг работоспособности виртуальных машин**||
| [Мониторинг виртуальной машины с помощью Azure Monitor](./../scripts/virtual-machines-windows-cli-sample-create-vm-oms.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Создает виртуальную машину, устанавливает агент Log Analytics и регистрирует виртуальную машину в рабочей области Log Analytics.  |
| | |
