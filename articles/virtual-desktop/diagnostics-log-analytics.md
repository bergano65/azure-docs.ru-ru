---
title: Служба анализа журналов диагностики виртуальных рабочих столов Windows — Azure
description: Использование log Analytics с функцией диагностики виртуальных рабочих столов Windows.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 04/30/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 99a9e68a2e0c39364cc5105f230b00ffb90d867d
ms.sourcegitcommit: b396c674aa8f66597fa2dd6d6ed200dd7f409915
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/07/2020
ms.locfileid: "82888799"
---
# <a name="use-log-analytics-for-the-diagnostics-feature"></a>Использование Log Analytics для функции диагностики

>[!IMPORTANT]
>Это содержимое относится к обновлению пружины 2020 с Azure Resource Manager объектов виртуальных рабочих столов Windows. Если вы используете виртуальный рабочий стол Windows в выпуске 2019 без Azure Resource Manager объектов, см. [эту статью](./virtual-desktop-fall-2019/diagnostics-log-analytics-2019.md).
>
> Обновление 2020 виртуального рабочего стола Windows в настоящее время находится в общедоступной предварительной версии. Эта предварительная версия предоставляется без соглашения об уровне обслуживания, и мы не рекомендуем использовать ее для рабочих нагрузок. Некоторые функции могут не поддерживаться или их возможности могут быть ограничены. 
> Дополнительные сведения см. в статье [Дополнительные условия использования предварительных выпусков Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Виртуальный рабочий стол Windows использует [Azure Monitor](../azure-monitor/overview.md) для мониторинга и оповещений, таких как многие другие службы Azure. Это позволяет администраторам выявление проблем с помощью одного интерфейса. Служба создает журналы действий как для пользователей, так и для административных действий. Каждый журнал действий попадает в следующие категории:  

- Действия по управлению:
    - Следите за тем, что попытки изменения объектов виртуальных рабочих столов Windows с помощью API или PowerShell выполнены успешно. Например, может ли кто-то успешно создать пул узлов с помощью PowerShell?
- Канал 
    - Могут ли пользователи успешно подписываться на рабочие области? 
    - Пользователи видят все ресурсы, опубликованные в удаленный рабочий столном клиенте?
- Подключения: 
    - Когда пользователи инициируют и завершают подключение к службе. 
- Регистрация узла: 
    - Был ли узел сеанса успешно зарегистрирован в службе при подключении?
- Errors: 
    - Пользователи сталкиваются с проблемами с конкретными действиями? Эта функция может создать таблицу, которая будет отслеживать данные действий при условии, что данные присоединены к действиям.
- Контрольные точки  
    - Конкретные действия в течение времени существования действия, которое было достигнуто. Например, во время сеанса пользователь выполнял балансировку нагрузки на определенный узел, затем пользователь вошел в систему во время подключения и т. д.

Подключения, которые не достигают виртуальных рабочих столов Windows, не отображаются в результатах диагностики, так как сама служба роли диагностики является частью виртуального рабочего стола Windows. Проблемы с подключением к виртуальным рабочим столам Windows могут возникнуть, если у пользователя возникли проблемы с сетевым подключением.

Azure Monitor позволяет анализировать данные виртуальных рабочих столов Windows и просматривать счетчики производительности виртуальных машин (ВМ) в одном инструменте. В этой статье вы узнаете, как включить диагностику для среды виртуальных рабочих столов Windows. 

>[!NOTE] 
>Сведения о мониторинге виртуальных машин в Azure см. в статье [наблюдение за виртуальными машинами Azure с помощью Azure Monitor](../azure-monitor/insights/monitor-vm-azure.md). Кроме того, проверьте [пороговые значения счетчика производительности](../virtual-desktop/virtual-desktop-fall-2019/deploy-diagnostics.md#windows-performance-counter-thresholds) , чтобы лучше понять работу пользователя на узле сеансов.

## <a name="before-you-get-started"></a>Необходимые условия

Прежде чем можно будет использовать Log Analytics, необходимо создать рабочую область. Для этого следуйте инструкциям в одной из двух следующих статей:

- Если вы предпочитаете использовать портал Azure, см. раздел [Создание рабочей области log Analytics в портал Azure](../azure-monitor/learn/quick-create-workspace.md).
- Если вы предпочитаете PowerShell, см. статью [создание log Analytics рабочей области с помощью PowerShell](../azure-monitor/learn/quick-create-workspace-posh.md).

После создания рабочей области следуйте инструкциям в статье [Подключение компьютеров Windows к Azure Monitor](../azure-monitor/platform/agent-windows.md#obtain-workspace-id-and-key) , чтобы получить следующие сведения.

- ИДЕНТИФИКАТОР рабочей области
- Первичный ключ рабочей области

Эти сведения понадобятся вам позже в процессе установки.

Обязательно изучите Управление разрешениями для Azure Monitor, чтобы обеспечить доступ к данным для тех, кто отслеживает и поддерживает среду виртуальных рабочих столов Windows. Дополнительные сведения см. в статье [Приступая к работе с ролями, разрешениями и безопасностью с помощью Azure Monitor](../azure-monitor/platform/roles-permissions-security.md). 

## <a name="push-diagnostics-data-to-your-workspace"></a>Отправка данных диагностики в рабочую область

Вы можете отправлять диагностические данные из объектов виртуальных рабочих столов Windows в Log Analytics рабочей области. Эту функцию можно настроить сразу после создания объектов.

Чтобы настроить Log Analytics для нового объекта, сделайте следующее:

1. Войдите в портал Azure и перейдите к **виртуальному рабочему столу Windows**. 

2. Перейдите к объекту (например, к пулу узлов, группе приложений или рабочей области), для которого требуется собрать журналы и события. 

3. Выберите **параметры диагностики** в меню в левой части экрана. 

4. Выберите **Добавить параметр диагностики** в меню, которое отображается в правой части экрана. 
   
    Параметры, показанные на странице параметры диагностики, зависят от типа редактируемого объекта.

    Например, при включении диагностики для группы приложений вы увидите параметры настройки контрольных точек, ошибок и управления. Для рабочих областей эти категории настраивают веб-канал для наблюдения за тем, когда пользователи подписываются на список приложений. Дополнительные сведения о параметрах диагностики см. в статье [Создание параметров диагностики для сбора журналов ресурсов и метрик в Azure](../azure-monitor/platform/diagnostic-settings.md). 

     >[!IMPORTANT] 
     >Не забудьте включить диагностику для каждого объекта Azure Resource Manager, который требуется отслеживать. Данные будут доступны для действий после включения диагностики. После первой настройки может потребоваться несколько часов.  

5. Введите имя для конфигурации параметров, а затем выберите **отправить в log Analytics**. Имя, которое вы используете, не должно содержать пробелы и соответствовать [соглашениям об именовании Azure](../azure-resource-manager/management/resource-name-rules.md). В рамках журналов можно выбрать все параметры, которые необходимо добавить в Log Analytics, такие как контрольная точка, ошибка, управление и т. д.

6. Щелкните **Сохранить**.

>[!NOTE]
>Log Analytics предоставляет возможность потоковой передачи данных в [концентраторы событий](../event-hubs/event-hubs-about.md) или их архивацию в учетную запись хранения. Дополнительные сведения об этой функции см. в статье [потоковая передача данных мониторинга Azure в концентратор событий](../azure-monitor/platform/stream-monitoring-data-event-hubs.md) и [Архивация журналов ресурсов Azure в учетную запись хранения](../azure-monitor/platform/resource-logs-collect-storage.md). 

## <a name="how-to-access-log-analytics"></a>Как получить доступ к Log Analytics

Доступ к рабочим областям Log Analytics можно получить на портал Azure или Azure Monitor.

### <a name="access-log-analytics-on-a-log-analytics-workspace"></a>Доступ к Log Analytics в Log Analytics рабочей области

1. Войдите на портал Azure.

2. Выполните поиск по **log Analytics рабочей области**. 

3. В разделе "службы" выберите **log Analytics рабочие области**. 
   
4. В списке выберите рабочую область, настроенную для объекта виртуального рабочего стола Windows.

5. В рабочей области выберите **журналы**. Вы можете отфильтровать список меню с помощью функции **поиска** . 

### <a name="access-log-analytics-on-azure-monitor"></a>Доступ к Log Analytics в Azure Monitor

1. Войдите на портал Azure.

2. Найдите и выберите **монитор**. 

3. Выберите **журналы**.

4. Следуйте инструкциям на странице Ведение журнала, чтобы задать область запроса.  

5. Вы готовы к диагностике запросов. Все таблицы диагностики имеют префикс "ВВД".

## <a name="cadence-for-sending-diagnostic-events"></a>Ритмичность для отправки диагностических событий

События диагностики отправляются в Log Analytics по завершении.

Log Analytics только отчеты в этих промежуточных состояниях для действий подключения:

- Запущено
- Подключен
- Завершено

## <a name="example-queries"></a>Примеры запросов

В следующих примерах запросов показано, как функция диагностики создает отчет для наиболее частых действий в системе.

Чтобы получить список подключений, сделанных пользователями, выполните следующий командлет:

```kusto
WVDConnections 
| project-away TenantId,SourceSystem 
| summarize arg_max(TimeGenerated, *), StartTime =  min(iff(State== 'Started', TimeGenerated , datetime(null) )), ConnectTime = min(iff(State== 'Connected', TimeGenerated , datetime(null) ))   by CorrelationId 
| join kind=leftouter ( 
    WVDErrors 
    |summarize Errors=makelist(pack('Code', Code, 'CodeSymbolic', CodeSymbolic, 'Time', TimeGenerated, 'Message', Message ,'ServiceError', ServiceError, 'Source', Source)) by CorrelationId 
    ) on CorrelationId     
| join kind=leftouter ( 
   WVDCheckpoints 
   | summarize Checkpoints=makelist(pack('Time', TimeGenerated, 'Name', Name, 'Parameters', Parameters, 'Source', Source)) by CorrelationId 
   | mv-apply Checkpoints on 
    ( 
        order by todatetime(Checkpoints['Time']) asc 
        | summarize Checkpoints=makelist(Checkpoints) 
    ) 
   ) on CorrelationId 
| project-away CorrelationId1, CorrelationId2 
| order by  TimeGenerated desc 
```

Чтобы просмотреть действия веб-канала пользователей, сделайте следующее:

```kusto
WVDFeeds  
| project-away TenantId,SourceSystem  
| join kind=leftouter (  
    WVDErrors  
    |summarize Errors=makelist(pack('Code', Code, 'CodeSymbolic', CodeSymbolic, 'Time', TimeGenerated, 'Message', Message ,'ServiceError', ServiceError, 'Source', Source)) by CorrelationId  
    ) on CorrelationId      
| join kind=leftouter (  
   WVDCheckpoints  
   | summarize Checkpoints=makelist(pack('Time', TimeGenerated, 'Name', Name, 'Parameters', Parameters, 'Source', Source)) by CorrelationId  
   | mv-apply Checkpoints on  
    (  
        order by todatetime(Checkpoints['Time']) asc  
        | summarize Checkpoints=makelist(Checkpoints)  
    )  
   ) on CorrelationId  
| project-away CorrelationId1, CorrelationId2  
| order by  TimeGenerated desc 
```

Чтобы найти все подключения для одного пользователя, выполните следующие действия. 

```kusto
|where UserName == "userupn" 
|take 100 
|sort by TimeGenerated asc, CorrelationId 
```
 

Чтобы узнать, сколько раз подключено пользователей в день:

```kusto
WVDConnections 
|where UserName == "userupn" 
|take 100 
|sort by TimeGenerated asc, CorrelationId 
|summarize dcount(CorrelationId) by bin(TimeGenerated, 1d) 
```
 

Поиск длительности сеанса по пользователю:

```kusto
let Events = WVDConnections | where UserName == "userupn" ; 
Events 
| where State == "Connected" 
| project CorrelationId , UserName, ResourceAlias , StartTime=TimeGenerated 
| join (Events 
| where State == "Completed" 
| project EndTime=TimeGenerated, CorrelationId) 
on CorrelationId 
| project Duration = EndTime - StartTime, ResourceAlias 
| sort by Duration asc 
```

Чтобы найти ошибки для определенного пользователя, выполните следующие действия.

```kusto
WVDErrors
| where UserName == "userupn" 
|take 100
```

Чтобы определить, произошла ли конкретная ошибка, сделайте следующее:

```kusto
WVDErrors 
| where CodeSymbolic =="ErrorSymbolicCode" 
| summarize count(UserName) by CodeSymbolic 
```

Чтобы найти вхождение ошибки для всех пользователей, выполните следующие действия.

```kusto
WVDErrors 
| where ServiceError =="false" 
| summarize usercount = count(UserName) by CodeSymbolic 
| sort by usercount desc
| render barchart 
```

>[!NOTE]
>Наиболее важной таблицей для устранения неполадок является Ввдеррорс. Используйте этот запрос, чтобы узнать, какие проблемы возникают для действий пользователей, например подключений или каналов, когда пользователь подписывается на список приложений или настольных систем. В таблице будут показаны ошибки управления, а также проблемы с регистрацией узла.
>
>Если в общедоступной предварительной версии вам нужна помощь по устранению проблемы, убедитесь, что в запросе на помощь вы придаете значение CorrelationID для ошибки. Кроме того, убедитесь, что в значении ошибки службы всегда указано значение Сервицееррор = "false". Значение "false" означает, что проблема может быть решена задачей администрирования в конечном итоге. Если Сервицееррор = "true", необходимо эскалировать эту ошибку в корпорацию Майкрософт.

## <a name="next-steps"></a>Дальнейшие действия 

Чтобы просмотреть распространенные сценарии ошибок, которые может определить функция диагностики, см. раздел [Определение и диагностика проблем](diagnostics-role-service.md#common-error-scenarios).
