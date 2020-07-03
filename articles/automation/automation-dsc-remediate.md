---
title: Исправлять несоответствующие требованиям серверы конфигурации состояния службы автоматизации Azure
description: Повторное применение конфигураций по запросу к серверам, на которых изменилось состояние конфигурации
services: automation
ms.service: automation
ms.subservice: dsc
author: mgreenegit
ms.author: migreene
ms.topic: conceptual
ms.date: 07/17/2019
manager: nirb
ms.openlocfilehash: f871b406793e455c857ca14c83434c9ed3e004df
ms.sourcegitcommit: 309a9d26f94ab775673fd4c9a0ffc6caa571f598
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/09/2020
ms.locfileid: "82993843"
---
# <a name="remediate-noncompliant-dsc-servers"></a>Исправлять несоответствующие требованиям серверы DSC

Когда серверы регистрируются в конфигурации состояния службы автоматизации Azure, для `ApplyOnly`режима конфигурации устанавливается значение, `ApplyandMonitor`или. `ApplyAndAutoCorrect` Если для режима не задано `ApplyAndAutoCorrect`значение, серверы, которые отменяют состояние соответствия по любой причине, остаются несоответствующими до тех пор, пока они не будут исправлены вручную.

Azure COMPUTE предлагает функцию с именем Run, которая позволяет пользователям выполнять сценарии в виртуальных машинах.
В этом документе приведены примеры сценариев для этой функции при ручном исправлении изменения конфигурации.

## <a name="correct-drift-of-windows-virtual-machines-using-powershell"></a>Правильное смещение виртуальных машин Windows с помощью PowerShell

Пошаговые инструкции по использованию функции выполнить команду на виртуальных машинах Windows см. на странице документации [Запуск сценариев PowerShell в виртуальной машине Windows с помощью команды Run](/azure/virtual-machines/windows/run-command).

Чтобы принудительно загрузить последнюю конфигурацию узла конфигурации состояния службы автоматизации Azure и применить его, используйте командлет [Update-DscConfiguration](/powershell/module/psdesiredstateconfiguration/update-dscconfiguration) .

```powershell
Update-DscConfiguration -Wait -Verbose
```

## <a name="correct-drift-of-linux-virtual-machines"></a>Правильное смещение виртуальных машин Linux

Аналогичные функции в настоящее время недоступны для серверов Linux.
Единственный вариант — повторить процесс регистрации.
Для узлов Azure можно исправить смещение от портал Azure или с помощью команды AZ Module командлетов. Сведения об этом процессе описаны в статье подключение [компьютеров для управления с помощью конфигурации состояния службы автоматизации Azure](automation-dsc-onboarding.md#enable-a-vm-using-azure-portal).
Для гибридных узлов можно исправить смещение с помощью прилагаемых скриптов Python.
См. сведения [о репозитории POWERSHELL DSC для Linux](https://github.com/Microsoft/PowerShell-DSC-for-Linux#performing-dsc-operations-from-the-linux-computer).

## <a name="next-steps"></a>Дальнейшие действия

- Справочник по командлетам PowerShell см. в документации по [Az.Automation](https://docs.microsoft.com/powershell/module/az.automation/?view=azps-3.7.0#automation
).
- Пример использования конфигурации состояния службы автоматизации Azure в конвейере непрерывного развертывания см. в статье [непрерывное развертывание с помощью конфигурации состояния службы автоматизации Azure и шоколадного](automation-dsc-cd-chocolatey.md)развертывания.