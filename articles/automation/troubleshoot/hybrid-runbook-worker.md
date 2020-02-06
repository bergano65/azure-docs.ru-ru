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
ms.openlocfilehash: 4d804499116631be6f922f67f8b8f6c7063a6d5c
ms.sourcegitcommit: f0f73c51441aeb04a5c21a6e3205b7f520f8b0e1
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/05/2020
ms.locfileid: "77030733"
---
# <a name="troubleshoot-hybrid-runbook-workers"></a>Устранение неполадок с гибридными рабочими ролями Runbook

В этой статье приводятся сведения об устранении неполадок с гибридными рабочими ролями Runbook.

## <a name="general"></a>Общие

Гибридная рабочая роль Runbook зависит от агента, который используется для взаимодействия с учетной записью автоматизации для регистрации рабочей роли, получения заданий runbook и сообщения о состоянии. Для Windows это агент Log Analytics для Windows, который также называется Microsoft Monitoring Agent (MMA). Для Linux это Log Analytics агент для Linux.

### <a name="runbook-execution-fails"></a>Сценарий: происходит сбой выполнения модуля Runbook

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

Убедитесь, что компьютер имеет исходящий доступ к *. azure-automation.net через порт 443.

Компьютеры, на которых работает гибридная Рабочая роль Runbook, должны соответствовать минимальным требованиям к оборудованию, прежде чем рабочий процесс будет настроен для размещения этой функции. Модули Runbook и фоновый процесс, которые они используют, могут привести к чрезмерному использованию системы и вызвать задержки заданий или истечения времени ожидания задания Runbook.

Убедитесь, что компьютер, на котором работает гибридная Рабочая роль Runbook, соответствует минимальным требованиям к оборудованию. Если требования выполнены, отследите использование ЦП и памяти, чтобы определить корреляцию между производительностью процессов гибридной рабочей роли Runbook и Windows. Любой недостаток памяти или ЦП может указывать на необходимость обновления ресурсов. Кроме того, можно выбрать другой ресурс вычислений, который поддерживает минимальные требования и масштабироваться, если требования рабочей нагрузки указывают на необходимость увеличения.

Проверьте журнал событий **Microsoft-SMA** на наличие соответствующего события с описанием *Процесс Win32 завершен с кодом [4294967295]* . Причиной этой ошибки является то, что вы не настроили проверку подлинности в модулях Runbook или указали учетные данные запуска от имени для группы гибридных рабочих ролей. Ознакомьтесь с [разрешениями Runbook](../automation-hrw-run-runbooks.md#runbook-permissions) , чтобы подтвердить правильность настройки аутентификации для модулей Runbook.

### <a name="no-cert-found"></a>Сценарий: сертификат не найден в хранилище сертификатов в гибридной рабочей роли Runbook

#### <a name="issue"></a>Проблема

Модуль Runbook, выполняющийся в гибридной рабочей роли Runbook, завершается со следующим сообщением об ошибке.

```error
Connect-AzureRmAccount : No certificate was found in the certificate store with thumbprint 0000000000000000000000000000000000000000
At line:3 char:1
+ Connect-AzureRmAccount -ServicePrincipal -Tenant $Conn.TenantID -Appl ...
+ ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
    + CategoryInfo          : CloseError: (:) [Connect-AzureRmAccount], ArgumentException
    + FullyQualifiedErrorId : Microsoft.Azure.Commands.Profile.ConnectAzureRmAccountCommand
```
#### <a name="cause"></a>Причина

Эта ошибка возникает при попытке использовать [учетную запись запуска от имени](../manage-runas-account.md) в модуле Runbook, который выполняется в гибридной рабочей роли Runbook, когда отсутствует сертификат учетной записи запуска от имени. Гибридные рабочие роли Runbook по умолчанию не имеют ресурса сертификата локально, что требуется для правильной работы учетной записи запуска от имени.

#### <a name="resolution"></a>Решение

Если Гибридная Рабочая роль Runbook является виртуальной машиной Azure, вместо нее можно использовать [управляемые удостоверения для ресурсов Azure](../automation-hrw-run-runbooks.md#managed-identities-for-azure-resources) . Этот сценарий упрощает проверку подлинности, позволяя выполнять проверку подлинности в ресурсах Azure с помощью управляемого удостоверения виртуальной машины Azure, а не учетной записи запуска от имени. Если Гибридная Рабочая роль Runbook является локальным компьютером, необходимо установить сертификат учетной записи запуска от имени на компьютере. Чтобы узнать, как установить сертификат, ознакомьтесь с инструкциями по запуску модуля Runbook PowerShell Export-RunAsCertificateToHybridWorker в [запущенных модулях Runbook в гибридной рабочей роли Runbook](../automation-hrw-run-runbooks.md).

### <a name="error-403-on-registration"></a>Сценарий: Ошибка 403 во время регистрации гибридной рабочей роли Runbook

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

Также может потребоваться обновить дату и часовой пояс компьютера. При выборе настраиваемого диапазона времени убедитесь, что диапазон находится в формате UTC, который может отличаться от местного часового пояса.

## <a name="linux"></a>Linux

Гибридная Рабочая роль Runbook Linux зависит от [агента log Analytics для Linux](../../azure-monitor/platform/log-analytics-agent.md) , чтобы взаимодействовать с учетной записью службы автоматизации для регистрации рабочего процесса, получения заданий Runbook и состояния отчета. Если при регистрации рабочей роли произошла ошибка, это могло произойти по следующим причинам:

### <a name="oms-agent-not-running"></a>Сценарий: агент Log Analytics для Linux не работает

#### <a name="issue"></a>Проблема

Агент Log Analytics для Linux не работает

#### <a name="cause"></a>Причина

Если агент не работает, он не сможет взаимодействовать с службой автоматизации Azure с помощью гибридной рабочей роли Runbook Linux. Возможно, агент не работает по разным причинам.

#### <a name="resolution"></a>Решение

 Проверьте, запущен ли агент, выполнив следующую команду: `ps -ef | grep python`. Вы увидите выходные данные, похожие на следующие; это процессы python, запущенные от имени учетной записи **nxautomation**. Если решения "Управление обновлениями" и "Служба автоматизации Azure" не включены, ни один из следующих процессов не будет запущен.

```bash
nxautom+   8567      1  0 14:45 ?        00:00:00 python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/worker/main.py /var/opt/microsoft/omsagent/state/automationworker/oms.conf rworkspace:<workspaceId> <Linux hybrid worker version>
nxautom+   8593      1  0 14:45 ?        00:00:02 python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/worker/hybridworker.py /var/opt/microsoft/omsagent/state/automationworker/worker.conf managed rworkspace:<workspaceId> rversion:<Linux hybrid worker version>
nxautom+   8595      1  0 14:45 ?        00:00:02 python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/worker/hybridworker.py /var/opt/microsoft/omsagent/<workspaceId>/state/automationworker/diy/worker.conf managed rworkspace:<workspaceId> rversion:<Linux hybrid worker version>
```

Ниже перечислены процессы, запущенные для гибридной рабочей роли Runbook Linux. Все они находятся в каталоге `/var/opt/microsoft/omsagent/state/automationworker/`.

* **OMS. conf** — процесс диспетчера рабочих ролей. Он запускается непосредственно из DSC.

* **Worker. conf** — автоматический зарегистрированный гибридный рабочий процесс. Он запускается диспетчером рабочих ролей. Этот процесс используется в решении "Управление обновлениями" и незаметен для пользователя. Если решение "Управление обновлениями" не включено на компьютере, этот процесс будет отсутствовать.

* **DIY/Worker. conf** — рабочий процесс гибридного рабочего процесса DIY. Процесс гибридной рабочей роли DIY используется для выполнения модулей Runbook пользователя в гибридной рабочей роли Runbook. Он отличается от автоматического зарегистрированного гибридного рабочего процесса в основных деталях, в которых используется другая конфигурация. Этот процесс отсутствует, если решение службы автоматизации Azure отключено, а Гибридная Рабочая роль DIY Linux не зарегистрирована.

Если агент не запущен, выполните следующую команду, чтобы запустить службу: `sudo /opt/microsoft/omsagent/bin/service_control restart`.

### <a name="class-does-not-exist"></a>Сценарий: указанный класс не существует

Если отображается ошибка **, указанный класс не существует.** в `/var/opt/microsoft/omsconfig/omsconfig.log`необходимо обновить агент Log Analytics для Linux. Выполните следующую команду, чтобы переустановить агент:

```bash
wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh -w <WorkspaceID> -s <WorkspaceKey>
```

## <a name="windows"></a>Windows

Гибридная Рабочая роль Runbook Windows зависит от [агента log Analytics для Windows](../../azure-monitor/platform/log-analytics-agent.md) , который должен взаимодействовать с учетной записью службы автоматизации для регистрации рабочего процесса, получения заданий Runbook и состояния отчета. Если при регистрации рабочей роли произошла ошибка, это могло произойти по следующим причинам:

### <a name="mma-not-running"></a>Сценарий: Microsoft Monitoring Agent не работает

#### <a name="issue"></a>Проблема

Служба `healthservice` не работает на компьютере с гибридной рабочей ролью Runbook.

#### <a name="cause"></a>Причина

Если Microsoft Monitoring Agent служба Майкрософт не запущена, это состояние предотвращает обмен данными между гибридной рабочей ролью Runbook и службой автоматизации Azure.

#### <a name="resolution"></a>Решение

Проверьте, запущен ли агент, введя в PowerShell команду `Get-Service healthservice`. Если служба остановлена, введите в PowerShell команду `Start-Service healthservice`, чтобы запустить эту службу.

### <a name="event-4502"></a>Сценарий: событие 4502 в журнале Operations Manager

#### <a name="issue"></a>Проблема

В журнале событий **Application and Services Logs\Operations Manager** отображается событие 4502 и EventMessage, содержащее **Microsoft. EnterpriseManagement. HealthService. AzureAutomation. HybridAgent** со следующим описанием: *сертификат, предоставленный службой \<WSID\>. OMS.opinsights.Azure.com, не был выдан центром сертификации, используемым для служб Майкрософт. Обратитесь к администратору сети, чтобы узнать, работает ли прокси-сервер, который перехватывает обмен данными по протоколу TLS/SSL.*

#### <a name="cause"></a>Причина

Эта проблема может быть вызвана тем, что прокси-сервер или сетевой брандмауэр блокируют подключение к Microsoft Azure. Проверьте, имеет ли компьютер исходящий доступ к *.azure-automation.net на порту 443. 

#### <a name="resolution"></a>Решение

Журналы сохраняются локально в каждом гибридном компоненте Worker по адресу C:\ProgramData\Microsoft\System Center\Orchestrator\7.2\SMA\Sandboxes. Вы можете проверить наличие предупреждений или ошибок в журнале событий **Application and Services логс\микрософт-сма\оператионс** и **Application and Services Logs\Operations Manager** , которые указывают на подключение или другую неполадку, влияющую на адаптации роли к службе автоматизации Azure или к проблемам при нормальных операциях. Дополнительные сведения об устранении неполадок, связанных с агентом Log Analytics, см. [в статье Устранение неполадок с агентом log Analytics Windows](../../azure-monitor/platform/agent-windows-troubleshoot.md).

[Выходные данные Runbook и сообщения](../automation-runbook-output-and-messages.md) отправляются в службу автоматизации Azure из гибридных рабочих ролей точно так же, как задания Runbook, которые выполняются в облаке. Потоки Verbose и Progress можно активировать точно так же, как и для других модулей Runbook.

### <a name="corrupt-cache"></a>Сценарий: Гибридная Рабочая роль Runbook не сообщает

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

### <a name="already-registered"></a>Сценарий: невозможно добавить гибридную рабочую роль Runbook

#### <a name="issue"></a>Проблема

Вы получите следующее сообщение при попытке добавить гибридную рабочую роль Runbook с помощью командлета `Add-HybridRunbookWorker`.

```error
Machine is already registered
```

#### <a name="cause"></a>Причина

Эта проблема может быть вызвана тем, что компьютер уже зарегистрирован в другой учетной записи службы автоматизации или если вы попытаетесь прочитать гибридную рабочую роль Runbook после ее удаления с компьютера.

#### <a name="resolution"></a>Решение

Чтобы устранить эту проблему, удалите следующий раздел реестра, перезапустите `HealthService` и повторите командлет `Add-HybridRunbookWorker`.

`HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\HybridRunbookWorker`

## <a name="next-steps"></a>Следующие шаги

Если вы не видите своего варианта проблемы или вам не удается ее устранить, дополнительные сведения можно получить, посетив один из следующих каналов.

* Получите ответы специалистов Azure на [форумах Azure](https://azure.microsoft.com/support/forums/).
* Подключитесь к [@AzureSupport](https://twitter.com/azuresupport) — официальной учетной записи Microsoft Azure. Она помогает оптимизировать работу пользователей благодаря возможности доступа к ресурсам сообщества Azure (ответы на вопросы, поддержка и консультации специалистов).
* Если вам нужна дополнительная помощь, отправьте запрос в службу поддержки Azure. Перейдите на [сайт поддержки Azure](https://azure.microsoft.com/support/options/) и щелкните **Получить поддержку**.