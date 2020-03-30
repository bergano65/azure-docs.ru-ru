---
title: Устранение неполадок с гибридными рабочими ролями Runbook в службе автоматизации Azure
description: В этой статье приводятся сведения об устранении неполадок с гибридными рабочими ролями Runbook в службе автоматизации Azure
services: automation
ms.service: automation
ms.subservice: ''
author: mgoedtel
ms.author: magoedte
ms.date: 11/25/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 33e3e162892f1e2a148258273160ca26fa9c2efd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80153528"
---
# <a name="troubleshoot-hybrid-runbook-workers"></a>Устранение неполадок с гибридными рабочими ролями Runbook

В этой статье приводятся сведения об устранении неполадок с гибридными рабочими ролями Runbook.

## <a name="general"></a>Общие сведения

Гибридная рабочая роль Runbook зависит от агента, который используется для взаимодействия с учетной записью автоматизации для регистрации рабочей роли, получения заданий runbook и сообщения о состоянии. Для Windows этот агент является агентом log Analytics для Windows, также именуемым агентом мониторинга Майкрософт (MMA). Для Linux, это журнал Analytics агент для Linux.

### <a name="scenario-runbook-execution-fails"></a><a name="runbook-execution-fails"></a>Сценарий: происходит сбой выполнения модуля Runbook

#### <a name="issue"></a>Проблема

Выполнение Runbook не удается, и вы получите следующую ошибку.

```error
"The job action 'Activate' cannot be run, because the process stopped unexpectedly. The job action was attempted three times."
```

Ваша книга приостанавливается вскоре после того, как она пытается выполнить три раза. Есть условия, которые могут прервать runbook от завершения. Связанное сообщение об ошибке может не содержать никакой дополнительной информации.

#### <a name="cause"></a>Причина

Возможны следующие причины:

* Runbooks не может проверить подлинность с помощью местных ресурсов.

* Гибридная рабочая роль находится за прокси-сервером или брандмауэром.

* Компьютер, настроенный для запуска функции Hybrid Runbook Worker, не отвечает минимальным требованиям к оборудованию.

#### <a name="resolution"></a>Решение

Убедитесь, что компьютер имеет исходящий доступ к q **.azure-automation.net** на порту 443.

Компьютеры под управлением Hybrid Runbook Worker должны соответствовать минимальным требованиям оборудования, прежде чем рабочий настроен на размещение этой функции. Runbooks и фоновый процесс, который они используют, могут привести к чрезмерному использованию системы и вызвать задержки работы runbook или тайм-ауты.

Подтвердите, что компьютер для запуска функции Hybrid Runbook Worker отвечает минимальным требованиям оборудования. Если требования выполнены, отследите использование ЦП и памяти, чтобы определить корреляцию между производительностью процессов гибридной рабочей роли Runbook и Windows. Любое давление памяти или процессора может указывать на необходимость обновления ресурсов. Можно также выбрать другой вычислительный ресурс, который поддерживает минимальные требования и масштаб, когда требования рабочей нагрузки указывают на необходимость увеличения.

Проверьте журнал событий **Microsoft-SMA** для `Win32 Process Exited with code [4294967295]`соответствующего события с описанием. Причиной этой ошибки является то, что вы не настроили аутентификацию в своих runbooks или не указали учетные данные Run As для группы Hybrid Runbook Worker. Просмотрите разрешения Runbook в [runbooks на гибридном Runbook Worker,](../automation-hrw-run-runbooks.md) чтобы подтвердить, что вы правильно настроили аутентификацию для ваших runbooks.

### <a name="scenario-event-15011-in-hybrid-runbook-worker"></a><a name="cannot-connect-signalr"></a>Сценарий: Событие 15011 в гибридных Runbook работника

#### <a name="issue"></a>Проблема

Гибридный Runbook Worker получает событие 15011, указывая, что результат запроса недействителен. Следующая ошибка появляется при попытке работника открыть соединение с [сервером SignalR.](https://docs.microsoft.com/aspnet/core/signalr/introduction?view=aspnetcore-3.1)

```error
[AccountId={c7d22bd3-47b2-4144-bf88-97940102f6ca}]
[Uri=https://cc-jobruntimedata-prod-su1.azure-automation.net/notifications/hub][Exception=System.TimeoutException: Transport timed out trying to connect
   at System.Runtime.ExceptionServices.ExceptionDispatchInfo.Throw()
   at System.Runtime.CompilerServices.TaskAwaiter.HandleNonSuccessAndDebuggerNotification(Task task)
   at JobRuntimeData.NotificationsClient.JobRuntimeDataServiceSignalRClient.<Start>d__45.MoveNext()
```

#### <a name="cause"></a>Причина

Гибридный Runbook Worker не был настроен правильно для автоматизированного решения развертывания. Это решение содержит часть, которая соединяет VM с рабочим пространством Log Analytics. Скрипт PowerShell ищет рабочее пространство в подписке с предоставленным именем. В этом случае рабочее пространство Log Analytics находится в другой подписке. Скрипт не может найти рабочее пространство и пытается создать его, но имя уже взято. Таким образом, развертывание завершается неудачей.

#### <a name="resolution"></a>Решение

У вас есть два варианта решения этой проблемы:

* Измените скрипт PowerShell, чтобы найти рабочее пространство Log Analytics в другой подписке. Это хорошее решение, если вы планируете развернуть много гибридных машин Runbook Worker в будущем.

* Вручную настроить рабочую машину для работы в песочнице оркестратора. Затем запустите беговую книгу, созданную в учетной записи Azure Automation для рабочего, чтобы проверить функциональность.

### <a name="scenario-windows-azure-vms-automatically-dropped-from-hybrid-worker-group"></a><a name="vm-automatically-dropped"></a>Сценарий: Windows Azure VMs автоматически вырывается из гибридной рабочей группы

#### <a name="issue"></a>Проблема

Вы не можете видеть гибридный Runbook работника или vMs, когда рабочая машина была выключена в течение длительного времени.

#### <a name="cause"></a>Причина

Гибридная машина Runbook Worker не работает в Azure Automation более 30 дней. В результате автоматизация очистила группу Hybrid Runbook Worker или группу системного рабочего. 

#### <a name="resolution"></a>Решение

Запустите рабочую машину, а затем перерегистрируйте ее с помощью Azure Automation. Смотрите инструкции по установке среды runbook и подключению к автоматизации Azure в [развертывании гибридного Runbook работника Windows.](../automation-windows-hrw-install.md)

### <a name="scenario-no-certificate-was-found-in-the-certificate-store-on-hybrid-runbook-worker"></a><a name="no-cert-found"></a>Сценарий: Сертификат не найден в магазине сертификатов на Hybrid Runbook Worker

#### <a name="issue"></a>Проблема

Запуск книги, работающего на гибридном Runbook Worker, выходит из строя со следующим сообщением об ошибке.

```error
Connect-AzureRmAccount : No certificate was found in the certificate store with thumbprint 0000000000000000000000000000000000000000
At line:3 char:1
+ Connect-AzureRmAccount -ServicePrincipal -Tenant $Conn.TenantID -Appl ...
+ ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
    + CategoryInfo          : CloseError: (:) [Connect-AzureRmAccount], ArgumentException
    + FullyQualifiedErrorId : Microsoft.Azure.Commands.Profile.ConnectAzureRmAccountCommand
```
#### <a name="cause"></a>Причина

Эта ошибка возникает при попытке использовать [учетную запись Run As](../manage-runas-account.md) в runbook, который работает на гибридном Runbook Worker, где нет сертификата учетной записи Run As. Hybrid Runbook Workers не имеют актива сертификата локально по умолчанию, который требуется учетной записи Run As для нормальной работы.

#### <a name="resolution"></a>Решение

Если ваш гибридный Runbook Worker является VM Azure, вы можете использовать [управляемые идентификаторы для ресурсов Azure.](../automation-hrw-run-runbooks.md#managed-identities-for-azure-resources) Этот сценарий упрощает аутентификацию, позволяя аутентифицировать ресурсы Azure с помощью управляемого итога ВМ Azure вместо учетной записи Run As. Когда Hybrid Runbook Worker является штатной машиной, необходимо установить сертификат учетной записи Run As на машине. Чтобы узнать, как установить сертификат, ознакомьтесь с шагами для запуска runbook PowerShell Export-RunAsCertificateToHybridWorker в [запущенных runbooks на гибридном Runbook Worker.](../automation-hrw-run-runbooks.md)

### <a name="scenario-error-403-during-registration-of-hybrid-runbook-worker"></a><a name="error-403-on-registration"></a>Сценарий: Ошибка 403 во время регистрации гибридного runbook работника

#### <a name="issue"></a>Проблема

Начальная фаза регистрации работника завершается неудачей, и вы получаете следующую ошибку (403).

```error
"Forbidden: You don't have permission to access / on this server."
```

#### <a name="cause"></a>Причина

Возможны следующие причины:
* В настройках агента имеется неправильно набранный идентификатор рабочего пространства или ключ рабочей области (первичный). 
* Гибридный Runbook Worker не может загрузить конфигурацию, что вызывает ошибку связывания учетной записи. Когда Azure предоставляет решения, он поддерживает только определенные регионы для увязки рабочего пространства log Analytics и учетной записи автоматизации. Также возможно, что на компьютере установлена неверная дата и/или время. Если время составляет 15 минут от текущего времени, посадка не удается.

#### <a name="resolution"></a>Решение

##### <a name="mistyped-workspace-idkey"></a>Mistyped идентификатор/ключ рабочей области
Чтобы проверить, был ли неправильно введен идентификатор рабочего пространства или ключ рабочего пространства агента агента, [см. Добавление или удаление рабочего пространства - агента Windows](../../azure-monitor/platform/agent-manage.md#windows-agent) для агента Windows или добавление или удаление [рабочего пространства - агента Linux](../../azure-monitor/platform/agent-manage.md#linux-agent) для агента Linux.  Убедитесь в том, чтобы выбрать полную строку из портала Azure и скопировать и вставить его тщательно.

##### <a name="configuration-not-downloaded"></a>Конфигурация не загружена

Рабочая область и учетная запись Log Analytics должны находиться в связанном регионе. Для списка поддерживаемых регионов [см.](../how-to/region-mappings.md)

Возможно, вам также потребуется обновить дату и часовой пояс вашего компьютера. Если вы выберете пользовательский диапазон времени, убедитесь, что диапазон находится в UTC, который может отличаться от вашего местного часового пояса.

## <a name="linux"></a>Linux

Linux Hybrid Runbook Worker зависит от [агента Log Analytics для Linux,](../../azure-monitor/platform/log-analytics-agent.md) чтобы связаться с вашей учетной записью Автоматизация, чтобы зарегистрировать работника, получить работу runbook, и сообщить о состоянии. Если при регистрации рабочей роли произошла ошибка, это могло произойти по следующим причинам:

### <a name="scenario-the-log-analytics-agent-for-linux-isnt-running"></a><a name="oms-agent-not-running"></a>Сценарий: Агент аналитики журнала для Linux не работает

#### <a name="issue"></a>Проблема

Агент Log Analytics для Linux не работает

#### <a name="cause"></a>Причина

Если агент не работает, он не позволяет Linux Hybrid Runbook Worker общаться с Azure Automation. Агент может не работать по разным причинам.

#### <a name="resolution"></a>Решение

 Проверить агент работает, введя команду `ps -ef | grep python`. Вы должны увидеть выход, аналогичный следующему, Процессы Python с учетной записью пользователя **nxautomation.** Если решение Update Management или Azure Automation не включено, ни один из следующих процессов не выполняется.

```bash
nxautom+   8567      1  0 14:45 ?        00:00:00 python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/worker/main.py /var/opt/microsoft/omsagent/state/automationworker/oms.conf rworkspace:<workspaceId> <Linux hybrid worker version>
nxautom+   8593      1  0 14:45 ?        00:00:02 python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/worker/hybridworker.py /var/opt/microsoft/omsagent/state/automationworker/worker.conf managed rworkspace:<workspaceId> rversion:<Linux hybrid worker version>
nxautom+   8595      1  0 14:45 ?        00:00:02 python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/worker/hybridworker.py /var/opt/microsoft/omsagent/<workspaceId>/state/automationworker/diy/worker.conf managed rworkspace:<workspaceId> rversion:<Linux hybrid worker version>
```

Ниже перечислены процессы, запущенные для гибридной рабочей роли Runbook Linux. Все они расположены в **/var/opt/microsoft/omsagent/state/automationworker/directory.**

* **oms.conf** - Процесс менеджера работника. Он запускается непосредственно из DSC.

* **worker.conf** - Автоматически зарегистрированный гибридный рабочий процесс. Он запускается диспетчером рабочих ролей. Этот процесс используется в решении "Управление обновлениями" и незаметен для пользователя. Если решение "Управление обновлениями" не включено на компьютере, этот процесс будет отсутствовать.

* **diy/worker.conf** - процесс diy гибридного работника. Процесс гибридной рабочей роли DIY используется для выполнения модулей Runbook пользователя в гибридной рабочей роли Runbook. Он отличается от процесса автоматической регистрации гибридных рабочих только в ключевой детали, что он использует другую конфигурацию. Этот процесс не присутствует, если решение Azure Automation отключено, а diy Linux Hybrid Worker не зарегистрирован.

Если агент не работает, запустите следующую команду `sudo /opt/microsoft/omsagent/bin/service_control restart`для запуска службы: .

### <a name="scenario-the-specified-class-doesnt-exist"></a><a name="class-does-not-exist"></a>Сценарий: Указанный класс не существует

Если вы видите ошибку `The specified class does not exist..` в **/var/opt/microsoft/omsconfig/omsconfig.log,** агент Log Analytics для Linux должен быть обновлен. Выполнить следующую команду для переустановки агента:

```bash
wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh -w <WorkspaceID> -s <WorkspaceKey>
```

## <a name="windows"></a>Windows

Работник Windows Hybrid Runbook зависит от [агента Log Analytics для Windows,](../../azure-monitor/platform/log-analytics-agent.md) который должен общаться с учетной записью Автоматизации для регистрации сотрудника, получения заданий в runbook и состояния отчета. Если регистрация работника не удается, этот раздел включает в себя некоторые возможные причины.

### <a name="scenario-the-microsoft-monitoring-agent-isnt-running"></a><a name="mma-not-running"></a>Сценарий: Агент мониторинга Майкрософт не работает

#### <a name="issue"></a>Проблема

Служба `healthservice` не работает на компьютере с гибридной рабочей ролью Runbook.

#### <a name="cause"></a>Причина

Если служба агента мониторинга Майкрософт не работает, гибридный сотрудник Runbook не может общаться с Azure Automation.

#### <a name="resolution"></a>Решение

Убедитесь, что агент работает, введя `Get-Service healthservice`следующую команду в PowerShell: . Если служба остановлена, введите в PowerShell команду `Start-Service healthservice`, чтобы запустить эту службу.

### <a name="scenario-event-4502-in-operations-manager-log"></a><a name="event-4502"></a>Сценарий: Событие 4502 в журнале менеджера операций

#### <a name="issue"></a>Проблема

В журнале событий **«Менеджер по приложениям и службам»** вы увидите `Microsoft.EnterpriseManagement.HealthService.AzureAutomation.HybridAgent` событие 4502 и EventMessage, содержащее следующее описание:<br>`The certificate presented by the service \<wsid\>.oms.opinsights.azure.com was not issued by a certificate authority used for Microsoft services. Please contact your network administrator to see if they are running a proxy that intercepts TLS/SSL communication.`

#### <a name="cause"></a>Причина

Эта проблема может быть вызвана тем, что прокси-сервер или сетевой брандмауэр блокируют подключение к Microsoft Azure. Убедитесь, что компьютер имеет исходящий доступ к q **.azure-automation.net** на порту 443. 

#### <a name="resolution"></a>Решение

Логи хранятся локально на каждом гибридном работнике в **C: «ProgramData»Microsoft»System Center»Orchestrator»7.2-SMA-Sandboxes**. Вы можете проверить, есть ли какие-либо события предупреждения или ошибки в журналах событий **Службы приложений и служб — Microsoft-SMA,Operations** and **Application and Services Logs'Operations Manager.** Эти журналы указывают на подключение или другой тип проблемы, которая влияет на подключение роли к автоматизации Azure, или проблему, возникаемую при обычных операциях. Для дополнительной помощи проблемы с устранением неполадок с агентом Log Analytics, [см.](../../azure-monitor/platform/agent-windows-troubleshoot.md)

Гибридные работники отправляют [выходы и сообщения Runbook](../automation-runbook-output-and-messages.md) в Azure Automation так же, как задания runbook, работающие в облаке, отправляют выходные данные и сообщения. Вы можете включить потоки Verbose и Progress так же, как и для runbooks.

### <a name="scenario-hybrid-runbook-worker-not-reporting"></a><a name="corrupt-cache"></a>Сценарий: Гибридный работник Runbook не отчитывается

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

Чтобы решить данную проблему, войдите в систему гибридной рабочей роли Runbook и запустите следующий сценарий. Он останавливает Microsoft Monitoring Agent, удаляет его кэш и перезапускает службу. Это действие заставляет гибридную рабочую роль Runbook повторно скачать свою конфигурацию из службы автоматизации Azure.

```powershell
Stop-Service -Name HealthService

Remove-Item -Path 'C:\Program Files\Microsoft Monitoring Agent\Agent\Health Service State' -Recurse

Start-Service -Name HealthService
```

### <a name="scenario-you-cant-add-a-hybrid-runbook-worker"></a><a name="already-registered"></a>Сценарий: Вы не можете добавить гибридный runbook работника

#### <a name="issue"></a>Проблема

Вы получите следующее сообщение при попытке добавить гибридную рабочую роль Runbook с помощью командлета `Add-HybridRunbookWorker`.

```error
Machine is already registered
```

#### <a name="cause"></a>Причина

Эта проблема может быть вызвана, если машина уже зарегистрирована с другой учетной записью Автоматизация или если вы попытаетесь прочитать гибридный Runbook работника после удаления его из машины.

#### <a name="resolution"></a>Решение

Чтобы решить эту проблему, удалите следующий ключ реестра, перезапустите, `HealthService`и попробуйте `Add-HybridRunbookWorker` cmdlet еще раз.

`HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\HybridRunbookWorker`

## <a name="next-steps"></a>Дальнейшие действия

Если вы не видите своего варианта проблемы или вам не удается ее устранить, дополнительные сведения можно получить, посетив один из следующих каналов.

* Получите ответы от экспертов Azure через [форумы Azure.](https://azure.microsoft.com/support/forums/)
* Связь [@AzureSupport](https://twitter.com/azuresupport) с официальной учетной записью Microsoft Azure для улучшения обслуживания клиентов путем подключения сообщества Azure к нужным ресурсам: ответам, поддержке и экспертам.
* Если вам нужна дополнительная помощь, отправьте запрос в службу поддержки Azure. Перейдите на [сайт поддержки Azure](https://azure.microsoft.com/support/options/) и выберите **«Получите поддержку».**