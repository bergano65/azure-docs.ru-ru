---
title: Исправление несоответствующих требованиям серверов State Configuration службы автоматизации Azure
description: В этой статье рассказывается, как повторно применить конфигурации по запросу к серверам, на которых изменилось состояние конфигурации.
services: automation
ms.service: automation
ms.subservice: dsc
author: mgreenegit
ms.author: migreene
ms.topic: conceptual
ms.date: 07/17/2019
manager: nirb
ms.openlocfilehash: ff785bf3ace7c65f83fe8e505f0544edd24776d8
ms.sourcegitcommit: 0b80a5802343ea769a91f91a8cdbdf1b67a932d3
ms.contentlocale: ru-RU
ms.lasthandoff: 05/25/2020
ms.locfileid: "83836861"
---
# <a name="remediate-noncompliant-azure-automation-state-configuration-servers"></a>Исправление несоответствующих требованиям серверов State Configuration службы автоматизации Azure

При регистрации сервера в State Configuration службы автоматизации Azure для него устанавливается режим конфигурации `ApplyOnly`, `ApplyandMonitor` или `ApplyAndAutoCorrect`. При любом режиме, кроме `ApplyAndAutoCorrect`, серверы с любыми отклонениями от состояния соответствия считаются несоответствующими до тех пор, пока не будут исправлены вручную.

Вычисления Azure предоставляют возможность "Выполнение команд", которая позволяет клиентам выполнять скрипты на виртуальных машинах.
В этом документе приводятся примеры скриптов для такого выполнения, которые позволяют вручную исправить изменения конфигурации.

## <a name="correct-drift-of-windows-virtual-machines-using-powershell"></a>Корректировка изменения конфигурации для виртуальных машин Windows с помощью PowerShell

Вы можете скорректировать изменение конфигурации для виртуальных машин Windows с помощью команды `Run`. См. статью [Выполнение сценариев PowerShell в виртуальной машине Windows с помощью функции выполнения команд](/azure/virtual-machines/windows/run-command).

Чтобы принудительно загрузить и применить самую свежую конфигурацию на узле State Configuration службы автоматизации Azure, используйте командлет [Update-DscConfiguration](/powershell/module/psdesiredstateconfiguration/update-dscconfiguration).

```powershell
Update-DscConfiguration -Wait -Verbose
```

## <a name="correct-drift-of-linux-virtual-machines"></a>Корректировка изменения конфигурации для виртуальных машин Linux

Для виртуальных машин Linux нет возможности применить команду `Run`. Исправить изменения их конфигурации можно, только повторив процесс регистрации. 

Для узлов Azure изменения конфигурации можно исправить на портале Azure или с помощью командлетов модуля Az. Сведения об этом процессе см. в разделе [Подключение виртуальной машины с помощью портала Azure](automation-dsc-onboarding.md#enable-a-vm-using-azure-portal).

Для гибридных узлов изменение конфигурации можно исправить с помощью скриптов Python. См. статью [о выполнении операций DSC на компьютере Linux](https://github.com/Microsoft/PowerShell-DSC-for-Linux#performing-dsc-operations-from-the-linux-computer).

## <a name="next-steps"></a>Дальнейшие действия

- Справочник по командлетам PowerShell см. в документации по [Az.Automation](https://docs.microsoft.com/powershell/module/az.automation/?view=azps-3.7.0#automation).
- Пример использования службы State Configuration в службе автоматизации Azure в конвейере непрерывного развертывания см. в разделе [Настройка непрерывного развертывания с помощью Chocolatey](automation-dsc-cd-chocolatey.md).