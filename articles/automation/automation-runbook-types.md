---
title: Типы модулей Runbook в службе автоматизации Azure
description: Описание различных типов модулей Runbook, которые можно использовать в службе автоматизации Azure, и рекомендации по определению используемого типа.
services: automation
ms.subservice: process-automation
ms.date: 03/05/2019
ms.topic: conceptual
ms.openlocfilehash: 1ac6347bd8e723f356da4803da54a6ea45a4a71a
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2020
ms.locfileid: "81535525"
---
# <a name="azure-automation-runbook-types"></a>Типы модулей Runbook в службе автоматизации Azure

Служба автоматизации процессов службы автоматизации Azure поддерживает несколько типов модулей Runbook, как определено в следующей таблице. Дополнительные сведения о среде автоматизации процессов см. [в разделе Выполнение Runbook в службе автоматизации Azure](automation-runbook-execution.md).

| Type | Описание |
|:--- |:--- |
| [Графический](#graphical-runbooks)|Графический модуль Runbook на основе Windows PowerShell, который полностью создан и отредактирован в графическом редакторе портал Azure. |
| [Графический модуль рабочего процесса PowerShell](#graphical-runbooks)|Графический модуль Runbook на основе рабочего процесса Windows PowerShell, который полностью создан и отредактирован в графическом редакторе портал Azure. |
| [PowerShell](#powershell-runbooks) |Текстовый модуль Runbook на основе сценариев Windows PowerShell. |
| [Рабочий процесс PowerShell](#powershell-workflow-runbooks)|Текстовый модуль Runbook на основе сценариев рабочего процесса Windows PowerShell. |
| [Python](#python-runbooks) |Текстовый модуль Runbook на основе сценариев Python. |

При определении типа, используемого для конкретного модуля Runbook, учитывайте следующие моменты.

* Вы не можете преобразовывать модули Runbook из графического в текстовый тип или наоборот.
* При использовании модулей Runbook различных типов в качестве дочерних модулей Runbook существуют ограничения. Дополнительные сведения см. в статье [Дочерние модули Runbook в службе автоматизации Azure](automation-child-runbooks.md).

>[!NOTE]
>Эта статья была изменена и теперь содержит сведения о новом модуле Az для Azure PowerShell. Вы по-прежнему можете использовать модуль AzureRM, исправления ошибок для которого будут продолжать выпускаться как минимум до декабря 2020 г. Дополнительные сведения о совместимости модуля Az с AzureRM см. в статье [Introducing the new Azure PowerShell Az module](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0) (Знакомство с новым модулем Az для Azure PowerShell). Инструкции по установке модуля Az в гибридной рабочей роли Runbook см. в статье об [установке модуля Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0). Чтобы обновить модули в учетной записи службы автоматизации, см. руководство по [обновлению модулей Azure PowerShell в службе автоматизации Azure](automation-update-azure-modules.md).

## <a name="graphical-runbooks"></a>Графические модули Runbook

Вы можете создавать и редактировать графические модули runbook и графические модули runbook рабочего процесса PowerShell с помощью графического редактора на портале Azure. Однако вы не можете создать или изменить этот тип Runbook с помощью другого средства. Основные возможности графических модулей Runbook:

* Можно экспортировать в файлы в учетной записи службы автоматизации, а затем импортировать в другую учетную запись службы автоматизации. 
* Создание кода PowerShell. 
* Можно преобразовать в графические модули Runbook рабочего процесса PowerShell или из него во время импорта. 

### <a name="advantages"></a>Преимущества

* Используйте визуальную модель разработки для вставки и настройки.
* Сосредоточьтесь на том, как данные проходят через процесс.
* Визуальное представление процессов управления.
* Включите другие модули Runbook в качестве дочерних модулей Runbook для создания рабочих процессов высокого уровня.
* Поощрение модульного программирования.

### <a name="limitations"></a>Ограничения

* Не может создавать или изменять вне портал Azure.
* Может потребоваться действие Code, содержащее код PowerShell для выполнения сложной логики.
* Невозможно преобразовать в один из [текстовых форматов](automation-runbook-types.md), а также преобразовать текстовый модуль Runbook в графический формат. 
* Не может просматривать или напрямую редактировать код PowerShell, создаваемый графическим рабочим процессом. Вы можете просмотреть код, создаваемый при любых действиях с кодом.
* Не удается запустить модули Runbook в гибридной рабочей роли Runbook Linux. См. статью [Автоматизация ресурсов в центре обработки данных или облаке с помощью гибридной рабочей роли Runbook](automation-hybrid-runbook-worker.md).

## <a name="powershell-runbooks"></a>Модули Runbook PowerShell

Модули Runbook PowerShell используют Windows PowerShell. Код модуля Runbook можно редактировать в текстовом редакторе на портале Azure.  Кроме того, можно использовать любой автономный текстовый редактор и [импортировать Runbook](manage-runbooks.md) в службу автоматизации Azure.

### <a name="advantages"></a>Преимущества

* Реализация сложной логики с помощью кода рабочего процесса PowerShell без дополнительного усложнения рабочего процесса PowerShell.
* Запускаются быстрее, чем модули Runbook рабочего процесса PowerShell, так как их не нужно компилировать перед запуском.
* Запустите в Azure и гибридных рабочих ролях Runbook для Windows и Linux.

### <a name="limitations"></a>Ограничения

* Вы должны быть знакомы с сценариями PowerShell.
* Модули Runbook не могут использовать [параллельную обработку](automation-powershell-workflow.md#parallel-processing) для параллельного выполнения нескольких действий.
* Модули Runbook не могут использовать [контрольные точки](automation-powershell-workflow.md#checkpoints) для возобновления работы модуля Runbook при возникновении ошибки.
* В качестве дочерних модулей Runbook можно включить только модули Runbook рабочего процесса PowerShell и графические модули Runbook с помощью командлета [Start-азаутоматионрунбук](https://docs.microsoft.com/powershell/module/az.automation/start-azautomationrunbook?view=azps-3.7.0) , который создает новое задание.

### <a name="known-issues"></a>Известные проблемы

Ниже приведены текущие известные проблемы с модулями Runbook PowerShell.

* Модули Runbook PowerShell не могут получить незашифрованный [ресурс переменной](automation-variables.md) со значением NULL.
* Модули Runbook PowerShell не имеют возможности извлекать ресурс переменной, в имени которого есть символ `*~*`.
* Операция [Get-Process](https://docs.microsoft.com/powershell/module/microsoft.powershell.management/get-process?view=powershell-7) в цикле в модуле Runbook PowerShell может аварийно завершить работу после 80 итераций.
* Модуль Runbook PowerShell может завершиться ошибкой, если он попытается записать большой объем данных в выходной поток одновременно. Обычно эту неполадку можно обойти, выполнив выходные данные Runbook только сведения, необходимые для работы с большими объектами. Например, вместо использования `Get-Process` без ограничений можно получить выходные данные командлета, только необходимые параметры, как в. `Get-Process | Select ProcessName, CPU`

## <a name="powershell-workflow-runbooks"></a>Модули Runbook рабочих процессов PowerShell

Модули Runbook рабочих процессов PowerShell — это текстовые модули Runbook, основанные на [рабочем процессе Windows PowerShell](automation-powershell-workflow.md). Код модуля Runbook можно редактировать в текстовом редакторе на портале Azure. Кроме того, можно использовать любой автономный текстовый редактор и [импортировать Runbook](manage-runbooks.md) в службу автоматизации Azure.

### <a name="advantages"></a>Преимущества

* Реализация сложной логики с помощью кода рабочего процесса PowerShell.
* Используйте [контрольные точки](automation-powershell-workflow.md#checkpoints) для возобновления операции при возникновении ошибки.
* Используйте [параллельную обработку](automation-powershell-workflow.md#parallel-processing) для параллельного выполнения нескольких действий.
* Может включать другие графические модули Runbook и модули Runbook рабочих процессов PowerShell в качестве дочерних модулей Runbook для создания рабочих процессов высокого уровня.

### <a name="limitations"></a>Ограничения

* Вы должны быть знакомы с рабочим процессом PowerShell.
* Модули Runbook должны работать с дополнительной сложностью рабочего процесса PowerShell, например [десериализованными объектами](automation-powershell-workflow.md#code-changes).
* Модули Runbook запускаются дольше, чем модули Runbook PowerShell, так как они должны быть скомпилированы перед запуском.
* Модули Runbook PowerShell можно включать в качестве дочерних модулей Runbook с помощью `Start-AzAutomationRunbook` командлета.
* Модули Runbook не могут выполняться в гибридной рабочей роли Runbook Linux.

## <a name="python-runbooks"></a>Модули Runbook Python

Модули Runbook Python компилируются в среде Python 2. Код модуля Runbook можно редактировать в текстовом редакторе на портале Azure. Можно также использовать автономный текстовый редактор и [импортировать модуль Runbook](manage-runbooks.md) в службу автоматизации Azure.

### <a name="advantages"></a>Преимущества

* Используйте надежные библиотеки Python.
* Может выполняться в Azure или в гибридных рабочих ролях Runbook для Linux. Гибридные рабочие роли Runbook Windows поддерживаются с установленным [Python 2.7](https://www.python.org/downloads/release/latest/python2) .

### <a name="limitations"></a>Ограничения

* Вы должны быть знакомы со сценариями Python.
* В настоящее время поддерживается только Python 2. Все функции, относящиеся к Python 3, завершаются ошибкой.
* Для использования сторонних библиотек необходимо [импортировать пакеты](python-packages.md) в учетную запись службы автоматизации.

## <a name="next-steps"></a>Дальнейшие шаги

* Дополнительные сведения о создании графических модулей Runbook см. [в статье графическая разработка в службе автоматизации Azure](automation-graphical-authoring-intro.md).
* Сведения о различиях между рабочими процессами PowerShell и PowerShell для модулей Runbook см. в статье [изучение рабочего процесса Windows PowerShell](automation-powershell-workflow.md).
* Дополнительные сведения о создании или импорте модуля Runbook см. в статье [Управление модулями Runbook в службе автоматизации Azure](manage-runbooks.md).
* Дополнительные сведения о PowerShell, включая Справочник по языку и обучающие модули, см. в документации по [PowerShell](https://docs.microsoft.com/powershell/scripting/overview).
