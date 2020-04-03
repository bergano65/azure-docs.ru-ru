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
ms.openlocfilehash: f4ca76f4be9d00e185f8774fc33296d1af1aeece
ms.sourcegitcommit: 3c318f6c2a46e0d062a725d88cc8eb2d3fa2f96a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/02/2020
ms.locfileid: "80585502"
---
# <a name="remediate-non-compliant-dsc-servers"></a>Исправление несоответствующих серверов DSC

Когда серверы зарегистрированы в конфигурации Azure Automation State Configuration, "Режим конфигурации" устанавливается для ApplyOnly, ApplyandMonitor или ApplyAndAutoCorrect.
Если режим не настроен на AutoCorrect, серверы, которые по какой-либо причине будут выходить из состояния совместимого по какой-либо причине, будут оставаться несовместимыми до тех пор, пока они не будут исправлены вручную.

Azure compute предлагает функцию под названием Run Command, которая позволяет клиентам запускать скрипты внутри виртуальных машин.
Этот документ предоставляет примеры скриптов для этой функции при ручной коррекции дрейфа конфигурации.

## <a name="correct-drift-of-windows-virtual-machines-using-powershell"></a>Правильный дрейф виртуальных компьютеров Windows с помощью PowerShell

Для инструкций шаг за шагом с помощью функции Run Command на виртуальных компьютерах Windows смотрите страницу документации [Run PowerShell скриптов в Windows VM с командой Run.](/azure/virtual-machines/windows/run-command)

Чтобы заставить узел состояния автоматизации Azure загрузить последнюю `Update-DscConfiguration` конфигурацию и применить ее, используйте cmdlet.
Для получения подробной информации, см cmdlet документации [Обновление-DscConfiguration](/powershell/module/psdesiredstateconfiguration/update-dscconfiguration).

```powershell
Update-DscConfiguration -Wait -Verbose
```

## <a name="correct-drift-of-linux-virtual-machines"></a>Правильный дрейф виртуальных машин Linux

Аналогичная функциональность в настоящее время не доступна для серверов Linux.
Единственным вариантом является повторение процесса регистрации.
Для узлов Azure корректировка дрейфа может быть сделана с портала или с помощью cmdlets Az Automation.
Подробная информация об этом процессе задокументирована на странице [Бортовых машин для управления Azure Automation State Configuration.](/azure/automation/automation-dsc-onboarding#onboard-a-vm-using-azure-portal)
Для гибридных узлов корректировка дрейфа может быть осуществлена с помощью включенных скриптов Python.
Смотрите документацию в [PowerShell DSC для репо Linux](https://github.com/Microsoft/PowerShell-DSC-for-Linux#performing-dsc-operations-from-the-linux-computer).

## <a name="next-steps"></a>Следующие шаги

- Справочник по командлетам PowerShell для службы "Настройка состояния службы автоматизации Azure" см. в [этой статье](/powershell/module/azurerm.automation/#automation).
- Пример использования службы "Настройка состояния службы автоматизации Azure" и Chocolatey в конвейере непрерывного развертывания см. в [этой статье](automation-dsc-cd-chocolatey.md).
