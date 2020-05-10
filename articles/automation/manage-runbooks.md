---
title: Управление модулями runbook в службе автоматизации Azure
description: В этой статье описывается управление модулями Runbook в службе автоматизации Azure. Он охватывает базовые операции и добавляет некоторые рекомендации.
services: automation
ms.subservice: process-automation
ms.date: 02/14/2019
ms.topic: conceptual
ms.openlocfilehash: cd89314b0d847909bf4196361e471b71ebb9b6e9
ms.sourcegitcommit: 309a9d26f94ab775673fd4c9a0ffc6caa571f598
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/09/2020
ms.locfileid: "82995495"
---
# <a name="manage-runbooks-in-azure-automation"></a>Управление модулями runbook в службе автоматизации Azure

Модуль Runbook можно добавить в службу автоматизации Azure, создав новый или импортировав существующий файл из файла или [коллекции Runbook](automation-runbook-gallery.md). В этой статье содержатся сведения об управлении модулем Runbook, импортированным из файла. Вы можете найти все сведения о доступе к модулям Runbook и модули сообщества в модуле [Runbook и галереях модулей для службы автоматизации Azure](automation-runbook-gallery.md).

>[!NOTE]
>Эта статья была изменена и теперь содержит сведения о новом модуле Az для Azure PowerShell. Вы по-прежнему можете использовать модуль AzureRM, исправления ошибок для которого будут продолжать выпускаться как минимум до декабря 2020 г. Дополнительные сведения о совместимости модуля Az с AzureRM см. в статье [Introducing the new Azure PowerShell Az module](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0) (Знакомство с новым модулем Az для Azure PowerShell). Инструкции по установке модуля Az в гибридной рабочей роли Runbook см. в статье об [установке модуля Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0). Чтобы обновить модули в учетной записи службы автоматизации, см. руководство по [обновлению модулей Azure PowerShell в службе автоматизации Azure](automation-update-azure-modules.md).

## <a name="create-a-runbook"></a>Создание модуля runbook

Создайте новый модуль Runbook в службе автоматизации Azure с помощью портал Azure или Windows PowerShell. После создания модуля Runbook его можно изменить с помощью сведений в:

* [Изменение текстового модуля Runbook в службе автоматизации Azure](automation-edit-textual-runbook.md) 
* [Знакомство с основными понятиями рабочих процессов Windows PowerShell для модулей Runbook службы автоматизации](automation-powershell-workflow.md)
* [Графическая разработка в службе автоматизации Azure](automation-graphical-authoring-intro.md)
* [Управление пакетами Python 2 в службе автоматизации Azure](python-packages.md)

### <a name="create-a-runbook-in-the-azure-portal"></a>Создание модуля Runbook на портале Azure

1. На портале Azure выберите свою учетную запись службы автоматизации.
2. В центре выберите **модули Runbook** в разделе **Автоматизация процессов** , чтобы открыть список модулей Runbook.
3. Щелкните **создать Runbook**.
4. Введите имя модуля Runbook и выберите его [тип](automation-runbook-types.md). Имя Runbook должно начинаться с буквы и может содержать буквы, цифры, символы подчеркивания и тире.
5. Щелкните **Создать** , чтобы создать модуль Runbook и открыть текстовый редактор.

### <a name="create-a-runbook-with-powershell"></a>Создание модуля Runbook с помощью PowerShell

Используйте командлет [New-азаутоматионрунбук](https://docs.microsoft.com/powershell/module/az.automation/new-azautomationrunbook?view=azps-3.5.0) для создания пустого модуля Runbook. Используйте параметр `Type` , чтобы указать один из типов Runbook, определенных для `New-AzAutomationRunbook`.

В следующем примере показано, как создать новый пустой Runbook.

```azurepowershell-interactive
New-AzAutomationRunbook -AutomationAccountName MyAccount `
-Name NewRunbook -ResourceGroupName MyResourceGroup -Type PowerShell
```

## <a name="import-a-runbook"></a>Импорт модуля Runbook

Вы можете импортировать скрипт PowerShell или рабочего процесса PowerShell (**. ps1**), графический Runbook (**. graphrunbook**) или сценарий Python 2 (**. копировать**), чтобы создать собственный модуль Runbook.  При этом необходимо указать [тип модуля Runbook](automation-runbook-types.md), который создается во время импорта, с учетом следующих рекомендаций.

* Вы можете импортировать **PS1** -файл, который не содержит рабочий процесс, в [модуль Runbook PowerShell](automation-runbook-types.md#powershell-runbooks) или [Runbook рабочего процесса PowerShell](automation-runbook-types.md#powershell-workflow-runbooks). Если импортировать его в модуль Runbook рабочего процесса PowerShell, он преобразуется в рабочий процесс. В этом случае комментарии включаются в модуль Runbook для описания внесенных изменений.

* Вы можете импортировать в [модуль Runbook рабочего процесса PowerShell](automation-runbook-types.md#powershell-workflow-runbooks)только **PS1** -файл, содержащий рабочий процесс PowerShell. Если файл содержит несколько рабочих процессов PowerShell, импорт завершится ошибкой. Каждый рабочий процесс необходимо сохранить в отдельный файл и импортировать отдельно.

* Не импортируйте **PS1** -файл, содержащий рабочий процесс PowerShell, в [модуль Runbook PowerShell](automation-runbook-types.md#powershell-runbooks), так как обработчик сценариев PowerShell не может распознать его.

* Импортируйте файл **. graphrunbook** в новый [графический модуль Runbook](automation-runbook-types.md#graphical-runbooks). 

### <a name="import-a-runbook-from-the-azure-portal"></a>Импорт модуля Runbook из портал Azure

Для импорта файла сценария в службу автоматизации Azure можно использовать описанную ниже процедуру.

> [!NOTE]
> Вы можете импортировать **PS1** -файл в Runbook рабочего процесса PowerShell с помощью портала.

1. На портале Azure выберите свою учетную запись службы автоматизации.
2. Щелкните **Модули Runbook** в разделе **Автоматизация процессов**, чтобы открыть список модулей runbook.
3. Щелкните **Импорт модуля Runbook**.
4. Щелкните **файл Runbook** и выберите файл для импорта.
5. Если поле **имя** включено, можно изменить имя модуля Runbook. Имя должно начинаться с буквы и может содержать буквы, цифры, символы подчеркивания и тире.
6. [Тип Runbook](automation-runbook-types.md) выбирается автоматически, но его можно изменить, учитывая применимые ограничения.
7. Нажмите кнопку **Создать**. Новый модуль Runbook появится в списке модулей Runbook для учетной записи службы автоматизации.
8. Перед запуском модуля его необходимо [опубликовать](#publish-a-runbook) .

> [!NOTE]
> После импорта графического модуля Runbook его можно преобразовать в другой тип. Однако графический модуль Runbook нельзя преобразовать в текстовый модуль Runbook.

### <a name="import-a-runbook-with-windows-powershell"></a>Импорт модуля Runbook с помощью Windows PowerShell

Используйте командлет [Import-азаутоматионрунбук](https://docs.microsoft.com/powershell/module/az.automation/import-azautomationrunbook?view=azps-3.5.0) , чтобы импортировать файл скрипта в качестве черновика модуля Runbook. Если модуль Runbook уже существует, Импорт завершается неудачей, `Force` если не используется параметр с командлетом.

В следующем примере показано, как импортировать файл скрипта в модуль Runbook.

```azurepowershell-interactive
$automationAccountName =  "AutomationAccount"
$runbookName = "Sample_TestRunbook"
$scriptPath = "C:\Runbooks\Sample_TestRunbook.ps1"
$RGName = "ResourceGroup"

Import-AzAutomationRunbook -Name $runbookName -Path $scriptPath `
-ResourceGroupName $RGName -AutomationAccountName $automationAccountName `
-Type PowerShellWorkflow
```

## <a name="handle-resources"></a>Ресурсы для управления

Если модуль Runbook создает [ресурс](automation-runbook-execution.md#resources), он должен проверить, существует ли уже ресурс, прежде чем пытаться его создать. Ниже приведен простой пример.

```powershell
$vmName = "WindowsVM1"
$resourceGroupName = "myResourceGroup"
$myCred = Get-AutomationPSCredential "MyCredential"
$vmExists = Get-AzResource -Name $vmName -ResourceGroupName $resourceGroupName

if(!$vmExists)
    {
    Write-Output "VM $vmName does not exist, creating"
    New-AzVM -Name $vmName -ResourceGroupName $resourceGroupName -Credential $myCred
    }
else
    {
    Write-Output "VM $vmName already exists, skipping"
    }
```

## <a name="retrieve-details-from-activity-log"></a>Получение сведений из журнала действий

Сведения о Runbook, такие как пользователь или учетная запись, запускающие Runbook, можно получить из [журнала действий](automation-runbook-execution.md#activity-logging) для учетной записи службы автоматизации. В следующем примере PowerShell представлен последний пользователь для запуска указанного модуля Runbook.

```powershell-interactive
$SubID = "00000000-0000-0000-0000-000000000000"
$AutomationResourceGroupName = "MyResourceGroup"
$AutomationAccountName = "MyAutomationAccount"
$RunbookName = "MyRunbook"
$StartTime = (Get-Date).AddDays(-1)
$JobActivityLogs = Get-AzLog -ResourceGroupName $AutomationResourceGroupName -StartTime $StartTime `
                                | Where-Object {$_.Authorization.Action -eq "Microsoft.Automation/automationAccounts/jobs/write"}

$JobInfo = @{}
foreach ($log in $JobActivityLogs)
{
    # Get job resource
    $JobResource = Get-AzResource -ResourceId $log.ResourceId

    if ($JobInfo[$log.SubmissionTimestamp] -eq $null -and $JobResource.Properties.runbook.name -eq $RunbookName)
    {
        # Get runbook
        $Runbook = Get-AzAutomationJob -ResourceGroupName $AutomationResourceGroupName -AutomationAccountName $AutomationAccountName `
                                            -Id $JobResource.Properties.jobId | ? {$_.RunbookName -eq $RunbookName}

        # Add job information to hashtable
        $JobInfo.Add($log.SubmissionTimestamp, @($Runbook.RunbookName,$Log.Caller, $JobResource.Properties.jobId))
    }
}
$JobInfo.GetEnumerator() | sort key -Descending | Select-Object -First 1
```

## <a name="track-progress"></a>Отслеживание хода выполнения

Рекомендуется создавать модули Runbook в виде модульного характера, используя логику, которая может быть повторно использована и перезапущена. Отслеживание хода выполнения в модуле Runbook гарантирует правильность выполнения логики модуля Runbook при возникновении проблем. 

Ход выполнения Runbook можно отслеживать с помощью внешнего источника, например учетной записи хранения, базы данных или общих файлов. Создайте логику в модуле Runbook, чтобы сначала проверить состояние последнего выполненного действия. Затем, основываясь на результатах проверки, логика может либо пропустить, либо продолжить выполнение конкретных задач в модуле Runbook.

## <a name="prevent-concurrent-jobs"></a>Предотвращение выполнения одновременных заданий

Некоторые модули Runbook ведут себя необычным образом, если они выполняются одновременно для нескольких заданий. В этом случае необходимо, чтобы модуль Runbook реализовал логику, чтобы определить, уже запущено ли задание. Ниже приведен простой пример.

```powershell
# Authenticate to Azure
$connection = Get-AutomationConnection -Name AzureRunAsConnection
Connect-AzAccount -ServicePrincipal -Tenant $connection.TenantID `
-ApplicationId $connection.ApplicationID -CertificateThumbprint $connection.CertificateThumbprint

$AzContext = Select-AzSubscription -SubscriptionId $connection.SubscriptionID

# Check for already running or new runbooks
$runbookName = "<RunbookName>"
$rgName = "<ResourceGroupName>"
$aaName = "<AutomationAccountName>"
$jobs = Get-AzAutomationJob -ResourceGroupName $rgName -AutomationAccountName $aaName -RunbookName $runbookName -AzContext $AzureContext

# Check to see if it is already running
$runningCount = ($jobs | ? {$_.Status -eq "Running"}).count

If (($jobs.status -contains "Running" -And $runningCount -gt 1 ) -Or ($jobs.Status -eq "New")) {
    # Exit code
    Write-Output "Runbook is already running"
    Exit 1
} else {
    # Insert Your code here
}
```

## <a name="handle-transient-errors-in-a-time-dependent-script"></a>Обработку временных ошибок в скрипте, зависящем от времени

Модули Runbook должны быть надежными и способны обрабатывать [ошибки](automation-runbook-execution.md#errors), включая временные ошибки, которые могут привести к перезапуску или сбою. В случае сбоя Runbook служба автоматизации Azure повторяет ее.

Если модуль Runbook обычно работает в пределах ограничения по времени, то для проверки времени выполнения скрипт должен реализовывать логику. Эта проверка обеспечивает выполнение таких операций, как запуск, завершение работы или масштабирование, только в определенное время.

> [!NOTE]
> Местное время для процесса "песочницы" Azure задается в формате UTC. Этот факт следует учитывать при вычислении даты и времени в модулях Runbook.

## <a name="work-with-multiple-subscriptions"></a>Использование нескольких подписок

Модуль Runbook должен иметь возможность работать с [подписками](automation-runbook-execution.md#subscriptions). Например, для решения нескольких подписок модуль Runbook использует командлет [Disable-азконтекстаутосаве](https://docs.microsoft.com/powershell/module/Az.Accounts/Disable-AzContextAutosave?view=azps-3.5.0) . Этот командлет гарантирует, что контекст проверки подлинности не будет получен из другого модуля Runbook, выполняющегося в той же песочнице. Модуль Runbook также использует`AzContext` параметр командлета AZ Module и передает его правильному контексту.

```powershell
# Ensures that you do not inherit an AzContext in your runbook
Disable-AzContextAutosave –Scope Process

$Conn = Get-AutomationConnection -Name AzureRunAsConnection
Connect-AzAccount -ServicePrincipal `
-Tenant $Conn.TenantID `
-ApplicationId $Conn.ApplicationID `
-CertificateThumbprint $Conn.CertificateThumbprint

$context = Get-AzContext

$ChildRunbookName = 'ChildRunbookDemo'
$AutomationAccountName = 'myAutomationAccount'
$ResourceGroupName = 'myResourceGroup'

Start-AzAutomationRunbook `
    -ResourceGroupName $ResourceGroupName `
    -AutomationAccountName $AutomationAccountName `
    -Name $ChildRunbookName `
    -DefaultProfile $context
```

## <a name="work-with-a-custom-script"></a>Работа с пользовательским скриптом

> [!NOTE]
> Обычно вы не можете запускать пользовательские сценарии и модули Runbook на узле с установленным агентом Log Analytics. 

Чтобы использовать пользовательский скрипт, выполните следующие действия.

1. Создайте учетную запись службы автоматизации и получите [роль участника](automation-role-based-access-control.md).
2. [Свяжите учетную запись с рабочей областью Azure](../security-center/security-center-enable-data-collection.md).
3. Включите [гибридную рабочую роль Runbook](automation-hybrid-runbook-worker.md), [Управление обновлениями](automation-update-management.md)или другую функцию автоматизации. 
4. Если на компьютере Linux требуются высокие разрешения. Войдите в систему, чтобы отключить [проверку подписи](automation-linux-hrw-install.md#turn-off-signature-validation).

## <a name="test-a-runbook"></a>Тестирование runbook

При тестировании модуля Runbook запускается его [черновая версия](#publish-a-runbook) и завершаются все действия, которые он выполняет. Журнал заданий не создается, но [выходные данные](automation-runbook-output-and-messages.md#output-stream) и потоки [предупреждений и ошибок](automation-runbook-output-and-messages.md#message-streams) отображаются в области вывода теста. Сообщения в [подробный поток](automation-runbook-output-and-messages.md#message-streams) отображаются в области вывода только в том случае, если [VerbosePreference](automation-runbook-output-and-messages.md#preference-variables) для `Continue`переменной VerbosePreference задано значение.

Несмотря на то что выполняется черновая версия, модуль Runbook работает в обычном режиме и выполняет все действия с использованием ресурсов среды. В связи с этим тестировать модули Runbook можно только в непроизводственных ресурсах.

Процедура проверки каждого [типа модуля Runbook](automation-runbook-types.md) одинакова. Нет никакой разницы при тестировании между текстовым редактором и графическим редактором в портал Azure.

1. Откройте черновую версию модуля Runbook в [текстовом редакторе](automation-edit-textual-runbook.md) или в [графическом редакторе](automation-graphical-authoring-intro.md).
1. Нажмите кнопку **проверить** , чтобы открыть тестовую страницу.
1. Если у модуля Runbook есть параметры, они перечислены в левой области, где можно указать значения, которые будут использоваться для теста.
1. Если вы хотите выполнить тест в [гибридной рабочей роли Runbook](automation-hybrid-runbook-worker.md), измените **Параметры запуска** на **гибридную рабочую роль** и выберите имя целевой группы.  В противном случае оставьте значение **Azure** по умолчанию, чтобы тест выполнялся в облаке.
1. Щелкните **Пуск**, чтобы начать тестирование.
1. Можно использовать кнопки в области вывода, чтобы остановить или приостановить [Рабочий процесс PowerShell](automation-runbook-types.md#powershell-workflow-runbooks) или [графический](automation-runbook-types.md#graphical-runbooks) модуль Runbook во время тестирования. В случае приостановки модуль Runbook завершает действие, начатое до приостановки. Приостановленный модуль Runbook можно остановить или перезапустить.
1. Проверьте выходные данные модуля Runbook в области вывода.

## <a name="publish-a-runbook"></a>Публикация модуля Runbook

Перед запуском вновь созданного или импортированного модуля Runbook его необходимо опубликовать. Каждый модуль Runbook в службе автоматизации Azure имеет черновую версию и опубликованную версию. Запустить можно только опубликованную версию, а изменить — только черновую. Изменения, внесенные в черновик, не влияют на опубликованную версию. Когда необходимо сделать черновую версию доступной, опубликуйте ее, перезаписав текущую опубликованную версию с черновой версией.

### <a name="publish-a-runbook-in-the-azure-portal"></a>Публикация модуля Runbook в портал Azure

1. Откройте модуль Runbook на портале Azure.
2. Щелкните **Правка**.
3. Щелкните **опубликовать** , а затем **Да** в ответ на проверочное сообщение.

### <a name="publish-a-runbook-using-powershell"></a>Публикация модуля Runbook с помощью PowerShell

Опубликуйте модуль Runbook с помощью командлета [Publish-азаутоматионрунбук](https://docs.microsoft.com/powershell/module/Az.Automation/Publish-AzAutomationRunbook?view=azps-3.5.0) . 

```azurepowershell-interactive
$automationAccountName =  "AutomationAccount"
$runbookName = "Sample_TestRunbook"
$RGName = "ResourceGroup"

Publish-AzAutomationRunbook -AutomationAccountName $automationAccountName `
-Name $runbookName -ResourceGroupName $RGName
```

## <a name="schedule-a-runbook-in-the-azure-portal"></a>Планирование Runbook в портал Azure

После публикации Runbook можно запланировать его выполнение:

1. Откройте модуль Runbook на портале Azure.
2. Выберите **расписания** в разделе **ресурсы**.
3. Выберите **Добавить расписание**.
4. На панели расписание Runbook выберите **связать расписание с модулем Runbook**.
5. Выберите **создать новое расписание** на панели расписание.
6. Введите имя, описание и другие параметры в области новое расписание. 
7. После создания расписания выделите его и нажмите кнопку **ОК**. Теперь она должна быть связана с модулем Runbook.
8. Найдите в почтовом ящике сообщение электронной почты с уведомлением о состоянии Runbook.

## <a name="obtain-job-statuses"></a>Получение состояний заданий

### <a name="view-statuses-in-the-azure-portal"></a>Просмотр состояний в портал Azure

Подробные сведения об обработке заданий в службе автоматизации Azure приведены в [заданиях](automation-runbook-execution.md#jobs). Когда вы будете готовы просмотреть задания Runbook, используйте портал Azure и получите доступ к учетной записи службы автоматизации. Справа можно просмотреть сводку по всем заданиям Runbook в **статистике заданий**. 

![Элемент "Статистика по заданиям"](./media/manage-runbooks/automation-account-job-status-summary.png)

В сводке отображается количество и графическое представление состояния задания для каждого выполненного задания.

Щелкните плитку, чтобы открылась страница Задания, на которой представлен сводный список всех выполненных заданий. На этой странице отображаются сведения о состоянии, имени Runbook, времени начала и времени выполнения каждого задания.

![Страница "Задания" учетной записи службы автоматизации](./media/manage-runbooks/automation-account-jobs-status-blade.png)

Список заданий можно отфильтровать, выбрав пункт **Фильтровать задания**. Выполните фильтрацию по определенному модулю Runbook, состоянию задания или выбору из раскрывающегося списка и укажите диапазон времени для поиска.

![Фильтрация состояния задания](./media/manage-runbooks/automation-account-jobs-filter.png)

Кроме того, можно просмотреть сводные данные о задании для конкретного модуля Runbook, выбрав его на странице модули Runbook в учетной записи службы автоматизации, а затем выбрав **задания**. Это действие представляет страницу задания. Здесь можно щелкнуть запись задания, чтобы просмотреть ее сведения и выходные данные.

![Страница "Задания" учетной записи службы автоматизации](./media/manage-runbooks/automation-runbook-job-summary-blade.png)

### <a name="retrieve-job-statuses-using-powershell"></a>Получение состояний заданий с помощью PowerShell

Используйте командлет [Get-азаутоматионжоб](https://docs.microsoft.com/powershell/module/Az.Automation/Get-AzAutomationJob?view=azps-3.7.0) , чтобы получить задания, созданные для модуля Runbook, и сведения о конкретном задании. Если вы запускаете модуль Runbook `Start-AzAutomationRunbook`с помощью, он возвращает результирующее задание. Чтобы получить выходные данные задания, используйте [Get-азаутоматионжобаутпут](https://docs.microsoft.com/powershell/module/Az.Automation/Get-AzAutomationJobOutput?view=azps-3.5.0) .

В следующем примере возвращается последнее задание для примера модуля Runbook и отображается его состояние, значения, указанные для параметров модуля Runbook, и выходные данные задания.

```azurepowershell-interactive
$job = (Get-AzAutomationJob –AutomationAccountName "MyAutomationAccount" `
–RunbookName "Test-Runbook" -ResourceGroupName "ResourceGroup01" | sort LastModifiedDate –desc)[0]
$job.Status
$job.JobParameters
Get-AzAutomationJobOutput -ResourceGroupName "ResourceGroup01" `
–AutomationAccountName "MyAutomationAcct" -Id $job.JobId –Stream Output
```

В следующем примере извлекается результат для конкретного задания и возвращается каждая запись. Если имеется [исключение](automation-runbook-execution.md#exceptions) для одной из записей, сценарий записывает исключение вместо значения. Такое поведение полезно, поскольку исключения могут предоставлять дополнительные сведения, которые могут не регистрироваться обычным образом во время вывода.

```azurepowershell-interactive
$output = Get-AzAutomationJobOutput -AutomationAccountName <AutomationAccountName> -Id <jobID> -ResourceGroupName <ResourceGroupName> -Stream "Any"
foreach($item in $output)
{
    $fullRecord = Get-AzAutomationJobOutputRecord -AutomationAccountName <AutomationAccountName> -ResourceGroupName <ResourceGroupName> -JobId <jobID> -Id $item.StreamRecordId
    if ($fullRecord.Type -eq "Error")
    {
        $fullRecord.Value.Exception
    }
    else
    {
    $fullRecord.Value
    }
}
```

## <a name="next-steps"></a>Дальнейшие действия

* Дополнительные сведения об обработке модулей Runbook см. [в статье выполнение Runbook в службе автоматизации Azure](automation-runbook-execution.md).
* Дополнительные сведения об изменении модулей Runbook PowerShell и рабочих процессов PowerShell с помощью текстового редактора см. в статье [Изменение текстовых модулей Runbook в службе автоматизации Azure](automation-edit-textual-runbook.md).
* Дополнительные сведения о создании графических модулей Runbook см. [в статье графическая разработка в службе автоматизации Azure](automation-graphical-authoring-intro.md).
