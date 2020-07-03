---
title: Дочерние модули Runbook в службе автоматизации Azure
description: Описывает различные методы запуска Runbook в службе автоматизации Azure из другого Runbook и обмена информацией между ними.
services: automation
ms.subservice: process-automation
ms.date: 01/17/2019
ms.topic: conceptual
ms.openlocfilehash: 21dc14362fed2abf80c2c5ecf57f688541c9c639
ms.sourcegitcommit: 309a9d26f94ab775673fd4c9a0ffc6caa571f598
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/09/2020
ms.locfileid: "82994794"
---
# <a name="child-runbooks-in-azure-automation"></a>Дочерние модули Runbook в службе автоматизации Azure

В службе автоматизации Azure рекомендуется писать повторно используемые модульные модули Runbook с дискретной функцией, которая вызывается другими модулями Runbook. Родительский Runbook часто вызывает один или несколько дочерних Runbook для выполнения требуемых функций. Существует два способа вызова дочернего модуля Runbook, и существуют отдельные отличия, которые необходимо знать, чтобы определить, какой из них лучше всего подходит для ваших сценариев.

## <a name="invoking-a-child-runbook-using-inline-execution"></a>Вызов дочернего Runbook с использованием встроенного выполнения

Чтобы вызвать встроенный модуль Runbook из другого модуля Runbook, используйте имя модуля Runbook и укажите значения для его параметров, как при использовании действия или командлета. Так все Runbook в одной учетной записи службы автоматизации доступны для использования всем остальным. Родительский модуль Runbook ожидает завершения дочернего модуля Runbook перед переходом к следующей строке, а все выходные данные возвращаются непосредственно родительскому объекту.

При встроенном вызове дочерний Runbook выполняется в той же задаче, что и родительский Runbook. В журнале заданий дочернего модуля Runbook нет указания. Любые исключения и потоковые выходные данные дочернего модуля Runbook связаны с родительским модулем. Такое поведение приводит к меньшему числу заданий и упрощает их мониторинг и устранение неполадок.

При публикации Runbook все дочерние Runbook, которые он вызывает, уже должны быть опубликованы. Причина в том, что служба автоматизации Azure создает связь с любыми дочерними модулями Runbook при компиляции модуля Runbook. Если дочерние модули Runbook еще не опубликованы, родительский модуль Runbook будет опубликован правильно, но при его запуске создает исключение. В этом случае можно повторно опубликовать родительский Runbook, чтобы исправить ссылки на дочерние Runbook. Повторная публикация родительского модуля Runbook не требуется, если изменен дочерний модуль Runbook, так как Ассоциация уже создана.

Параметры дочернего модуля Runbook, называемого встроенным, могут иметь любой тип данных, включая сложные объекты. [Сериализация JSON](start-runbooks.md#runbook-parameters)отсутствует, как при запуске модуля Runbook с помощью портал Azure или командлета [Start-азаутоматионрунбук](/powershell/module/Az.Automation/Start-AzAutomationRunbook) .

### <a name="runbook-types"></a>Типы Runbook

Какие типы Runbook могут вызывать друг друга?

* [Модуль Runbook PowerShell](automation-runbook-types.md#powershell-runbooks) и [графический модуль Runbook](automation-runbook-types.md#graphical-runbooks) могут вызывать друг друга, как и на основе PowerShell.
* [Runbook рабочего процесса PowerShell](automation-runbook-types.md#powershell-workflow-runbooks) и графический модуль Runbook рабочего процесса PowerShell могут вызывать друг друга, как и на основе рабочих процессов PowerShell.
* Типы PowerShell и типов рабочих процессов PowerShell не могут вызывать друг друга и должны использовать `Start-AzAutomationRunbook`.

Когда зависит порядок публикации?

Порядок публикации модулей Runbook имеет значение только для рабочих процессов PowerShell и графических модулей Runbook рабочих процессов PowerShell.

Когда модуль Runbook обращается к графическому модулю или дочернему Runbook рабочего процесса PowerShell с помощью встроенного выполнения, он использует имя модуля Runbook. Имя должно начинаться с `.\\` , чтобы указать, что сценарий находится в локальном каталоге.

### <a name="example"></a>Пример

В следующем примере запускается тестовый дочерний модуль Runbook, который принимает сложный объект, целочисленное значение и логическое значение. Выходные данные дочернего Runbook присваиваются переменной. В этом случае дочерний модуль runbook является модулем runbook рабочего процесса PowerShell.

```azurepowershell-interactive
$vm = Get-AzVM –ResourceGroupName "LabRG" –Name "MyVM"
$output = PSWF-ChildRunbook –VM $vm –RepeatCount 2 –Restart $true
```

Ниже приведен тот же пример с использованием модуля Runbook PowerShell в качестве дочернего.

```azurepowershell-interactive
$vm = Get-AzVM –ResourceGroupName "LabRG" –Name "MyVM"
$output = .\PS-ChildRunbook.ps1 –VM $vm –RepeatCount 2 –Restart $true
```

## <a name="starting-a-child-runbook-using-a-cmdlet"></a>Запуск дочернего модуля Runbook с помощью командлета

> [!IMPORTANT]
> Если модуль Runbook вызывает дочерний модуль Runbook с `Start-AzAutomationRunbook` помощью командлета `Wait` с параметром, а дочерний модуль Runbook создает результат объекта, операция может столкнуться с ошибкой. Чтобы обойти эту ошибку, см. раздел [дочерние модули Runbook с выводом объекта](troubleshoot/runbooks.md#child-runbook-object) , чтобы узнать, как реализовать логику для опроса результатов с помощью командлета [Get-азаутоматионжобаутпутрекорд](/powershell/module/az.automation/get-azautomationjoboutputrecord) .

Вы можете использовать `Start-AzAutomationRunbook` для запуска модуля Runbook, как описано в разделе [, чтобы запустить модуль Runbook с помощью Windows PowerShell](start-runbooks.md#start-a-runbook-with-powershell). Этот командлет можно использовать в двух режимах. В одном режиме командлет возвращает идентификатор задания при создании задания для дочернего модуля Runbook. В другом режиме, который включает сценарий, указав параметр *Wait* , командлет ожидает завершения дочернего задания и возвращает выходные данные дочернего модуля Runbook.

Задание из дочернего модуля Runbook, запущенного с помощью командлета, выполняется отдельно от родительского задания Runbook. Это приводит к большему результату заданий, чем запуск встроенного модуля Runbook, и затрудняет их отслеживание. Родитель может асинхронно запускать несколько дочерних модулей Runbook, не дожидаясь завершения каждого из них. Для этого параллельного выполнения, вызывающего встроенные дочерние модули Runbook, родительский модуль Runbook должен использовать [ключевое слово Parallel](automation-powershell-workflow.md#parallel-processing).

Выходные данные дочернего модуля Runbook надежно не возвращаются в родительский Runbook из-за времени. Кроме того, переменные, такие `$VerbosePreference`как `$WarningPreference`, и другие, могут не распространяться на дочерние модули Runbook. Чтобы избежать этих проблем, можно запустить дочерние модули Runbook в качестве отдельных заданий автоматизации `Start-AzAutomationRunbook` с помощью `Wait` параметра. Этот метод блокирует родительский модуль Runbook до завершения дочернего модуля Runbook.

Если вы не хотите, чтобы родительский модуль Runbook блокировался в ожидании, можно запустить дочерний Runbook `Wait` , используя `Start-AzAutomationRunbook` параметр без параметра. В этом случае модуль Runbook должен использовать командлет [Get-азаутоматионжоб](/powershell/module/az.automation/get-azautomationjob) для ожидания завершения задания. Для получения результатов также необходимо использовать [Get-азаутоматионжобаутпут](/powershell/module/az.automation/get-azautomationjoboutput) и [Get-азаутоматионжобаутпутрекорд](/powershell/module/az.automation/get-azautomationjoboutputrecord) .

Параметры для дочернего модуля Runbook, запущенного с помощью командлета, предоставляются в виде хэш-таблицы, как описано в разделе [Параметры модуля Runbook](start-runbooks.md#runbook-parameters). Можно использовать только простые типы данных. Если у Runbook есть параметр со сложным типом данных, то для него необходимо использовать встроенный вызов.

Контекст подписки может быть потерян при запуске дочерних Runbook в качестве отдельных заданий. Чтобы дочерний модуль Runbook выполнял командлеты AZ Module для конкретной подписки Azure, дочерний модуль должен пройти проверку подлинности в этой подписке независимо от родительского модуля Runbook.

Если задания в пределах одной учетной записи службы автоматизации работают с несколькими подписками, выбор подписки в одном задании может изменить контекст текущей подписки для других заданий. Чтобы избежать этой ситуации, используйте `Disable-AzContextAutosave –Scope Process` в начале каждого модуля Runbook. Это действие сохраняет только контекст для выполнения этого модуля Runbook.

### <a name="example"></a>Пример

В следующем примере запускается дочерний модуль Runbook с параметрами, а затем выполняется ожидание его завершения `Start-AzAutomationRunbook` с помощью командлета с `Wait` параметром. После завершения этот пример собирает выходные данные командлета из дочернего модуля Runbook. Для использования `Start-AzAutomationRunbook`скрипт должен пройти проверку подлинности в подписке Azure.

```azurepowershell-interactive
# Ensure that the runbook does not inherit an AzContext
Disable-AzContextAutosave –Scope Process

# Connect to Azure with Run As account
$ServicePrincipalConnection = Get-AutomationConnection -Name 'AzureRunAsConnection'

Connect-AzAccount `
    -ServicePrincipal `
    -Tenant $ServicePrincipalConnection.TenantId `
    -ApplicationId $ServicePrincipalConnection.ApplicationId `
    -CertificateThumbprint $ServicePrincipalConnection.CertificateThumbprint

$AzureContext = Get-AzSubscription -SubscriptionId $ServicePrincipalConnection.SubscriptionID

$params = @{"VMName"="MyVM";"RepeatCount"=2;"Restart"=$true}

Start-AzAutomationRunbook `
    –AutomationAccountName 'MyAutomationAccount' `
    –Name 'Test-ChildRunbook' `
    -ResourceGroupName 'LabRG' `
    -AzContext $AzureContext `
    –Parameters $params –Wait
```

## <a name="comparison-of-methods-for-calling-a-child-runbook"></a>Сравнение методов вызова дочернего Runbook

В следующей таблице приведены различия между двумя способами вызова модуля Runbook из другого модуля Runbook.

|  | Встроенный | Командлет |
|:--- |:--- |:--- |
| Задание |Дочерние Runbook выполняются в том же задании, что и родительский. |Для дочернего Runbook создается отдельное задание. |
| Выполнение |Родительский Runbook ожидает завершения дочернего Runbook, прежде чем продолжить выполнение. |Родительский модуль Runbook продолжает работу сразу после запуска дочернего модуля Runbook *или* ожидает завершения дочернего задания. |
| Выходные данные |Родительский Runbook может получить выходные данные непосредственно из дочернего Runbook. |Родительский модуль Runbook должен получить выходные данные из задания дочернего модуля Runbook *или* может получить выходные данные непосредственно из дочернего модуля Runbook. |
| Параметры |Значения параметров дочернего Runbook указываются отдельно и могут иметь любой тип данных. |Значения для параметров дочернего Runbook должны быть объединены в одну хэш-таблицу. Эта хэш-таблица может содержать только простые, регулярные и объектные типы данных, использующие сериализацию JSON. |
| Учетная запись службы автоматизации |Родительский модуль Runbook может использовать только дочерний Runbook в той же учетной записи службы автоматизации. |Родительские модули Runbook могут использовать дочерний модуль Runbook из любой учетной записи службы автоматизации, из той же подписки Azure и даже из другой подписки, к которой у вас есть подключение. |
| Публикация |Перед публикацией родительского Runbook должен быть опубликован дочерний Runbook. |Дочерний модуль Runbook публикуется в любое время до запуска родительского модуля Runbook. |

## <a name="next-steps"></a>Дальнейшие действия

* [Запуск модуля Runbook в службе автоматизации Azure](start-runbooks.md)
* [Выходные данные и сообщения Runbook в службе автоматизации Azure](automation-runbook-output-and-messages.md)