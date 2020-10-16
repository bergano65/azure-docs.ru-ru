---
title: Типы модулей Runbook в службе автоматизации Azure
description: В этой статье описаны различные типы модулей runbook, которые можно использовать в службе автоматизации Azure, и приведены рекомендации по выбору типа модуля.
services: automation
ms.subservice: process-automation
ms.date: 03/05/2019
ms.topic: conceptual
ms.openlocfilehash: 24d0123eecc56b56573e94d831283d8d360cd16e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/09/2020
ms.locfileid: "86185931"
---
# <a name="azure-automation-runbook-types"></a>Типы модулей Runbook в службе автоматизации Azure

Компонент автоматизации процессов службы автоматизации Azure поддерживает несколько типов модулей runbook, как описано в следующей таблице. Операционная среда автоматизации процессов описана в руководстве по [выполнению модуля runbook в службе автоматизации Azure](automation-runbook-execution.md).

| Тип | Описание |
|:--- |:--- |
| [Графический](#graphical-runbooks)|Графический модуль runbook, который основан на Windows PowerShell. Создать и изменить его можно в графическом редакторе на портале Azure. |
| [Графический модуль рабочего процесса PowerShell](#graphical-runbooks)|Графический модуль runbook, который основан на рабочем процессе Windows PowerShell. Создать и изменить его можно в графическом редакторе на портале Azure. |
| [PowerShell](#powershell-runbooks) |Текстовый модуль runbook, основанный на скриптах Windows PowerShell. |
| [Рабочий процесс PowerShell](#powershell-workflow-runbooks)|Текстовый модуль runbook, основанный на скриптах рабочего процесса Windows PowerShell. |
| [Python](#python-runbooks) |Текстовый модуль runbook, основанный на скриптах Python. |

Выбирая тип модуля runbook, принимайте во внимание следующие рекомендации.

* Runbook нельзя преобразовать из графического типа в текстовый или наоборот.
* При использовании других типов дочерних модулей runbook могут действовать некоторые ограничения. Дополнительные сведения см. в статье [Дочерние модули Runbook в службе автоматизации Azure](automation-child-runbooks.md).

## <a name="graphical-runbooks"></a>Графические модули Runbook

Вы можете создавать и редактировать графические модули runbook и графические модули runbook рабочего процесса PowerShell с помощью графического редактора на портале Azure. Однако вы не можете создать или изменить этот тип модуля runbook с помощью другого инструмента. Основные возможности графических модулей runbook:

* Можно экспортировать в файлы в учетной записи службы автоматизации, а затем импортировать в другую учетную запись службы автоматизации. 
* Создание кода PowerShell. 
* Во время импорта можно преобразовывать в модули runbook рабочего процесса PowerShell и наоборот. 

### <a name="advantages"></a>Преимущества

* Использование визуальной модели создания insert-link-configure (вставить, связать и настроить).
* Фокусировка на том, как происходит поток данных.
* Визуальное представление процессов управления.
* Включение других модулей runbook в качестве дочерних для создания высокоуровневых рабочих процессов.
* Удобство модульного программирования.

### <a name="limitations"></a>Ограничения

* Нельзя создавать или изменять вне портала Azure.
* Для выполнения сложной логики может потребоваться действие Code, содержащее код PowerShell.
* Нельзя преобразовать в один из [текстовых форматов](automation-runbook-types.md). Также нельзя преобразовать текстовый модуль runbook в графический формат. 
* Нет возможности отобразить или напрямую изменить код PowerShell, создаваемый графическим рабочим процессом. Вы можете просматривать код в любых действиях Code.
* Нельзя запустить runbook в гибридной рабочей роли Runbook для Linux. См. статью [Автоматизация ресурсов в центре обработки данных или в облаке с помощью использования гибридной рабочей роли Runbook](automation-hybrid-runbook-worker.md).

## <a name="powershell-runbooks"></a>Модули Runbook PowerShell

Модули Runbook PowerShell используют Windows PowerShell. Код модуля Runbook можно редактировать в текстовом редакторе на портале Azure.  Кроме того, можно использовать любой автономный текстовый редактор и [импортировать Runbook](manage-runbooks.md) в службу автоматизации Azure.

### <a name="advantages"></a>Преимущества

* Реализация сложной логики с помощью кода рабочего процесса PowerShell без дополнительного усложнения рабочего процесса PowerShell.
* Запускаются быстрее, чем модули runbook рабочего процесса PowerShell, так как не требуют предварительной компиляции.
* Работают в Azure и гибридных рабочих ролях Runbook для Windows и Linux.

### <a name="limitations"></a>Ограничения

* Необходимо знание скриптов PowerShell.
* Нельзя использовать [параллельную обработку](automation-powershell-workflow.md#use-parallel-processing) для одновременного выполнения нескольких действий.
* Модули runbook не могут использовать [контрольные точки](automation-powershell-workflow.md#use-checkpoints-in-a-workflow) для возобновления в случае ошибки.
* Модули runbook рабочих процессов PowerShell и графические модули runbook можно включать в качестве дочерних модулей с помощью командлета [Start-AzureAutomationRunbook](/powershell/module/az.automation/start-azautomationrunbook?view=azps-3.7.0), который создает новое задание.

### <a name="known-issues"></a>Известные проблемы

Ниже перечислены проблемы с модулями runbook PowerShell, известные на данный момент.

* Модули runbook PowerShell не могут извлечь незашифрованный [ресурс-контейнер переменной](./shared-resources/variables.md) со значением NULL.
* Модули runbook PowerShell не имеют возможности извлекать ресурс-контейнер переменной, в имени которого есть символ `*~*`.
* Операция [Get-Process](/powershell/module/microsoft.powershell.management/get-process?view=powershell-7) в цикле в модуле runbook PowerShell может аварийно завершить работу после примерно 80 итераций.
* В работе модуля runbook PowerShell может произойти сбой, если он попытается записать слишком большой объем данных в поток вывода за один раз. Обычно эту проблему можно обойти, настроив модуль runbook так, чтобы при работе с большими объектами он выводил только необходимые данные. Например, вместо использования `Get-Process` без ограничений можно сделать так, чтобы командлет выводил только необходимые параметры, например `Get-Process | Select ProcessName, CPU`.

## <a name="powershell-workflow-runbooks"></a>Модули Runbook рабочих процессов PowerShell

Runbook рабочих процессов PowerShell представляют собой текстовые Runbook, основанные на [рабочем процессе Windows PowerShell](automation-powershell-workflow.md). Код модуля Runbook можно редактировать в текстовом редакторе на портале Azure. Кроме того, можно использовать любой автономный текстовый редактор и [импортировать Runbook](manage-runbooks.md) в службу автоматизации Azure.

### <a name="advantages"></a>Преимущества

* Реализация сложной логики с помощью кода рабочего процесса PowerShell.
* Использование [контрольных точек](automation-powershell-workflow.md#use-checkpoints-in-a-workflow) для возобновления операции в случае ошибки.
* Использование [параллельной обработки](automation-powershell-workflow.md#use-parallel-processing) для одновременного выполнения нескольких действий.
* Возможность использования других графических модулей runbook и модулей runbook рабочих процессов PowerShell в качестве дочерних для создания высокоуровневых рабочих процессов.

### <a name="limitations"></a>Ограничения

* Необходимо знание рабочего процесса PowerShell.
* Модули runbook должны справляться с усложнениями рабочего процесса PowerShell, например с [десериализованными объектами](automation-powershell-workflow.md#deserialized-objects).
* Запускаются дольше, чем модули runbook PowerShell, так как требуют предварительной компиляции.
* Модули runbook PowerShell можно включать только в качестве дочерних модулей runbook с помощью командлета `Start-AzAutomationRunbook`.
* Нельзя запустить в гибридной рабочей роли Runbook для Linux.

## <a name="python-runbooks"></a>Модули Runbook Python

Модули Runbook Python компилируются в среде Python 2. Код модуля Runbook можно редактировать в текстовом редакторе на портале Azure. Кроме того, можно использовать любой автономный текстовый редактор и [импортировать модуль runbook](manage-runbooks.md) в службу автоматизации Azure.

### <a name="advantages"></a>Преимущества

* Вы можете использовать надежные библиотеки Python.
* Можно выполнять в Azure или в гибридных рабочих ролях Runbook для Linux. Гибридные рабочие роли Runbook для Windows поддерживаются, если установлен [Python 2.7](https://www.python.org/downloads/release/latest/python2).

### <a name="limitations"></a>Ограничения

* Требуется знание скриптов Python.
* Сейчас поддерживается только Python 2. Функции, относящиеся к Python 3, завершаются ошибкой.
* Чтобы использовать библиотеки сторонних производителей, вы должны [импортировать пакет](python-packages.md) в учетную запись службы автоматизации.

## <a name="next-steps"></a>Дальнейшие действия

* Сведения о модулях runbook PowerShell см. в статье [Руководство. Создание модуля runbook PowerShell](learn/automation-tutorial-runbook-textual-powershell.md).
* Сведения о модулях runbook для рабочих процессов PowerShell см. в статье [Руководство. Создание модуля runbook рабочего процесса PowerShell](learn/automation-tutorial-runbook-textual.md).
* Сведения о графических модулях runbook см. в статье [Руководство. Создание графического модуля runbook](learn/automation-tutorial-runbook-graphical.md).
* Сведения о модулях runbook Python см. в статье [Руководство. Создание runbook в Python](learn/automation-tutorial-runbook-textual-python2.md).
