---
title: Создание оповещений о производительности в Azure Monitor для контейнеров | Документация Майкрософт
description: В этой статье описывается, как создать настраиваемые оповещения Azure на основе запросов к журналу для получения сведений об использовании памяти и ЦП с помощью Azure Monitor для контейнеров.
services: azure-monitor
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: azure-monitor
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/12/2019
ms.author: magoedte
ms.openlocfilehash: c275d50ab927895eca3aa018b71ab6989a4dde5a
ms.sourcegitcommit: de81b3fe220562a25c1aa74ff3aa9bdc214ddd65
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/13/2019
ms.locfileid: "56238847"
---
# <a name="how-to-set-up-alerts-in-azure-monitor-for-container-performance-problems"></a>Сведения о настройке в Azure Monitor оповещений о проблемах с производительностью контейнеров
Azure Monitor для контейнеров отслеживает производительность рабочих нагрузок контейнеров, развернутых в службе "Экземпляры контейнеров Azure" или на управляемых кластерах Kubernetes в Службе Azure Kubernetes (AKS). 

В этой статье описывается, как включить отправку оповещений при превышении установленного порога для использования процессора и памяти на узлах кластера.

## <a name="create-alert-on-cluster-cpu-or-memory-utilization"></a>Создание оповещения об использовании ЦП или памяти в кластере
Чтобы получать оповещения о высоком уровне использования ЦП или памяти для кластера, создайте правило генерации оповещений на основе измерения метрик, которое работает по предоставленным запросам к журналу. Эти запросы сопоставляют дату и время с текущими, используя оператор `now` и период в один час назад. Все даты, которые хранятся в Azure Monitor для контейнеров, указаны в формате UTC.  

Прежде чем приступить к работе, изучите статью [Обзор оповещений в Microsoft Azure](../platform/alerts-overview.md), если вы не знакомы с оповещениями в Azure Monitor. Дополнительные сведения о создании оповещений с использованием запросов к журналу см. в статье [Оповещения журнала в Azure Monitor](../platform/alerts-unified-log.md).

1. Войдите на [портале Azure](https://portal.azure.com).
2. На панели слева на портале Azure выберите **Монитор**. В разделе **Аналитика** выберите **Контейнеры**.    
3. На вкладке **Отслеживаемые кластеры** выберите из списка нужный кластер, щелкнув его имя.
4. На панели слева в разделе **Мониторинг** выберите **Журналы**, чтобы открыть страницу журналов Azure Monitor, где можно создать и выполнить запросы к Azure Log Analytics.
5. На странице **Журналы** щелкните **+ Новое правило генерации оповещений**.
6. В разделе **Условие** щелкните предварительно определенное пользовательское условие журнала с именем **Whenever the Custom log search is <logic undefined>** (Всякий раз, когда пользовательский поиск по журналу...). Для нас здесь автоматически устанавливается тип сигнала **Custom log search** (Пользовательский поиск по журналам), так как мы перешли к созданию правила генерации оповещений непосредственно со страницы журналов Azure Monitor.  
7. Вставьте один из приведенных ниже запросов в поле **Поисковый запрос**. Следующий запрос вычисляет средний уровень использования ЦП как среднее значение использования ЦП по всем узлам за каждую минуту.

    ```
    let endDateTime = now();
    let startDateTime = ago(1h);
    let trendBinSize = 1m;
    let capacityCounterName = 'cpuCapacityNanoCores';
    let usageCounterName = 'cpuUsageNanoCores';
    KubeNodeInventory
    | where TimeGenerated < endDateTime
    | where TimeGenerated >= startDateTime
    // cluster filter would go here if multiple clusters are reporting to the same Log Analytics workspace
    | distinct ClusterName, Computer
    | join hint.strategy=shuffle (
       Perf
       | where TimeGenerated < endDateTime
       | where TimeGenerated >= startDateTime
       | where ObjectName == 'K8SNode'
       | where CounterName == capacityCounterName
       | summarize LimitValue = max(CounterValue) by Computer, CounterName, bin(TimeGenerated, trendBinSize)
       | project Computer, CapacityStartTime = TimeGenerated, CapacityEndTime = TimeGenerated + trendBinSize, LimitValue
    ) on Computer
    | join kind=inner hint.strategy=shuffle (
       Perf
       | where TimeGenerated < endDateTime + trendBinSize
       | where TimeGenerated >= startDateTime - trendBinSize
       | where ObjectName == 'K8SNode'
       | where CounterName == usageCounterName
       | project Computer, UsageValue = CounterValue, TimeGenerated
    ) on Computer
    | where TimeGenerated >= CapacityStartTime and TimeGenerated < CapacityEndTime
    | project ClusterName, Computer, TimeGenerated, UsagePercent = UsageValue * 100.0 / LimitValue
    | summarize AggregatedValue = avg(UsagePercent) by bin(TimeGenerated, trendBinSize), ClusterName
    ```

    Следующий запрос вычисляет среднее использование памяти как среднее значение использования памяти по всем узлам за каждую минуту.

    ```
    let endDateTime = now();
    let startDateTime = ago(1h);
    let trendBinSize = 1m;
    let capacityCounterName = 'memoryCapacityBytes';
    let usageCounterName = 'memoryRssBytes';
    KubeNodeInventory
    | where TimeGenerated < endDateTime
    | where TimeGenerated >= startDateTime
    // cluster filter would go here if multiple clusters are reporting to the same Log Analytics workspace
    | distinct ClusterName, Computer
    | join hint.strategy=shuffle (
       Perf
       | where TimeGenerated < endDateTime
       | where TimeGenerated >= startDateTime
       | where ObjectName == 'K8SNode'
       | where CounterName == capacityCounterName
       | summarize LimitValue = max(CounterValue) by Computer, CounterName, bin(TimeGenerated, trendBinSize)
       | project Computer, CapacityStartTime = TimeGenerated, CapacityEndTime = TimeGenerated + trendBinSize, LimitValue
    ) on Computer
    | join kind=inner hint.strategy=shuffle (
       Perf
       | where TimeGenerated < endDateTime + trendBinSize
       | where TimeGenerated >= startDateTime - trendBinSize
       | where ObjectName == 'K8SNode'
       | where CounterName == usageCounterName
       | project Computer, UsageValue = CounterValue, TimeGenerated
    ) on Computer
    | where TimeGenerated >= CapacityStartTime and TimeGenerated < CapacityEndTime
    | project ClusterName, Computer, TimeGenerated, UsagePercent = UsageValue * 100.0 / LimitValue
    | summarize AggregatedValue = avg(UsagePercent) by bin(TimeGenerated, trendBinSize), ClusterName
    ```

8. Настройте оповещение, указав следующие сведения:

    a. В раскрывающемся списке **На основе** выберите **Измерение метрик**. Измерение метрик позволяет создать оповещение для каждого объекта в запросе, для которого значение превышает указанное пороговое значение.  
    б) В поле **Условие**выберите **Больше, чем** и введите значение **75** в качестве начального **порогового значения** (или другое значение, которое соответствует вашим требованиям).  
    c. В поле **Условие срабатывания предупреждения** выберите **Последовательные бреши**, а из раскрывающегося списка выберите **Больше, чем** и введите значение **2**.  
    4.3. В разделе **Вычисляется на основе** задайте для параметра **Период** значение 60 (минут). Это правило будет запускаться каждые пять минут и возвращать все записи, созданные за последний час. Установка большего периода потенциально может привести к задержкам при передаче данных, но в то же время гарантирует, что запрос всегда будет возвращать данные. Это, в свою очередь, позволит избежать ложноотрицательных результатов, при которых оповещение не срабатывает. 

9. Щелкните **Готово**, чтобы завершить создание правила генерации оповещений.
10. Введите имя оповещения в поле **Имя правила генерации оповещений**. Укажите для оповещения подробное **описание** и выберите подходящий уровень серьезности из предложенных вариантов.
11. Чтобы правило генерации оповещений начало работать сразу после создания, сохраните установленный по умолчанию флажок **Включить правило при создании**.
12. На последнем шаге укажите или создайте **группу действий**, которая позволит гарантировать выполнение одинаковых действий при каждом срабатывании оповещения. Эту функцию можно использовать для каждого созданного правила. При настройке учитывайте, как управляют инцидентами ИТ-специалисты или DevOps в вашей организации. 
13. Щелкните **Создать правило генерации оповещений**, чтобы завершить создание этого правила. Оно начнет выполняться немедленно.

## <a name="next-steps"></a>Дополнительная информация

* Изучите [примеры запросов к журналу](container-insights-analyze.md#search-logs-to-analyze-data), чтобы разобраться в предопределенных запросах, оценить и внедрить примеры, настроить оповещения для других сценариев. 
* Чтобы продолжить изучение способов использования Azure Monitor и отслеживать другие аспекты кластера AKS, ознакомьтесь со статьей [Общие сведения о производительности кластера AKS с Azure Monitor для контейнеров](container-insights-analyze.md).
