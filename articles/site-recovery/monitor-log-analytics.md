---
title: Мониторинг восстановления сайта Azure с помощью журналов Azure Monitor
description: Узнайте, как контролировать восстановление сайта Azure с помощью журналов мониторинга Azure (Log Analytics)
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/15/2019
ms.author: raynew
ms.openlocfilehash: f20d0d38a7fbd831d3e97a69373bac04b9b330aa
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74133415"
---
# <a name="monitor-site-recovery-with-azure-monitor-logs"></a>Мониторинг Site Recovery с помощью журналов Azure Monitor

В этой статье описывается, как контролировать машины, реплицированные путем [восстановления сайта](site-recovery-overview.md)Azure, с помощью [журналов Azure Monitor](../azure-monitor/platform/data-platform-logs.md)и [analytics log.](../azure-monitor/log-query/log-query-overview.md)

Журналы Azure Monitor обеспечивают платформу данных журналов, которая собирает журналы активности и диагностики, а также другие данные мониторинга. В журналах Azure Monitor вы используете журналНую аналитику для записи и тестирования запросов журналов, а также для интерактивного анализа данных журналов. Можно визуализировать результаты журналов и запросов, а также настроить оповещения для действий на основе контролируемых данных.

Для восстановления сайта можно выйти из журналов мониторинга Azure, которые помогут вам сделать следующее:

- **Мониторинг здоровья и состояния восстановления сайта.** Например, можно отслеживать состояние работоспособности репликации, состояние сбоя теста, события восстановления сайта, цели точек восстановления (RPOs) для защищенных машин и скорость изменения диска/данных.
- **Настройка оповещений для восстановления сайта.** Например, можно настроить оповещения для работоспособности машины, состояния сбоя теста или задания восстановления сайта.

Использование журналов Azure Monitor с восстановлением сайта поддерживается для репликации **Azure к Azure,** а **VMware VM/физический сервер — для** репликации Azure.

> [!NOTE]
> Чтобы получить журналы данных оттока и журналы скорости загрузки для VMware и физических машин, необходимо установить агент мониторинга Майкрософт на сервере процесса. Этот агент отправляет журналы репликационных машин в рабочее пространство. Эта возможность доступна только для версии агента мобильности и далее.

## <a name="before-you-start"></a>Перед началом работы

Вам потребуется следующее.

- По крайней мере одна машина защищена в хранилище служб восстановления.
- Рабочее пространство для хранения журналов восстановления сайта для хранения журналов «Аналитика журнала». [Узнайте о](../azure-monitor/learn/quick-create-workspace.md) настройке рабочего пространства.
- Базовое понимание того, как писать, запускать и анализировать запросы журналов в журнале Analytics. Ознакомьтесь с [дополнительными сведениями](../azure-monitor/log-query/get-started-portal.md).

Мы рекомендуем вам просмотреть [общие вопросы мониторинга,](monitoring-common-questions.md) прежде чем начать.

## <a name="configure-site-recovery-to-send-logs"></a>Настройка восстановления сайта для отправки журналов

1. В хранилище нажмите **Диагностические настройки** > **Добавить диагностическую настройку.**

    ![Выберите диагностическую регистрацию](./media/monitoring-log-analytics/add-diagnostic.png)

2. В **настройках Диагностики**укажите имя и проверьте поле **Send to Log Analytics.**
3. Выберите подписку на журналы Azure Monitor и рабочее пространство для анализа журналов.
4. Выберите **Azure Diagnostics** в переключении.
5. Из списка журналов выберите все журналы с приставкой **AzureSiteRecovery.** После этого щелкните **OK**.

    ![Выбор рабочей области](./media/monitoring-log-analytics/select-workspace.png)

В выбранном рабочем пространстве журналы восстановления сайта начинают поступать в таблицу **(AzureDiagnostics)** в выбранном рабочем пространстве.

## <a name="configure-microsoft-monitoring-agent-on-the-process-server-to-send-churn-and-upload-rate-logs"></a>Настройка агента мониторинга Майкрософт на сервере процесса для отправки журналов ставок оттока и загрузки

Вы можете зафиксировать информацию о скорости оттока данных и информацию о частоте загрузки исходных данных для ваших VMware/физических машин в помещениях. Для этого на сервере процесса требуется установка агента мониторинга Майкрософт.

1. Перейдите в рабочее пространство Log Analytics и нажмите на **Расширенные настройки.**
2. Нажмите на страницу **Подключенных источников** и выберите **серверы Windows.**
3. Загрузите windows Agent (64 бита) на сервере процесса. 
4. [Получение идентификатора рабочего пространства и ключа](../azure-monitor/platform/agent-windows.md#obtain-workspace-id-and-key)
5. [Настройка агента для использования TLS 1.2](../azure-monitor/platform/agent-windows.md#configure-agent-to-use-tls-12)
6. [Завершите установку агента,](../azure-monitor/platform/agent-windows.md#install-the-agent-using-setup-wizard) предоставив полученный идентификатор рабочего пространства и ключ.
7. После завершения установки перейдите в рабочее пространство Log Analytics и нажмите на **Расширенные настройки.** Перейдите на страницу **данных** и нажмите на **счетчики производительности Windows.** 
8. Нажмите на **кнопку «Я»,** чтобы добавить следующие два счетчика с интервалом в 300 секунд:

        ASRAnalytics(*)\SourceVmChurnRate 
        ASRAnalytics(*)\SourceVmThrpRate 

Данные о частоте оттока и загрузки начнут подаваться в рабочее пространство.


## <a name="query-the-logs---examples"></a>Запрос журналов - примеры

Вы извлекаете данные из журналов с помощью запросов журналов, написанных [языком запроса Kusto.](../azure-monitor/log-query/get-started-queries.md) В этом разделе приведено несколько примеров общих запросов, которые можно использовать для мониторинга восстановления сайта.

> [!NOTE]
> Некоторые примеры используют **replicationProviderName_s** установлены на **A2A**. Это извлечения VMs Azure, которые реплицируются во вторичную область Azure с помощью восстановления сайта. В этих примерах можно заменить **A2A** на **InMageAzureV2,** если вы хотите получить на местах VMwv или физические серверы, которые реплицируются в Azure с помощью восстановления сайта.


### <a name="query-replication-health"></a>Здоровье репликации запросов

Этот запрос графики круговой диаграммы для текущего состояния репликации всех защищенных VMs Azure, разбитых на три состояния: Normal, Предупреждение или Критический.

```
AzureDiagnostics  
| where replicationProviderName_s == "A2A"   
| where isnotempty(name_s) and isnotnull(name_s)  
| summarize hint.strategy=partitioned arg_max(TimeGenerated, *) by name_s  
| project name_s , replicationHealth_s  
| summarize count() by replicationHealth_s  
| render piechart   
```
### <a name="query-mobility-service-version"></a>Версия службы мобильности запросов

Этот запрос графики круговой диаграммы для Azure VMs реплицируется с восстановлением сайта, разбитые версии агента мобильности, что они работают.

```
AzureDiagnostics  
| where replicationProviderName_s == "A2A"   
| where isnotempty(name_s) and isnotnull(name_s)  
| summarize hint.strategy=partitioned arg_max(TimeGenerated, *) by name_s  
| project name_s , agentVersion_s  
| summarize count() by agentVersion_s  
| render piechart 
```

### <a name="query-rpo-time"></a>Время запроса RPO

Этот запрос показывает диаграмму баров Azure VMs, реплицированную с помощью восстановления сайта, разбиваемых по цели точки восстановления (RPO): менее 15 минут, от 15 до 30 минут, более 30 минут.

```
AzureDiagnostics 
| where replicationProviderName_s == "A2A"   
| where isnotempty(name_s) and isnotnull(name_s)  
| extend RPO = case(rpoInSeconds_d <= 900, "<15Min",   
rpoInSeconds_d <= 1800, "15-30Min", ">30Min")  
| summarize hint.strategy=partitioned arg_max(TimeGenerated, *) by name_s  
| project name_s , RPO  
| summarize Count = count() by RPO  
| render barchart 
```

![Запрос RPO](./media/monitoring-log-analytics/example1.png)

### <a name="query-site-recovery-jobs"></a>Задания восстановления сайта запросов

Этот запрос получает все задания восстановления сайта (для всех сценариев аварийного восстановления), срабатывающие за последние 72 часа, и состояние их завершения.

```
AzureDiagnostics  
| where Category == "AzureSiteRecoveryJobs"  
| where TimeGenerated >= ago(72h)   
| project JobName = OperationName , VaultName = Resource , TargetName = affectedResourceName_s, State = ResultType  
```

### <a name="query-site-recovery-events"></a>События восстановления сайта запроса

Этот запрос извлекает все события восстановления сайта (для всех сценариев аварийного восстановления), поднятые за последние 72 часа, а также их серьезность. 

```
AzureDiagnostics   
| where Category == "AzureSiteRecoveryEvents"   
| where TimeGenerated >= ago(72h)   
| project AffectedObject=affectedResourceName_s , VaultName = Resource, Description_s = healthErrors_s , Severity = Level  
```

### <a name="query-test-failover-state-pie-chart"></a>Состояние совмещение теста запроса (круговая диаграмма)

Этот запрос графики круговой диаграммы для тестового статуса failover Azure VMs реплицируется с восстановлением сайта.

```
AzureDiagnostics  
| where replicationProviderName_s == "A2A"   
| where isnotempty(name_s) and isnotnull(name_s)  
| where isnotempty(failoverHealth_s) and isnotnull(failoverHealth_s)  
| summarize hint.strategy=partitioned arg_max(TimeGenerated, *) by name_s  
| project name_s , Resource, failoverHealth_s  
| summarize count() by failoverHealth_s  
| render piechart 
```

### <a name="query-test-failover-state-table"></a>Состояние совмещение теста запроса (таблица)

Этот запрос графики таблицы для теста failover статус Azure VMs реплицируется с восстановлением сайта.

```
AzureDiagnostics   
| where replicationProviderName_s == "A2A"   
| where isnotempty(name_s) and isnotnull(name_s)   
| where isnotempty(failoverHealth_s) and isnotnull(failoverHealth_s)   
| summarize hint.strategy=partitioned arg_max(TimeGenerated, *) by name_s   
| project VirtualMachine = name_s , VaultName = Resource , TestFailoverStatus = failoverHealth_s 
```

### <a name="query-machine-rpo"></a>Машина запроса RPO

Этот запрос графиков график тенденции, который отслеживает RPO конкретного Azure VM (ContosoVM123) в течение последних 72 часов.

```
AzureDiagnostics   
| where replicationProviderName_s == "A2A"   
| where TimeGenerated > ago(72h)  
| where isnotempty(name_s) and isnotnull(name_s)   
| where name_s == "ContosoVM123"  
| project TimeGenerated, name_s , RPO_in_seconds = rpoInSeconds_d   
| render timechart 
```
![Машина запроса RPO](./media/monitoring-log-analytics/example2.png)

### <a name="query-data-change-rate-churn-and-upload-rate-for-an-azure-vm"></a>Скорость изменения данных запросов (отток) и скорость загрузки для Azure VM

Этот запрос графики график тренда для конкретного Azure VM (ContosoVM123), который представляет скорость изменения данных (Запись байтов в секунду), и скорость загрузки данных. 

```
AzureDiagnostics   
| where Category in ("AzureSiteRecoveryProtectedDiskDataChurn", "AzureSiteRecoveryReplicationDataUploadRate")   
| extend CategoryS = case(Category contains "Churn", "DataChurn",   
Category contains "Upload", "UploadRate", "none")  
| extend InstanceWithType=strcat(CategoryS, "_", InstanceName_s)   
| where TimeGenerated > ago(24h)   
| where InstanceName_s startswith "ContosoVM123"   
| project TimeGenerated , InstanceWithType , Churn_MBps = todouble(Value_s)/1048576   
| render timechart  
```
![Изменение данных запросов](./media/monitoring-log-analytics/example3.png)

### <a name="query-data-change-rate-churn-and-upload-rate-for-a-vmware-or-physical-machine"></a>Скорость изменения данных запросов (отток) и скорость загрузки для VMware или физической машины

> [!Note]
> Убедитесь, что вы установите агент мониторинга на сервере процесса, чтобы получить эти журналы. Ссылайтесь [на шаги для настройки агента мониторинга.](#configure-microsoft-monitoring-agent-on-the-process-server-to-send-churn-and-upload-rate-logs)

Этот запрос графики график тренда для конкретного **диска0** реплицированных пункта **win-9r7sfh9qlru**, который представляет скорость изменения данных (Запись Байтов в секунду), и скорость загрузки данных. Вы можете найти имя диска на лезвии **дисков** реплицированного элемента в хранилище служб восстановления. Имя экземпляра, которое будет использоваться в запросе, — это имя Машины DNS, за которым следует имя q и имя диска, как в этом примере.

```
Perf
| where ObjectName == "ASRAnalytics"
| where InstanceName contains "win-9r7sfh9qlru_disk0"
| where TimeGenerated >= ago(4h) 
| project TimeGenerated ,CounterName, Churn_MBps = todouble(CounterValue)/5242880 
| render timechart
```
Process Server передвигает эти данные каждые 5 минут в рабочее пространство Log Analytics. Эти точки данных представляют собой среднее вычисление за 5 минут.

### <a name="query-disaster-recovery-summary-azure-to-azure"></a>Резюме аварийного восстановления запросов (Azure to Azure)

В этом запросе приводится сводная таблица для VMs Azure, реплицируемая на второстепенную область Azure.  Он показывает имя VM, статус репликации и защиты, RPO, статус сбоя теста, версию агента мобильности, любые активные ошибки репликации и место исходного кода.

```
AzureDiagnostics 
| where replicationProviderName_s == "A2A"   
| where isnotempty(name_s) and isnotnull(name_s)   
| summarize hint.strategy=partitioned arg_max(TimeGenerated, *) by name_s   
| project VirtualMachine = name_s , Vault = Resource , ReplicationHealth = replicationHealth_s, Status = protectionState_s, RPO_in_seconds = rpoInSeconds_d, TestFailoverStatus = failoverHealth_s, AgentVersion = agentVersion_s, ReplicationError = replicationHealthErrors_s, SourceLocation = primaryFabricName_s 
```

### <a name="query-disaster-recovery-summary-vmwarephysical-servers"></a>Резюме аварийного восстановления запросов (VMware/физические серверы)

В этом запросе приводится сводная таблица для VMware VMs и физических серверов, реплицированных в Azure.  Он показывает имя машины, статус репликации и защиты, RPO, статус сбоя теста, версию агента мобильности, любые активные ошибки репликации и соответствующий сервер процесса.

```
AzureDiagnostics  
| where replicationProviderName_s == "InMageAzureV2"   
| where isnotempty(name_s) and isnotnull(name_s)   
| summarize hint.strategy=partitioned arg_max(TimeGenerated, *) by name_s   
| project VirtualMachine = name_s , Vault = Resource , ReplicationHealth = replicationHealth_s, Status = protectionState_s, RPO_in_seconds = rpoInSeconds_d, TestFailoverStatus = failoverHealth_s, AgentVersion = agentVersion_s, ReplicationError = replicationHealthErrors_s, ProcessServer = processServerName_g  
```

## <a name="set-up-alerts---examples"></a>Настройка предупреждений - примеры

Можно настроить оповещения о восстановлении сайтов на основе данных Azure Monitor. [Подробнее](../azure-monitor/platform/alerts-log.md#managing-log-alerts-from-the-azure-portal) о настройке оповещений о журналах. 

> [!NOTE]
> Некоторые примеры используют **replicationProviderName_s** установлены на **A2A**. Это устанавливает оповещения для VMs Azure, которые реплицируются в второстепенную область Azure. В этих примерах можно заменить **A2A** на **InMageAzureV2,** если вы хотите установить оповещения для напремусорных VMw VMw или физических серверов, реплицированных в Azure.

### <a name="multiple-machines-in-a-critical-state"></a>Несколько машин в критическом состоянии

Нависьте оповещение, если более 20 реплицированных VMs Azure перейдут в критическое состояние.

```
AzureDiagnostics   
| where replicationProviderName_s == "A2A"   
| where replicationHealth_s == "Critical"  
| where isnotempty(name_s) and isnotnull(name_s)   
| summarize hint.strategy=partitioned arg_max(TimeGenerated, *) by name_s   
| summarize count() 
```
Для оповещения установите **значение Порога** до 20.

### <a name="single-machine-in-a-critical-state"></a>Одиночная машина в критическом состоянии

Нависьте оповещение, если определенный реплицированный VM Azure переходит в критическое состояние.

```
AzureDiagnostics   
| where replicationProviderName_s == "A2A"   
| where replicationHealth_s == "Critical"  
| where name_s == "ContosoVM123"  
| where isnotempty(name_s) and isnotnull(name_s)   
| summarize hint.strategy=partitioned arg_max(TimeGenerated, *) by name_s   
| summarize count()  
```
Для оповещения установите **значение Порога** до 1.

### <a name="multiple-machines-exceed-rpo"></a>Несколько машин превышают RPO

Наполните оповещение, если RPO для более чем 20 VMs Azure превышает 30 минут.
```
AzureDiagnostics   
| where replicationProviderName_s == "A2A"   
| where isnotempty(name_s) and isnotnull(name_s)   
| where rpoInSeconds_d > 1800  
| summarize hint.strategy=partitioned arg_max(TimeGenerated, *) by name_s   
| project name_s , rpoInSeconds_d   
| summarize count()  
```
Для оповещения установите **значение Порога** до 20.

### <a name="single-machine-exceeds-rpo"></a>Одиночная машина превышает RPO

Назначь оповещение, если RPO для одного Azure VM превышает 30 минут.

```
AzureDiagnostics   
| where replicationProviderName_s == "A2A"   
| where isnotempty(name_s) and isnotnull(name_s)   
| where name_s == "ContosoVM123"  
| where rpoInSeconds_d > 1800  
| summarize hint.strategy=partitioned arg_max(TimeGenerated, *) by name_s   
| project name_s , rpoInSeconds_d   
| summarize count()  
```
Для оповещения установите **значение Порога** до 1.

### <a name="test-failover-for-multiple-machines-exceeds-90-days"></a>Сбой теста для нескольких машин превышает 90 дней

Нависните оповещение, если последний успешный тест-неудачник длился более 90 дней, для более чем 20 ВМ. 

```
AzureDiagnostics  
| where replicationProviderName_s == "A2A"   
| where Category == "AzureSiteRecoveryReplicatedItems"  
| where isnotempty(name_s) and isnotnull(name_s)   
| where lastSuccessfulTestFailoverTime_t <= ago(90d)   
| summarize hint.strategy=partitioned arg_max(TimeGenerated, *) by name_s   
| summarize count()  
```
Для оповещения установите **значение Порога** до 20.

### <a name="test-failover-for-single-machine-exceeds-90-days"></a>Сбой теста для одной машины превышает 90 дней

Навеменить оповещение, если последний успешный тест failover для конкретного VM было более 90 дней назад.
```
AzureDiagnostics  
| where replicationProviderName_s == "A2A"   
| where Category == "AzureSiteRecoveryReplicatedItems"  
| where isnotempty(name_s) and isnotnull(name_s)   
| where lastSuccessfulTestFailoverTime_t <= ago(90d)   
| where name_s == "ContosoVM123"  
| summarize hint.strategy=partitioned arg_max(TimeGenerated, *) by name_s   
| summarize count()  
```
Для оповещения установите **значение Порога** до 1.

### <a name="site-recovery-job-fails"></a>Задание восстановления сайта не удается

Настройка оповещения, если задание восстановления сайта (в данном случае задание Reprotect) не выполняется для любого сценария восстановления сайта в течение последнего дня. 
```
AzureDiagnostics   
| where Category == "AzureSiteRecoveryJobs"   
| where OperationName == "Reprotect"  
| where ResultType == "Failed"  
| summarize count()  
```

Для оповещения установите **значение Порога** до 1 и **период** до 1440 минут, чтобы проверить сбои в последний день.

## <a name="next-steps"></a>Дальнейшие действия

[Узнайте о](site-recovery-monitor-and-troubleshoot.md) встроенном мониторинге восстановления сайта.
