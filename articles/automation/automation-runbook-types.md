---
title: Типы модулей Runbook в службе автоматизации Azure
description: Описывает различные типы runbooks, которые можно использовать в Автоматизации Azure, и соображения для определения типа для использования.
services: automation
ms.subservice: process-automation
ms.date: 03/05/2019
ms.topic: conceptual
ms.openlocfilehash: 1ac6347bd8e723f356da4803da54a6ea45a4a71a
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81535525"
---
# <a name="azure-automation-runbook-types"></a>Типы модулей Runbook в службе автоматизации Azure

Служба автоматизации процессов Azure Automation поддерживает несколько типов рунбуков, как это определено в следующей таблице. Чтобы узнать об среде [Runbook execution in Azure Automation](automation-runbook-execution.md)автоматизации процессов, см.

| Тип | Описание |
|:--- |:--- |
| [Графический](#graphical-runbooks)|Графический рулет, основанный на Windows PowerShell и созданный и полностью отредактированный в графическом редакторе портала Azure. |
| [Графический модуль рабочего процесса PowerShell](#graphical-runbooks)|Графический рулет, основанный на рабочем процессе Windows PowerShell, создан и полностью отредактирован в графическом редакторе портала Azure. |
| [PowerShell](#powershell-runbooks) |Текстовая книга, основанная на скриптах Windows PowerShell. |
| [Рабочий процесс PowerShell](#powershell-workflow-runbooks)|Текстовая книга, основанная на скриптах рабочего процесса Windows PowerShell. |
| [Python](#python-runbooks) |Текстовая книга, основанная на сценарие Python. |

Примите во внимание следующие соображения при определении того, какой тип использовать для конкретного runbook.

* Вы не можете преобразовать runbooks из графических в текстовый тип, или наоборот.
* Существуют ограничения при использовании runbooks различных типов, как детские книги. Дополнительные сведения см. в статье [Дочерние модули Runbook в службе автоматизации Azure](automation-child-runbooks.md).

>[!NOTE]
>Эта статья была изменена и теперь содержит сведения о новом модуле Az для Azure PowerShell. Вы по-прежнему можете использовать модуль AzureRM, исправления ошибок для которого будут продолжать выпускаться как минимум до декабря 2020 г. Дополнительные сведения о совместимости модуля Az с AzureRM см. в статье [Introducing the new Azure PowerShell Az module](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0) (Знакомство с новым модулем Az для Azure PowerShell). Для инструкций по установке модуля Az на гибридном Runbook Worker [см.](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0) Для учетной записи Автоматизация вы можете обновить свои модули до последней версии, используя [как обновить модули Azure PowerShell в Azure Automation.](automation-update-azure-modules.md)

## <a name="graphical-runbooks"></a>Графические модули Runbook

Вы можете создавать и редотировать графические и графические учебники PowerShell Workflow с помощью графического редактора на портале Azure. Тем не менее, вы не можете создать или отобразить этот тип runbook с другим инструментом. Основные особенности графических рун:

* Можно экспортировать в файлы в учетной записи Автоматизации, а затем импортировать в другую учетную запись Автоматизация. 
* Создайте код PowerShell. 
* Может быть преобразован в или из графических PowerShell Workflow runbooks во время импорта. 

### <a name="advantages"></a>Преимущества

* Используйте визуальную модель авторства вставки-ссылки.
* Сосредоточьтесь на том, как потоки данных в процессе.
* Визуальное представление процессов управления.
* Включите другие сборники в число детских runbooks для создания рабочих процессов высокого уровня.
* Поощряйте модульное программирование.

### <a name="limitations"></a>Ограничения

* Не успомяно создать или отсвазать за пределами портала Azure.
* Для выполнения сложной логики может потребоваться действие кода, содержащего код PowerShell.
* Не устоияните к одному из [форматов текста,](automation-runbook-types.md)и вы не можете преобразовать текст runbook к графическому формату. 
* Не уможет просматривать или непосредственно отодрять код PowerShell, который создает графический рабочий процесс. Вы можете просматривать создаваемый код в любых действиях по коду.
* Не утихнет запуск овечку на Linux Hybrid Runbook Worker. Просматривайте [ресурсы автоматизации в центре обработки данных или в облаке с помощью Hybrid Runbook Worker.](automation-hybrid-runbook-worker.md)

## <a name="powershell-runbooks"></a>Модули Runbook PowerShell

Модули Runbook PowerShell используют Windows PowerShell. Код модуля Runbook можно редактировать в текстовом редакторе на портале Azure.  Кроме того, можно использовать любой автономный текстовый редактор и [импортировать Runbook](manage-runbooks.md) в службу автоматизации Azure.

### <a name="advantages"></a>Преимущества

* Реализация сложной логики с помощью кода рабочего процесса PowerShell без дополнительного усложнения рабочего процесса PowerShell.
* Запуск работает быстрее, чем runbooks PowerShell Workflow, так как они не должны быть составлены перед запуском.
* Запуск в Azure и на гибридных Runbook работников для Windows и Linux.

### <a name="limitations"></a>Ограничения

* Вы должны быть знакомы со сценарием PowerShell.
* Runbooks не может использовать [параллельную обработку](automation-powershell-workflow.md#parallel-processing) для выполнения нескольких действий параллельно.
* Runbooks не может использовать [контрольно-пропускные пункты](automation-powershell-workflow.md#checkpoints) для возобновления runbook, если есть ошибка.
* Вы можете включить только runbooks Workflow PowerShell и графические runbooks как детские runbooks с помощью [cmdlet Start-AzAutomationRunbook,](https://docs.microsoft.com/powershell/module/az.automation/start-azautomationrunbook?view=azps-3.7.0) который создает новую работу.

### <a name="known-issues"></a>Известные проблемы

Ниже приведены текущие известные проблемы с PowerShell runbooks:

* Runbooks PowerShell не может получить незашифрованный [переменный актив](automation-variables.md) с нулевым значением.
* Модули Runbook PowerShell не имеют возможности извлекать ресурс переменной, в имени которого есть символ `*~*`.
* Операция [Get-Process](https://docs.microsoft.com/powershell/module/microsoft.powershell.management/get-process?view=powershell-7) в цикле в runbook PowerShell может выйти из строя примерно после 80 итераций.
* Запуск PowerShell может выйти из строя, если он попытается написать большой объем данных в поток вывода сразу. Обычно можно обойти эту проблему, имея выход runbook только на информацию, необходимую для работы с крупными объектами. Например, вместо `Get-Process` использования без ограничений, вы можете иметь выход cmdlet `Get-Process | Select ProcessName, CPU`только необходимые параметры, как в .

## <a name="powershell-workflow-runbooks"></a>Модули Runbook рабочих процессов PowerShell

Runbook рабочих процессов PowerShell представляют собой текстовые Runbook, основанные на [рабочем процессе Windows PowerShell](automation-powershell-workflow.md). Код модуля Runbook можно редактировать в текстовом редакторе на портале Azure. Кроме того, можно использовать любой автономный текстовый редактор и [импортировать Runbook](manage-runbooks.md) в службу автоматизации Azure.

### <a name="advantages"></a>Преимущества

* Реализация сложной логики с помощью кода рабочего процесса PowerShell.
* Используйте [контрольно-пропускные пункты](automation-powershell-workflow.md#checkpoints) для возобновления работы при наличии ошибки.
* Используйте [параллельную обработку](automation-powershell-workflow.md#parallel-processing) для параллельного действия нескольких действий.
* Может включать в себя другие графические runbooks и PowerShell Workflow runbooks в качестве детских runbooks для создания рабочих процессов высокого уровня.

### <a name="limitations"></a>Ограничения

* Вы должны быть знакомы с рабочим процессом PowerShell.
* Runbooks должны иметь дело с дополнительной сложностью рабочего процесса PowerShell, например, [с десериализованными объектами.](automation-powershell-workflow.md#code-changes)
* Запуск runbooks занимает больше времени, чем запускpower PowerShell, так как они должны быть составлены перед запуском.
* Вы можете включить в число runbooks PowerShell `Start-AzAutomationRunbook` только в качестве детских книг с помощью cmdlet.
* Runbooks не может работать на Linux Гибридный Runbook работника.

## <a name="python-runbooks"></a>Модули Runbook Python

Модули Runbook Python компилируются в среде Python 2. Вы можете непосредственно отсеивать код runbook с помощью текстового редактора на портале Azure. Вы также можете использовать автономный текстовый редактор и [импортировать книгу в](manage-runbooks.md) Azure Automation.

### <a name="advantages"></a>Преимущества

* Используйте надежные библиотеки Python.
* Может работать в Azure или на Linux Hybrid Runbook workers. Windows Hybrid Runbook Работников поддерживаются с [python2.7](https://www.python.org/downloads/release/latest/python2) установлен.

### <a name="limitations"></a>Ограничения

* Вы должны быть знакомы со сценарием Python.
* Только Python 2 поддерживается в настоящее время. Любые функции Python 3-специфические сбой.
* Для использования сторонних библиотек необходимо [импортировать пакеты](python-packages.md) в учетную запись Automation.

## <a name="next-steps"></a>Следующие шаги

* Чтобы узнать больше об авторе графического runbook, смотрите [графическое авторство в Azure Automation](automation-graphical-authoring-intro.md).
* Чтобы понять различия между рабочими процессами PowerShell [Learning Windows PowerShell Workflow](automation-powershell-workflow.md)и PowerShell для runbooks, см.
* Для получения дополнительной информации о том, как создать или импортировать runbook, [см.](manage-runbooks.md)
* Чтобы узнать больше о PowerShell, включая языковые ссылки и учебные модули, обратитесь к [Документам PowerShell](https://docs.microsoft.com/powershell/scripting/overview).
