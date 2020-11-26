---
title: Запрос данных в Azure Monitor с помощью обозреватель данных Azure (Предварительная версия)
description: Используйте обозреватель данных Azure для выполнения перекрестных запросов между обозреватель данных Azure, Log Analytics рабочими областями и классическими Application Insights приложениями в Azure Monitor.
author: orens
ms.author: bwren
ms.reviewer: bwren
ms.subservice: logs
ms.topic: conceptual
ms.date: 10/13/2020
ms.openlocfilehash: 2a21d7a06e8a92022b620704d1fb51a303da3ae0
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/26/2020
ms.locfileid: "96185986"
---
# <a name="query-data-in-azure-monitor-using-azure-data-explorer-preview"></a>Запрос данных в Azure Monitor с помощью обозреватель данных Azure (Предварительная версия)
Кластер Azure обозреватель данных прокси позволяет выполнять перекрестные запросы между обозреватель данных Azure, Log Analytics рабочими областями и классическими Application Insights приложениями в Azure Monitor. Вы можете сопоставлять Log Analytics рабочие области в Azure Monitor или классические Application Insights приложениях в качестве кластерных прокси-серверов. Затем можно выполнить запрос к кластеру прокси-сервера с помощью средств обозреватель данных Azure и обратиться к нему в межкластерном запросе. В этой статье показано, как подключиться к прокси-кластеру, добавить прокси-кластер в Azure обозреватель данных Web UI и выполнить запросы к рабочим областям Log Analytics или классическим Application Insights приложениям из Azure обозреватель данных.

На следующей схеме показан поток прокси-сервера Azure обозреватель данных.

:::image type="content" source="media/azure-data-explorer-monitor-proxy/azure-data-explorer-monitor-proxy-flow.png" alt-text="Поток прокси обозревателя данных Azure.":::


> [!NOTE]
> Прокси-сервер Azure обозреватель данных доступен в общедоступной предварительной версии. [Подключитесь к прокси-](#connect-to-the-proxy) серверу, чтобы включить функцию прокси для кластеров. 

## <a name="connect-to-the-proxy"></a>Подключение к прокси-серверу
Чтобы подключить рабочую область Log Analytics или классическое приложение Application Insights, откройте[Пользовательский веб-интерфейс Azure обозреватель данных](https://dataexplorer.azure.com/clusters). Прежде чем подключиться к кластеру Log Analytics или Application Insights, убедитесь, что в меню слева находится собственный кластер Azure обозреватель данных (например, " *Справочный* кластер)".

:::image type="content" source="media/azure-data-explorer-monitor-proxy/azure-data-explorer-web-ui-help-cluster.png" alt-text="Azure обозреватель данных собственный кластер.":::

Щелкните **Добавить кластер** , а затем добавьте URL-адрес кластера Log Analytics или Application Insights в одном из следующих форматов. 
    
* Для Log Analytics: `https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>`
* Для Application Insights: `https://ade.applicationinsights.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.insights/components/<ai-app-name>`

Нажмите кнопку **Добавить** , чтобы установить соединение.

:::image type="content" source="media/azure-data-explorer-monitor-proxy/azure-monitor-proxy-add-cluster.png" alt-text="Добавить кластер.":::
 
> [!NOTE]
> При добавлении подключения к нескольким кластерам прокси-серверов присвойте каждому из них другое имя. В противном случае все они будут иметь одинаковое имя в левой области.

После установки подключения Log Analytics или Application Insights кластер будет отображаться в левой области с помощью собственного кластера Azure обозреватель данных. 

:::image type="content" source="media/azure-data-explorer-monitor-proxy/azure-monitor-azure-data-explorer-clusters.png" alt-text="Log Analytics и кластеры обозреватель данных Azure.":::
 
> [!NOTE]
> Количество Azure Monitor рабочих областей, которые могут быть сопоставлены, ограничено 100.

## <a name="create-queries-using-azure-monitor-data"></a>Создание запросов с помощью Azure Monitor данных

Запросы можно выполнять с помощью клиентских средств, поддерживающих запросы Kusto, таких как Kusto Explorer, Azure обозреватель данных Web UI, Jupyter Кклмагик, Flow, PowerQuery, PowerShell, Джарвис, линз и REST API.

> [!NOTE]
> Функция прокси-сервера Azure обозреватель данных используется только для получения данных. Дополнительные сведения см. в разделе [Поддержка функций](#function-supportability).

> [!TIP]
> * Имя базы данных должно совпадать с именем ресурса, указанного в кластере прокси. В именах учитывается регистр.
> * В запросах между кластерами убедитесь в правильности именования Application Insights приложений и Log Analytics рабочих областей.
>     * Если имена содержат специальные символы, они заменяются на кодировку URL-адресов в имени кластера прокси-сервера. 
>     * Если имена содержат символы, которые не соответствуют [правилам именования идентификаторов ККЛ](/azure/data-explorer/kusto/query/schema-entities/entity-names), они заменяются **-** символом дефиса.

### <a name="direct-query-from-your-log-analytics-or-application-insights-proxy-cluster"></a>Прямой запрос из Log Analytics или кластера Application Insights прокси-сервера

Выполнение запросов в Log Analytics или кластере Application Insights. Убедитесь, что кластер выбран в левой области. 
 
```kusto
Perf | take 10 // Demonstrate query through the proxy on the Log Analaytics workspace
```

:::image type="content" source="media/azure-data-explorer-monitor-proxy/azure-monitor-proxy-query-la.png" alt-text="Запрос Log Analytics рабочей области.":::

### <a name="cross-query-of-your-log-analytics-or-application-insights-proxy-cluster-and-the-azure-data-explorer-native-cluster"></a>Перекрестный запрос Log Analytics или кластера Application Insights прокси-сервера и собственного кластера Azure обозреватель данных

При выполнении запросов между кластерами от прокси-сервера убедитесь, что на левой панели выбран собственный кластер Azure обозреватель данных. В следующих примерах демонстрируется объединение таблиц кластера Azure обозреватель данных с помощью оператора [Union](/azure/data-explorer/kusto/query/unionoperator) с рабочей областью log Analytics.

```kusto
union StormEvents, cluster('https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>').database('<workspace-name>').Perf
| take 10
```

```kusto
let CL1 = 'https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>';
union <Azure Data Explorer table>, cluster(CL1).database(<workspace-name>).<table name>
```
При использовании [ `join` оператора](/azure/data-explorer/kusto/query/joinoperator?pivots=azuremonitor)вместо объединения может потребоваться [Указание](/azure/data-explorer/kusto/query/joinoperator?pivots=azuremonitor#join-hints) на его выполнение в собственном кластере Azure обозреватель данных (а не на прокси-сервере). 

### <a name="join-data-from-an-azure-data-explorer-cluster-in-one-tenant-with-an-azure-monitor-resource-in-another"></a>Присоединение данных из кластера Azure обозреватель данных в одном клиенте с ресурсом Azure Monitor в другом

Запросы между клиентами не поддерживаются прокси-сервером Azure обозреватель данных. Вы вошли в один клиент для выполнения запроса, охватывающего оба ресурса.

Если ресурс обозреватель данных Azure находится в клиенте "A", а Log Analytics Рабочая область находится в клиенте "B", используйте один из следующих двух методов:

- Azure обозреватель данных позволяет добавлять роли для участников в разные клиенты. Добавьте свой идентификатор пользователя в клиенте "B" в качестве полномочного пользователя в кластере Azure обозреватель данных. Проверьте свойство *["трустедекстерналтенант"](/powershell/module/az.kusto/update-azkustocluster)* в кластере Azure обозреватель данных содержит клиент "B". Выполнение перекрестного запроса полностью в клиенте "B".

- Используйте [лигхсаусе](../../lighthouse/index.yml) для проецирования ресурса Azure Monitor в клиент "A".

### <a name="connect-to-azure-data-explorer-clusters-from-different-tenants"></a>Подключение к кластерам обозреватель данных Azure из разных клиентов

Kusto Explorer автоматически подписывает вас в клиент, к которому изначально принадлежит учетная запись пользователя. Чтобы получить доступ к ресурсам в других клиентах с одной и той же учетной записью пользователя, `tenantId` необходимо явно указать в строке подключения: `Data Source=https://ade.applicationinsights.io/subscriptions/SubscriptionId/resourcegroups/ResourceGroupName;Initial Catalog=NetDefaultDB;AAD Federated Security=True;Authority ID=` **TenantId**

## <a name="function-supportability"></a>Поддержка функций

Кластер Azure обозреватель данных прокси-сервер поддерживает функции как для Log Analytics, так и для Application Insights. Эта возможность позволяет запросам между кластерами ссылаться на Azure Monitor табличные функции напрямую.

Прокси-сервер поддерживает следующие команды:

* `.show functions`
* `.show function {FunctionName}`
* `.show database {DatabaseName} schema as json`

На следующем рисунке показан пример запроса табличной функции из пользовательского веб-интерфейса Azure обозреватель данных. Чтобы использовать функцию, запустите ее имя в окне запроса.

:::image type="content" source="media/azure-data-explorer-monitor-proxy/azure-monitor-proxy-function-query.png" alt-text="Запрос табличной функции из пользовательского веб-интерфейса Azure обозреватель данных.":::
 
> [!NOTE]
> Azure Monitor поддерживает только табличные функции, которые не поддерживают параметры.

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
- Узнайте, как [писать запросы в Azure обозреватель данных](/azure/data-explorer/write-queries).