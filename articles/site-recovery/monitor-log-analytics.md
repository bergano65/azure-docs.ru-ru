---
title: Мониторинг Azure Site Recovery с помощью журналов Azure Monitor (Log Analytics)
description: Сведения о мониторинге Azure Site Recovery с помощью журналов Azure Monitor (Log Analytics)
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 07/30/2019
ms.author: raynew
ms.openlocfilehash: 4eb88658437d3b29cc55d24bb83f73b660daea43
ms.sourcegitcommit: a52f17307cc36640426dac20b92136a163c799d0
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/01/2019
ms.locfileid: "68718487"
---
# <a name="monitor-site-recovery-with-azure-monitor-logs"></a>Мониторинг Site Recovery с помощью журналов Azure Monitor

В этой статье описывается мониторинг компьютеров, реплицированных с помощью Azure [Site Recovery](site-recovery-overview.md), с помощью [журналов Azure Monitor](../azure-monitor/platform/data-platform-logs.md)и [log Analytics](../azure-monitor/log-query/log-query-overview.md).

Журналы Azure Monitor предоставляют платформу данных журнала, которая собирает журналы действий и диагностики, а также другие данные мониторинга. В журналах Azure Monitor используется Log Analytics для написания и тестирования запросов журналов, а также для интерактивного анализа данных журнала. Вы можете визуализировать и запрашивать результаты журнала, а также настраивать оповещения для выполнения действий на основе наблюдаемых данных.

Для Site Recovery можно Azure Monitor журналы, которые помогут выполнить следующие действия.

- **Наблюдение за работоспособностью и состоянием Site Recovery**. Например, можно наблюдать за работоспособностью репликации, состоянием тестовой отработки отказа, Site Recovery событиями, целями точки восстановления (RPO) для защищенных компьютеров и частотой изменения дисковых данных.
- **Настройте оповещения для Site Recovery**. Например, можно настроить оповещения о работоспособности компьютера, состоянии тестовой отработки отказа или Site Recovery состояния задания.

Использование журналов Azure Monitor с Site Recovery поддерживается для репликации Azure в Azure, а также для репликации виртуальных машин и физических серверов VMware в Azure.
## <a name="before-you-start"></a>Перед началом

Вам потребуется следующее.

- По крайней мере один компьютер, защищенный в хранилище служб восстановления.
- Рабочая область Log Analytics для хранения журналов Site Recovery. [Сведения о](../azure-monitor/learn/quick-create-workspace.md) настройке рабочей области.
- Основные сведения о том, как писать, выполнять и анализировать запросы журналов в Log Analytics. [Узнайте больше](../azure-monitor/log-query/get-started-portal.md).

Перед началом работы рекомендуется ознакомиться с [общими вопросами мониторинга](monitoring-common-questions.md) .

## <a name="configure-site-recovery-to-send-logs"></a>Настройка Site Recovery для отправки журналов

1. В хранилище щелкните **Параметры** > диагностики**Добавить параметр диагностики**.

    ![Выбор журнала диагностики](./media/monitoring-log-analytics/add-diagnostic.png)

2. В окне **параметры диагностики**укажите имя действия журнала и выберите **отправить в log Analytics**.
3. Выберите подписку на Azure Monitor журналы и рабочую область Log Analytics.
4. В списке Журнал выберите все журналы с префиксом **азуреситерековери**. Затем нажмите кнопку **ОК**.

    ![Выберите рабочую область](./media/monitoring-log-analytics/select-workspace.png)

Журналы Site Recovery начинается с перевода в таблицу (**AzureDiagnostics**) в выбранной рабочей области.


## <a name="query-the-logs---examples"></a>Запрос журналов — примеры

Данные из журналов извлекаются с помощью запросов журналов, написанных на [языке запросов Kusto](../azure-monitor/log-query/get-started-queries.md). В этом разделе приводится несколько примеров распространенных запросов, которые можно использовать для мониторинга Site Recovery.

> [!NOTE]
> В некоторых примерах для параметра **replicationProviderName_s** используется значение **A2A**. При этом будут извлечены виртуальные машины Azure, которые реплицируются в дополнительный регион Azure, с помощью Site Recovery. В этих примерах можно заменить **A2A** на **InMageAzureV2**, если вы хотите получить локальные виртуальные машины VMware или физические серверы, которые реплицируются в Azure с помощью Site Recovery.


### <a name="query-replication-health"></a>Запрос работоспособности репликации

Этот запрос строит круговую диаграмму о текущей работоспособности репликации всех защищенных виртуальных машин Azure, разбитую на три состояния: Обычная, предупреждение или критическое.

```
AzureDiagnostics  
| where replicationProviderName_s == "A2A"   
| where isnotempty(name_s) and isnotnull(name_s)  
| summarize hint.strategy=partitioned arg_max(TimeGenerated, *) by name_s  
| project name_s , replicationHealth_s  
| summarize count() by replicationHealth_s  
| render piechart   
```
### <a name="query-mobility-service-version"></a>Запрос версии службы Mobility Service

Этот запрос строит круговую диаграмму виртуальных машин Azure, реплицированных с Site Recovery, с разбивкой по версии агента мобильности, в которой они выполняются.

```
AzureDiagnostics  
| where replicationProviderName_s == "A2A"   
| where isnotempty(name_s) and isnotnull(name_s)  
| summarize hint.strategy=partitioned arg_max(TimeGenerated, *) by name_s  
| project name_s , agentVersion_s  
| summarize count() by agentVersion_s  
| render piechart 
```

### <a name="query-rpo-time"></a>Время RPO запроса

Этот запрос строит линейчатую диаграмму виртуальных машин Azure, реплицированных с Site Recovery, разбитой на целевую точку восстановления (RPO): Менее чем за 15 минут, от 15-30 минут до 30 минут.

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

![ЦЕЛЕВая точка запроса](./media/monitoring-log-analytics/example1.png)

### <a name="query-site-recovery-jobs"></a>Запрос Site Recovery заданий

Этот запрос получает все задания Site Recovery (для всех сценариев аварийного восстановления), которые запускаются за последние 72 часов и их состояние завершения.

```
AzureDiagnostics  
| where Category == "AzureSiteRecoveryJobs"  
| where TimeGenerated >= ago(72h)   
| project JobName = OperationName , VaultName = Resource , TargetName = affectedResourceName_s, State = ResultType  
```

### <a name="query-site-recovery-events"></a>События Site Recovery запросов

Этот запрос получает все события Site Recovery (для всех сценариев аварийного восстановления), которые возникают за последние 72 часов и их серьезность. 

```
AzureDiagnostics   
| where Category == "AzureSiteRecoveryEvents"   
| where TimeGenerated >= ago(72h)   
| project AffectedObject=affectedResourceName_s , VaultName = Resource, Description_s = healthErrors_s , Severity = Level  
```

### <a name="query-test-failover-state-pie-chart"></a>Состояние тестовой отработки отказа запроса (круговая диаграмма)

Этот запрос строит круговую диаграмму для состояния тестовой отработки отказа виртуальных машин Azure, реплицированных с помощью Site Recovery.

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

### <a name="query-test-failover-state-table"></a>Состояние тестовой отработки отказа запроса (таблица)

Этот запрос отображает таблицу для состояния тестовой отработки отказа виртуальных машин Azure, реплицированных с помощью Site Recovery.

```
AzureDiagnostics   
| where replicationProviderName_s == "A2A"   
| where isnotempty(name_s) and isnotnull(name_s)   
| where isnotempty(failoverHealth_s) and isnotnull(failoverHealth_s)   
| summarize hint.strategy=partitioned arg_max(TimeGenerated, *) by name_s   
| project VirtualMachine = name_s , VaultName = Resource , TestFailoverStatus = failoverHealth_s 
```

### <a name="query-machine-rpo"></a>Запросить ЦЕЛЕВое значение Machine

Этот запрос строит граф тенденций, который отслеживает RPO определенной виртуальной машины Azure (ContosoVM123) за последние 72 часов.

```
AzureDiagnostics   
| where replicationProviderName_s == "A2A"   
| where TimeGenerated > ago(72h)  
| where isnotempty(name_s) and isnotnull(name_s)   
| where name_s == "ContosoVM123"  
| project TimeGenerated, name_s , RPO_in_seconds = rpoInSeconds_d   
| render timechart 
```
![Запросить ЦЕЛЕВое значение Machine](./media/monitoring-log-analytics/example2.png)

### <a name="query-data-change-rate-churn-for-a-vm"></a>Частота запросов на изменение данных (обновление) для виртуальной машины

Этот запрос строит диаграмму тренда для конкретной виртуальной машины Azure (ContosoVM123), которая отслеживает скорость изменения данных (байт в секунду) и скорость передачи данных. Эта информация доступна только для виртуальных машин Azure, реплицируемых в дополнительный регион Azure.

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
![Изменение данных запроса](./media/monitoring-log-analytics/example3.png)

### <a name="query-disaster-recovery-summary-azure-to-azure"></a>Запрос сводки аварийного восстановления (Azure в Azure)

Этот запрос строит сводную таблицу для виртуальных машин Azure, реплицированных в дополнительный регион Azure.  В нем отображаются имя виртуальной машины, репликация и состояние защиты, RPO, состояние тестовой отработки отказа, версия агента мобильности, все активные ошибки репликации и исходное расположение.

```
AzureDiagnostics 
| where replicationProviderName_s == "A2A"   
| where isnotempty(name_s) and isnotnull(name_s)   
| summarize hint.strategy=partitioned arg_max(TimeGenerated, *) by name_s   
| project VirtualMachine = name_s , Vault = Resource , ReplicationHealth = replicationHealth_s, Status = protectionState_s, RPO_in_seconds = rpoInSeconds_d, TestFailoverStatus = failoverHealth_s, AgentVersion = agentVersion_s, ReplicationError = replicationHealthErrors_s, SourceLocation = primaryFabricName_s 
```

### <a name="query-disaster-recovery-summary-vmwarephysical-servers"></a>Запрос сводки аварийного восстановления (VMware или физические серверы)

Этот запрос строит сводную таблицу для виртуальных машин VMware и физических серверов, реплицированных в Azure.  В нем отображаются имя компьютера, репликация и состояние защиты, RPO, состояние тестовой отработки отказа, версия агента мобильности, все активные ошибки репликации и соответствующий сервер обработки.

```
AzureDiagnostics  
| where replicationProviderName_s == "InMageAzureV2"   
| where isnotempty(name_s) and isnotnull(name_s)   
| summarize hint.strategy=partitioned arg_max(TimeGenerated, *) by name_s   
| project VirtualMachine = name_s , Vault = Resource , ReplicationHealth = replicationHealth_s, Status = protectionState_s, RPO_in_seconds = rpoInSeconds_d, TestFailoverStatus = failoverHealth_s, AgentVersion = agentVersion_s, ReplicationError = replicationHealthErrors_s, ProcessServer = processServerName_g  
```

## <a name="set-up-alerts---examples"></a>Настройка оповещений — примеры

Вы можете настроить Site Recovery оповещения на основе Azure Monitor данных. Дополнительные [сведения](../azure-monitor/platform/alerts-log.md#managing-log-alerts-from-the-azure-portal) о настройке оповещений журнала. 

> [!NOTE]
> В некоторых примерах для параметра **replicationProviderName_s** используется значение **A2A**. Этот параметр задает оповещения для виртуальных машин Azure, которые реплицируются в дополнительный регион Azure. В этих примерах можно заменить **A2A** на **InMageAzureV2** , если вы хотите настроить оповещения для локальных виртуальных машин VMware или физических серверов, реплицированных в Azure.

### <a name="multiple-machines-in-a-critical-state"></a>Несколько компьютеров в критическом состоянии

Настройте оповещение, если более 20 реплицированных виртуальных машин Azure переходит в критическое состояние.

```
AzureDiagnostics   
| where replicationProviderName_s == "A2A"   
| where replicationHealth_s == "Critical"  
| where isnotempty(name_s) and isnotnull(name_s)   
| summarize hint.strategy=partitioned arg_max(TimeGenerated, *) by name_s   
| summarize count() 
```
Для предупреждения установите пороговое **значение** 20.

### <a name="single-machine-in-a-critical-state"></a>Один компьютер в критическом состоянии

Настройте оповещение, если конкретная реплицированная виртуальная машина Azure переходит в критическое состояние.

```
AzureDiagnostics   
| where replicationProviderName_s == "A2A"   
| where replicationHealth_s == "Critical"  
| where name_s == "ContosoVM123"  
| where isnotempty(name_s) and isnotnull(name_s)   
| summarize hint.strategy=partitioned arg_max(TimeGenerated, *) by name_s   
| summarize count()  
```
Для предупреждения установите пороговое **значение** 1.

### <a name="multiple-machines-exceed-rpo"></a>Превышение RPO для нескольких компьютеров

Настройте оповещение, если значение RPO для более 20 виртуальных машин Azure превышает 30 минут.
```
AzureDiagnostics   
| where replicationProviderName_s == "A2A"   
| where isnotempty(name_s) and isnotnull(name_s)   
| where rpoInSeconds_d > 1800  
| summarize hint.strategy=partitioned arg_max(TimeGenerated, *) by name_s   
| project name_s , rpoInSeconds_d   
| summarize count()  
```
Для предупреждения установите пороговое **значение** 20.

### <a name="single-machine-exceeds-rpo"></a>Один компьютер превышает RPO

Настройте оповещение, если значение RPO для одной виртуальной машины Azure превышает 30 минут.

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
Для предупреждения установите пороговое **значение** 1.

### <a name="test-failover-for-multiple-machines-exceeds-90-days"></a>Тестовая отработка отказа для нескольких компьютеров превышает 90 дней

Настройте оповещение, если последняя успешная тестовая отработка отказа превысила 90 дней, для более чем 20 виртуальных машин. 

```
AzureDiagnostics  
| where replicationProviderName_s == "A2A"   
| where Category == "AzureSiteRecoveryReplicatedItems"  
| where isnotempty(name_s) and isnotnull(name_s)   
| where lastSuccessfulTestFailoverTime_t <= ago(90d)   
| summarize hint.strategy=partitioned arg_max(TimeGenerated, *) by name_s   
| summarize count()  
```
Для предупреждения установите пороговое **значение** 20.

### <a name="test-failover-for-single-machine-exceeds-90-days"></a>Тестовая отработка отказа для одного компьютера превышает 90 дней

Настройте оповещение, если последняя успешная тестовая отработка отказа для конкретной виртуальной машины была более 90 дней назад.
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
Для предупреждения установите пороговое **значение** 1.

### <a name="site-recovery-job-fails"></a>Сбой задания Site Recovery

Настройте оповещение, если задание Site Recovery (в данном случае задание повторной защиты) завершается сбоем для любого сценария Site Recovery, в течение последнего дня. 
```
AzureDiagnostics   
| where Category == "AzureSiteRecoveryJobs"   
| where OperationName == "Reprotect"  
| where ResultType == "Failed"  
| summarize count()  
```

Для предупреждения установите пороговое **значение** 1, а **период** — 1440 минут, чтобы проверить ошибки за последний день.

## <a name="next-steps"></a>Следующие шаги

[Дополнительные сведения о](site-recovery-monitor-and-troubleshoot.md) встроенном мониторинге Site Recovery.
