---
title: Типы модулей Runbook в службе автоматизации Azure
description: Описание различных типов модулей Runbook, которые можно использовать в службе автоматизации Azure, и рекомендации по выбору типа модуля.
services: automation
ms.subservice: process-automation
ms.date: 03/05/2019
ms.topic: conceptual
ms.openlocfilehash: 10f9c829207dc17fa39711e273ae4fbfab3ecbcd
ms.sourcegitcommit: ae3d707f1fe68ba5d7d206be1ca82958f12751e8
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/10/2020
ms.locfileid: "81010177"
---
# <a name="azure-automation-runbook-types"></a>Типы модулей Runbook в службе автоматизации Azure

Служба автоматизации процессов Azure поддерживает несколько типов рунбуков. Типы кратко определены в следующей таблице и описаны более подробно в разделах ниже. Чтобы узнать об среде [Runbook execution in Azure Automation](automation-runbook-execution.md)автоматизации процессов, см.

| Тип | Описание |
|:--- |:--- |
| [Графический](#graphical-runbooks)|Графический рулет, основанный на Windows PowerShell и созданный и полностью отредактированный в графическом редакторе портала Azure. |
| [Графический модуль рабочего процесса PowerShell](#graphical-runbooks)|Графический рулет, основанный на рабочем процессе Windows PowerShell, создан и полностью отредактирован в графическом редакторе портала Azure. |
| [PowerShell](#powershell-runbooks) |Текстовая книга, основанная на скриптах Windows PowerShell. |
| [Рабочий процесс PowerShell](#powershell-workflow-runbooks)|Текстовая книга, основанная на скриптах рабочего процесса Windows PowerShell. |
| [Python](#python-runbooks) |Текстовая книга, основанная на сценарие Python. |

## <a name="graphical-runbooks"></a>Графические модули Runbook

Вы можете создавать и редотировать графические и графические учебники PowerShell Workflow с помощью графического редактора на портале Azure. Тем не менее, вы не можете создать или отобразить этот тип runbook с другим инструментом.

Графический runbook имеет следующие основные характеристики:

* Можно экспортировать в файл в учетной записи Автоматизации, а затем импортировать в другую учетную запись Автоматизация. 
* Генерирует код PowerShell. 
* Может быть преобразован в или из графического PowerShell Workflow runbook во время импорта. 

### <a name="advantages"></a>Преимущества

* Использует визуальную модель авторства вставки-ссылки.
* Основное внимание уделяется потокам данных в процессе.
* Визуально представляет процессы управления.
* Включает в себя другие runbooks в качестве детских runbooks для создания рабочих процессов высокого уровня.
* Поощряет модульное программирование.

### <a name="limitations"></a>Ограничения

* Нельзя создавать или редактировать за пределами портала Azure.
* Для выполнения сложной логики может потребоваться действие кода, содержащего код PowerShell.
* Не может быть преобразован в один из [текстовых форматов,](automation-runbook-types.md)и не может учебник быть преобразован в графический формат. 
* Не позволяет просматривать или непосредственно отодывать код PowerShell, который создает графический рабочий процесс. Вы можете просматривать создаваемый код в любых действиях по коду.
* Не работает на Linux Hybrid Runbook worker. Просматривайте [ресурсы автоматизации в центре обработки данных или в облаке с помощью Hybrid Runbook Worker.](automation-hybrid-runbook-worker.md)

## <a name="powershell-runbooks"></a>Модули Runbook PowerShell

Модули Runbook PowerShell используют Windows PowerShell. Код модуля Runbook можно редактировать в текстовом редакторе на портале Azure.  Кроме того, можно использовать любой автономный текстовый редактор и [импортировать Runbook](manage-runbooks.md) в службу автоматизации Azure.

### <a name="advantages"></a>Преимущества

* Реализация сложной логики с помощью кода рабочего процесса PowerShell без дополнительного усложнения рабочего процесса PowerShell.
* Модуль Runbook запускается быстрее, чем модули рабочего процесса PowerShell, поскольку не требуют предварительной компиляции.
* Может работать в Azure или как на Linux, так и на Windows Hybrid Runbook Workers

### <a name="limitations"></a>Ограничения

* Необходимо знание сценариев PowerShell.
* Вам не удастся использовать [параллельную обработку](automation-powershell-workflow.md#parallel-processing) для одновременного выполнения нескольких действий.
* Вам не удастся использовать [контрольные точки](automation-powershell-workflow.md#checkpoints) для возобновления Runbook в случае ошибки.
* Runbook рабочих процессов PowerShell и графические Runbook можно включать в качестве дочерних Runbook с помощью командлета Start-AzureAutomationRunbook, который создает задание.

### <a name="known-issues"></a>Известные проблемы

Ниже перечислены проблемы с модулями Runbook PowerShell, известные на данный момент.

* Runbooks PowerShell не может получить незашифрованный [переменный актив](automation-variables.md) с нулевым значением.
* Модули Runbook PowerShell не имеют возможности извлекать [ресурс переменной](automation-variables.md), в имени которого есть символ *~*.
* Модуль Get-Process в цикле в модуле Runbook PowerShell может аварийно завершить работу после примерно 80 итераций.
* Модуль Runbook PowerShell может завершиться ошибкой, если попытается записать слишком большой объем данных в поток вывода за один раз.   Обычно эту проблему можно обойти, выводя при работе с большими объектами только необходимые данные.  Например, вместо использования метода *Get-Process* можно вывести только требуемые поля, указав *Get-Process | Select ProcessName, CPU*.

## <a name="powershell-workflow-runbooks"></a>Модули Runbook рабочих процессов PowerShell

Runbook рабочих процессов PowerShell представляют собой текстовые Runbook, основанные на [рабочем процессе Windows PowerShell](automation-powershell-workflow.md).  Код модуля Runbook можно редактировать в текстовом редакторе на портале Azure.  Кроме того, можно использовать любой автономный текстовый редактор и [импортировать Runbook](manage-runbooks.md) в службу автоматизации Azure.

### <a name="advantages"></a>Преимущества

* Реализация сложной логики с помощью кода рабочего процесса PowerShell.
* Использование [контрольных точек](automation-powershell-workflow.md#checkpoints) для возобновления модулей Runbook в случае ошибки.
* Использование [параллельной обработки](automation-powershell-workflow.md#parallel-processing) для одновременного выполнения нескольких действий.
* Возможность использования других графических модулей Runbook и модулей Runbook рабочих процессов PowerShell в качестве дочерних для создания высокоуровневых рабочих процессов.

### <a name="limitations"></a>Ограничения

* Автор должен знать рабочий процесс PowerShell.
* Runbook должен справляться с усложнениями рабочего процесса PowerShell, например с [десериализованными объектами](automation-powershell-workflow.md#code-changes).
* Модуль Runbook запускается дольше, чем модули Runbook PowerShell, поскольку требует предварительной компиляции.
* Runbook PowerShell можно включать только как дочерние Runbook с помощью командлета Start-AzureAutomationRunbook, который создает задание.
* Не утихнет на Linux Hybrid Runbook Worker.

## <a name="python-runbooks"></a>Модули Runbook Python

Модули Runbook Python компилируются в среде Python 2. Вы можете отредактировать код модуля Runbook с помощью текстового редактора на портале Azure или автономного текстового редактора, а затем [импортировать модуль Runbook](manage-runbooks.md) в службу автоматизации Azure.

### <a name="advantages"></a>Преимущества

* Вы можете использовать надежные библиотеки Python.
* Может работать в Azure или на обоих Linux Hybrid Runbook работников. Windows Hybrid Runbook Работников поддерживаются с [python2.7](https://www.python.org/downloads/release/latest/python2) установлен.

### <a name="limitations"></a>Ограничения

* Требуется знание скриптов Python.
* Сейчас поддерживается только версия Python 2. Это значит, что определенные функции Python 3 завершатся ошибкой.
* Для использования сторонних библиотек необходимо [импортировать пакет](python-packages.md) в учетную запись Автоматизации для его использования.

## <a name="considerations"></a>Рекомендации

Выбирая тип модуля Runbook, принимайте во внимание следующие рекомендации.

* Вы не можете преобразовать модули Runbook из графического типа в текстовый или наоборот.
* При использовании других типов дочерних модулей Runbook могут действовать некоторые ограничения. Дополнительные сведения см. в статье [Дочерние модули Runbook в службе автоматизации Azure](automation-child-runbooks.md).

## <a name="next-steps"></a>Следующие шаги

* Дополнительные сведения о графической разработке модулей Runbook см. в статье [Графическая разработка в службе автоматизации Azure](automation-graphical-authoring-intro.md).
* Чтобы понять различия между PowerShell и рабочими процессами PowerShell для модулей Runbook, см. статью [Изучение рабочего процесса Windows PowerShell](automation-powershell-workflow.md).
* Дополнительные сведения о создании и импорте модуля Runbook см. в статье [Создание или импорт модуля Runbook в службе автоматизации Azure](manage-runbooks.md).
* Для получения дополнительной информации о PowerShell, включая языковые справочные и учебные модули, обратитесь к [документам PowerShell](https://docs.microsoft.com/powershell/scripting/overview).
