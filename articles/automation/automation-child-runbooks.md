---
title: Дочерние модули Runbook в службе автоматизации Azure
description: Описывает различные методы запуска Runbook в службе автоматизации Azure из другого Runbook и обмена информацией между ними.
services: automation
ms.subservice: process-automation
ms.date: 01/17/2019
ms.topic: conceptual
ms.openlocfilehash: 42362a170f493afd51a5d4ee139620ad25b54e79
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79367369"
---
# <a name="child-runbooks-in-azure-automation"></a>Дочерние модули Runbook в службе автоматизации Azure

В Azure Automation рекомендуется писать многоразовые модульные runbooks с дискретной функцией, которая называется другими рунбуками. Родительский Runbook часто вызывает один или несколько дочерних Runbook для выполнения требуемых функций. Есть два способа позвонить в детскую книгу, и существуют различные различия, которые вы должны понимать, чтобы иметь возможность определить, что лучше всего подходит для ваших сценариев.

>[!NOTE]
>Эта статья была изменена и теперь содержит сведения о новом модуле Az для Azure PowerShell. Вы по-прежнему можете использовать модуль AzureRM, исправления ошибок для которого будут продолжать выпускаться как минимум до декабря 2020 г. Дополнительные сведения о совместимости модуля Az с AzureRM см. в статье [Introducing the new Azure PowerShell Az module](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0) (Знакомство с новым модулем Az для Azure PowerShell). Для инструкций по установке модуля Az на гибридном Runbook Worker [см.](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0) Для учетной записи Автоматизация вы можете обновить свои модули до последней версии, используя [как обновить модули Azure PowerShell в Azure Automation.](automation-update-azure-modules.md)

## <a name="invoking-a-child-runbook-using-inline-execution"></a>Вызов дочернего Runbook с использованием встроенного выполнения

Чтобы вызвать строку runbook из другого runbook, используйте название runbook и предоставьте значения для его параметров, точно так же, как вы использовали бы действие или cmdlet. Так все Runbook в одной учетной записи службы автоматизации доступны для использования всем остальным. Родительская книга ожидания для завершения запуска детской книги перед переходом к следующей строке возвращается непосредственно к родительскому.

При встроенном вызове дочерний Runbook выполняется в той же задаче, что и родительский Runbook. Существует никаких указаний в истории работы ребенка runbook. Любые исключения и любые выходы потока из детской книги связаны с родительским. Такое поведение приводит к меньшему количеству заданий и облегчает их отслеживание и устранение неполадок.

При публикации Runbook все дочерние Runbook, которые он вызывает, уже должны быть опубликованы. Причина в том, что Azure Automation создает ассоциацию с любыми дочерними книгами, когда составляет книгу. Если книги о запуске детей еще не опубликованы, родительский runbook, кажется, публикуется должным образом, но генерирует исключение при запуске. В этом случае можно повторно опубликовать родительский Runbook, чтобы исправить ссылки на дочерние Runbook. Вам не нужно переиздавать родительский runbook, если какой-либо ребенок runbook изменен, потому что ассоциация уже была создана.

Параметры детской ступеньки, называемой воновой, могут быть любого типа данных, включая сложные объекты. Нет [сериализации JSON,](start-runbooks.md#runbook-parameters)как это происходит при запуске с помощью портала Azure или с cmdlet [Start-AzAutomationRunbook.](/powershell/module/Az.Automation/Start-AzAutomationRunbook)

### <a name="runbook-types"></a>Типы Runbook

Какие типы runbook можно назвать друг другом?

* [Запуск PowerShell](automation-runbook-types.md#powershell-runbooks) и [графический runbook](automation-runbook-types.md#graphical-runbooks) могут называть друг друга вline, так как оба PowerShell основе.
* [Запуск рабочего процесса PowerShell](automation-runbook-types.md#powershell-workflow-runbooks) и графический runbook PowerShell Workflow могут называть друг друга внеочередными, так как оба основаны на рабочем процессе PowerShell.
* Типы PowerShell и типы рабочего процесса PowerShell не могут `Start-AzAutomationRunbook`называть друг друга внеочередными и должны использоваться.

Когда имеет значение публикация заказа?

Порядок публикации runbooks имеет значение только для PowerShell Workflow и графических runflow PowerShell.

Когда в вашем runbook звонит графический или PowerShell Workflow ребенка runbook с использованием встроконного исполнения, он использует имя runbook. Имя должно начинаться с `.\\` указания того, что скрипт находится в локальном каталоге.

### <a name="example"></a>Пример

В следующем примере запускается запуск тестового детского запуска, который принимает сложный объект, значение степлена и значение boolean. Выходные данные дочернего Runbook присваиваются переменной. В этом случае дочерний модуль runbook является модулем runbook рабочего процесса PowerShell.

```azurepowershell-interactive
$vm = Get-AzVM –ResourceGroupName "LabRG" –Name "MyVM"
$output = PSWF-ChildRunbook –VM $vm –RepeatCount 2 –Restart $true
```

Вот тот же пример, используя PowerShell runbook как ребенок.

```azurepowershell-interactive
$vm = Get-AzVM –ResourceGroupName "LabRG" –Name "MyVM"
$output = .\PS-ChildRunbook.ps1 –VM $vm –RepeatCount 2 –Restart $true
```

## <a name="starting-a-child-runbook-using-a-cmdlet"></a>Запуск детской книги с помощью cmdlet

> [!IMPORTANT]
> Если в runbook вывешется `Start-AzAutomationRunbook` детская книга `Wait` с помощью cmdlet с параметром, а детская книга приводит к результату объекта, операция может столкнуться с ошибкой. Чтобы обойти ошибку, см [Чайнд runbooks с выводом объектов,](troubleshoot/runbooks.md#child-runbook-object) чтобы узнать, как реализовать логику для опроса результатов с помощью [Get-AzAutomationJobOutputRecord](/powershell/module/az.automation/get-azautomationjoboutputrecord) cmdlet.

Вы можете `Start-AzAutomationRunbook` использовать для запуска runbook, как описано в [Чтобы начать runbook с Windows PowerShell](start-runbooks.md#start-a-runbook-with-powershell). Этот командлет можно использовать в двух режимах. В одном режиме cmdlet возвращает идентификатор задания при создании задания для детской книги. В другом режиме, который позволяет сценарий, указывая параметр *Ожидания,* cmdlet ждет, пока работа дочерней компании не закончится, и возвращает выход из детской ступеньки.

Задание из детской книги началось с cmdlet работает отдельно от родительской работы runbook. Такое поведение приводит к большему количестве заданий, чем запуск вставки, и делает задания более трудными для отслеживания. Родитель может начать более одного ребенка runbook асинхронно, не дожидаясь каждого для завершения. Для этого параллельного выполнения вызова ребенка runbooks в строке, родитель runbook должен использовать [параллельное ключевое слово.](automation-powershell-workflow.md#parallel-processing)

Выход детской книги не возвращается в родительский runbook надежно из-за времени. Кроме того, такие `$VerbosePreference`переменные, как , `$WarningPreference`и другие не могут распространяться на детские книги. Чтобы избежать этих проблем, можно запустить детские учебники `Wait` в виде отдельных заданий автоматизации, используя `Start-AzAutomationRunbook` параметр. Этот метод блокирует родительскую книгу до тех пор, пока не будет завершена работа детской книги.

Если вы не хотите, чтобы родительская книга была заблокирована при ожидании, `Wait` вы можете запустить детскую книгу, используя `Start-AzAutomationRunbook` без параметра. В этом случае в вашем runbook необходимо использовать [Get-AzAutomationJob,](/powershell/module/az.automation/get-azautomationjob) чтобы дождаться завершения работы. Он также должен использовать [Get-AzAutomationJobOutput](/powershell/module/az.automation/get-azautomationjoboutput) и [Get-AzAutomationJobOutputRecord](/powershell/module/az.automation/get-azautomationjoboutputrecord) для получения результатов.

Параметры для детского бегового книжки, начатые с cmdlet, предоставляются как хэшбит, как описано в [параметрах Runbook.](start-runbooks.md#runbook-parameters) Можно использовать только простые типы данных. Если у Runbook есть параметр со сложным типом данных, то для него необходимо использовать встроенный вызов.

Контекст подписки может быть потерян при запуске дочерних Runbook в качестве отдельных заданий. Для выполнения детской книги для выполнения cmdlets модулей Az против определенной подписки Azure ребенок должен подтвердить подлинность этой подписки независимо от родительской книги.

Если задания в одной учетной записи Automation работают с более чем одной подпиской, выбор подписки в одном заданиях может изменить выбранный в настоящее время контекст подписки для других заданий. Чтобы избежать этой `Disable-AzContextAutosave –Scope Process` ситуации, используйте в начале каждого запуска. Это действие сохраняет только контекст для выполнения этого модуля Runbook.

### <a name="example"></a>Пример

Следующий пример запускает детскую книгу с параметрами, а `Start-AzAutomationRunbook` затем ждет, `Wait` пока она будет завершена с помощью cmdlet с параметром. После завершения, пример собирает выход cmdlet из детской книги. Для `Start-AzAutomationRunbook`использования скрипт должен быть проверен на подписку Azure.

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

В следующей таблице кратко излагаются различия между двумя способами вызова runbook из другого runbook.

|  | Встроенный | Командлет |
|:--- |:--- |:--- |
| Задание |Дочерние Runbook выполняются в том же задании, что и родительский. |Для дочернего Runbook создается отдельное задание. |
| Выполнение |Родительский Runbook ожидает завершения дочернего Runbook, прежде чем продолжить выполнение. |Родительский модуль Runbook продолжает работу сразу после запуска дочернего модуля Runbook *или* ожидает завершения дочернего задания. |
| Выходные данные |Родительский Runbook может получить выходные данные непосредственно из дочернего Runbook. |Родительский модуль Runbook должен получить выходные данные из задания дочернего модуля Runbook *или* может получить выходные данные непосредственно из дочернего модуля Runbook. |
| Параметры |Значения параметров дочернего Runbook указываются отдельно и могут иметь любой тип данных. |Значения для параметров дочернего Runbook должны быть объединены в одну хэш-таблицу. Эта хэш-таблица может содержать только простые, регулярные и объектные типы данных, использующие сериализацию JSON. |
| Учетная запись службы автоматизации |Родительская книга может использоваться только в учетной записи «Автоматизация». |Родительские runbooks могут использовать детскую книгу из любой учетной записи Automation, из той же подписки Azure и даже из другой подписки, к которой у вас есть подключение. |
| Публикация |Перед публикацией родительского Runbook должен быть опубликован дочерний Runbook. |Детский runbook публикуется в любое время до запуска родительской книги. |

## <a name="next-steps"></a>Дальнейшие действия

* [Запуск модуля Runbook в службе автоматизации Azure](start-runbooks.md)
* [Выход и сообщения Runbook в Azure Automation](automation-runbook-output-and-messages.md)