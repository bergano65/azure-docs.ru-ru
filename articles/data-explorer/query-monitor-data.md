---
title: Запросы к данным в Azure Monitor с помощью обозревателя данных Azure (Предварительная версия)
description: В этом разделе запрашивать данные в Azure Monitor, создание прокси обозревателя данных Azure для запросов векторного произведения в Application Insights и Log Analytics
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: conceptual
ms.date: 07/10/2019
ms.openlocfilehash: f363e59e6faa6b115eb40a2a5d35432f02299d52
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/11/2019
ms.locfileid: "67811197"
---
# <a name="query-data-in-azure-monitor-using-azure-data-explorer-preview"></a>Запросы к данным в Azure Monitor с помощью обозревателя данных Azure (Предварительная версия)

Кластер прокси-сервера обозревателя данных Azure (ADX прокси) находится сущность, которая позволяет выполнять запросы векторное произведение между обозреватель данных Azure, [Application Insights (AI)](/azure/azure-monitor/app/app-insights-overview), и [Log Analytics (LA)](/azure/azure-monitor/platform/data-platform-logs) в [Azure Monitor](/azure/azure-monitor/) службы. Вы можете сопоставить рабочих областей Azure Monitor Log Analytics или приложений Application Insights как кластер прокси-сервера. Затем можно запросить кластер прокси-сервера, с помощью средства обозревателя данных Azure и ссылаться на него в запросе перекрестного кластера. Статьи показано, как подключиться к кластеру прокси-сервера, добавить кластер прокси-сервера для Azure Data Explorer веб-интерфейса и выполнять запросы к ваших приложений искусственного Интеллекта или LA рабочих областей с помощью обозревателя данных Azure.

Поток прокси-сервера обозревателя данных Azure: 

![Поток ADX прокси-сервера](media/adx-proxy/adx-proxy-flow.png)

## <a name="prerequisites"></a>предварительные требования

> [!NOTE]
> ADX прокси-сервер находится в режиме предварительного просмотра. Чтобы включить эту функцию, обратитесь в службу [ADXProxy](mailto:adxproxy@microsoft.com) team.

## <a name="connect-to-the-proxy"></a>Подключение к прокси-сервера

1. Проверка вашего собственного кластера обозреватель данных Azure (таких как *помочь* кластера) появится в меню слева, прежде чем подключиться к кластеру Log Analytics или Application Insights.

    ![ADX собственного кластера](media/adx-proxy/web-ui-help-cluster.png)

1. В пользовательском Интерфейсе обозревателя данных Azure (https://dataexplorer.azure.com/clusters) выберите **добавить кластер**.

1. В **добавить кластер** окна:

    * Добавьте URL-адрес кластера LA или искусственного Интеллекта. Например: `https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>`

    * Выберите **Добавить**.

    ![Добавление кластера](media/adx-proxy/add-cluster.png)

    При добавлении соединения для нескольких кластеров прокси-сервера, каждый можно присвойте другое имя. В противном случае они все получите тем же именем в области слева.

1. После того как соединение установлено, ЛА или AI кластера будет отображаться в левой области в собственном кластере ADX. 

    ![Log Analytics и Azure Data Explorer кластеров](media/adx-proxy/la-adx-clusters.png)

## <a name="run-queries"></a>Выполнение запросов

Обозреватель Kusto, ADX веб-обозревателе Jupyter Kqlmagic или REST API можно использовать для запроса прокси-сервера кластеров. 

> [!TIP]
> * Имя базы данных должно иметь имя, совпадающее с именем ресурса, указанного в прокси-сервера кластера. Имена чувствительны к регистру.
> * В перекрестные запросы к кластера, убедитесь, что [приложений и рабочих областей и](#application-insights-app-and-log-analytics-workspace-names) указано правильно.

### <a name="query-against-the-native-azure-data-explorer-cluster"></a>Запросы к собственного кластера обозреватель данных Azure 

Выполнение запросов в кластере Azure обозреватель данных (таких как *StormEvents* в таблицу *помочь* кластера). При запуске запроса, должен быть выбран вашего собственного кластера обозреватель данных Azure в левой области.

```kusto
StormEvents | take 10 // Demonstrate query through the native ADX cluster
```

![Запросы к таблице StormEvents](media/adx-proxy/query-adx.png)

### <a name="query-against-your-la-or-ai-cluster"></a>Запросы к кластера LA или искусственного Интеллекта

При выполнении запросов в кластере LA или AL, должен быть выбран кластера LA или работы с искусственным Интеллектом в области слева. 

```kusto
Perf | take 10 // Demonstrate query through the proxy on the LA workspace
```

![Рабочую область LA запроса](media/adx-proxy/query-la.png)

### <a name="query-your-la-or-ai-cluster-from-the-adx-proxy"></a>Запрос к кластеру LA или искусственного Интеллекта от ADX прокси  

При выполнении запросов в кластере LA или искусственного Интеллекта от прокси-сервер, убедитесь, что собственный кластер ADX выбран в области слева. В следующем примере демонстрируется запрос с помощью собственного кластера ADX рабочей области LA

```kusto
cluster('https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>').database('<workspace-name').Perf
| take 10 
```

![Запросы от прокси-сервер обозревателя данных Azure](media/adx-proxy/query-adx-proxy.png)

### <a name="cross-query-of-la-or-ai-cluster-and-the-adx-cluster-from-the-adx-proxy"></a>На разных запросов LA или AI кластера и кластера ADX ADX прокси-сервера 

При выполнении запросов между кластера из прокси-сервер, убедитесь, что в левой панели выбран ADX собственного кластера. В следующих примерах демонстрируется объединение ADX кластера таблицы (с помощью `union`) с рабочей областью ЛА.

```kusto
union StormEvents, cluster('https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>').database('<workspace-name>').Perf
| take 10 
```

```kusto
let CL1 = 'https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>';
union <ADX table>, cluster(CL1).database(<workspace-name>).<table name>
```

![На разных запросов прокси-сервера обозревателя данных Azure](media/adx-proxy/cross-query-adx-proxy.png)

С помощью [ `join` оператор](/azure/kusto/query/joinoperator), вместо объединения, может потребоваться подсказку, чтобы запустить его в собственном кластере обозреватель данных Azure (а не на прокси-сервер). 

## <a name="additional-syntax-examples"></a>Примеры дополнительных синтаксиса

При вызове кластеров Application Insights (AI) или Log Analytics (LA), становятся доступными следующие параметры синтаксиса:

|Описание синтаксиса  |Application Insights  |Log Analytics  |
|----------------|---------|---------|
| Базы данных в пределах кластера, содержащего только определенного ресурса в этой подписке (**рекомендуется для кластера запросов между**) |   кластер (`https://ade.applicationinsights.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.insights/components/<ai-app-name>').database('<ai-app-name>`) | кластер (`https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>').database('<workspace-name>`)     |
| Кластер, который содержит все приложения и рабочие области в этой подписке    |     кластер (`https://ade.applicationinsights.io/subscriptions/<subscription-id>`)    |    кластер (`https://ade.loganalytics.io/subscriptions/<subscription-id>`)     |
|Кластер, который содержит все приложения и рабочие области в подписке и являются членами этой группы ресурсов    |   кластер (`https://ade.applicationinsights.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>`)      |    кластер (`https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>`)      |
|Кластер содержит только определенный ресурс в этой подписке      |    кластер (`https://ade.applicationinsights.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.insights/components/<ai-app-name>`)    |  кластер (`https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>`)     |

### <a name="application-insights-app-and-log-analytics-workspace-names"></a>Приложение Application Insights и имена рабочих областей Log Analytics

* Если имена содержат специальные символы, они все заменены URL-адрес, кодирование в имя прокси-сервера кластера. 
* Если имена включают символы, которые не соответствуют [KQL идентификатор имени правила](/azure/kusto/query/schema-entities/entity-names), будут заменены штриха **-** символ.

## <a name="next-steps"></a>Следующие шаги

[Написание запросов](write-queries.md)