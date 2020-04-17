---
title: Выходные данные и сообщения Runbook в службе автоматизации Azure
description: Описывает, как создавать и извлекать выходные и сообщения об ошибках из runbooks в Azure Automation.
services: automation
ms.subservice: process-automation
ms.date: 12/04/2018
ms.topic: conceptual
ms.openlocfilehash: 92b6378b00e12f618d07798b5ce789cbd9971544
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81535542"
---
# <a name="runbook-output-and-messages-in-azure-automation"></a>Выходные данные и сообщения Runbook в службе автоматизации Azure

В большинстве модулей runbook в службе автоматизации Azure используются выходные данные определенного типа. Этот вывод может быть сообщением об ошибке для пользователя или сложным объектом, предназначенным для использования в другой запущенной книге. Windows PowerShell предоставляет [несколько потоков](/powershell/module/microsoft.powershell.core/about/about_redirection) для отправки выходных данных из сценария или рабочего процесса. Служба автоматизации Azure работает с каждым из этих потоков по-разному. При создании сборника необходимо следовать рекомендациям по использованию потоков.

В следующей таблице кратко описывается каждый поток с его поведением на портале Azure для опубликованных runbooks и во время [тестирования runbook](automation-testing-runbook.md). Поток выходов является основным потоком, используемым для связи между runbooks. Другие потоки классифицируются как потоки сообщений, предназначенные для передачи информации пользователю. 

| Поток | Описание | Опубликован | Тест |
|:--- |:--- |:--- |:--- |
| Error |Сообщение об ошибке, предназначенное для пользователя. В отличие от исключения, runbook продолжается после сообщения об ошибке по умолчанию. |Написано к истории работы |Отображается в тестовом панели вывода |
| Отладка |Сообщения, предназначенные для интерактивного пользователя. Не следует использовать в модулях runbook. |Не написано на историю работы |Не отображается в тестовом панели вывода |
| Выходные данные |Объекты, предназначенные для использования другими Runbook. |Написано к истории работы |Отображается в тестовом панели вывода |
| Ход выполнения |До и после каждого действия в Runbook автоматически создаются записи. В runbook не следует пытаться создавать свои собственные записи прогресса, так как они предназначены для интерактивного пользователя. |Написано в истории задания только в том случае, если журнал прогресса включен для runbook |Не отображается в тестовом панели вывода |
| Подробный |Сообщения, содержащие общую или отладочную информацию. |Написано в истории работы только в том случае, если многословная регистрация включена для runbook |Отображается в тестовом панели `VerbosePreference` вывода только в том случае, если переменная установлена для продолжения в runbook |
| Предупреждение |Предупреждающее сообщение, предназначенное для пользователя. |Написано к истории работы |Отображается в тестовом панели вывода |

>[!NOTE]
>Эта статья была изменена и теперь содержит сведения о новом модуле Az для Azure PowerShell. Вы по-прежнему можете использовать модуль AzureRM, исправления ошибок для которого будут продолжать выпускаться как минимум до декабря 2020 г. Дополнительные сведения о совместимости модуля Az с AzureRM см. в статье [Introducing the new Azure PowerShell Az module](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0) (Знакомство с новым модулем Az для Azure PowerShell). Для инструкций по установке модуля Az на гибридном Runbook Worker [см.](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0) Для учетной записи Автоматизация вы можете обновить свои модули до последней версии, используя [как обновить модули Azure PowerShell в Azure Automation.](automation-update-azure-modules.md)

## <a name="output-stream"></a>Поток вывода

Поток вывода используется для вывода объектов, созданных скриптом или рабочим процессом, когда он работает правильно. Azure Automation в основном использует этот поток для объектов, которые будут потребляться родительскими runbooks, которые называют [текущий runbook.](automation-child-runbooks.md) Когда родитель [вызывает строку runbook,](automation-child-runbooks.md#invoking-a-child-runbook-using-inline-execution)ребенок возвращает данные из потока вывода в родительский. 

В runbook используется поток выходного времени для передачи клиенту общей информации только в том случае, если он никогда не вызывается другим runbook. Однако в качестве наилучшей практики, как правило, для передачи общей информации пользователю следует использовать [поток Verbose.](#verbose-stream)

Попросите ли вся книга написания данных в поток вывода с помощью [Write-Output](https://technet.microsoft.com/library/hh849921.aspx). Кроме того, в скрипте можно поместить объект на свою собственную линию.

```powershell
#The following lines both write an object to the output stream.
Write-Output –InputObject $object
$object
```

### <a name="handling-output-from-a-function"></a>Обработка вывода от функции

Когда функция runbook записывается в поток вывода, выход передается обратно в рунбук. Если запуск присваивает этот вывод переменной, вывод не запихивается в поток вывода. Запись в любые другие потоки из функции сопровождается записью в соответствующий поток для runbook. Рассмотрим следующий пример запуска рабочего процесса PowerShell.

```powershell
Workflow Test-Runbook
{
  Write-Verbose "Verbose outside of function" -Verbose
  Write-Output "Output outside of function"
  $functionOutput = Test-Function
  $functionOutput

  Function Test-Function
  {
    Write-Verbose "Verbose inside of function" -Verbose
    Write-Output "Output inside of function"
  }
}
```

Поток вывода для задания runbook:

```output
Output inside of function
Output outside of function
```

Поток Verbose для работы runbook:

```output
Verbose outside of function
Verbose inside of function
```

После того как вы опубликовали runbook и прежде чем начать его, вы также должны включить многословную запись в настройках runbook, чтобы получить выход потока Verbose.

### <a name="declaring-output-data-type"></a>Объявление типа выходных данных

Ниже приведены примеры типов выходных данных:

* `System.String`
* `System.Int32`
* `System.Collections.Hashtable`
* `Microsoft.Azure.Commands.Compute.Models.PSVirtualMachine`

#### <a name="declare-output-data-type-in-a-workflow"></a>Объявление типа данных вывода в рабочем процессе

Рабочий процесс определяет тип вывода данных с помощью [атрибута OutputType.](https://technet.microsoft.com/library/hh847785.aspx) Этот атрибут не имеет никакого эффекта во время выполнения, но он дает вам указание на время проектирования ожидаемого вывода runbook. По мере того, как набор инструментов для runbooks продолжает развиваться, возрастает важность объявления типов выходных данных во время проектирования. Поэтому лучше всего включать эту декларацию в любые создаваемые книги.

Следующий пример Runbook выводит строковый объект и включает в себя объявление типа выходных данных. Если Runbook выводит массив определенного типа, все равно следует указать его тип, а не массив типа.

```powershell
Workflow Test-Runbook
{
  [OutputType([string])]

  $output = "This is some string output."
  Write-Output $output
}
 ```

#### <a name="declare-output-data-type-in-a-graphical-runbook"></a>Объявить тип выходных данных в графическом runbook

Чтобы объявить тип вывода в графическом или графическом runbook PowerShell Workflow, можно выбрать опцию **меню ввода и вывода** и ввести тип вывода. Рекомендуется использовать полное имя класса .NET, чтобы сделать тип легко идентифицируемым, когда родитель ссылается на него. Использование полного имени предоставляет все свойства класса в шины в runbook и повышает гибкость, когда свойства используются для условной логики, ведения журнала и ссылок в качестве значений для других действий runbook.<br> ![Runbook: раздел "Входные и выходные данные"](media/automation-runbook-output-and-messages/runbook-menu-input-and-output-option.png)

>[!NOTE]
>После ввода значения в поле **типа вывода** в панели свойств ввода и вывода обязательно щель вне элемента управления, чтобы распознать вашу запись.

В следующем примере показаны два графических рунбука, демонстрирующие функцию ввода и вывода. Применяя модульную модель дизайна runbook, у вас есть одна книга, как шаблон Authenticate Runbook, управляющий аутентификацией с помощью учетной записи Run As. Второй runbook, который обычно выполняет основную логику для автоматизации данного сценария, в этом случае выполняет шаблон Authenticate Runbook. Он отображает результаты на панели тестового вывода. В обычных условиях этот модуль runbook выполнял бы определенные действия в отношении ресурса, используя выходные данные дочернего модуля runbook.

Ниже представлена основная логика модуля Runbook **AuthenticateTo-Azure**.<br> ![Runbook: пример шаблона аутентификации](media/automation-runbook-output-and-messages/runbook-authentication-template.png).

В runbook включен `Microsoft.Azure.Commands.Profile.Models.PSAzureContext`тип вывода, который возвращает свойства профиля проверки подлинности.<br> ![Runbook: пример типа выходных данных](media/automation-runbook-output-and-messages/runbook-input-and-output-add-blade.png)

Хотя этот runbook прост, есть один элемент конфигурации, чтобы вызвать здесь. Последнее действие выполняет `Write-Output` cmdlet для записи данных профиля в `Inputobject` переменную, используя выражение PowerShell для параметра. Этот параметр `Write-Output`необходим для .

Второй runbook в этом примере, названный **Test-ChildOutputType**, просто определяет два вида деятельности.<br> ![Runbook: пример типа выходных данных дочернего модуля](media/automation-runbook-output-and-messages/runbook-display-authentication-results-example.png)

Первое действие вызывается в runbook **AuthenticateTo-Azure.** Второе действие `Write-Verbose` выполняет cmdlet с **исходным ресурсом данных,** установленным на **выходе активности.** Кроме того, **полевый путь** установлен на **Context.Subscription.SubscriptionName,** выход контекста из runbook **AuthenticateTo-Azure.**<br> ![Источник данных параметра Write-Verbose Cmdlet](media/automation-runbook-output-and-messages/runbook-write-verbose-parameters-config.png)

Результат — имя подписки.<br> ![Runbook: результаты командлета Test-ChildOutputType](media/automation-runbook-output-and-messages/runbook-test-childoutputtype-results.png)

## <a name="message-streams"></a>Потоки сообщений

В отличие от потока вывода, потоки сообщений передают информацию пользователю. Существует несколько потоков сообщений для различных видов информации, и Azure Automation обрабатывает каждый поток по-разному.

### <a name="warning-and-error-streams"></a>Потоки предупреждений и ошибок

Потоки «Предупреждение и ошибка» регистрируют проблемы, возникающие в сборнике. Azure Automation записывает эти потоки в историю задания при выполнения runbook. Автоматизация включает потоки в тестовом панели вывода на портале Azure при тестировании свектора. 

По умолчанию запуск продолжает выполняться после предупреждения или ошибки. Можно указать, что запуск должен приостановить предупреждение или ошибку, заставив набор [предпочтений](#preference-variables) перед созданием сообщения. Например, чтобы заставить запуск приостановить ошибку, как это происходит `ErrorActionPreference` с исключением, установите переменную для остановки.

Создайте предупреждение или сообщение об ошибке с помощью командлета [Write-Warning](https://technet.microsoft.com/library/hh849931.aspx) или [Write-Error](https://technet.microsoft.com/library/hh849962.aspx). Действия также могут записываться в потоки Предупреждения и Ошибки.

```powershell
#The following lines create a warning message and then an error message that will suspend the runbook.

$ErrorActionPreference = "Stop"
Write-Warning –Message "This is a warning message."
Write-Error –Message "This is an error message that will stop the runbook because of the preference variable."
```

### <a name="debug-stream"></a>Поток отладки

Azure Automation использует поток сообщений Debug для интерактивных пользователей. Он не должен использоваться в runbooks.

### <a name="verbose-stream"></a>Подробный поток

Поток сообщений Verbose поддерживает общую информацию об операции Runbook. Поскольку поток Debug недоступен для runbook, в runbook следует использовать многословные сообщения для отладки информации. 

По умолчанию в истории задания не хранятся многословные сообщения из опубликованных runbooks по причинам производительности. Для хранения многословных сообщений используйте вкладку Azure **Portal Configure** с настройкой **записей журналов Verbose,** чтобы настроить опубликованные руны для записи многословных сообщений. Включайте этот параметр только для устранения неполадок и отладки Runbook. В большинстве случаев следует сохранить настройку по умолчанию, не регистрируя многословные записи.

При [тестировании модуля runbook](automation-testing-runbook.md) подробные сообщения не отображаются, даже если для runbook настроено добавление подробных записей в журнал. Для отображения многословных сообщений при `VerbosePreference` [тестировании runbook](automation-testing-runbook.md)необходимо установить переменную для продолжения. С этим переменным набором многословные сообщения отображаются в тестовом панели вывода портала Azure.

Следующий код создает многословное сообщение с помощью cmdlet [Write-Verbose.](https://technet.microsoft.com/library/hh849951.aspx)

```powershell
#The following line creates a verbose message.

Write-Verbose –Message "This is a verbose message."
```

## <a name="progress-records"></a>Записи о ходе выполнения

Для настройки книги для регистрации записей о ходе работможно настроить вкладку **Накидк** портала Azure. Установка по умолчанию заключается в том, чтобы не регистрировать записи, чтобы максимизировать производительность. В большинстве случаев следует сохранить настройки по умолчанию. Включайте этот параметр только для устранения неполадок и отладки Runbook. 

Если вы включите журнал записи прогресса, ваша книга записи записи к истории работы до и после каждого действия выполняется. Тестирование runbook не отображает сообщения о ходе работы, даже если запущенная книга настроена на записи записей прогресса.

>[!NOTE]
>Командлет [Write-Progress](https://technet.microsoft.com/library/hh849902.aspx) недействителен в runbook, так как предназначен для использования интерактивным пользователем.

## <a name="preference-variables"></a>Привилегированные переменные

Можно установить определенные [переменные предпочтений](https://technet.microsoft.com/library/hh847796.aspx) Windows PowerShell в своих runbooks для управления ответом на данные, отправленные в различные потоки выходных данных. В следующей таблице перечислены переменные предпочтений, которые могут быть использованы в runbooks, с их значениями по умолчанию и действительными значениями. Дополнительные значения доступны для переменных предпочтений при использовании в Windows PowerShell за пределами Azure Automation.

| Переменная | Значение по умолчанию | Допустимые значения |
|:--- |:--- |:--- |
| `WarningPreference` |Continue |Остановить<br>Continue<br>SilentlyContinue |
| `ErrorActionPreference` |Continue |Остановить<br>Continue<br>SilentlyContinue |
| `VerbosePreference` |SilentlyContinue |Остановить<br>Continue<br>SilentlyContinue |

В следующей таблице перечислено поведение переменных значений предпочтений, действительных в runbooks.

| Значение | Поведение |
|:--- |:--- |
| Continue |Записывает сообщение в журнал и продолжает выполнение Runbook. |
| SilentlyContinue |Продолжает выполнение Runbook без добавления сообщения в журнал. При таком значении сообщение игнорируется. |
| Остановить |Записывает сообщение в журнал и приостанавливает выполнение Runbook. |

## <a name="retrieving-runbook-output-and-messages"></a><a name="runbook-output"></a>Извлечение выходных данных и сообщений runbook

### <a name="retrieve-runbook-output-and-messages-in-azure-portal"></a>Извлечение выходов и сообщений runbook на портале Azure

Вы можете просмотреть сведения о работе runbook на портале Azure, используя вкладку **«Вакансии»** для runbook. Резюме задания отображает параметры ввода и [поток вывода,](#output-stream)в дополнение к общей информации о заданиях и любых исключениях, которые произошли. История задания включает в себя сообщения из потока вывода и [потоки предупреждений и ошибок.](#warning-and-error-streams) Он также включает в себя сообщения из [потока Verbose](#verbose-stream) и [записи прогресса,](#progress-records) если запуск настроен для записи многословных и записей прогресса.

### <a name="retrieve-runbook-output-and-messages-in-windows-powershell"></a>Извлекать выход и сообщения runbook в Windows PowerShell

В Windows PowerShell можно получить выход и сообщения из смотра с помощью cmdlet [Get-AzAutomationJobOutput.](https://docs.microsoft.com/powershell/module/Az.Automation/Get-AzAutomationJobOutput?view=azps-3.5.0) Это cmdlet требует идентификатора `Stream` задания и имеет параметр, в котором указано поток для извлечения. Можно указать значение Any для этого параметра, чтобы получить все потоки для задания.

В следующем примере запускается пример Runbook и ожидается его завершение. Как только запуск завершает выполнение, скрипт собирает поток вывода runbook из задания.

```powershell
$job = Start-AzAutomationRunbook -ResourceGroupName "ResourceGroup01" `
  –AutomationAccountName "MyAutomationAccount" –Name "Test-Runbook"

$doLoop = $true
While ($doLoop) {
  $job = Get-AzAutomationJob -ResourceGroupName "ResourceGroup01" `
    –AutomationAccountName "MyAutomationAccount" -Id $job.JobId
  $status = $job.Status
  $doLoop = (($status -ne "Completed") -and ($status -ne "Failed") -and ($status -ne "Suspended") -and ($status -ne "Stopped"))
}

Get-AzAutomationJobOutput -ResourceGroupName "ResourceGroup01" `
  –AutomationAccountName "MyAutomationAccount" -Id $job.JobId –Stream Output

# For more detailed job output, pipe the output of Get-AzAutomationJobOutput to Get-AzAutomationJobOutputRecord
Get-AzAutomationJobOutput -ResourceGroupName "ResourceGroup01" `
  –AutomationAccountName "MyAutomationAccount" -Id $job.JobId –Stream Any | Get-AzAutomationJobOutputRecord
```

### <a name="retrieve-runbook-output-and-messages-in-graphical-runbooks"></a>Извлекать выход runbook и сообщения в графических runbooks

Для графических runbooks, дополнительные регистрации выходных и сообщений доступны в виде отслеживания уровня активности. Существует два уровня трассировки: базовая и подробная. Базовая трассировка отображает время начала и окончания для каждого действия в runbook, а также информацию, связанную с любыми повторами действий. Некоторые примеры — количество попыток и время начала действия. Детальная трассировка включает основные функции отслеживания плюс регистрацию входных и выходных данных для каждого вида деятельности. 

В настоящее время отслеживание уровня активности записи записей с помощью потока Verbose. Поэтому при отслеживании необходимо включить многословную регистрацию. Для графических модулей runbook с включенной трассировкой вести записи о ходе выполнения не требуется. Базовая трассировка не только решает эту задачу, но и является более информативной.

![Представление потоков заданий графической разработки](media/automation-runbook-output-and-messages/job-streams-view-blade.png)

На снимке видно, что позволяет осуществлять многословную регистрацию и отслеживание графических рункниг, что делает гораздо больше информации, доступной в **представлении** рабочих потоков производства. Эта дополнительная информация может иметь важное значение для устранения производственных проблем с помощью runbook. 

Однако, если вам не потребуется эта информация для отслеживания хода запуска для устранения неполадок, вы можете сохранить отслеживание выключен в качестве общей практики. Записи следов могут быть особенно многочисленными. С графическим отслеживанием runbook, вы можете получить от двух до четырех записей за деятельность, в зависимости от конфигурации основных или подробных трассировки.

**Для отслеживания уровня активности:**

1. На портале Azure выберите свою учетную запись службы автоматизации.
2. Выберите **Runbooks** под **автоматизацией процессов,** чтобы открыть список runbooks.
3. На странице Runbooks выберите графический справочник из списка runbooks.
4. В разделе **Параметры** щелкните **Ведение журналов и трассировка**.
5. На странице регистрации и отслеживания, под **журналом многословные записи**, нажмите **на,** чтобы включить многословную регистрацию.
6. При **отслеживании уровня активности**измените уровень трассировки на **Basic** или **Detailed,** в зависимости от требуемого уровня трассировки.<br>

   ![Страница ведения журналов и трассировки графической разработки](media/automation-runbook-output-and-messages/logging-and-tracing-settings-blade.png)

### <a name="retrieve-runbook-output-and-messages-in-microsoft-azure-monitor-logs"></a>Получение выходных и сообщений runbook в журналах Microsoft Azure Monitor

Azure Automation может отправлять статус работы и потоки заданий в рабочее пространство Log Analytics. Azure Monitor поддерживает журналы, которые позволяют:

* получить информацию о заданиях службы автоматизации;
* Триггер электронной почты или оповещения на основе вашего статуса работы runbook, например, не удалось или приостановлено.
* Напишите расширенные запросы в потоках заданий.
* коррелировать задания и учетные записи службы автоматизации;
* Визуализируйте историю задания.

Для получения дополнительной информации о настройке интеграции с журналами Azure Monitor [Forward job status and job streams from Automation to Azure Monitor logs](automation-manage-send-joblogs-log-analytics.md)для сбора, соотношения и действия по данным о работе см.

## <a name="next-steps"></a>Следующие шаги

* Чтобы узнать больше о выполнении runbook, мониторинге заданий runbook и других технических деталях, [см.](automation-runbook-execution.md)
* Чтобы понять, как проектировать и использовать детские книги, смотрите [в учебниках «Дети» в Azure Automation.](automation-child-runbooks.md)
* Для получения дополнительной информации о PowerShell, включая [PowerShell Docs](/powershell/scripting/overview)языковые справочные и учебные модули, см.
