---
title: Исправлять несовместимые серверы конфигурации состояния службы автоматизации Azure
description: Повторное применение конфигураций по запросу к серверам, на которых изменилось состояние конфигурации
services: automation
ms.service: automation
ms.subservice: dsc
author: mgreenegit
ms.author: migreene
ms.topic: conceptual
ms.date: 07/17/2019
manager: nirb
ms.openlocfilehash: a6c7639cb4988eb13dfaa1c151085cda6e53b5d3
ms.sourcegitcommit: 6cff17b02b65388ac90ef3757bf04c6d8ed3db03
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/29/2019
ms.locfileid: "68614502"
---
# <a name="remediate-non-compliant-dsc-servers"></a>Исправление несоответствующих серверов DSC

Когда серверы регистрируются в конфигурации состояния службы автоматизации Azure, для параметра "режим конфигурации" устанавливается значение ApplyOnly, ApplyandMonitor или ApplyAndAutoCorrect.
Если режим автозамены не установлен, серверы, которые отменяют состояние соответствия по любой причине, останутся несоответствующими до тех пор, пока они не будут исправлены вручную.

Azure COMPUTE предлагает функцию с именем Run, которая позволяет пользователям выполнять сценарии в виртуальных машинах.
В этом документе приведены примеры сценариев для этой функции при ручном исправлении изменения конфигурации.

## <a name="correct-drift-of-windows-virtual-machines-using-powershell"></a>Правильное смещение виртуальных машин Windows с помощью PowerShell

Пошаговые инструкции по использованию функции "выполнить команду" на виртуальных машинах Windows см. на странице документации [Запуск сценариев PowerShell в виртуальной машине Windows с помощью команды Run](/azure/virtual-machines/windows/run-command).

Чтобы принудительно загрузить последнюю конфигурацию узла конфигурации состояния службы автоматизации Azure и применить его, используйте `Update-DscConfiguration` командлет.
Дополнительные сведения см. в документации по командлету [Update-DscConfiguration](/powershell/module/psdesiredstateconfiguration/update-dscconfiguration).

```powershell
Update-DscConfiguration -Wait -Verbose
```

## <a name="correct-drift-of-linux-virtual-machines"></a>Правильное смещение виртуальных машин Linux

Аналогичные функции в настоящее время недоступны для серверов Linux.
Единственный вариант — повторить процесс регистрации.
Для узлов Azure можно выполнить исправление с использованием портала или команду AZ Automation командлетов.
Сведения об этом процессе описаны на страницах адаптации страниц [для управления с помощью конфигурации состояния службы автоматизации Azure](/azure/automation/automation-dsc-onboarding#azure-portal).
Для гибридных узлов исправление отклонения можно выполнить с помощью входящих в них скриптов Python.
См. документацию в [репозитории POWERSHELL DSC для Linux](https://github.com/Microsoft/PowerShell-DSC-for-Linux#performing-dsc-operations-from-the-linux-computer).

## <a name="next-steps"></a>Следующие шаги

- Справочник по командлетам PowerShell для службы "Настройка состояния службы автоматизации Azure" см. в [этой статье](/powershell/module/azurerm.automation/#automation).
- Пример использования службы "Настройка состояния службы автоматизации Azure" и Chocolatey в конвейере непрерывного развертывания см. в [этой статье](automation-dsc-cd-chocolatey.md).
