---
title: Запрос данных в Azure Monitor с помощью обозреватель данных Azure (Предварительная версия)
description: В этом разделе вы запрашиваете данные в Azure Monitor, создавая прокси-сервер обозреватель данных Azure для перекрестных запросов с помощью Application Insights и Log Analytics
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: conceptual
ms.date: 07/10/2019
ms.openlocfilehash: 8e61f52282bcbc62a3eb069272cd7c1f3e329d3b
ms.sourcegitcommit: ee61ec9b09c8c87e7dfc72ef47175d934e6019cc
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/30/2019
ms.locfileid: "70172705"
---
# <a name="query-data-in-azure-monitor-using-azure-data-explorer-preview"></a>Запрос данных в Azure Monitor с помощью обозреватель данных Azure (Предварительная версия)

Кластер Azure обозреватель данных Proxy (прокси-сервер ADX) — это сущность, которая позволяет выполнять перекрестные запросы между Azure обозреватель данных, [Application Insights (AI)](/azure/azure-monitor/app/app-insights-overview)и [log Analytics (La)](/azure/azure-monitor/platform/data-platform-logs) в службе [Azure Monitor](/azure/azure-monitor/) . Вы можете сопоставлять Azure Monitor Log Analytics рабочие области или приложения Application Insights в качестве кластера прокси-сервера. Затем можно выполнить запрос к кластеру прокси-сервера с помощью средств обозреватель данных Azure и обратиться к нему в межкластерном запросе. В этой статье показано, как подключиться к кластеру прокси-сервера, добавить прокси-кластер в Azure обозреватель данных Web UI и выполнить запросы к приложениям ии или LA рабочей области из Azure обозреватель данных.

Поток прокси-сервера Azure обозреватель данных: 

![Поток прокси-сервера ADX](media/adx-proxy/adx-proxy-flow.png)

## <a name="prerequisites"></a>Предварительные требования

> [!NOTE]
> Прокси-сервер ADX находится в режиме предварительного просмотра. Чтобы включить эту функцию, обратитесь к команде [адкспрокси](mailto:adxproxy@microsoft.com) .

## <a name="connect-to-the-proxy"></a>Подключение к прокси-серверу

1. Прежде чем подключиться к кластеру Log Analytics или Application Insights , убедитесь, что в меню слева находится собственный кластер Azure обозреватель данных (например, "справочный кластер)".

    ![ADX собственный кластер](media/adx-proxy/web-ui-help-cluster.png)

1. В пользовательском интерфейсе Azure обозреватель данных https://dataexplorer.azure.com/clusters) (выберите **Добавить кластер**.

1. В окне **Добавление кластера** :

    * Добавьте URL-адрес в LA или ИСКУССТВЕНный кластер. Например: `https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>`

    * Выберите **Добавить**.

    ![Добавление кластера](media/adx-proxy/add-cluster.png)

    При добавлении подключения к нескольким кластерам прокси-серверов присвойте каждому из них другое имя. В противном случае все они будут иметь одинаковое имя в левой области.

1. После установления подключения на левой панели отобразится кластер LA или AI с собственным кластером ADX. 

    ![Кластеры обозреватель данных Log Analytics и Azure](media/adx-proxy/la-adx-clusters.png)

## <a name="run-queries"></a>Выполнение запросов

Для запроса кластеров прокси-серверов можно использовать Kusto Explorer, ADX Web Explorer, Jupyter Кклмагик или REST API. 

> [!TIP]
> * Имя базы данных должно совпадать с именем ресурса, указанного в кластере прокси. В именах учитывается регистр.
> * В запросах между кластерами убедитесь в правильности именования Application Insights приложений и Log Analytics рабочих областей.
>     * Если имена содержат специальные символы, они заменяются на кодировку URL-адресов в имени кластера прокси-сервера. 
>     * Если имена содержат символы, которые не соответствуют [правилам именования идентификаторов ККЛ](/azure/kusto/query/schema-entities/entity-names), они заменяются символом дефиса **-** .

### <a name="query-against-the-native-azure-data-explorer-cluster"></a>Запрос к собственному кластеру Azure обозреватель данных 

Выполнение запросов в кластере Azure обозреватель данных (например, в таблице *стормевентс* в *справочном* кластере). При выполнении запроса убедитесь, что на левой панели выбран собственный кластер Azure обозреватель данных.

```kusto
StormEvents | take 10 // Demonstrate query through the native ADX cluster
```

![Таблица Стормевентс запросов](media/adx-proxy/query-adx.png)

### <a name="query-against-your-la-or-ai-cluster"></a>Запрос к кластеру LA или искусственного интеллекта

При выполнении запросов в кластере LA или AL убедитесь, что на левой панели выбран кластер LA или ИСКУССТВЕНный. 

```kusto
Perf | take 10 // Demonstrate query through the proxy on the LA workspace
```

![Запрос LA Workspace](media/adx-proxy/query-la.png)

### <a name="query-your-la-or-ai-cluster-from-the-adx-proxy"></a>Запрос к кластеру LA или искусственного интеллекта от прокси-сервера ADX  

При выполнении запросов в кластере LA или AI от прокси-сервера убедитесь, что на левой панели выбран собственный кластер ADX. В следующем примере показан запрос LA рабочей области с помощью собственного кластера ADX.

```kusto
cluster('https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>').database('<workspace-name').Perf
| take 10 
```

![Запрос из прокси-сервера Azure обозреватель данных](media/adx-proxy/query-adx-proxy.png)

### <a name="cross-query-of-la-or-ai-cluster-and-the-adx-cluster-from-the-adx-proxy"></a>Перекрестный запрос к кластеру LA или искусственного интеллекта и кластеру ADX из прокси-сервера ADX 

При выполнении запросов между кластерами от прокси-сервера убедитесь, что на левой панели выбран собственный кластер ADX. В следующих примерах демонстрируется объединение таблиц кластера ADX `union`(с помощью) с La Workspace.

```kusto
union StormEvents, cluster('https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>').database('<workspace-name>').Perf
| take 10 
```

```kusto
let CL1 = 'https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>';
union <ADX table>, cluster(CL1).database(<workspace-name>).<table name>
```

![Перекрестный запрос от прокси-сервера Azure обозреватель данных](media/adx-proxy/cross-query-adx-proxy.png)

При использовании оператора вместо объединения может потребоваться указание на его выполнение в собственном кластере Azure обозреватель данных (а не на прокси-сервере). [ `join` ](/azure/kusto/query/joinoperator) 

## <a name="additional-syntax-examples"></a>Дополнительные примеры синтаксиса

При вызове кластеров Application Insights (AI) или Log Analytics (LA) доступны следующие параметры синтаксиса:

|Описание синтаксиса  |Application Insights  |Log Analytics  |
|----------------|---------|---------|
| База данных в кластере, которая содержит только определенный ресурс в этой подписке (**рекомендуется для запросов между кластерами**) |   кластер (`https://ade.applicationinsights.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.insights/components/<ai-app-name>').database('<ai-app-name>`) | кластер (`https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>').database('<workspace-name>`)     |
| Кластер, содержащий все приложения и рабочие области в этой подписке    |     кластер (`https://ade.applicationinsights.io/subscriptions/<subscription-id>`)    |    кластер (`https://ade.loganalytics.io/subscriptions/<subscription-id>`)     |
|Кластер, содержащий все приложения и рабочие области в подписке и входящие в эту группу ресурсов    |   кластер (`https://ade.applicationinsights.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>`)      |    кластер (`https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>`)      |
|Кластер, содержащий только определенный ресурс в этой подписке      |    кластер (`https://ade.applicationinsights.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.insights/components/<ai-app-name>`)    |  кластер (`https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>`)     |

## <a name="next-steps"></a>Следующие шаги

[Написание запросов](write-queries.md)