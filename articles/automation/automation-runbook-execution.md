---
title: Выполнение модуля Runbook в службе автоматизации Azure
description: Рассматривается обработка модуля Runbook в службе автоматизации Azure.
services: automation
ms.service: automation
ms.subservice: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 04/03/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 9d4661f6c975265ec710b29a8a05cc7ef41b4011
ms.sourcegitcommit: b4ad15a9ffcfd07351836ffedf9692a3b5d0ac86
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/05/2019
ms.locfileid: "59057427"
---
# <a name="runbook-execution-in-azure-automation"></a>Выполнение модуля Runbook в службе автоматизации Azure

При запуске модуля Runbook в службе автоматизации Azure создается задание. Задание — это одиночный выполняемый экземпляр модуля Runbook. Рабочий процесс службы автоматизации Azure назначается для выполнения каждого задания. Пока рабочие роли используются многими учетными записями Azure, задания от различных учетных записей службы автоматизации изолируются друг от друга. Вы не контролируете, какая рабочая роль обслуживает запрос для вашего задания. В одном модуле Runbook может быть запущено множество заданий одновременно. Можно повторно использовать среду выполнения для заданий из одной учетной записи службы автоматизации. Чем больше заданий выполняются одновременно, тем вероятнее, что они будут отправлены в одну и ту же песочницу. Задания, которые выполняются в рамках одного процесса песочницы, могут влиять друг на друга (как, например, при выполнении командлета `Disconnect-AzureRMAccount`). Если запустить этот командлет, то все задания runbook в общем процессе песочницы будут отключены. При просмотре списка модулей Runbook на портале Azure в нем отобразится список состояний всех заданий для каждого модуля Runbook. Вы можете просматривать список заданий для каждого модуля Runbook и отслеживать состояние каждого из них. Журналы заданий хранятся не более 30 дней. Описание различных состояний заданий см. в разделе [Состояния заданий](#job-statuses).

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-dsr-and-stp-note.md)]

На следующей схеме показан жизненный цикл задания runbook для [модули Runbook PowerShell](automation-runbook-types.md#powershell-runbooks), [графических модулей Runbook](automation-runbook-types.md#graphical-runbooks) и [Runbook рабочих процессов PowerShell](automation-runbook-types.md#powershell-workflow-runbooks).

![Состояния задания — рабочий процесс PowerShell](./media/automation-runbook-execution/job-statuses.png)

Задания имеют доступ к ресурсам Azure за счет подключения к ресурсам подписки Azure. Задания имеют доступ только к ресурсам в центре обработки данных, если эти ресурсы доступны из общедоступного облака.

## <a name="where-to-run-your-runbooks"></a>Место запуска модулей Runbook

Модули Runbook в службе автоматизации Azure можно запускать в песочнице Azure или в [гибридной рабочей роли Runbook](automation-hybrid-runbook-worker.md). Песочница представляет собой общую среду в Azure, которую могут использовать несколько заданий. Для заданий, использующих одну и ту же песочницу, применяются ее ограничения ресурсов. Гибридные рабочие роли Runbook можно запускать модули Runbook непосредственно на компьютере, на котором размещается роль и с ресурсами в среде, чтобы управлять этими локальными ресурсами. Для хранения модулей Runbook и управления ими используется служба автоматизации Azure, затем они передаются на один или несколько назначенных компьютеров. В большинстве модулей Runbook можно легко запускать в "песочницах" Azure. Есть определенные сценарии, в которых рекомендуется выполнять модуль Runbook в гибридном компоненте Runbook, а не в песочнице Azure. Список примеров сценариев см. в следующей таблице:

|Задача|Лучший вариант|Примечания|
|---|---|---|
|Интеграция с ресурсами Azure|Песочница Azure|Размещается в Azure, аутентификация проще. Если на виртуальной машине Azure используется гибридная рабочая роль Runbook, вы можете использовать [управляемые удостоверения для ресурсов Azure](automation-hrw-run-runbooks.md#managed-identities-for-azure-resources).|
|Оптимальная производительность для управления ресурсами Azure|Песочница Azure|Скрипт выполняется в той же среде, которая в свою очередь имеет меньше задержка|
|Снижение операционных расходов|Песочница Azure|Отсутствуют накладные расходы на вычисления, нет необходимости в виртуальной машине|
|Длительный сценарий|гибридный компонент Runbook Worker|Песочницы Azure имеют [ограничения на ресурсы](../azure-subscription-service-limits.md#automation-limits)|
|Взаимодействие с локальными службами|гибридный компонент Runbook Worker|Есть непосредственный доступ к хост-компьютеру|
|Требуется стороннее программное обеспечение и исполняемые файлы|гибридный компонент Runbook Worker|Вы управляете операционной системой и можете устанавливать программное обеспечение|
|Мониторинг файла или папки с модулем Runbook|гибридный компонент Runbook Worker|Используйте [задачу "Наблюдатель"](automation-watchers-tutorial.md) на гибридной рабочей роли Runbook|
|Ресурсоемкий сценарий|гибридный компонент Runbook Worker| Песочницы Azure имеют [ограничения на ресурсы](../azure-subscription-service-limits.md#automation-limits)|
|Использование модулей с определенными требованиями| гибридный компонент Runbook Worker|Ниже приведены некоторые примеры.</br> **WinSCP** — зависимость от winscp.exe. </br> **IISAdministration** — необходимо включить IIS.|
|Установка модуля, для которого требуется установщик|гибридный компонент Runbook Worker|Модули для "песочницы" должно быть отображается|
|Использование модулей Runbook или модулей, для которых требуется платформа .NET Framework, отличная от 4.7.2|гибридный компонент Runbook Worker|В песочницах службы автоматизации установлена платформа .NET Framework 4.7.2, и ее невозможно обновить|
|Сценарии, требующие повышения прав|гибридный компонент Runbook Worker|"Песочницы" не допускают повышение прав. Чтобы решить эту проблему, используйте гибридной рабочей роли Runbook, и можно отключить контроль учетных Записей и использование `Invoke-Command` при запуске команды требуется повышение прав|
|Сценарии, требующие доступа к инструментарию WMI|гибридный компонент Runbook Worker|Заданий, выполняемых в "песочницах" облаке [не имеют доступ к WMI](#device-and-application-characteristics)|

## <a name="runbook-behavior"></a>Поведение модуля Runbook

Выполнение модулей Runbook основано на логике, которая определяется внутри них. Если работа модуля Runbook прерывается, он перезапускается. Для этого модули Runbook должны быть разработаны с поддержкой перезапуска при наличии временных проблем.

### <a name="creating-resources"></a>Создание ресурсов

Если ваш сценарий создает ресурсы, прежде чем создавать ресурсы, необходимо проверить, существуют ли они. В следующем фрагменте показан простой пример:

```powershell
$vmName = "WindowsVM1"
$resourceGroupName = "myResourceGroup"
$myCred = Get-AutomationPSCredential "MyCredential"
$vmExists = Get-AzureRmResource -Name $vmName -ResourceGroupName $resourceGroupName

if(!$vmExists)
    {
    Write-Output "VM $vmName does not exists, creating"
    New-AzureRmVM -Name $vmName -ResourceGroupName $resourceGroupName -Credential $myCred
    }
else
    {
    Write-Output "VM $vmName already exists, skipping"
    }
```

### <a name="time-dependant-scripts"></a>Зависящие от времени сценарии

Процесс разработки модулей Runbook должен быть очень тщательным. Как упоминалось ранее, модули Runbook должны быть разработаны таким образом, чтобы они были надежными и могли обрабатывать временные ошибки, которые могут привести к перезапуску или сбою модуля Runbook. В случае сбоя модуля runbook будет повторяться. Если модуль runbook обычно выполняется в рамках ограничения времени, логику для проверки времени выполнения, следует реализовать модуль runbook, чтобы операции, такие как начало, завершение работы и горизонтальное масштабирование, выполняются только в определенное время.

### <a name="tracking-progress"></a>Отслеживание хода выполнения

Рекомендуем создавать модули Runbook с модульной структурой. Это подразумевает структурирование логики в модуле Runbook таким образом, чтобы его можно было легко использовать повторно и перезапускать. Отслеживание хода выполнения в модуле runbook — хороший способ убедиться, что логика в модуле runbook выполняется правильно при наличии проблем. Отслеживать ход выполнения модуля Runbook можно с использованием внешнего источника, например учетных записей хранения, базы данных или общих файлов. Отслеживая состоянием во внешней, можно создать логику для первой проверки в модуле runbook состояние последнего действия, которое потребовалось runbook. Затем на основе результатов, либо пропустите или продолжить определенные задачи в модуле runbook.

### <a name="prevent-concurrent-jobs"></a>Предотвращение выполнения одновременных заданий

При выполнении нескольких заданий одновременно некоторые модули Runbook могут работать некорректно. В этом случае важно реализовать логику для проверки на наличие в модуле Runbook уже выполняющегося задания. В следующем примере показан простой пример того, как это можно сделать:

```powershell
# Authenticate to Azure
$connection = Get-AutomationConnection -Name AzureRunAsConnection
Connect-AzureRmAccount -ServicePrincipal -Tenant $connection.TenantID `
-ApplicationID $connection.ApplicationID -CertificateThumbprint $connection.CertificateThumbprint

$AzureContext = Select-AzureRmSubscription -SubscriptionId $connection.SubscriptionID

# Check for already running or new runbooks
$runbookName = "<RunbookName>"
$rgName = "<ResourceGroupName>"
$aaName = "<AutomationAccountName>"
$jobs = Get-AzureRmAutomationJob -ResourceGroupName $rgName -AutomationAccountName $aaName -RunbookName $runbookName -AzureRmContext $AzureContext

# If then check to see if it is already running
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

При разработке модулей Runbook, которые работают с несколькими подписками, ваш модуль runbook должен использовать [Disable-AzureRmContextAutosave](/powershell/module/azurerm.profile/disable-azurermcontextautosave) командлет, чтобы убедиться, что контекст проверки подлинности не извлекается из другого модуля runbook, который может быть выполняется в той же песочнице. Затем необходимо использовать `-AzureRmContext` параметр на вашей `AzureRM` командлетов и передать его в правильный контекст.

```powershell
# Ensures you do not inherit an AzureRMContext in your runbook
Disable-AzureRmContextAutosave –Scope Process

$Conn = Get-AutomationConnection -Name AzureRunAsConnection
Connect-AzureRmAccount -ServicePrincipal `
-Tenant $Conn.TenantID `
-ApplicationID $Conn.ApplicationID `
-CertificateThumbprint $Conn.CertificateThumbprint

$context = Get-AzureRmContext

$ChildRunbookName = 'ChildRunbookDemo'
$AutomationAccountName = 'myAutomationAccount'
$ResourceGroupName = 'myResourceGroup'

Start-AzureRmAutomationRunbook `
    -ResourceGroupName $ResourceGroupName `
    -AutomationAccountName $AutomationAccountName `
    -Name $ChildRunbookName `
    -DefaultProfile $context
```

### <a name="handling-exceptions"></a>Обработка исключений

При создании скриптов, важно иметь возможность обрабатывать исключения и потенциальных временные сбои. Вот несколько различных способов обработки исключений или временные неполадки с помощью модулей Runbook:

#### <a name="erroractionpreference"></a>$ErrorActionPreference

[$ErrorActionPreference](/powershell/module/microsoft.powershell.core/about/about_preference_variables#erroractionpreference) привилегированной переменной определяет, как PowerShell реагирует на устранимую ошибку. Прерывающие ошибки не подвержены `$ErrorActionPreference`, они всегда прекращает выполнение. С помощью `$ErrorActionPreference`, обычно неустранимой ошибки, такие как `PathNotFound` из `Get-ChildItem` командлет останавливает выполнение модуля runbook. В следующем примере показано использование `$ErrorActionPreference`. Конечный `Write-Output` строки никогда не будет выполнена, так как сценарий остановит.

```powershell-interactive
$ErrorActionPreference = 'Stop'
Get-Childitem -path nofile.txt
Write-Output "This message will not show"
```

#### <a name="try-catch-finally"></a>Try Catch, Finally

[Try Catch](/powershell/module/microsoft.powershell.core/about/about_try_catch_finally) используется в скриптах PowerShell при обработке прерывающие ошибки. С помощью Try Catch, можно перехватывать определенные исключения, или Общие. Оператор Catch должен быть используются для отслеживания ошибок или для обработки ошибки. В следующем примере предпринимается в том загрузить файл, который не существует. Он перехватывает `System.Net.WebException` исключение, если другое исключение, возвращается последнее значение.

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

[Исключение](/powershell/module/microsoft.powershell.core/about/about_throw) может использоваться для создания неустранимую ошибку. Это могут быть полезны при определении собственной логики в модуле runbook. При соблюдении определенных условий, следует остановить сценарий, можно использовать `throw` для остановки сценария. В следующем примере показан компьютера необходимо с помощью параметра функции `throw`.

```powershell-interactive
function Get-ContosoFiles
{
  param ($path = $(throw "The Path parameter is required."))
  Get-ChildItem -Path $path\*.txt -recurse
}
```

### <a name="using-executables-or-calling-processes"></a>Использование исполняемых файлов или вызов процессов

Модули Runbook, выполняемые в изолированной среде Azure не поддерживают вызывающий процессы (например, .exe или subprocess.call). Это обусловлено "песочницах" Azure — это общие процессы, которые выполняются в контейнерах, которые не могут иметь доступ ко всем базовый API. Для сценариев, где требуется стороннее программное обеспечение или вызов подпроцессов, мы рекомендуем выполнить модуль Runbook в [гибридной рабочей роли Runbook](automation-hybrid-runbook-worker.md).

### <a name="device-and-application-characteristics"></a>Характеристики устройств и приложений

Задания Runbook выполняются в "песочницах" Azure нет доступа к любой характеристики устройства или приложения. Наиболее распространенных API, используемый для запроса метрик производительности в Windows — WMI. Некоторые из этих общие метрики, использование памяти и ЦП. Тем не менее, не важно, что используется API. Заданий, выполняемых в облаке нет доступа к реализации Microsoft Web на основе управления предприятием (WBEM), которая построена на модели Common Information (CIM), которые являются отраслевые стандарты для определения характеристик устройства и приложений.

## <a name="job-statuses"></a>Состояния заданий

Следующая таблица содержит описание различных состояний задания. В PowerShell возникает два типа ошибок: устранимые и неустранимые. При устранимой ошибке для состояния модуля Runbook задается значение **Failed**. Если возникают неустранимые ошибки, сценарий все равно продолжает выполняться. Для примера неустранимой ошибки используется командлет `Get-ChildItem` с отсутствующим путем. PowerShell видит, что путь не существует, выдает ошибку и переходит к следующей папке. При этой ошибке для состояния модуля runbook не будет задаваться значение **Failed** и оно может быть помечено состоянием **Завершено**. Чтобы выполнить принудительную остановку модуля Runbook при неустранимой ошибке, вы можете использовать `-ErrorAction Stop` в командлете.

| Status | ОПИСАНИЕ |
|:--- |:--- |
| Завершено |Задание успешно выполнено. |
| Failed |Для [графических модулей Runbook и модулей Runbook рабочих процессов PowerShell](automation-runbook-types.md): не удалось скомпилировать модуль Runbook. Для [модулей Runbook сценариев PowerShell](automation-runbook-types.md): не удалось запустить Runbook или в задании возникло исключение. |
| Задание не выполнено. Ожидание ресурсов. |Задание не выполнено, так как задание превысило ограничение [доли](#fair-share) три раза и было запущено с той же контрольной точки или с момента запуска модуля Runbook. |
| Поставлено в очередь |Задание ожидает появления доступных ресурсов в очереди автоматизации, которые позволят запустить задание. |
| Запуск |Задание было назначено рабочей роли, и система его запускает. |
| Возобновление |Система возобновляет задание после его приостановки. |
| Выполнение |Задание выполняется. |
| Задание выполняется. Ожидание ресурсов. |Задание выгружено, поскольку превышено ограничение [доли](#fair-share) . Задание будет возобновлено позже с его последней контрольной точки. |
| Остановлено |Задание было остановлено пользователем до его завершения. |
| Остановка |Система останавливает задание. |
| Приостановлено |Задание было приостановлено пользователем, системой или с помощью команды в модуле Runbook. Если в модуле Runbook нет контрольной точки, задание начнется с начала модуля. Если в модуле есть контрольная точка, задание возобновится с последней контрольной точки. Модуль Runbook будет приостановлен системой только при возникновении исключения. По умолчанию переменная ErrorActionPreference имеет значение **Продолжить**, то есть задание продолжит работу при возникновении ошибки. Если для этой переменной задать значение **Остановить**, задание будет приостановлено при возникновении ошибки. Применяется только к [графическим модулям Runbook и модулям Runbook рабочих процессов PowerShell](automation-runbook-types.md) . |
| Приостановка |Система пытается приостановить задание по запросу пользователя. Модуль Runbook должен достичь следующей контрольной точки, прежде чем задание может быть приостановлено. Если последняя контрольная точка уже пройдена, задание будет завершено, прежде чем оно будет приостановлено. Применяется только к [графическим модулям Runbook и модулям Runbook рабочих процессов PowerShell](automation-runbook-types.md) . |

## <a name="viewing-job-status-from-the-azure-portal"></a>Просмотр состояний заданий на портале Azure

Вы можете просмотреть сводку по состоянию всех заданий Runbook или просмотреть подробные сведения об определенном задании на портале Azure. Вы также можете настроить интеграцию с рабочей областью Log Analytics для пересылки состояния задания и потоков задания модуля Runbook. Дополнительные сведения об интеграции журналов Azure Monitor см. в разделе [пересылка состояния задания и потоков заданий из службы автоматизации в Azure Monitor журналы](automation-manage-send-joblogs-log-analytics.md).

### <a name="automation-runbook-jobs-summary"></a>Сводные данные заданий Runbook службы автоматизации

В правой части выбранной учетной записи службы автоматизации можно посмотреть сводные данные обо всех заданиях Runbook на плитке **Статистика по заданиям**.

![Элемент "Статистика по заданиям"](./media/automation-runbook-execution/automation-account-job-status-summary.png)

В этом элементе отображается количество и графическое представление состояний всех выполненных заданий.

Щелкните плитку, чтобы открылась страница **Задания**, на которой представлен сводный список всех выполненных заданий. На этой странице отображаются: состояние, время начала и завершения выполнения.

![Страница "Задания" учетной записи службы автоматизации](./media/automation-runbook-execution/automation-account-jobs-status-blade.png)

Чтобы отфильтровать список заданий, выберите **Фильтровать задания** и укажите критерий фильтрации. Для этого введите определенное имя модуля Runbook, выберите состояние задания и диапазон дат и времени из раскрывающегося списка.

![Фильтрация по состоянию задания](./media/automation-runbook-execution/automation-account-jobs-filter.png)

Кроме того, для просмотра сводных данных о состоянии заданий определенного модуля Runbook можно выбрать этот модуль на странице **Модули Runbook** учетной записи службы автоматизации, а затем выбрать элемент **Задания**. Отобразится страница **Задания**, на которой можно выбрать конкретную запись и просмотреть подробные сведения о задании и его выходные данные.

![Страница "Задания" учетной записи службы автоматизации](./media/automation-runbook-execution/automation-runbook-job-summary-blade.png)

### <a name="job-summary"></a>Сводные данные задания

Вы можете просмотреть список всех заданий, созданных для конкретного модуля Runbook, и их последнее состояние. Можно фильтровать список по состоянию заданий и изменять диапазон дат для последнего изменения задания. Чтобы просмотреть подробные сведения и результаты выполнения для задания, щелкните его имя. Подробное представление задания содержит значения параметров Runbook, которые были указаны для этого задания.

Чтобы просмотреть задания для модуля Runbook, выполните следующие шаги.

1. На портале Azure щелкните **Автоматизация**, а затем выберите имя учетной записи службы автоматизации.
2. В центре выберите **Модули Runbook**, а затем на странице **Модули Runbook** выберите нужный модуль Runbook.
3. На странице для выбранного модуля Runbook щелкните плитку **Задания**.
4. Щелкните одно из заданий в списке, и вы сможете просмотреть сведения о нем и выходные данные на странице сведений о задании модуля Runbook.

## <a name="retrieving-job-status-using-powershell"></a>Получение состояния задания с помощью PowerShell

Чтобы получить сведения о созданных для модуля Runbook заданиях и о конкретном задании, воспользуйтесь командлетом [Get-AzureRmAutomationJob](https://docs.microsoft.com/powershell/module/azurerm.automation/get-azurermautomationjob). Если запустить модуль runbook с помощью PowerShell [Start-AzureRmAutomationRunbook](https://docs.microsoft.com/powershell/module/azurerm.automation/start-azurermautomationrunbook), то он возвращает результирующее задание. Получить выходные данные задания можно с помощью команды [Get-AzureRmAutomationJobOutput](https://docs.microsoft.com/powershell/module/azurerm.automation/get-azurermautomationjoboutput).

Приведенные ниже примеры команд получают сведения о последнем задании для примера runbook и отображают его состояние, значения параметров runbook, а также выходные данные этого задания.

```azurepowershell-interactive
$job = (Get-AzureRmAutomationJob –AutomationAccountName "MyAutomationAccount" `
–RunbookName "Test-Runbook" -ResourceGroupName "ResourceGroup01" | sort LastModifiedDate –desc)[0]
$job.Status
$job.JobParameters
Get-AzureRmAutomationJobOutput -ResourceGroupName "ResourceGroup01" `
–AutomationAccountName "MyAutomationAcct" -Id $job.JobId –Stream Output
```

В следующем примере извлекаются выходные данные для конкретного задания и возвращается каждая запись. Если для одной из записей возникает исключение, оно записывается вместо значения. Это полезно, так как исключения могут предоставлять дополнительные сведения, которые могли не записаться обычно во время вывода.

```azurepowershell-interactive
$output = Get-AzureRmAutomationJobOutput -AutomationAccountName <AutomationAccountName> -Id <jobID> -ResourceGroupName <ResourceGroupName> -Stream "Any"
foreach($item in $output)
{
    $fullRecord = Get-AzureRmAutomationJobOutputRecord -AutomationAccountName <AutomationAccountName> -ResourceGroupName <ResourceGroupName> -JobId <jobID> -Id $item.StreamRecordId
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

## <a name="get-details-from-activity-log"></a>Получение сведений из журнала действий

Другие сведения, например пользователь или учетная запись, которая была использована для запуска runbook, можно получить из журнала действий учетной записи службы автоматизации. В следующем примере PowerShell определяется последний пользователь, выполнивший указанный runbook.

```powershell-interactive
$SubID = "00000000-0000-0000-0000-000000000000"
$AutomationResourceGroupName = "MyResourceGroup"
$AutomationAccountName = "MyAutomationAccount"
$RunbookName = "MyRunbook"
$StartTime = (Get-Date).AddDays(-1)
$JobActivityLogs = Get-AzureRmLog -ResourceGroupName $AutomationResourceGroupName -StartTime $StartTime `
                                | Where-Object {$_.Authorization.Action -eq "Microsoft.Automation/automationAccounts/jobs/write"}

$JobInfo = @{}
foreach ($log in $JobActivityLogs)
{
    # Get job resource
    $JobResource = Get-AzureRmResource -ResourceId $log.ResourceId

    if ($JobInfo[$log.SubmissionTimestamp] -eq $null -and $JobResource.Properties.runbook.name -eq $RunbookName)
    { 
        # Get runbook
        $Runbook = Get-AzureRmAutomationJob -ResourceGroupName $AutomationResourceGroupName -AutomationAccountName $AutomationAccountName `
                                            -Id $JobResource.Properties.jobId | ? {$_.RunbookName -eq $RunbookName}

        # Add job information to hash table
        $JobInfo.Add($log.SubmissionTimestamp, @($Runbook.RunbookName,$Log.Caller, $JobResource.Properties.jobId))
    }
}
$JobInfo.GetEnumerator() | sort key -Descending | Select-Object -First 1
```

## <a name="fair-share"></a>доли

Для совместного использования ресурсов всеми модулями Runbook в облаке, службы автоматизации Azure временно выгружает или останавливает любой задаче, для более трех часов. Задания для [модулей Runbook на основе PowerShell](automation-runbook-types.md#powershell-runbooks) и [модулей Runbook Python](automation-runbook-types.md#python-runbooks) остановлены и не перезапускаются, а состояние задания отображается как "Остановлено".

Для длительных задач рекомендуем использовать [гибридную рабочую роль Runbook](automation-hrw-run-runbooks.md#job-behavior). Гибридные рабочие роли Runbook не ограничены равномерным распределением и могут выполняться сколько угодно. Другие [границы](../azure-subscription-service-limits.md#automation-limits) заданий применимы к песочницам Azure и к гибридным рабочим ролям Runbook. Хотя гибридных рабочих ролей Runbook не ограничиваются ограничение справедливого 3 часа, модули Runbook, выполняющихся на них необходимо разрабатывать для поддержки поведения перезапуска из проблем Непредвиденная локальной инфраструктуры.

Еще один вариант — оптимизировать модуль Runbook с помощью дочерних модулей. Если модуль Runbook циклически выполняет одну и ту же функцию для ряда ресурсов, например определенную операцию в нескольких базах данных, такую функцию можно переместить в [дочерний модуль Runbook](automation-child-runbooks.md) и вызвать ее с помощью командлета [Start-AzureRMAutomationRunbook](/powershell/module/azurerm.automation/start-azurermautomationrunbook). Каждый из этих дочерних модулей runbook выполняется в параллельном режиме отдельными процессами. Это уменьшает количество времени на завершение родительского модуля runbook. Можно использовать [Get-AzureRmAutomationJob](/powershell/module/azurerm.automation/Get-AzureRmAutomationJob) командлета в модуле runbook, чтобы проверить состояние задания для каждого дочернего элемента, если существуют операции, выполняемые после завершения дочернего модуля runbook.

## <a name="next-steps"></a>Дальнейшие действия

* Дополнительные сведения о разных методах запуска модуля Runbook см. в статье [Запуск модуля Runbook в службе автоматизации Azure](automation-starting-a-runbook.md).

