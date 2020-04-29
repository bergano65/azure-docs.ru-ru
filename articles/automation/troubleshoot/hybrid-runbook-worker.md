---
title: Устранение неполадок гибридных рабочих ролей Runbook службы автоматизации Azure
description: Эта статья содержит сведения об устранении неполадок гибридных рабочих ролей Runbook службы автоматизации Azure.
services: automation
ms.service: automation
ms.subservice: ''
author: mgoedtel
ms.author: magoedte
ms.date: 11/25/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 2b3bf6706e977bdb6915335dee59da3c250e7895
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2020
ms.locfileid: "81679333"
---
# <a name="troubleshoot-hybrid-runbook-workers"></a>Устранение неполадок с гибридными рабочими ролями Runbook

В этой статье приводятся сведения об устранении неполадок с гибридными рабочими ролями Runbook.

>[!NOTE]
>Эта статья была изменена и теперь содержит сведения о новом модуле Az для Azure PowerShell. Вы по-прежнему можете использовать модуль AzureRM, исправления ошибок для которого будут продолжать выпускаться как минимум до декабря 2020 г. Дополнительные сведения о совместимости модуля Az с AzureRM см. в статье [Introducing the new Azure PowerShell Az module](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0) (Знакомство с новым модулем Az для Azure PowerShell). Инструкции по установке модуля Az в гибридной рабочей роли Runbook см. в статье об [установке модуля Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0). Чтобы обновить модули в учетной записи службы автоматизации, см. руководство по [обновлению модулей Azure PowerShell в службе автоматизации Azure](../automation-update-azure-modules.md).

## <a name="general"></a>Общие

Гибридная рабочая роль Runbook зависит от агента, который используется для взаимодействия с учетной записью автоматизации для регистрации рабочей роли, получения заданий runbook и сообщения о состоянии. Для Windows это агент Log Analytics для Windows. Для Linux это Log Analytics агент для Linux.

### <a name="scenario-runbook-execution-fails"></a><a name="runbook-execution-fails"></a>Сценарий: происходит сбой выполнения модуля Runbook

#### <a name="issue"></a>Проблема

Выполнение модуля Runbook завершается сбоем, и появляется следующее сообщение об ошибке.

```error
"The job action 'Activate' cannot be run, because the process stopped unexpectedly. The job action was attempted three times."
```

Работа модуля Runbook будет приостановлена вскоре после попыток выполнить три раза. Существуют условия, которые могут прервать выполнение модуля Runbook. Связанное сообщение об ошибке может не включать дополнительные сведения.

#### <a name="cause"></a>Причина

Возможны следующие причины:

* Модули Runbook не могут проходить проверку подлинности с локальными ресурсами.
* Гибридная рабочая роль находится за прокси-сервером или брандмауэром.
* Компьютер, настроенный для запуска гибридной рабочей роли Runbook, не соответствует минимальным требованиям к оборудованию.

#### <a name="resolution"></a>Решение

Убедитесь, что компьютер имеет исходящий доступ к ***. Azure-Automation.NET** через порт 443.

Компьютеры, на которых работает гибридная Рабочая роль Runbook, должны соответствовать минимальным требованиям к оборудованию, прежде чем рабочий процесс будет настроен для размещения этой функции. Модули Runbook и фоновый процесс, которые они используют, могут привести к чрезмерному использованию системы и вызвать задержки заданий или истечения времени ожидания задания Runbook.

Убедитесь, что компьютер, на котором работает гибридная Рабочая роль Runbook, соответствует минимальным требованиям к оборудованию. Если требования выполнены, отследите использование ЦП и памяти, чтобы определить корреляцию между производительностью процессов гибридной рабочей роли Runbook и Windows. Любой недостаток памяти или ЦП может указывать на необходимость обновления ресурсов. Кроме того, можно выбрать другой ресурс вычислений, который поддерживает минимальные требования и масштабироваться, если требования рабочей нагрузки указывают на необходимость увеличения.

Проверьте журнал событий **Microsoft-SMA** на наличие соответствующего события с описанием `Win32 Process Exited with code [4294967295]`. Причина этой ошибки заключается в том, что вы не настроили проверку подлинности в модулях Runbook или указали учетные данные запуска от имени для группы гибридной рабочей роли Runbook. Проверьте разрешения Runbook в [работе модулей Runbook в гибридной рабочей роли Runbook](../automation-hrw-run-runbooks.md) , чтобы убедиться в правильности настройки проверки подлинности для модулей Runbook.

### <a name="scenario-event-15011-in-hybrid-runbook-worker"></a><a name="cannot-connect-signalr"></a>Сценарий: событие 15011 в гибридной рабочей роли Runbook

#### <a name="issue"></a>Проблема

Гибридная Рабочая роль Runbook получает событие 15011, указывающее на недопустимый результат запроса. Следующая ошибка возникает, когда исполнитель пытается открыть подключение к [серверу SignalR](https://docs.microsoft.com/aspnet/core/signalr/introduction?view=aspnetcore-3.1).

```error
[AccountId={c7d22bd3-47b2-4144-bf88-97940102f6ca}]
[Uri=https://cc-jobruntimedata-prod-su1.azure-automation.net/notifications/hub][Exception=System.TimeoutException: Transport timed out trying to connect
   at System.Runtime.ExceptionServices.ExceptionDispatchInfo.Throw()
   at System.Runtime.CompilerServices.TaskAwaiter.HandleNonSuccessAndDebuggerNotification(Task task)
   at JobRuntimeData.NotificationsClient.JobRuntimeDataServiceSignalRClient.<Start>d__45.MoveNext()
```

#### <a name="cause"></a>Причина

Гибридная Рабочая роль Runbook неправильно настроена для решения автоматического развертывания. Это решение содержит часть, которая подключает виртуальную машину к рабочей области Log Analytics. Сценарий PowerShell ищет рабочую область в подписке с заданным именем. В этом случае Рабочая область Log Analytics находится в другой подписке. Скрипт не может найти рабочую область и пытается создать ее, но имя уже занято. Поэтому развертывание завершается ошибкой.

#### <a name="resolution"></a>Решение

Существует два варианта решения этой проблемы.

* Измените сценарий PowerShell, чтобы найти рабочую область Log Analytics в другой подписке. Это хорошее решение, если в будущем планируется развернуть много виртуальных машин гибридной рабочей роли Runbook.

* Вручную настройте рабочий компьютер для запуска в песочнице Orchestrator. Затем запустите модуль Runbook, созданный в учетной записи службы автоматизации Azure для рабочей роли, чтобы протестировать функциональные возможности.

### <a name="scenario-windows-azure-vms-automatically-dropped-from-hybrid-worker-group"></a><a name="vm-automatically-dropped"></a>Сценарий: виртуальные машины Windows Azure автоматически удалены из группы гибридных рабочих ролей

#### <a name="issue"></a>Проблема

Гибридные рабочие роли Runbook или виртуальные машины не отображаются, если рабочий компьютер был выключен в течение длительного времени.

#### <a name="cause"></a>Причина

Компьютер гибридной рабочей роли Runbook не проверяет связь с Azure Automation в течение более чем 30 дней. В результате Автоматизация удаляет группу гибридных рабочих ролей Runbook или рабочую группу System. 

#### <a name="resolution"></a>Решение

Запустите рабочий компьютер, а затем реререгистер его с помощью службы автоматизации Azure. См. инструкции по установке среды Runbook и подключению к службе автоматизации Azure в разделе [развертывание гибридной рабочей роли Runbook Windows](../automation-windows-hrw-install.md).

### <a name="scenario-no-certificate-was-found-in-the-certificate-store-on-hybrid-runbook-worker"></a><a name="no-cert-found"></a>Сценарий: сертификат не найден в хранилище сертификатов в гибридной рабочей роли Runbook

#### <a name="issue"></a>Проблема

Модуль Runbook, выполняющийся в гибридной рабочей роли Runbook, завершается со следующим сообщением об ошибке.

```error
Connect-AzAccount : No certificate was found in the certificate store with thumbprint 0000000000000000000000000000000000000000
At line:3 char:1
+ Connect-AzAccount -ServicePrincipal -Tenant $Conn.TenantID -Appl ...
+ ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
    + CategoryInfo          : CloseError: (:) [Connect-AzAccount], ArgumentException
    + FullyQualifiedErrorId : Microsoft.Azure.Commands.Profile.ConnectAzAccountCommand
```
#### <a name="cause"></a>Причина

Эта ошибка возникает при попытке использовать [учетную запись запуска от имени](../manage-runas-account.md) в модуле Runbook, который выполняется в гибридной рабочей роли Runbook, когда отсутствует сертификат учетной записи запуска от имени. По умолчанию для гибридных рабочих ролей Runbook ресурс сертификата не используется локально. Для правильной работы этого актива требуется учетная запись запуска от имени.

#### <a name="resolution"></a>Решение

Если Гибридная Рабочая роль Runbook является виртуальной машиной Azure, вместо нее можно использовать [управляемые удостоверения для ресурсов Azure](../automation-hrw-run-runbooks.md#managed-identities-for-azure-resources) . Этот сценарий упрощает проверку подлинности, позволяя выполнять проверку подлинности в ресурсах Azure с помощью управляемого удостоверения виртуальной машины Azure, а не учетной записи запуска от имени. Если Гибридная Рабочая роль Runbook является локальным компьютером, необходимо установить сертификат учетной записи запуска от имени на компьютере. Чтобы узнать, как установить сертификат, ознакомьтесь с инструкциями по запуску модуля Runbook PowerShell **Export-RunAsCertificateToHybridWorker** в [запущенных модулях Runbook в гибридной рабочей роли Runbook](../automation-hrw-run-runbooks.md).

### <a name="scenario-error-403-during-registration-of-hybrid-runbook-worker"></a><a name="error-403-on-registration"></a>Сценарий: Ошибка 403 во время регистрации гибридной рабочей роли Runbook

#### <a name="issue"></a>Проблема

На этапе первоначальной регистрации работника происходит сбой, и появляется следующее сообщение об ошибке (403).

```error
"Forbidden: You don't have permission to access / on this server."
```

#### <a name="cause"></a>Причина

Возможны следующие причины:

* В параметрах агента есть недопустимый идентификатор рабочей области или ключ рабочей области (основной). 

* Гибридная Рабочая роль Runbook не может скачать конфигурацию, что приведет к ошибке связывания учетной записи. Когда Azure предоставляет решения, она поддерживает только определенные регионы для связывания рабочей области Log Analytics и учетной записи службы автоматизации. Также возможно, что на компьютере установлено неправильное значение даты и времени. Если время составляет +/-15 минут от текущего времени, происходит сбой адаптации.

#### <a name="resolution"></a>Решение

##### <a name="mistyped-workspace-idkey"></a>Нетипизированный идентификатор или ключ рабочей области
Чтобы проверить, не был ли введен идентификатор рабочей области или ключ рабочей области агента, см. раздел [Добавление или удаление рабочей области — Агент Windows](../../azure-monitor/platform/agent-manage.md#windows-agent) для агента Windows или [Добавление или удаление рабочей области — агента Linux](../../azure-monitor/platform/agent-manage.md#linux-agent) для агента Linux.  Обязательно выберите полную строку из портал Azure и скопируйте и вставьте ее с осторожностью.

##### <a name="configuration-not-downloaded"></a>Конфигурация не скачана

Рабочая область Log Analytics и учетная запись службы автоматизации должны находиться в связанном регионе. Список поддерживаемых регионов см. в разделе [сопоставления рабочей области службы автоматизации Azure и log Analytics](../how-to/region-mappings.md).

Также может потребоваться обновить дату и/или часовой пояс компьютера. При выборе настраиваемого диапазона времени убедитесь, что диапазон находится в формате UTC, который может отличаться от местного часового пояса.

## <a name="linux"></a>Linux

Гибридная Рабочая роль Runbook Linux зависит от [агента log Analytics для Linux](../../azure-monitor/platform/log-analytics-agent.md) , чтобы взаимодействовать с учетной записью службы автоматизации для регистрации рабочего процесса, получения заданий Runbook и состояния отчета. Если при регистрации рабочей роли произошла ошибка, это могло произойти по следующим причинам:

### <a name="scenario-the-log-analytics-agent-for-linux-isnt-running"></a><a name="oms-agent-not-running"></a>Сценарий: агент Log Analytics для Linux не работает

#### <a name="issue"></a>Проблема

Агент Log Analytics для Linux не работает

#### <a name="cause"></a>Причина

Если агент не работает, он не сможет взаимодействовать с службой автоматизации Azure с помощью гибридной рабочей роли Runbook Linux. Возможно, агент не работает по разным причинам.

#### <a name="resolution"></a>Решение

 Убедитесь, что агент работает, введя команду `ps -ef | grep python`. Вы должны увидеть результат, аналогичный приведенному ниже, процесс Python с учетной записью пользователя **нксаутоматион** . Если Управление обновлениями или решение службы автоматизации Azure не включено, ни один из следующих процессов не будет запущен.

```bash
nxautom+   8567      1  0 14:45 ?        00:00:00 python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/worker/main.py /var/opt/microsoft/omsagent/state/automationworker/oms.conf rworkspace:<workspaceId> <Linux hybrid worker version>
nxautom+   8593      1  0 14:45 ?        00:00:02 python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/worker/hybridworker.py /var/opt/microsoft/omsagent/state/automationworker/worker.conf managed rworkspace:<workspaceId> rversion:<Linux hybrid worker version>
nxautom+   8595      1  0 14:45 ?        00:00:02 python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/worker/hybridworker.py /var/opt/microsoft/omsagent/<workspaceId>/state/automationworker/diy/worker.conf managed rworkspace:<workspaceId> rversion:<Linux hybrid worker version>
```

Ниже перечислены процессы, запущенные для гибридной рабочей роли Runbook Linux. Все они находятся в каталоге **/Вар/ОПТ/Микрософт/омсажент/Стате/аутоматионворкер/** .

* **OMS. conf** — процесс диспетчера рабочих ролей. Он запускается непосредственно из DSC.

* **Worker. conf** — автоматический зарегистрированный гибридный рабочий процесс. Он запускается диспетчером рабочих ролей. Этот процесс используется в решении "Управление обновлениями" и незаметен для пользователя. Если решение "Управление обновлениями" не включено на компьютере, этот процесс будет отсутствовать.

* **DIY/Worker. conf** — рабочий процесс гибридного рабочего процесса DIY. Процесс гибридной рабочей роли DIY используется для выполнения модулей Runbook пользователя в гибридной рабочей роли Runbook. Он отличается от автоматического зарегистрированного гибридного рабочего процесса в основных деталях, в которых используется другая конфигурация. Этот процесс отсутствует, если решение службы автоматизации Azure отключено, а Гибридная Рабочая роль DIY Linux не зарегистрирована.

Если агент не запущен, выполните следующую команду, чтобы запустить службу: `sudo /opt/microsoft/omsagent/bin/service_control restart`.

### <a name="scenario-the-specified-class-doesnt-exist"></a><a name="class-does-not-exist"></a>Сценарий: указанный класс не существует

Если вы видите ошибку `The specified class does not exist..` в **/Вар/ОПТ/Микрософт/омсконфиг/омсконфиг.лог**, необходимо обновить агент log Analytics для Linux. Выполните следующую команду, чтобы переустановить агент:

```bash
wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh -w <WorkspaceID> -s <WorkspaceKey>
```

## <a name="windows"></a>Windows

Гибридная Рабочая роль Runbook Windows зависит от [агента log Analytics для Windows](../../azure-monitor/platform/log-analytics-agent.md) , который должен взаимодействовать с учетной записью службы автоматизации для регистрации рабочего процесса, получения заданий Runbook и состояния отчета. В случае сбоя регистрации рабочей роли этот раздел включает некоторые возможные причины.

### <a name="scenario-the-log-analytics-agent-for-windows-isnt-running"></a><a name="mma-not-running"></a>Сценарий: агент Log Analytics для Windows не работает

#### <a name="issue"></a>Проблема

`healthservice` Не работает на компьютере гибридной рабочей роли Runbook.

#### <a name="cause"></a>Причина

Если служба Log Analytics для Windows не запущена, гибридная Рабочая роль Runbook не сможет взаимодействовать со службой автоматизации Azure.

#### <a name="resolution"></a>Решение

Убедитесь, что агент работает, введя следующую команду в PowerShell: `Get-Service healthservice`. Если служба остановлена, введите в PowerShell команду `Start-Service healthservice`, чтобы запустить эту службу.

### <a name="scenario-event-4502-in-operations-manager-log"></a><a name="event-4502"></a>Сценарий: событие 4502 в журнале Operations Manager

#### <a name="issue"></a>Проблема

В журнале событий **Application and Services Logs\Operations Manager** отображается событие 4502 и EventMessage, содержащее `Microsoft.EnterpriseManagement.HealthService.AzureAutomation.HybridAgent` следующее описание:<br>`The certificate presented by the service \<wsid\>.oms.opinsights.azure.com was not issued by a certificate authority used for Microsoft services. Please contact your network administrator to see if they are running a proxy that intercepts TLS/SSL communication.`

#### <a name="cause"></a>Причина

Эта проблема может быть вызвана тем, что прокси-сервер или сетевой брандмауэр блокируют подключение к Microsoft Azure. Убедитесь, что компьютер имеет исходящий доступ к ***. Azure-Automation.NET** через порт 443. 

#### <a name="resolution"></a>Решение

Журналы хранятся локально в каждой гибридной рабочей роли на сайте **К:\програмдата\микрософт\систем Center\Orchestrator\7.2\SMA\Sandboxes**. Можно проверить наличие предупреждений или ошибок в журналах событий Application and Services **логс\микрософт-сма\оператионс** и **Application and Services Logs\Operations Manager** . Эти журналы указывают на возможность подключения или другой тип проблемы, которая влияет на подключение роли к службе автоматизации Azure, или на проблемы, возникающие при нормальных операциях. Дополнительные сведения об устранении неполадок, связанных с агентом Log Analytics, см. [в статье Устранение неполадок с агентом log Analytics Windows](../../azure-monitor/platform/agent-windows-troubleshoot.md).

Гибридные работники отправляют [выходные данные и сообщения Runbook](../automation-runbook-output-and-messages.md) в службу автоматизации Azure точно так же, как задания Runbook, выполняемые в облаке, отправляют выходные данные и сообщения. Вы можете включить подробные и ход выполнения потоков так же, как и для модулей Runbook.

### <a name="scenario-orchestratorsandboxexe-cant-connect-to-office-365-through-proxy"></a><a name="no-orchestrator-sandbox-connect-O365"></a>Сценарий: Orchestrator. Sandbox. exe не может подключиться к Office 365 через прокси-сервер

#### <a name="issue"></a>Проблема

Сценарий, выполняющийся в гибридной рабочей роли Runbook Windows, не может подключиться к Office 365 в песочнице Orchestrator в качестве ожидаемого. Сценарий использует [Connect-MsolService](https://docs.microsoft.com/powershell/module/msonline/connect-msolservice?view=azureadps-1.0) для подключения. 

При настройке **Orchestrator. Sandbox. exe. config** для задания прокси-сервера и списка пропусков Песочница по-прежнему не подключается должным образом. Файл **Powershell_ise. exe. config** с тем же прокси и параметрами списка обхода, кажется, работает так, как вы ждете. Журналы Service Management Automation (SMA) и журналы PowerShell не предоставляют никаких сведений о прокси-сервере.

#### <a name="cause"></a>Причина

Соединение с службы федерации Active Directory (AD FS) (ADFS) на сервере не может обходить прокси-сервер. Помните, что Песочница PowerShell запускается от имени зарегистрированного пользователя. Однако Песочница Orchestrator сильно настроена и может игнорировать параметры файла **Orchestrator. Sandbox. exe. config** . Он содержит специальный код для обработки параметров прокси-сервера или компьютера или MMA, но не для обработки других настраиваемых параметров прокси-сервера. 

#### <a name="resolution"></a>Решение

Вы можете устранить проблему для песочницы Orchestrator, переполнив скрипт, чтобы использовать модули Azure AD вместо модуля MSOnline для командлетов PowerShell. См. статью [Переход с Orchestrator на службу автоматизации Azure (бета-версия)](https://docs.microsoft.com/azure/automation/automation-orchestrator-migration).

Если вы хотите продолжить использовать командлеты модуля MSOnline, измените сценарий так, чтобы использовалась [команда Invoke-Command](https://docs.microsoft.com/powershell/module/microsoft.powershell.core/invoke-command?view=powershell-7). Укажите значения для параметров `ComputerName` и `Credential` . 

```powershell
$Credential = Get-AutomationPSCredential -Name MyProxyAccessibleCredential
Invoke-Command -ComputerName $env:COMPUTERNAME -Credential $Credential 
{ Connect-MsolService … }
```

Это изменение кода запускает совершенно новый сеанс PowerShell в контексте указанных учетных данных. Он должен обеспечить передачу трафика через прокси-сервер, выполняющий проверку подлинности активного пользователя.

>[!NOTE]
>Это решение не требуется для работы с файлом конфигурации песочницы. Даже после того, как файл конфигурации будет работать с скриптом, он уничтожается при каждом обновлении гибридного агента рабочей роли Runbook.

### <a name="scenario-hybrid-runbook-worker-not-reporting"></a><a name="corrupt-cache"></a>Сценарий: Гибридная Рабочая роль Runbook не сообщает

#### <a name="issue"></a>Проблема

Компьютер гибридной рабочей роли Runbook работает, но в рабочей области не отображаются данные о пульсе для компьютера.

В следующем примере запроса показаны компьютеры в рабочей области и их последние сведения о пульсе:

```loganalytics
// Last heartbeat of each computer
Heartbeat
| summarize arg_max(TimeGenerated, *) by Computer
```

#### <a name="cause"></a>Причина

Эта проблема может быть вызвана повреждением кэша в гибридной рабочей роли Runbook.

#### <a name="resolution"></a>Решение

Чтобы решить данную проблему, войдите в систему гибридной рабочей роли Runbook и запустите следующий сценарий. Этот скрипт останавливает агент Log Analytics для Windows, удаляет его кэш и перезапускает службу. Это действие заставляет гибридную рабочую роль Runbook повторно скачать свою конфигурацию из службы автоматизации Azure.

```powershell
Stop-Service -Name HealthService

Remove-Item -Path 'C:\Program Files\Microsoft Monitoring Agent\Agent\Health Service State' -Recurse

Start-Service -Name HealthService
```

### <a name="scenario-you-cant-add-a-hybrid-runbook-worker"></a><a name="already-registered"></a>Сценарий: невозможно добавить гибридную рабочую роль Runbook

#### <a name="issue"></a>Проблема

Вы получите следующее сообщение при попытке добавить гибридную рабочую роль Runbook с помощью командлета `Add-HybridRunbookWorker`.

```error
Machine is already registered
```

#### <a name="cause"></a>Причина

Эта проблема может быть вызвана тем, что компьютер уже зарегистрирован в другой учетной записи службы автоматизации или если вы попытаетесь прочитать гибридную рабочую роль Runbook после ее удаления с компьютера.

#### <a name="resolution"></a>Решение

Чтобы устранить эту проблему, удалите следующий раздел реестра, перезапустите `HealthService`и повторите `Add-HybridRunbookWorker` командлет.

`HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\HybridRunbookWorker`

## <a name="next-steps"></a>Дальнейшие шаги

Если вы не видите проблему выше или не можете устранить проблему, воспользуйтесь одним из следующих каналов для получения дополнительной поддержки:

* Получите ответы от экспертов Azure на [форумах Azure](https://azure.microsoft.com/support/forums/).
* Подключайтесь с [@AzureSupport](https://twitter.com/azuresupport)помощью официальной учетной записи Microsoft Azure для улучшения качества работы клиентов, подключив сообщество Azure к нужным ресурсам: ответы, поддержка и эксперты.
* Отправьте запрос в службу поддержки Azure Перейдите на [сайт поддержки Azure](https://azure.microsoft.com/support/options/) и выберите **получить поддержку**.