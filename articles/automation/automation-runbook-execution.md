---
title: Выполнение модуля Runbook в службе автоматизации Azure
description: Рассматривается обработка модуля Runbook в службе автоматизации Azure.
services: automation
ms.subservice: process-automation
ms.date: 04/04/2019
ms.topic: conceptual
ms.openlocfilehash: 1907eb7cde482927ee8e6b0a2522158f05c1808f
ms.sourcegitcommit: ae3d707f1fe68ba5d7d206be1ca82958f12751e8
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/10/2020
ms.locfileid: "81010942"
---
# <a name="runbook-execution-in-azure-automation"></a>Выполнение модуля Runbook в службе автоматизации Azure

Runbooks выполняются на основе логики, определенной внутри них. Если работа модуля Runbook прерывается, он перезапускается. Такое поведение требует, чтобы вы написали runbooks, которые поддерживают перезагрузку, если возникают временные проблемы.

Запуск запуска запуска в Azure Automation создает задание, которое представляет собой единый экземпляр выполнения runbook. Каждое задание имеет доступ к ресурсам Azure, подключявшись к подписке Azure. Задание имеет доступ к ресурсам в центре обработки данных только в том случае, если эти ресурсы доступны из общедоступного облака.

Azure Automation назначает сотрудника для выполнения каждого задания во время выполнения runbook. Пока рабочие роли используются многими учетными записями Azure, задания от различных учетных записей службы автоматизации изолируются друг от друга. У вас нет контроля над тем, какой сотрудник обслуживает ваш запрос на работу.

При просмотре списка runbooks на портале Azure отображается состояние каждого задания, запущенного для каждой запущенной книги. Azure Automation хранит журналы вакансий максимум на 30 дней. 

На следующей диаграмме показан жизненный цикл работы для runbook для [powerShell runbooks](automation-runbook-types.md#powershell-runbooks), [графических runbooks](automation-runbook-types.md#graphical-runbooks), и [PowerShell Workflow runbooks](automation-runbook-types.md#powershell-workflow-runbooks).

![Состояния задания — рабочий процесс PowerShell](./media/automation-runbook-execution/job-statuses.png)

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-dsr-and-stp-note.md)]

>[!NOTE]
>Эта статья была изменена и теперь содержит сведения о новом модуле Az для Azure PowerShell. Вы по-прежнему можете использовать модуль AzureRM, исправления ошибок для которого будут продолжать выпускаться как минимум до декабря 2020 г. Дополнительные сведения о совместимости модуля Az с AzureRM см. в статье [Introducing the new Azure PowerShell Az module](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0) (Знакомство с новым модулем Az для Azure PowerShell). Для инструкций по установке модуля Az на гибридном Runbook Worker [см.](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0) Для учетной записи Автоматизация вы можете обновить свои модули до последней версии, используя [как обновить модули Azure PowerShell в Azure Automation.](automation-update-azure-modules.md)

## <a name="where-to-run-your-runbooks"></a>Место запуска модулей Runbook

Runbooks в Azure Automation может работать либо на песочнице Azure, либо на [гибридном Runbook Worker.](automation-hybrid-runbook-worker.md) Большинство runbooks можно легко запустить в песочнице Azure, общей среде, которую могут использовать несколько заданий. Для заданий, использующих одну и ту же песочницу, применяются ее ограничения ресурсов.

Можно использовать Hybrid Runbook Worker для запуска runbooks непосредственно на компьютере, который размещает роль, и против местных ресурсов в среде. Azure Automation хранит и управляет рунбуками, а затем доставляет их на один или несколько назначенных компьютеров.

В следующей таблице перечислены некоторые задачи выполнения runbook с рекомендуемой средой выполнения, указанной для каждой из них.

|Задача|Лучший вариант|Примечания|
|---|---|---|
|Интеграция с ресурсами Azure|Песочница Azure|Хостинг в Azure, проверка подлинности проще. Если вы используете Hybrid Runbook Worker на Azure VM, можно использовать [управляемые идентификаторы для ресурсов Azure.](automation-hrw-run-runbooks.md#managed-identities-for-azure-resources)|
|Получение оптимальной производительности для управления ресурсами Azure|Песочница Azure|Сценарий работает в той же среде, которая имеет меньше задержки.|
|Снижение операционных расходов|Песочница Azure|Нет вычислительных накладных расходов и нет необходимости в VM.|
|Выполнение длительного скрипта|Гибридная рабочая роль Runbook|Панзурные песочницы имеют [ограничения на ресурсы.](../azure-resource-manager/management/azure-subscription-service-limits.md#automation-limits)|
|Взаимодействие с местными службами|Гибридная рабочая роль Runbook|Может иметь доступ непосредственно к принимающей машине.|
|Требуется стороннее программное обеспечение и исполнители|Гибридная рабочая роль Runbook|Вы управляете операционной системой и можете установить программное обеспечение.|
|Мониторинг файла или папки с модулем Runbook|Гибридная рабочая роль Runbook|Используйте [задачу Watcher](automation-watchers-tutorial.md) на гибридном работнике Runbook.|
|Запуск ресурсоемкого скрипта|Гибридная рабочая роль Runbook| Панзурные песочницы имеют [ограничения на ресурсы.](../azure-resource-manager/management/azure-subscription-service-limits.md#automation-limits)|
|Использование модулей с определенными требованиями| Гибридная рабочая роль Runbook|Некоторые примеры.</br> WinSCP — зависимость от winscp.exe. </br> IISAdministration - зависимость от включения IIS.|
|Установка модуля с установщиком|Гибридная рабочая роль Runbook|Модули для песочницы должны поддерживать копирование.|
|Используйте runbooks или модули, требующие версии .NET Framework, отличающиеся от 4.7.2|Гибридная рабочая роль Runbook|Автоматизация песочницы имеют .NET Framework 4.7.2, и нет никакого способа обновить его.|
|Запуск скриптов, требующих высоты|Гибридная рабочая роль Runbook|Песочницы не позволяют высоты. С помощью гибридного Runbook Worker можно отключить UAC и использовать **Invoke-Command** при запуске команды, требующей высоты.|
|Запуск скриптов, требующих доступа к WMI|Гибридная рабочая роль Runbook|Вакансии, работающие в песочницах в облаке, не имеют доступа к WMI. |

## <a name="runbook-behavior"></a>Поведение модуля Runbook

### <a name="creating-resources"></a>Создание ресурсов

Если в runbook создается ресурс, скрипт должен проверить, существует ли ресурс, прежде чем пытаться создать его. Вот основной пример.

```powershell
$vmName = "WindowsVM1"
$resourceGroupName = "myResourceGroup"
$myCred = Get-AutomationPSCredential "MyCredential"
$vmExists = Get-AzResource -Name $vmName -ResourceGroupName $resourceGroupName

if(!$vmExists)
    {
    Write-Output "VM $vmName does not exist, creating"
    New-AzureRMVM -Name $vmName -ResourceGroupName $resourceGroupName -Credential $myCred
    }
else
    {
    Write-Output "VM $vmName already exists, skipping"
    }
```

### <a name="supporting-time-dependent-scripts"></a>Поддержка зависимых от времени скриптов

Ваши runbooks должны быть надежными и способными обрабатывать временные ошибки, которые могут привести к их перезапуску или сбою. Если запуск выходит из строя, Azure Automation повторно ее проверяет.

Если ваша книга обычно работает в пределах ограниченности времени, поите логику реализации скрипта, чтобы проверить время выполнения. Эта проверка обеспечивает запуск таких операций, как запуск, выключение или масштабирование только в определенное время.

> [!NOTE]
> Локальное время в процессе песочницы Azure настроено на UTC. Расчеты по дате и времени в ваших runbooks должны принимать этот факт во внимание.

### <a name="tracking-progress"></a>Отслеживание хода выполнения

Это хорошая практика, чтобы автор ваших runbooks быть модульной по своему характеру, структурирование runbook логики, так что он может быть повторно использован и перезапущен легко. Отслеживание прогресса в runbook является хорошим способом убедиться, что логика runbook выполняется правильно, если есть проблемы. Можно отслеживать ход выполнения с помощью внешнего источника, например учетной записи хранения, базы данных или общих файлов. Можно создать логику в своей книге, чтобы сначала проверить состояние последнего действия. Затем, на основе результатов проверки, логика может либо пропустить, либо продолжить выполнение конкретных задач в runbook.

### <a name="preventing-concurrent-jobs"></a>Предотвращение одновременных заданий

Некоторые runbooks ведут себя странно, если они сталкивались с несколькими заданиями в то же время. В этом случае для runbook важно реализовать логику, чтобы определить, есть ли уже работа. Вот основной пример.

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

### <a name="working-with-multiple-subscriptions"></a>Использование нескольких подписок

Чтобы справиться с несколькими подписками, ваша книга должна использовать [cmdlet Disable-AzContextAutosave,](https://docs.microsoft.com/powershell/module/Az.Accounts/Disable-AzContextAutosave?view=azps-3.5.0) чтобы гарантировать, что контекст проверки подлинности не будет извлечен из другой запущенной книги, работая в той же песочнице. В runbook также`AzContext` используется параметр на cmdlets модуля Az и передается соответствующий контекст.

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

### <a name="handling-exceptions"></a>Обработка исключений

В этом разделе описаны некоторые способы обработки исключений или периодических проблем в ваших runbooks.

#### <a name="erroractionpreference"></a>ErrorActionPreference

Переменная [ErrorActionPreference](/powershell/module/microsoft.powershell.core/about/about_preference_variables#erroractionpreference) определяет, как PowerShell реагирует на ошибку, не прекращающуюся. Ошибки прерывания всегда заканчиваются и не затрагиваются *Ошибкой.*

Когда runbook `ErrorActionPreference`использует, обычно непрекращающейся ошибки, такие как `Get-ChildItem` **PathNotFound** от cmdlet останавливает runbook от завершения. В следующем коде показано использование функции `ErrorActionPreference`. Окончательная `Write-Output` команда никогда не выполняется, как сценарий останавливается.

```powershell-interactive
$ErrorActionPreference = 'Stop'
Get-Childitem -path nofile.txt
Write-Output "This message will not show"
```

#### <a name="try-catch-finally"></a>Попробуйте поймать, наконец,

[Try Catch Finally](/powershell/module/microsoft.powershell.core/about/about_try_catch_finally) используется в скриптах PowerShell для обработки ошибок прекращения. Скрипт может использовать этот механизм для ловли конкретных исключений или общих исключений. Заявление `catch` должно использоваться для отслеживания или попытки обработки ошибок. Следующий пример пытается загрузить файл, который не существует. Он ловит `System.Net.WebException` исключение и возвращает последнее значение для любого другого исключения.

```powershell-interactive
try
{
   $wc = new-object System.Net.WebClient
   $wc.DownloadFile("http://www.contoso.com/MyDoc.doc")
}
catch [System.Net.WebException]
{
    "Unable to download MyDoc.doc from http://www.contoso.com."
}
catch
{
    "An error occurred that could not be resolved."
}
```

#### <a name="throw"></a>Throw

[Бросок](/powershell/module/microsoft.powershell.core/about/about_throw) может быть использован для создания ошибки прекращения. Этот механизм может быть полезен при определении собственной логики в runbook. Если скрипт соответствует критерию, который должен остановить его, он может использовать `throw` оператора, чтобы остановить. В следующем примере используется это утверждение для отображения требуемого параметра функции.

```powershell-interactive
function Get-ContosoFiles
{
  param ($path = $(throw "The Path parameter is required."))
  Get-ChildItem -Path $path\*.txt -recurse
}
```

### <a name="using-executables-or-calling-processes"></a>Использование исполняемых файлов или вызов процессов

Runbooks, запускаемые в песочницах Azure, не поддерживают процессы вызовов, такие как исполняемые **(файлы .exe)** или подпроцессы.  Причина этого заключается в том, что песочница Azure — это общий процесс, запущенный в контейнере, который может не иметь доступа ко всем базовым AIS. Для сценариев, требующих стороннего программного обеспечения или вызовов подпроцессов, рекомендуется выполнить запуск [на гибридном Runbook Worker.](automation-hybrid-runbook-worker.md)

### <a name="accessing-device-and-application-characteristics"></a>Доступ к характеристикам устройства и приложения

Задания Runbook, которые работают в песочницах Azure, не имеют доступа к каким-либо характеристикам устройства или приложения. Наиболее распространенным API, используемым для запроса метрик производительности на Windows, является WMI, при этом некоторые из общих метрик являются память и использование процессора. Однако не имеет значения, что используется API, так как задания, работающие в облаке, не имеют доступа к реализации Microsoft веб-управления предприятиями (WBEM). Эта платформа построена на общей информационной модели (CIM), обеспечивающей отраслевые стандарты для определения характеристик устройств и приложений.

## <a name="handling-errors"></a>Обработка ошибок

Ваши runbooks должны быть способны обрабатывать ошибки. PowerShell имеет два типа ошибок, прекращающихся и непрекращающихся. Прекращение ошибок останавливает выполнение runbook, когда они происходят. Runbook останавливается с состоянием задания Не удалось.

Ошибки, не прекращающиеся, позволяют скрипту продолжаться даже после их возникновения. Примером ошибки, не прекращающейся, является ошибка, которая `Get-ChildItem` возникает, когда беговая книга использует cmdlet с несуществующей траекторией. PowerShell видит, что путь не существует, выдает ошибку и переходит к следующей папке. Ошибка в этом случае не устанавливает статус состояния задания runbook к неудавшимся, и задание может быть даже завершено. Чтобы выполнить принудительную остановку модуля Runbook при неустранимой ошибке, вы можете использовать `-ErrorAction Stop` в командлете.

## <a name="handling-jobs"></a>Обработка заданий

Среду выполнения можно повторно использовать для заданий из той же учетной записи Automation. В одном модуле Runbook может быть запущено множество заданий одновременно. Чем больше заданий выполняются одновременно, тем вероятнее, что они будут отправлены в одну и ту же песочницу.

Задания, работающие в одном и том же процессе песочницы, могут влиять друг на друга. Одним из примеров является запуск `Disconnect-AzAccount` cmdlet. Выполнение этого cmdlet отключает каждое задание runbook в общем процессе песочницы.

Задания PowerShell, начатые с запуска книги, которая работает в песочнице Azure, могут работать не в полном языковом режиме. Чтобы узнать больше о режимах языка PowerShell, смотрите [языковые режимы PowerShell.](/powershell/module/microsoft.powershell.core/about/about_language_modes) Дополнительные сведения о взаимодействии с заданиями в Azure Automation можно найти в [PowerShell.](#retrieving-job-status-using-powershell)

### <a name="job-statuses"></a>Состояния заданий

В следующей таблице описаны статусы, которые возможны для работы.

| Состояние | Описание |
|:--- |:--- |
| Завершено |Задание успешно выполнено. |
| Ошибка |Графический или PowerShell Workflow runbook не удалось составить. Запуск сценария PowerShell не был запущен или задания было исключением. Смотрите [типы runbook Автоматизации Azure.](automation-runbook-types.md)|
| Задание не выполнено. Ожидание ресурсов. |Задание не выполнено, так как задание превысило ограничение [доли](#fair-share) три раза и было запущено с той же контрольной точки или с момента запуска модуля Runbook. |
| Поставлено в очередь |Задание ждет, когда ресурсы на сотрудника автоматизации станут доступными, чтобы его можно было запустить. |
| Запуск |Задание было назначено рабочей роли, и система его запускает. |
| Возобновление |Система возобновляет задание после его приостановки. |
| Запущен |Задание выполняется. |
| Задание выполняется. Ожидание ресурсов. |Задание выгружено, поскольку превышено ограничение доли . Задание будет возобновлено позже с его последней контрольной точки. |
| Остановлена |Задание было остановлено пользователем до его завершения. |
| Остановка |Система останавливает задание. |
| Приостановлена |Применяется только к [графическим и PowerShell Workflow runbooks.](automation-runbook-types.md) Задание было приостановлено пользователем, системой или с помощью команды в модуле Runbook. Если в беговой книге нет контрольно-пропускного пункта, она начинается с самого начала. Если в модуле есть контрольная точка, задание возобновится с последней контрольной точки. Система приостанавливает работу только в случае возникновения исключения. По умолчанию `ErrorActionPreference` переменная настроена на продолжение, что указывает на то, что задание продолжает выполняться по ошибке. Если переменная предпочтения установлена для остановки, задание приостанавливается на ошибке.  |
| Приостановка |Применяется только к [графическим и PowerShell Workflow runbooks.](automation-runbook-types.md) Система пытается приостановить задание по запросу пользователя. Модуль Runbook должен достичь следующей контрольной точки, прежде чем задание может быть приостановлено. Если он уже прошел свой последний контрольно-пропускной пункт, он завершается, прежде чем он может быть приостановлен. |

### <a name="viewing-job-status-from-the-azure-portal"></a>Просмотр состояний заданий на портале Azure

Вы можете просмотреть сводку по состоянию всех заданий Runbook или просмотреть подробные сведения об определенном задании на портале Azure. Вы также можете настроить интеграцию с рабочей областью Log Analytics для пересылки состояния задания и потоков задания модуля Runbook. Для получения дополнительной информации об интеграции [Forward job status and job streams from Automation to Azure Monitor logs](automation-manage-send-joblogs-log-analytics.md)с журналами Azure Monitor см.

Справа от выбранной учетной записи Автоматизация вы можете увидеть резюме всех заданий runbook под плиткой **Статистики Работы.**

![Элемент "Статистика по заданиям"](./media/automation-runbook-execution/automation-account-job-status-summary.png)

Эта плитка отображает графическое и графическое представление состояния задания для каждого выполненного задания.

Щелкните плитку, чтобы открылась страница Задания, на которой представлен сводный список всех выполненных заданий. На этой странице отображается статус, имя runbook, время начала и время завершения каждого задания.

![Страница "Задания" учетной записи службы автоматизации](./media/automation-runbook-execution/automation-account-jobs-status-blade.png)

Вы можете отфильтровать список заданий, выбрав **задания фильтра.** Фильтр на определенном runbook, статус работы, или выбор из списка выпадающих, и обеспечить диапазон времени для поиска.

![Статус задания фильтра](./media/automation-runbook-execution/automation-account-jobs-filter.png)

Кроме того, можно просмотреть сведения о сводных вакансиях для конкретного сборника, выбрав эту книгу из страницы Runbooks в учетной записи Автоматизации, а затем выбрав плитку **Вакансии.** В этом действии представлена страница Вакансии. Отсюда вы можете нажать на запись задания, чтобы просмотреть ее детали и выход.

![Страница "Задания" учетной записи службы автоматизации](./media/automation-runbook-execution/automation-runbook-job-summary-blade.png)

### <a name="viewing-the-job-summary"></a>Просмотр резюме задания

Резюме вакансий, описанное выше, позволяет посмотреть на список всех заданий, которые были созданы для конкретного runbook и их последнего статуса. Чтобы увидеть подробную информацию и выход для задания, щелкните его имя в списке. Подробное представление задания включает значения параметров runbook, которые были предоставлены этому заданиям.

Чтобы просмотреть задания для модуля Runbook, выполните следующие шаги.

1. На портале Azure щелкните **Автоматизация**, а затем выберите имя учетной записи службы автоматизации.
2. Из концентратора выберите **Runbooks** под **автоматизацией процессов.**
3. На странице Runbooks выберите книгу из списка.
3. На странице для выбранного модуля Runbook щелкните плитку **Задания**.
4. Нажмите на одно из заданий в списке и просмотрите его детали и выход на странице данных о работе runbook.

### <a name="retrieving-job-status-using-powershell"></a>Получение статуса задания с помощью PowerShell

Используйте `Get-AzAutomationJob` cmdlet для получения заданий, созданных для свекольного сборника, и деталей конкретной работы. Если вы начинаете runbook `Start-AzAutomationRunbook`с использованием PowerShell, он возвращает полученную работу. Используйте [Get-AzAutomationJobOutput](https://docs.microsoft.com/powershell/module/Az.Automation/Get-AzAutomationJobOutput?view=azps-3.5.0) для получения результатов работ.

Следующий пример получает последнее задание для примера runbook и отображает его статус, значения, предусмотренные для параметров runbook, и выход задания.

```azurepowershell-interactive
$job = (Get-AzAutomationJob –AutomationAccountName "MyAutomationAccount" `
–RunbookName "Test-Runbook" -ResourceGroupName "ResourceGroup01" | sort LastModifiedDate –desc)[0]
$job.Status
$job.JobParameters
Get-AzAutomationJobOutput -ResourceGroupName "ResourceGroup01" `
–AutomationAccountName "MyAutomationAcct" -Id $job.JobId –Stream Output
```

Следующий пример получает выход для конкретного задания и возвращает каждую запись. Если есть исключение для одной из записей, скрипт записывает исключение вместо значения. Такое поведение полезно, так как исключения могут предоставить дополнительную информацию, которая не может быть зарегистрирована нормально во время вывода.

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

## <a name="getting-details-from-the-activity-log"></a>Получение подробной информации из журнала activity

Вы можете получить данные runbook, такие как человек или учетную запись, которая запустила runbook, из журнала активности для учетной записи автоматизации. Следующий пример PowerShell предоставляет последнему пользователю для запуска указанной runbook.

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

## <a name="sharing-resources-among-runbooks"></a><a name="fair-share"></a>Совместное использование ресурсов между runbooks

Чтобы поделиться ресурсами между всеми запущенными книгами в облаке, Azure Automation временно разгружает или останавливает любую работу, которая работает более трех часов. Вакансии для [runbooks PowerShell](automation-runbook-types.md#powershell-runbooks) и [Runbooks Python](automation-runbook-types.md#python-runbooks) прекращаются и не перезапускаются, а статус задания прекращается.

Для длительных задач рекомендуется использовать гибридный Runbook Worker. Гибридные рабочие роли Runbook не ограничены равномерным распределением и могут выполняться сколько угодно. Другие [границы](../azure-resource-manager/management/azure-subscription-service-limits.md#automation-limits) заданий применимы к песочницам Azure и к гибридным рабочим ролям Runbook. В то время как Hybrid Runbook Workers не ограничены 3-часовым лимитом справедливой доли, следует разработать runbooks для работы на рабочих, которые поддерживают перезагрузки из неожиданных проблем локальной инфраструктуры.

Другой вариант заключается в оптимизации runbook с помощью детских книг. Например, в службе запуска запуска может цикл и та же функция на нескольких ресурсах, например в работе базы данных на нескольких базах данных. Вы можете переместить эту функцию в [детскую книгу](automation-child-runbooks.md) и позвонить в свой runbook с помощью. `Start-AzAutomationRunbook` Детские runbooks выполняются параллельно в отдельных процессах.

Использование детских runbooks уменьшает общее количество времени для родительской runbook для завершения. Ваша книга может `Get-AzAutomationJob` использовать cmdlet для проверки статуса работы для детской книги, если она все еще выполняет операции после завершения ребенка.

## <a name="next-steps"></a>Следующие шаги

* Чтобы узнать больше о методах, которые можно использовать для запуска [Starting a runbook in Azure Automation](automation-starting-a-runbook.md)запуска сборника в Azure Automation, см.
* Для получения дополнительной информации о PowerShell, включая языковые справочные и учебные модули, обратитесь к [документам PowerShell](https://docs.microsoft.com/powershell/scripting/overview).
