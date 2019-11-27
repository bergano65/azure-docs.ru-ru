---
title: Устранение ошибок загрузки в Azure Виртуальные машины Linux | Документация Майкрософт
description: Эта статья поможет вам привязать вас к статьям, чтобы устранить ошибки загрузки в Azure Виртуальные машины Linux.
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
ms.sourcegitcommit: dd0304e3a17ab36e02cf9148d5fe22deaac18118
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/22/2019
ms.locfileid: "74408746"
---
# <a name="troubleshoot-azure-linux-virtual-machines-boot-errors"></a>Устранение ошибок загрузки Azure Виртуальные машины Linux

В этой статье перечислены распространенные ошибки загрузки, которые могут быть получены при запуске виртуальной машины Linux в Microsoft Azure. Дополнительные сведения об ошибках см. в статьях в разделе **Boot errors and solutions** (Ошибки загрузки и их решения).

## <a name="boot-errors-and-solutions"></a>Ошибки загрузки и их решения

* [GRUB помощь](troubleshoot-vm-boot-error.md)

## <a name="next-steps"></a>Дополнительная информация

- [Последовательная консоль виртуальной машины](serial-console-linux.md)

Устранение неполадок виртуальной машины Linux путем подключения диска ОС к виртуальной машине восстановления с помощью Azure:

- [Восстановление виртуальной машины Azure](repair-linux-vm-using-azure-virtual-machine-repair-commands.md)

 Переключение диска. его можно автоматизировать с помощью следующих средств:
- [Сценарии восстановления PowerShell](https://github.com/Azure/azure-support-scripts/tree/master/VMRecovery/ResourceManager)
- [Скрипты Bash Recovery](https://github.com/sribs/azure-support-scripts)

- [ИНТЕРФЕЙС КОМАНДНОЙ СТРОКИ](troubleshoot-recovery-disks-linux.md)
- [портал Azure](troubleshoot-recovery-disks-portal-linux.md)


## <a name="disk-swap-video"></a>Видео о переключении диска:

Если у вас нет доступа к GRUB, просмотрите [это](https://youtu.be/m5t0GZ5oGAc) видео и посмотрите, как можно легко автоматизировать процедуру переключения диска для восстановления виртуальной машины.

## <a name="unofficial-solution"></a>Неофициальное решение

Восстановление виртуальной машины также можно выполнить с помощью неподдерживаемой бета-версии скрипта [Алар](https://github.com/malachma/azure-auto-recover)