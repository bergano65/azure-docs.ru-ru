---
title: Запрос данных в Azure Monitor с помощью обозреватель данных Azure (Предварительная версия)
description: Используйте обозреватель данных Azure для выполнения перекрестных запросов между обозреватель данных Azure, Log Analytics рабочими областями и классическими Application Insights приложениями в Azure Monitor.
author: orens
ms.author: bwren
ms.reviewer: bwren
ms.subservice: logs
ms.topic: conceptual
ms.date: 10/13/2020
ms.openlocfilehash: dd29b18dda46a00966a0e5adea7e06be8f43ad35
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/01/2020
ms.locfileid: "96444922"
---
# <a name="query-data-in-azure-monitor-using-azure-data-explorer-preview"></a>Запрос данных в Azure Monitor с помощью обозреватель данных Azure (Предварительная версия)

Обозреватель данных Azure поддерживает запросы между службами между Azure обозреватель данных, [Application Insights (AI)](/azure/azure-monitor/app/app-insights-overview)и [log Analytics (La)](/azure/azure-monitor/platform/data-platform-logs). Затем вы можете запросить рабочую область Log Analytics/Application Insights с помощью средств обозреватель данных Azure и обратиться к ней в запросе кросс-обслуживания. В этой статье показано, как создать запрос между службами и как добавить рабочую область Log Analytics/Application Insights в веб-интерфейс Azure обозреватель данных.

Поток запросов между службами в Azure обозреватель данных: :::image type="content" source="media\azure-data-explorer-monitor-proxy\azure-data-explorer-monitor-flow.png" alt-text="поток прокси обозревателя данных Azure.":::

> [!NOTE]
> * Возможность запрашивать Azure Monitor данных из Azure обозреватель данных или напрямую из клиентских средств Azure обозреватель данных или косвенно, выполняя запрос в кластере Azure обозреватель данных, находится в режиме предварительного просмотра.
>* Обратитесь к группе по [запросам обслуживания](mailto:adxproxy@microsoft.com) с любыми вопросами.

## <a name="add-a-log-analyticsapplication-insights-workspace-to-azure-data-explorer-client-tools"></a>Добавление рабочей области Log Analytics и Application Insights в клиентские средства Azure обозреватель данных

1. Прежде чем подключиться к кластеру Log Analytics или Application Insights, убедитесь, что в меню слева находится собственный кластер Azure обозреватель данных (например, " *Справочный* кластер)".

:::image type="content" source="media/azure-data-explorer-monitor-proxy/azure-data-explorer-web-ui-help-cluster.png" alt-text="Azure обозреватель данных собственный кластер.":::

 В пользовательском интерфейсе Azure обозреватель данных ( https://dataexplorer.azure.com/clusters) выберите **Добавить кластер**.

2. В окне **Добавление кластера** добавьте URL-адрес кластера Ла или искусственного интеллекта.

    * Для LA: `https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>`
    * Для искусственного интеллекта: `https://ade.applicationinsights.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.insights/components/<ai-app-name>`

    * Выберите **Добавить**.

:::image type="content" source="media/azure-data-explorer-monitor-proxy/azure-monitor-proxy-add-cluster.png" alt-text="Добавить кластер.":::
 
>[!NOTE]
>Если вы добавляете подключение к нескольким областям Log Analytics или Application Insights, дайте каждому из них другое имя. В противном случае все они будут иметь одинаковое имя в левой области.

 После установки подключения Рабочая область Log Analytics или Application Insights появится в левой области с собственным кластером Azure обозреватель данных.

:::image type="content" source="media/azure-data-explorer-monitor-proxy/azure-monitor-azure-data-explorer-clusters.png" alt-text="Log Analytics и кластеры обозреватель данных Azure.":::
 
> [!NOTE]
> Количество Azure Monitor рабочих областей, которые могут быть сопоставлены, ограничено 100.

## <a name="create-queries-using-azure-monitor-data"></a>Создание запросов с помощью Azure Monitor данных

Запросы можно выполнять с помощью клиентских средств, поддерживающих запросы Kusto, таких как Kusto Explorer, Azure обозреватель данных Web UI, Jupyter Кклмагик, Flow, PowerQuery, PowerShell, линз, REST API.

> [!NOTE]
> Возможность запросов между службами используется только для извлечения данных. Дополнительные сведения см. в разделе [Поддержка функций](#function-supportability).

> [!TIP]
> * Имя базы данных должно совпадать с именем ресурса, указанным в запросе Cross Service. В именах учитывается регистр.
> * В запросах между кластерами убедитесь в правильности именования Application Insights приложений и Log Analytics рабочих областей.
> * Если имена содержат специальные символы, они заменяются на кодировку URL-адресов в запросе Cross Service.
> * Если имена содержат символы, которые не соответствуют [правилам именования идентификаторов ККЛ](https://docs.microsoft.com/azure/data-explorer/kusto/query/schema-entities/entity-names), они заменяются **-** символом дефиса.

### <a name="direct-query-on-your-log-analytics-or-application-insights-workspaces-from-azure-data-explorer-client-tools"></a>Прямой запрос для Log Analytics или Application Insights рабочих областей из клиентских средств Azure обозреватель данных

Выполнение запросов к рабочим областям Log Analytics или Application Insights. Убедитесь, что Рабочая область выбрана в левой области.
 
```kusto
Perf | take 10 // Demonstrate cross service query on the Log Analytics workspace
```

:::image type="content" source="media/azure-data-explorer-monitor-proxy/azure-monitor-proxy-query-la.png" alt-text="Запрос Log Analytics рабочей области.":::

### <a name="cross-query-of-your-log-analytics-or-application-insights-and-the-azure-data-explorer-native-cluster"></a>Перекрестный запрос Log Analytics или Application Insights и собственного кластера Azure обозреватель данных

При выполнении запросов между службами кластеров убедитесь, что на левой панели выбран собственный кластер обозреватель данных Azure. В следующих примерах демонстрируется объединение таблиц кластера Azure обозреватель данных [с помощью объединения](/azure/data-explorer/kusto/query/unionoperator) с рабочей областью log Analytics.

```kusto
union StormEvents, cluster('https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>').database('<workspace-name>').Perf
| take 10
```

```kusto
let CL1 = 'https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>';
union <Azure Data Explorer table>, cluster(CL1).database(<workspace-name>).<table name>
```

:::image type="content" source="media\azure-data-explorer-monitor-proxy\azure-data-explorer-cross-query-proxy.png" alt-text="Запрос между службами из обозреватель данных Azure.":::

При использовании [ `join` оператора](https://docs.microsoft.com/azure/data-explorer/kusto/query/joinoperator)вместо Union может потребоваться, [`hint`](https://docs.microsoft.com/azure/data-explorer/kusto/query/joinoperator#join-hints) чтобы запустить его в собственном кластере Azure обозреватель данных.

### <a name="join-data-from-an-azure-data-explorer-cluster-in-one-tenant-with-an-azure-monitor-resource-in-another"></a>Присоединение данных из кластера Azure обозреватель данных в одном клиенте с ресурсом Azure Monitor в другом

Межклиентские запросы между службами не поддерживаются. Вы вошли в один клиент для выполнения запроса, охватывающего оба ресурса.

Если ресурс обозреватель данных Azure находится в клиенте "A", а Log Analytics Рабочая область находится в клиенте "B", используйте один из следующих двух методов:

1. Azure обозреватель данных позволяет добавлять роли для участников в разные клиенты. Добавьте свой идентификатор пользователя в клиенте "B" в качестве полномочного пользователя в кластере Azure обозреватель данных. Проверьте свойство *["трустедекстерналтенант"](https://docs.microsoft.com/powershell/module/az.kusto/update-azkustocluster)* в кластере Azure обозреватель данных содержит клиент "B". Выполнение перекрестного запроса полностью в клиенте "B".

2. Используйте [лигхсаусе](https://docs.microsoft.com/azure/lighthouse/) для проецирования ресурса Azure Monitor в клиент "A".
### <a name="connect-to-azure-data-explorer-clusters-from-different-tenants"></a>Подключение к кластерам обозреватель данных Azure из разных клиентов

Kusto Explorer автоматически подписывает вас в клиент, к которому изначально принадлежит учетная запись пользователя. Чтобы получить доступ к ресурсам в других клиентах с одной и той же учетной записью пользователя, `tenantId` необходимо явно указать в строке подключения: `Data Source=https://ade.applicationinsights.io/subscriptions/SubscriptionId/resourcegroups/ResourceGroupName;Initial Catalog=NetDefaultDB;AAD Federated Security=True;Authority ID=` **TenantId**

## <a name="function-supportability"></a>Поддержка функций

Запросы перекрестных служб в Azure обозреватель данных поддерживают функции как для Application Insights, так и для Log Analytics.
Эта возможность позволяет запросам между кластерами ссылаться на Azure Monitor табличные функции напрямую.
В запросе Cross Service поддерживаются следующие команды:

* `.show functions`
* `.show function {FunctionName}`
* `.show database {DatabaseName} schema as json`

На следующем рисунке показан пример запроса табличной функции из пользовательского веб-интерфейса Azure обозреватель данных.
Чтобы использовать функцию, запустите ее имя в окне запроса.

:::image type="content" source="media/azure-data-explorer-monitor-proxy/azure-monitor-proxy-function-query.png" alt-text="Запрос табличной функции из пользовательского веб-интерфейса Azure обозреватель данных.":::

## <a name="additional-syntax-examples"></a>Дополнительные примеры синтаксиса

При вызове кластеров Log Analytics или Application Insights доступны следующие параметры синтаксиса:

|Описание синтаксиса  |Application Insights  |Log Analytics  |
|----------------|---------|---------|
| База данных в кластере, которая содержит только определенный ресурс в этой подписке (**рекомендуется для запросов между кластерами**) |   кластер ( `https://ade.applicationinsights.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.insights/components/<ai-app-name>').database('<ai-app-name>` ) | кластер ( `https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>').database('<workspace-name>` )     |
| Кластер, содержащий все приложения и рабочие области в этой подписке    |     кластер ( `https://ade.applicationinsights.io/subscriptions/<subscription-id>` )    |    кластер ( `https://ade.loganalytics.io/subscriptions/<subscription-id>` )     |
|Кластер, содержащий все приложения и рабочие области в подписке и входящие в эту группу ресурсов    |   кластер ( `https://ade.applicationinsights.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>` )      |    кластер ( `https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>` )      |
|Кластер, содержащий только определенный ресурс в этой подписке      |    кластер ( `https://ade.applicationinsights.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.insights/components/<ai-app-name>` )    |  кластер ( `https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>` )     |

## <a name="next-steps"></a>Дальнейшие действия

- Узнайте больше о [структуре данных рабочих областей log Analytics и Application Insights](data-platform-logs.md).
- Узнайте, как [писать запросы в Azure обозреватель данных](https://docs.microsoft.com/azure/data-explorer/write-queries).