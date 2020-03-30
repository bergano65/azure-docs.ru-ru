---
title: Ошибки в загрузке в виртуальных машинах Azure Linux (ru) Документы Майкрософт
description: Эта статья помогает связать вас со статьями с устранением ошибок загрузки в виртуальных машинах Azure Linux.
services: virtual-machines-linux
documentationCenter: ''
author: vilibert
manager: spogge
editor: ''
ms.service: virtual-machines-linux
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 11/20/2019
ms.author: vilibert
ms.openlocfilehash: 37cb201751f72918838efe5837aa0e357d483f24
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74408746"
---
# <a name="troubleshoot-azure-linux-virtual-machines-boot-errors"></a>Устранение проблем Azure Linux Виртуальные машины загружают ошибки

В этой статье перечислены распространенные ошибки загрузки, которые вы можете получить при запуске виртуальной машины Linux (VM) в Microsoft Azure. Дополнительные сведения об ошибках см. в статьях в разделе **Boot errors and solutions** (Ошибки загрузки и их решения).

## <a name="boot-errors-and-solutions"></a>Ошибки загрузки и их решения

* [GRUB спасения](troubleshoot-vm-boot-error.md)

## <a name="next-steps"></a>Дальнейшие действия

- [Последовательная консоль виртуальной машины](serial-console-linux.md)

Устранение неполадок Linux VM путем присоединения диска ОС к восстановлению VM с помощью Azure:

- [Ремонт Azure VM](repair-linux-vm-using-azure-virtual-machine-repair-commands.md)

 Disk Swap - это может быть автоматизировано с помощью либо:
- [Скрипты восстановления энергетической оболочки](https://github.com/Azure/azure-support-scripts/tree/master/VMRecovery/ResourceManager)
- [Баш Восстановление Скрипты](https://github.com/sribs/azure-support-scripts)

- [Cli](troubleshoot-recovery-disks-linux.md)
- [Портал Azure](troubleshoot-recovery-disks-portal-linux.md)


## <a name="disk-swap-video"></a>Диск Своп Видео:

Если у вас нет доступа к GRUB смотреть [это](https://youtu.be/m5t0GZ5oGAc) видео и посмотреть, как вы можете легко автоматизировать процедуру своп диска, чтобы восстановить ваш VM

## <a name="unofficial-solution"></a>Неофициальное решение

Восстановление VM также может быть предпринято с неподдерживаемым сценарием BETA [ALAR](https://github.com/malachma/azure-auto-recover)