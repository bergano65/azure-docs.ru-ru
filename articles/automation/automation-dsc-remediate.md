---
title: Исправление несовместимых серверов конфигурации состояния автоматизации Azure
description: Как повторно применить конфигурации по требованию к серверам, где состояние конфигурации дрейфовало
services: automation
ms.service: automation
ms.subservice: dsc
author: mgreenegit
ms.author: migreene
ms.topic: conceptual
ms.date: 07/17/2019
manager: nirb
ms.openlocfilehash: dfe62c54bfb10d70f1dbf19daec90eec68e66431
ms.sourcegitcommit: ea006cd8e62888271b2601d5ed4ec78fb40e8427
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/14/2020
ms.locfileid: "81383157"
---
# <a name="remediate-noncompliant-dsc-servers"></a>Исправление несовместимых серверов DSC

Когда серверы зарегистрированы в Azure Automation State `ApplyOnly`Configuration, режим конфигурации устанавливается на `ApplyandMonitor`режим настройки, или `ApplyAndAutoCorrect`. Если режим не `ApplyAndAutoCorrect`установлен, серверы, которые по какой-либо причине выходят из состояния совместимого, остаются несовместимыми до тех пор, пока они не будут исправлены вручную.

Azure compute предлагает функцию под названием Run Command, которая позволяет клиентам запускать скрипты внутри виртуальных машин.
Этот документ предоставляет примеры скриптов для этой функции при ручной коррекции дрейфа конфигурации.

## <a name="correct-drift-of-windows-virtual-machines-using-powershell"></a>Правильный дрейф виртуальных компьютеров Windows с помощью PowerShell

Для пошаговых инструкций с помощью функции Run Command на виртуальных компьютерах Windows смотрите страницу документации [Run PowerShell в вашем Windows VM с командой Run.](/azure/virtual-machines/windows/run-command)

Чтобы заставить узел состояния автоматизации Azure загрузить последнюю конфигурацию и применить ее, используйте cmdlet [Update-DscConfiguration.](/powershell/module/psdesiredstateconfiguration/update-dscconfiguration)

```powershell
Update-DscConfiguration -Wait -Verbose
```

## <a name="correct-drift-of-linux-virtual-machines"></a>Правильный дрейф виртуальных машин Linux

Аналогичная функциональность в настоящее время не доступна для серверов Linux.
Единственным вариантом является повторение процесса регистрации.
Для узлов Azure можно исправить дрейф с портала Azure или с помощью cmdlets модуля Az. Подробная информация об этом процессе задокументирована в [бортовых машинах для управления Azure Automation State Configuration.](automation-dsc-onboarding.md#onboard-a-vm-using-azure-portal)
Для гибридных узлов можно исправить дрейф с помощью включенных скриптов Python.
Смотрите [PowerShell DSC для репо Linux](https://github.com/Microsoft/PowerShell-DSC-for-Linux#performing-dsc-operations-from-the-linux-computer).

## <a name="next-steps"></a>Следующие шаги

- Для справки PowerShell cmdlet [см.](https://docs.microsoft.com/powershell/module/az.automation/?view=azps-3.7.0#automation
)
- Чтобы увидеть пример использования конфигурации состояния автоматизации Azure в непрерывном конвейере развертывания, [см.](automation-dsc-cd-chocolatey.md)