---
title: Примеры Azure CLI
description: Примеры интерфейса командной строки для виртуальных машин Azure
author: cynthn
ms.service: virtual-machines-linux
ms.topic: how-to
ms.date: 03/01/2019
ms.author: cynthn
ms.custom: devx-track-azurecli
ms.openlocfilehash: 63c420a7ae082cbf38b529e71d8324550a84b7bd
ms.sourcegitcommit: 5ed504a9ddfbd69d4f2d256ec431e634eb38813e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/02/2020
ms.locfileid: "89319174"
---
# <a name="azure-cli-samples-for-linux-virtual-machines"></a>Примеры Azure CLI для виртуальных машин Linux

В следующей таблице содержатся ссылки на скрипты Bash, созданные с помощью Azure CLI.

| Скрипт | Описание |
|---|---|
|**Создание виртуальных машин**||
| [Создание виртуальной машины](./../scripts/virtual-machines-linux-cli-sample-create-vm-quick-create.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Создает виртуальную машину Linux с минимальной конфигурацией. |
| [Создание полностью настроенной виртуальной машины](./../scripts/virtual-machines-linux-cli-sample-create-vm.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Создает группу ресурсов, виртуальную машину и все связанные ресурсы.|
| [Создание высокодоступной виртуальной машины](./../scripts/virtual-machines-linux-cli-sample-nlb.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Создает несколько виртуальных машин с высокодоступной конфигурацией с балансировкой нагрузки. |
| [Создание виртуальной машины и выполнение скрипта настройки](./../scripts/virtual-machines-linux-cli-sample-create-vm-nginx.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Создает виртуальную машину и использует расширение пользовательских скриптов Azure для установки NGINX. |
| [Создание виртуальной машины с установленным экземпляром WordPress](./../scripts/virtual-machines-linux-cli-sample-create-vm-wordpress.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Создает виртуальную машину и использует расширение пользовательских скриптов Azure для установки WordPress. |
| [Создание виртуальной машины с помощью существующего управляемого диска ОС с использованием CLI](./../scripts/virtual-machines-linux-cli-sample-create-vm-from-managed-os-disks.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Создает виртуальную машину путем подключения имеющегося управляемого диска как диска ОС. |
| [Создание виртуальной машины из моментального снимка с помощью PowerShell](./../scripts/virtual-machines-linux-cli-sample-create-vm-from-snapshot.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Создает виртуальную машину из моментального снимка, сначала создав из него управляемый диск, а затем подключив этот диск как диск ОС. |
|**Управление хранилищем**||
| [Создание управляемого диска на основе VHD](../scripts/virtual-machines-cli-sample-create-managed-disk-from-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Создает управляемый диск из специализированного VHD в качестве диска ОС или виртуального жесткого диска данных в качестве диска данных.  |
| [Create a managed disk from a snapshot with PowerShell](../scripts/virtual-machines-cli-sample-create-managed-disk-from-snapshot.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) (Создание управляемого диска из моментального снимка с помощью PowerShell) | Создание управляемого диска из моментального снимка. |
| [Копирование управляемого диска в ту же или другую подписку](../scripts/virtual-machines-cli-sample-copy-managed-disks-to-same-or-different-subscription.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Копирование управляемого диска в ту же или в другую подписку, но в том же регионе, что и родительский управляемый диск. 
| [Экспорт моментального снимка в виде VHD в учетную запись хранения](../scripts/virtual-machines-cli-sample-copy-snapshot-to-storage-account.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Экспорт управляемого моментального снимка в качестве VHD в учетную запись хранения в другом регионе. |
| [Экспорт VHD управляемого диска в учетную запись хранения](../scripts/virtual-machines-cli-sample-copy-managed-disks-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Экспорт базового VHD управляемого диска в учетную запись хранения в другом регионе. |
| [Копирование моментального снимка в ту же или в другую подписку](../scripts/virtual-machines-cli-sample-copy-snapshot-to-same-or-different-subscription.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Копирование моментального снимка в ту же или в другую подписку, но в том же регионе, что и родительский моментальный снимок. |
|**Сети виртуальных машин**||
| [Защита сетевого трафика между виртуальными машинами](./../scripts/virtual-machines-linux-cli-sample-create-vm-nsg.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Создает две виртуальные машины, все связанные ресурсы, а также внешние и внутренние группы безопасности сети (NSG). |
|**Защита виртуальных машин**||
| [Шифрование виртуальной машины и дисков данных](./../scripts/virtual-machines-linux-cli-sample-encrypt-vm.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Создание Azure Key Vault, ключа шифрования и субъекта-службы. Шифрование виртуальной машины. |
|**Мониторинг виртуальных машин**||
| [Мониторинг виртуальной машины с помощью журналов Azure Monitor](./../scripts/virtual-machines-linux-cli-sample-create-vm-oms.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Создает виртуальную машину, устанавливает агент Log Analytics и регистрирует виртуальную машину в рабочей области Log Analytics.  |
|**Устранение неполадок с виртуальными машинами**||
| [Устранение неполадок диска операционной системы виртуальных машин](./../scripts/virtual-machines-linux-cli-sample-mount-os-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Подключает диск операционной системы из одной виртуальной машины в качестве диска данных ко второй виртуальной машине. |
| | |
