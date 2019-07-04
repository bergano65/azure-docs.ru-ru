---
title: Типы модулей Runbook в службе автоматизации Azure
description: 'Описание различных типов модулей Runbook, которые можно использовать в службе автоматизации Azure, и рекомендации по выбору типа модуля. '
services: automation
ms.service: automation
ms.subservice: process-automation
author: bobbytreed
ms.author: robreed
ms.date: 03/05/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 8d90f1c03a9ed892a9dc8a4fecbeb9815c2dd376
ms.sourcegitcommit: f811238c0d732deb1f0892fe7a20a26c993bc4fc
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/29/2019
ms.locfileid: "67476887"
---
# <a name="azure-automation-runbook-types"></a>Типы модулей Runbook в службе автоматизации Azure

Служба автоматизации Azure поддерживает несколько типов модулей Runbook, кратко описанных в приведенной ниже таблице.  Следующие разделы содержат дополнительную информацию о каждом типе, включая рекомендацию по использованию.

| type | Описание |
|:--- |:--- |
| [Графический](#graphical-runbooks)|Опирается на Windows PowerShell, а полностью создается и редактируется в графическом редакторе на портале Azure. |
| [Графический модуль рабочего процесса PowerShell](#graphical-runbooks)|Опирается на рабочий процесс Windows PowerShell, а полностью создается и редактируется в графическом редакторе на портале Azure. |
| [PowerShell](#powershell-runbooks) |Текстовый модуль Runbook, основанный на сценарии Windows PowerShell. |
| [Рабочий процесс PowerShell](#powershell-workflow-runbooks)|Текстовый модуль Runbook, основанный на рабочем процессе Windows PowerShell. |
| [Python](#python-runbooks) |Текстовый модуль Runbook, основанный на Python. |

## <a name="graphical-runbooks"></a>Графические модули Runbook

[Графические](automation-runbook-types.md#graphical-runbooks) модули Runbook и графические модули Runbook рабочего процесса PowerShell создаются и редактируются в графическом редакторе на портале Azure.  Их можно экспортировать в файл и затем импортировать в другую учетную запись службы автоматизации. Создавать и редактировать их в других инструментах нельзя. Графические модули Runbook генерируют код PowerShell, но просматривать и изменять код напрямую нельзя. Графические модули Runbook нельзя конвертировать ни в один из [текстовых форматов](automation-runbook-types.md), а текстовые модули нельзя превратить в графические. Во время импорта графические модули Runbook можно преобразовывать в графические модули Runbook рабочего процесса PowerShell и наоборот.

### <a name="advantages"></a>Преимущества

* Визуальная модель создания insert-link-configure.  
* Фокусировка на том, как происходит поток данных.  
* Визуальное представление процессов управления.  
* Включение других модулей Runbook в качестве дочерних для создания высокоуровневых рабочих процессов.  
* Способствует модульному программированию.  

### <a name="limitations"></a>Ограничения

* Редактировать модуль Runbook за пределами портала Azure нельзя.
* Для выполнения сложной логики может потребоваться действие Code, содержащее код PowerShell.
* Вам не удастся отобразить или напрямую изменить код PowerShell, созданный графическим рабочим процессом. Вы можете просматривать код в любых действиях Code.
* Не может выполняться на гибридной рабочей роли Runbook Linux

## <a name="powershell-runbooks"></a>Модули Runbook PowerShell

Модули Runbook PowerShell используют Windows PowerShell.  Код модуля Runbook можно редактировать в текстовом редакторе на портале Azure.  Кроме того, можно использовать любой автономный текстовый редактор и [импортировать Runbook](manage-runbooks.md) в службу автоматизации Azure.

### <a name="advantages"></a>Преимущества

* Реализация сложной логики с помощью кода рабочего процесса PowerShell без дополнительного усложнения рабочего процесса PowerShell.
* Модуль Runbook запускается быстрее, чем модули рабочего процесса PowerShell, поскольку не требуют предварительной компиляции.
* Можно запускать в Azure или в гибридных рабочих ролей Runbook Windows и Linux

### <a name="limitations"></a>Ограничения

* Необходимо знание сценариев PowerShell.
* Вам не удастся использовать [параллельную обработку](automation-powershell-workflow.md#parallel-processing) для одновременного выполнения нескольких действий.
* Вам не удастся использовать [контрольные точки](automation-powershell-workflow.md#checkpoints) для возобновления Runbook в случае ошибки.
* Runbook рабочих процессов PowerShell и графические Runbook можно включать в качестве дочерних Runbook с помощью командлета Start-AzureAutomationRunbook, который создает задание.

### <a name="known-issues"></a>Известные проблемы

Ниже перечислены проблемы с модулями Runbook PowerShell, известные на данный момент.

* Модули Runbook PowerShell не могут извлечь незашифрованный [переменный ресурс](automation-variables.md) со значением NULL.
* Модули Runbook PowerShell не имеют возможности извлекать [ресурс переменной](automation-variables.md), в имени которого есть символ *~* .
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
* Не может выполняться на гибридной рабочей роли Runbook Linux

## <a name="python-runbooks"></a>Модули Runbook Python

Модули Runbook Python компилируются в среде Python 2.  Вы можете отредактировать код модуля Runbook с помощью текстового редактора на портале Azure или автономного текстового редактора, а затем [импортировать модуль Runbook](manage-runbooks.md) в службу автоматизации Azure.

### <a name="advantages"></a>Преимущества

* Вы можете использовать надежные библиотеки Python.
* Можно запускать в Azure или на обоих Linux гибридных рабочих ролей Runbook. Гибридные рабочие роли Runbook Windows поддерживаются с [Python версии 2.7](https://www.python.org/downloads/release/latest/python2) установлен.

### <a name="limitations"></a>Ограничения

* Требуется знание скриптов Python.
* Сейчас поддерживается только версия Python 2. Это значит, что определенные функции Python 3 завершатся ошибкой.
* Чтобы использовать библиотеки сторонних производителей, вы должны [импортировать пакет](python-packages.md) в учетную запись службы автоматизации для его использования.

## <a name="considerations"></a>Рекомендации

Выбирая тип модуля Runbook, принимайте во внимание следующие рекомендации.

* Вы не можете преобразовать модули Runbook из графического типа в текстовый или наоборот.
* При использовании других типов дочерних модулей Runbook могут действовать некоторые ограничения. Дополнительные сведения см. в статье [Дочерние модули Runbook в службе автоматизации Azure](automation-child-runbooks.md).

## <a name="next-steps"></a>Дальнейшие действия

* Дополнительные сведения о графической разработке модулей Runbook см. в статье [Графическая разработка в службе автоматизации Azure](automation-graphical-authoring-intro.md).
* Чтобы понять различия между PowerShell и рабочими процессами PowerShell для модулей Runbook, см. статью [Изучение рабочего процесса Windows PowerShell](automation-powershell-workflow.md).
* Дополнительные сведения о создании и импорте модуля Runbook см. в статье [Создание или импорт модуля Runbook в службе автоматизации Azure](manage-runbooks.md).

