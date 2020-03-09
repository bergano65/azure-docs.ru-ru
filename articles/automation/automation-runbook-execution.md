---
title: Выполнение модуля Runbook в службе автоматизации Azure
description: Рассматривается обработка модуля Runbook в службе автоматизации Azure.
services: automation
ms.subservice: process-automation
ms.date: 04/04/2019
ms.topic: conceptual
ms.openlocfilehash: 6a51e57bd2411c19dfd5e7740f9e918d0bd09e27
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/05/2020
ms.locfileid: "78372610"
---
# <a name="runbook-execution-in-azure-automation"></a>Выполнение модуля Runbook в службе автоматизации Azure

Модули Runbook выполняются на основе определенной в них логики. Если работа модуля Runbook прерывается, он перезапускается. Это поведение требует написания модулей Runbook, которые поддерживают перезапуск при возникновении временных проблем.

При запуске модуля Runbook в службе автоматизации Azure создается задание. Задание — это один экземпляр выполнения модуля Runbook. Каждое задание имеет доступ к ресурсам Azure, делая подключение к подписке Azure. Задание имеет доступ к ресурсам в центре обработки данных, только если эти ресурсы доступны из общедоступного облака.

Служба автоматизации Azure назначает рабочую роль для выполнения каждого задания во время выполнения модуля Runbook. Пока рабочие роли используются многими учетными записями Azure, задания от различных учетных записей службы автоматизации изолируются друг от друга. Вы не можете контролировать, какие рабочие роли запрашивают ваш запрос на задание.

При просмотре списка модулей Runbook в портал Azure отображается состояние каждого задания, запущенного для каждого модуля Runbook. Служба автоматизации Azure хранит журналы заданий не более 30 дней. 

На следующей схеме показан жизненный цикл задания Runbook для [модулей Runbook PowerShell](automation-runbook-types.md#powershell-runbooks), [графических модулей Runbook](automation-runbook-types.md#graphical-runbooks)и [модулей Runbook рабочего процесса PowerShell](automation-runbook-types.md#powershell-workflow-runbooks).

![Состояния задания — рабочий процесс PowerShell](./media/automation-runbook-execution/job-statuses.png)

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-dsr-and-stp-note.md)]

>[!NOTE]
>Эта статья была изменена и теперь содержит сведения о новом модуле Az для Azure PowerShell. Вы по-прежнему можете использовать модуль AzureRM, исправления ошибок для которого будут продолжать выпускаться как минимум до декабря 2020 г. Дополнительные сведения о совместимости модуля Az с AzureRM см. в статье [Introducing the new Azure PowerShell Az module](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0) (Знакомство с новым модулем Az для Azure PowerShell). Инструкции по установке AZ Module в гибридной рабочей роли Runbook см. в статье [Установка модуля Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0). Для учетной записи службы автоматизации можно обновить модули до последней версии, используя [обновление модулей Azure PowerShell в службе автоматизации Azure](automation-update-azure-modules.md).

## <a name="where-to-run-your-runbooks"></a>Место запуска модулей Runbook

Модули Runbook в службе автоматизации Azure могут выполняться в песочнице Azure или [гибридной рабочей роли Runbook](automation-hybrid-runbook-worker.md). Большинство модулей Runbook можно легко запустить в песочнице Azure, общей среде, которую могут использовать несколько заданий. Для заданий, использующих одну и ту же песочницу, применяются ее ограничения ресурсов.

Вы можете использовать гибридную рабочую роль Runbook для запуска модулей Runbook непосредственно на компьютере, на котором размещается роль, а также в локальных ресурсах в среде. Служба автоматизации Azure хранит и управляет модулями Runbook, а затем доставляет их одному или нескольким назначенным компьютерам.

В следующей таблице перечислены некоторые задачи выполнения Runbook с рекомендуемой средой выполнения, перечисленными для каждого из них.

|Задача|Лучший вариант|Примечания|
|---|---|---|
|Интеграция с ресурсами Azure|Песочница Azure|При размещении в Azure проверка подлинности выполняется проще. Если вы используете гибридную рабочую роль Runbook на виртуальной машине Azure, вы можете использовать [управляемые удостоверения для ресурсов Azure](automation-hrw-run-runbooks.md#managed-identities-for-azure-resources).|
|Получите оптимальную производительность для управления ресурсами Azure|Песочница Azure|Сценарий выполняется в той же среде с меньшей задержкой.|
|Снижение операционных расходов|Песочница Azure|Нет никаких затрат на вычислительные ресурсы и виртуальных машин.|
|Выполнение долго выполняемого скрипта|Гибридная рабочая роль Runbook|В песочницах Azure есть [ограничения на ресурсы](../azure-resource-manager/management/azure-subscription-service-limits.md#automation-limits).|
|Взаимодействие с локальными службами|Гибридная рабочая роль Runbook|Может иметь доступ непосредственно к компьютеру размещения.|
|Требовать программное обеспечение и исполняемые файлы стороннего производителя|Гибридная рабочая роль Runbook|Вы управляете операционной системой и можете устанавливать программное обеспечение.|
|Мониторинг файла или папки с модулем Runbook|Гибридная рабочая роль Runbook|Используйте [задачу наблюдателя](automation-watchers-tutorial.md) в гибридной рабочей роли Runbook.|
|Запуск скрипта, интенсивно использующий ресурсы|Гибридная рабочая роль Runbook| В песочницах Azure есть [ограничения на ресурсы](../azure-resource-manager/management/azure-subscription-service-limits.md#automation-limits).|
|Использование модулей с конкретными требованиями| Гибридная рабочая роль Runbook|Некоторые примеры.</br> WinSCP — зависимость от WinSCP. exe </br> Иисадминистратион — зависимость от включения служб IIS.|
|Установка модуля с помощью установщика|Гибридная рабочая роль Runbook|Модули для песочницы должны поддерживать копирование.|
|Используйте Runbook или модули, для которых требуется .NET Framework версия, отличающаяся от версии 4.7.2|Гибридная рабочая роль Runbook|Песочницы службы автоматизации имеют .NET Framework 4.7.2, и их невозможно обновить.|
|Запуск сценариев, требующих повышения прав|Гибридная рабочая роль Runbook|"Песочницы" не допускают повышение прав. С помощью гибридной рабочей роли Runbook можно отключить UAC и использовать **командлет Invoke-Command** при выполнении команды, требующей повышения прав.|
|Запуск сценариев, требующих доступа к WMI|Гибридная рабочая роль Runbook|Задания, выполняемые в песочницах в облаке, не имеют доступа к инструментарию WMI. |

## <a name="runbook-behavior"></a>Поведение модуля Runbook

### <a name="creating-resources"></a>Создание ресурсов

Если модуль Runbook создает ресурс, он должен проверить, существует ли уже ресурс, прежде чем пытаться его создать. Ниже приведен простой пример.

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

### <a name="supporting-time-dependent-scripts"></a>Поддержка скриптов, зависящих от времени

Модули Runbook должны быть надежными и способны обрабатывать временные ошибки, которые могут привести к перезапуску или сбою. В случае сбоя Runbook служба автоматизации Azure повторяет ее.

Если модуль Runbook обычно работает в пределах ограничения по времени, то для проверки времени выполнения скрипт должен реализовывать логику. Эта проверка обеспечивает выполнение таких операций, как запуск, завершение работы или масштабирование, только в определенное время.

> [!NOTE]
> Местное время для процесса "песочницы" Azure задается в формате UTC. Этот факт следует учитывать при вычислении даты и времени в модулях Runbook.

### <a name="tracking-progress"></a>Отслеживание хода выполнения

Рекомендуется создавать модули Runbook в виде модульной природы, структурировать логику Runbook, чтобы ее можно было повторно использовать и перезапустить. Отслеживание хода выполнения в модуле Runbook — хороший способ гарантировать, что логика модуля Runbook будет выполнена правильно при возникновении проблем. Ход выполнения Runbook можно отслеживать с помощью внешнего источника, например учетной записи хранения, базы данных или общих файлов. Вы можете создать логику в модуле Runbook, чтобы сначала проверить состояние последнего выполненного действия. Затем, основываясь на результатах проверки, логика может пропускать или продолжать конкретные задачи в модуле Runbook.

### <a name="preventing-concurrent-jobs"></a>Предотвращение параллельных заданий

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

### <a name="working-with-multiple-subscriptions"></a>Использование нескольких подписок

Чтобы работать с несколькими подписками, модуль Runbook должен использовать командлет [Disable-азконтекстаутосаве](https://docs.microsoft.com/powershell/module/Az.Accounts/Disable-AzContextAutosave?view=azps-3.5.0) , чтобы убедиться, что контекст проверки подлинности не получен из другого модуля Runbook, выполняющегося в той же песочнице. Модуль Runbook также использует параметр *азконтекст* в командлетах AZ Module и передает его правильному контексту.

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

В этом разделе описываются некоторые способы управления исключениями или временные проблемы в модулях Runbook.

#### <a name="erroractionpreference"></a>ErrorActionPreference

Переменная [ErrorActionPreference](/powershell/module/microsoft.powershell.core/about/about_preference_variables#erroractionpreference) определяет, как PowerShell реагирует на устранимую ошибку. Завершающие ошибки всегда завершаются и не затрагиваются *ErrorActionPreference*.

Когда модуль Runbook использует *ErrorActionPreference*, обычно Неустранимая ошибка, например **паснотфаунд** из командлета **Get-ChildItem** , останавливает выполнение модуля Runbook. В следующем примере показано использование *ErrorActionPreference*. Последняя команда **Write-Output** не выполняется, так как скрипт останавливается.

```powershell-interactive
$ErrorActionPreference = 'Stop'
Get-Childitem -path nofile.txt
Write-Output "This message will not show"
```

#### <a name="try-catch-finally"></a>Попробуйте catch finally

[Try catch finally](/powershell/module/microsoft.powershell.core/about/about_try_catch_finally) используется в сценариях PowerShell для решения завершающих ошибок. Скрипт может использовать этот механизм для перехвата конкретных исключений или общих исключений. Оператор **catch** следует использовать для отслеживания или попыток обрабатывать ошибки. В следующем примере предпринимается попытка скачать несуществующий файл. Он перехватывает исключение System .NET., за исключением исключения, и возвращает последнее значение для любого другого исключения.

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

Для создания завершающей ошибки можно использовать [throw](/powershell/module/microsoft.powershell.core/about/about_throw) . Этот механизм может быть полезен при определении собственной логики в модуле Runbook. Если сценарий соответствует критерию, который должен его прерывать, он может использовать оператор **throw** для его отмены. В следующем примере эта инструкция используется для отображения требуемого параметра функции.

```powershell-interactive
function Get-ContosoFiles
{
  param ($path = $(throw "The Path parameter is required."))
  Get-ChildItem -Path $path\*.txt -recurse
}
```

### <a name="using-executables-or-calling-processes"></a>Использование исполняемых файлов или вызов процессов

Модули Runbook, работающие в изолированных средах Azure, не поддерживают вызывающие процессы, такие как исполняемые файлы (**exe** ) или подпроцессы.  Причина в том, что Песочница Azure — это общий процесс в контейнере, который может не иметь доступа ко всем базовым API. Для сценариев, в которых требуется программное обеспечение сторонних разработчиков или вызовы подпроцессов, рекомендуется выполнить модуль Runbook в [гибридной рабочей роли Runbook](automation-hybrid-runbook-worker.md).

### <a name="accessing-device-and-application-characteristics"></a>Доступ к характеристикам устройств и приложений

Задания Runbook, которые выполняются в изолированных средах Azure, не имеют доступа к характеристикам устройства или приложения. Наиболее распространенным API-интерфейсом, используемым для запроса метрик производительности в Windows, является WMI, с некоторыми общими метриками использования памяти и ЦП. Однако не имеет значения, какой интерфейс API используется, так как задания, выполняемые в облаке, не имеют доступа к реализации Майкрософт для управления предприятием на основе веб-служб (WBEM). Эта платформа построена на основе модель CIM (CIM), предоставляя отраслевые стандарты для определения характеристик устройств и приложений.

## <a name="handling-errors"></a>Обработка ошибок

Модули Runbook должны поддерживать обработку ошибок. В PowerShell есть два типа ошибок: остановка и незавершение. Прекращение обработки ошибок останавливает выполнение модуля Runbook при их возникновении. Модуль Runbook останавливается с состоянием задания **Failed**.

Неустранимые ошибки позволяют продолжить выполнение сценария даже после его появления. Пример неустранимой ошибки — это событие, которое происходит, когда модуль Runbook использует командлет **Get-ChildItem** с несуществующим путем. PowerShell видит, что путь не существует, выдает ошибку и переходит к следующей папке. Ошибка в этом случае не устанавливает состояние состояния задания Runbook в значение **Failed**, и задание может даже быть завершено. Чтобы выполнить принудительную остановку модуля Runbook при неустранимой ошибке, вы можете использовать `-ErrorAction Stop` в командлете.

## <a name="handling-jobs"></a>Обработка заданий

Среду выполнения можно повторно использовать для заданий из той же учетной записи службы автоматизации. В одном модуле Runbook может быть запущено множество заданий одновременно. Чем больше заданий выполняются одновременно, тем вероятнее, что они будут отправлены в одну и ту же песочницу.

Задания, выполняемые в одном и том же процессе "песочницы", могут влиять друг на друга. Одним из примеров является выполнение командлета **Disconnect-азаккаунт** . Выполнение этого командлета отключает каждое задание Runbook в общем изолированном процессе.

Задания PowerShell, запущенные из модуля Runbook, который работает в песочнице Azure, могут не работать в полном языковом режиме. Дополнительные сведения о языковых режимах PowerShell см. в статье о [языковых режимах PowerShell](/powershell/module/microsoft.powershell.core/about/about_language_modes). Дополнительные сведения о взаимодействии с заданиями в службе автоматизации Azure см. в статье [Получение состояния задания с помощью PowerShell](#retrieving-job-status-using-powershell).

### <a name="job-statuses"></a>Состояния заданий

В следующей таблице описаны возможные состояния задания.

| Состояние | Описание |
|:--- |:--- |
| Завершено |Задание успешно выполнено. |
| Ошибка |Не удалось выполнить компиляцию графического модуля Runbook или рабочего процесса PowerShell. Не удалось запустить Runbook скрипта PowerShell, или задание имело исключение. См. раздел [типы Runbook службы автоматизации Azure](automation-runbook-types.md).|
| Задание не выполнено. Ожидание ресурсов. |Задание не выполнено, так как задание превысило ограничение [доли](#fair-share) три раза и было запущено с той же контрольной точки или с момента запуска модуля Runbook. |
| Поставлено в очередь |Задание ожидает, пока ресурсы в сотруднике службы автоматизации станут доступны, чтобы его можно было запустить. |
| Запуск |Задание было назначено рабочей роли, и система его запускает. |
| Возобновление |Система возобновляет задание после его приостановки. |
| Запущена |Задание выполняется. |
| Задание выполняется. Ожидание ресурсов. |Задание выгружено, так как оно достигло предельного количества ресурсов. Задание будет возобновлено позже с его последней контрольной точки. |
| Остановлена |Задание было остановлено пользователем до его завершения. |
| Stopping |Система останавливает задание. |
| Suspended |Применяется только к [графическим модулям Runbook и рабочим процессам PowerShell](automation-runbook-types.md) . Задание было приостановлено пользователем, системой или с помощью команды в модуле Runbook. Если у модуля Runbook нет контрольной точки, он начинается с начала. Если в модуле есть контрольная точка, задание возобновится с последней контрольной точки. Система приостанавливает модуль Runbook только при возникновении исключения. По умолчанию переменная *ErrorActionPreference* имеет значение **Continue**, что означает, что задание продолжает выполняться при возникновении ошибки. Если для привилегированной переменной задано значение **остановить**, задание приостанавливается при возникновении ошибки.  |
| Приостановка |Применяется только к [графическим модулям Runbook и рабочим процессам PowerShell](automation-runbook-types.md) . Система пытается приостановить задание по запросу пользователя. Модуль Runbook должен достичь следующей контрольной точки, прежде чем задание может быть приостановлено. Если она уже прошла последнюю контрольную точку, она завершится, прежде чем ее можно будет приостановить. |

### <a name="viewing-job-status-from-the-azure-portal"></a>Просмотр состояний заданий на портале Azure

Вы можете просмотреть сводку по состоянию всех заданий Runbook или просмотреть подробные сведения об определенном задании на портале Azure. Вы также можете настроить интеграцию с рабочей областью Log Analytics для пересылки состояния задания и потоков задания модуля Runbook. Дополнительные сведения об интеграции с журналами Azure Monitor см. [в разделе Пересылка состояния задания и потоков заданий из службы автоматизации в журналы Azure Monitor](automation-manage-send-joblogs-log-analytics.md).

Справа от выбранной учетной записи службы автоматизации можно просмотреть сводку по всем заданиям Runbook на плитке **Статистика задания** .

![Элемент "Статистика по заданиям"](./media/automation-runbook-execution/automation-account-job-status-summary.png)

На этой плитке отображается количество и графическое представление состояния задания для каждого выполненного задания.

Щелкните плитку, чтобы открылась страница **Задания**, на которой представлен сводный список всех выполненных заданий. На этой странице отображаются сведения о состоянии, имени Runbook, времени начала и времени выполнения каждого задания.

![Страница "Задания" учетной записи службы автоматизации](./media/automation-runbook-execution/automation-account-jobs-status-blade.png)

Список заданий можно отфильтровать, выбрав пункт **Фильтровать задания**. Выполните фильтрацию по определенному модулю Runbook, состоянию задания или выбору из раскрывающегося списка и укажите диапазон времени для поиска.

![Фильтрация состояния задания](./media/automation-runbook-execution/automation-account-jobs-filter.png)

Кроме того, можно просмотреть сводные данные о задании для конкретного модуля Runbook, выбрав его на странице **модули Runbook** в учетной записи службы автоматизации, а затем щелкнув плитку **задания** . Это действие представляет страницу **задания** . Здесь можно щелкнуть запись задания, чтобы просмотреть ее сведения и выходные данные.

![Страница "Задания" учетной записи службы автоматизации](./media/automation-runbook-execution/automation-runbook-job-summary-blade.png)

### <a name="viewing-the-job-summary"></a>Просмотр сводки по заданию

Описанная выше сводка по заданию позволяет просмотреть список всех заданий, созданных для конкретного модуля Runbook, и их Последнее состояние. Чтобы просмотреть подробные сведения и выходные данные для задания, щелкните его имя в списке. Подробное представление задания содержит значения параметров модуля Runbook, которые были предоставлены этому заданию.

Чтобы просмотреть задания для модуля Runbook, выполните следующие шаги.

1. На портале Azure щелкните **Автоматизация**, а затем выберите имя учетной записи службы автоматизации.
2. В центре выберите **модули Runbook** в разделе **Автоматизация процессов**.
3. На странице **модули Runbook** выберите Runbook из списка.
3. На странице для выбранного модуля Runbook щелкните плитку **Задания**.
4. Щелкните одно из заданий в списке и просмотрите его сведения и выходные данные на странице сведений о задании Runbook.

### <a name="retrieving-job-status-using-powershell"></a>Получение состояния задания с помощью PowerShell

Используйте командлет **Get-азаутоматионжоб** , чтобы получить задания, созданные для модуля Runbook, и сведения о конкретном задании. При запуске модуля Runbook с помощью PowerShell с параметром **Start-азаутоматионрунбук**возвращается результирующее задание. Чтобы получить выходные данные задания, используйте [Get-азаутоматионжобаутпут](https://docs.microsoft.com/powershell/module/Az.Automation/Get-AzAutomationJobOutput?view=azps-3.5.0) .

В следующем примере возвращается последнее задание для примера модуля Runbook и отображается его состояние, значения, указанные для параметров модуля Runbook, и выходные данные задания.

```azurepowershell-interactive
$job = (Get-AzAutomationJob –AutomationAccountName "MyAutomationAccount" `
–RunbookName "Test-Runbook" -ResourceGroupName "ResourceGroup01" | sort LastModifiedDate –desc)[0]
$job.Status
$job.JobParameters
Get-AzAutomationJobOutput -ResourceGroupName "ResourceGroup01" `
–AutomationAccountName "MyAutomationAcct" -Id $job.JobId –Stream Output
```

В следующем примере извлекается результат для конкретного задания и возвращается каждая запись. Если существует исключение для одной из записей, скрипт записывает исключение вместо значения. Такое поведение полезно, так как исключения могут предоставлять дополнительные сведения, которые могут не регистрироваться обычным образом во время вывода.

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

## <a name="getting-details-from-the-activity-log"></a>Получение сведений из журнала действий

Сведения о Runbook, такие как пользователь или учетная запись, запускающие Runbook, можно получить из журнала действий для учетной записи службы автоматизации. В следующем примере PowerShell представлен последний пользователь для запуска указанного модуля Runbook.

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

## <a name="fair-share"></a>Совместное использование ресурсов модулями Runbook

Для совместного использования ресурсов всеми модулями Runbook в облаке служба автоматизации Azure временно выгружает или останавливает любое задание, которое выполнялось более трех часов. Задания для [модулей Runbook PowerShell](automation-runbook-types.md#powershell-runbooks) и [модулей Runbook Python](automation-runbook-types.md#python-runbooks) остановлены, не перезапускаются и состояние задания **останавливается**.

Для выполнения длительных задач рекомендуется использовать гибридную рабочую роль Runbook. Гибридные рабочие роли Runbook не ограничены равномерным распределением и могут выполняться сколько угодно. Другие [границы](../azure-resource-manager/management/azure-subscription-service-limits.md#automation-limits) заданий применимы к песочницам Azure и к гибридным рабочим ролям Runbook. Хотя гибридные рабочие роли Runbook не ограничиваются ограничением в 3 часа, следует разрабатывать модули Runbook для рабочих процессов, которые поддерживают перезапуск, от непредвиденных проблем с локальной инфраструктурой.

Другой вариант — оптимизировать модуль Runbook с помощью дочерних модулей Runbook. Например, модуль Runbook может пройти одну и ту же функцию для нескольких ресурсов, например операцию с базой данных в нескольких базах данных. Эту функцию можно переместить в [дочерний модуль Runbook](automation-child-runbooks.md) , а модуль Runbook вызвать его с помощью команды **Start-азаутоматионрунбук**. Дочерние модули Runbook выполняются параллельно в отдельных процессах.

Использование дочерних модулей Runbook сокращает общее время, необходимое для выполнения родительского модуля Runbook. Модуль Runbook может использовать командлет **Get-азаутоматионжоб** , чтобы проверить состояние задания для дочернего модуля Runbook, если по-прежнему имеются операции, выполняемые после завершения дочернего элемента.

## <a name="next-steps"></a>Следующие шаги

* Дополнительные сведения о методах, которые можно использовать для запуска модуля Runbook в службе автоматизации Azure, см. в статье [Запуск модуля Runbook в службе автоматизации Azure](automation-starting-a-runbook.md).
* Дополнительные сведения о PowerShell, включая Справочник по языку и обучающие модули, см. в документации по [PowerShell](https://docs.microsoft.com/powershell/scripting/overview).
