---
title: обозреватель данных Azure для запросов между ресурсами с помощью Azure Monitor
description: Используйте Azure Monitor для выполнения перекрестных запросов между обозреватель данных Azure, Log Analytics рабочими областями и классическими Application Insights приложениями в Azure Monitor.
author: orens
ms.author: bwren
ms.reviewer: bwren
ms.subservice: logs
ms.topic: conceptual
ms.date: 12/02/2020
ms.openlocfilehash: 8884fd56cfc1e18a0a358d6902f7f8bcbfc1f5d3
ms.sourcegitcommit: 65db02799b1f685e7eaa7e0ecf38f03866c33ad1
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/03/2020
ms.locfileid: "96553884"
---
# <a name="cross-resource-query-azure-data-explorer-using-azure-monitor"></a>обозреватель данных Azure для запросов между ресурсами с помощью Azure Monitor
Azure Monitor поддерживает запросы между службами между Azure обозреватель данных, [Application Insights (AI)](/azure/azure-monitor/app/app-insights-overview)и [log Analytics (La)](/azure/azure-monitor/platform/data-platform-logs). Затем можно выполнить запрос к кластеру Azure обозреватель данных с помощью средств Log Analytics/Application Insights и обратиться к нему в межсервисном запросе. В этой статье показано, как выполнить кросс-Сервисный запрос.

Azure Monitor кросс: :::image type="content" source="media\azure-data-explorer-monitor-proxy\azure-monitor-data-explorer-flow.png" alt-text="Azure Monitor и azure обозреватель данных Cross Service Flow.":::

>[!NOTE]
>* Azure Monitor прокси-сервер находится в частном предварительной версии — требуется Алловлистинг.
>* Свяжитесь с [группой обслуживания](mailto:ADXProxy@microsoft.com) с любыми вопросами.
## <a name="cross-query-your-log-analytics-or-application-insights-resources-and-azure-data-explorer"></a>Перекрестный запрос Log Analytics или Application Insights ресурсов и Azure обозреватель данных

Запросы между ресурсами можно выполнять с помощью клиентских средств, поддерживающих запросы Kusto, например: Log Analytics веб-интерфейс, книги, PowerShell, REST API и многое другое.

* Введите идентификатор кластера Azure обозреватель данных в запросе в шаблоне "ADX", за которым следует имя базы данных и таблица.

```kusto
adx('https://help.kusto.windows.net/Samples').StormEvents
```
:::image type="content" source="media/azure-data-explorer-monitor-proxy/azure-monitor-cross-service-query-example.png" alt-text="Пример запроса Cross Service.":::

> [!NOTE]
>* В именах баз данных учитывается регистр.
>* Запрос перекрестных ресурсов в качестве предупреждения не поддерживается.
## <a name="combining-azure-data-explorer-cluster-tables-using-union-and-join-with-la-workspace"></a>Объединение таблиц кластера Azure обозреватель данных (с помощью объединения и объединения) с LA Workspace.

```kusto
union customEvents, adx('https://help.kusto.windows.net/Samples').StormEvents
| take 10
```
```kusto
let CL1 = adx('https://help.kusto.windows.net/Samples').StormEvents;
union customEvents, CL1 | take 10
```
:::image type="content" source="media/azure-data-explorer-monitor-proxy/azure-monitor-union-cross-query.png" alt-text="Пример запроса Cross Service с Union.":::

>[!Tip]
>* Допустимый сокращенный формат — имя_кластера/InitialCatalog. Например, ADX (' Help/Samples ') преобразуется в ADX (' Help. kusto. Windows. NET/Samples ').
## <a name="join-data-from-an-azure-data-explorer-cluster-in-one-tenant-with-an-azure-monitor-resource-in-another"></a>Присоединение данных из кластера Azure обозреватель данных в одном клиенте с ресурсом Azure Monitor в другом

Межклиентские запросы между службами не поддерживаются. Вы вошли в один клиент для выполнения запроса, охватывающего оба ресурса.

Если ресурс обозреватель данных Azure находится в клиенте "A", а Log Analytics Рабочая область находится в клиенте "B", используйте один из следующих двух методов:

*  Azure обозреватель данных позволяет добавлять роли для участников в разные клиенты. Добавьте свой идентификатор пользователя в клиенте "B" в качестве полномочного пользователя в кластере Azure обозреватель данных. Проверьте свойство *["трустедекстерналтенант"](https://docs.microsoft.com/powershell/module/az.kusto/update-azkustocluster)* в кластере Azure обозреватель данных содержит клиент "B". Выполнение перекрестного запроса полностью в клиенте "B".
*  Используйте [лигхсаусе](https://docs.microsoft.com/azure/lighthouse/) для проецирования ресурса Azure Monitor в клиент "A".
## <a name="connect-to-azure-data-explorer-clusters-from-different-tenants"></a>Подключение к кластерам обозреватель данных Azure из разных клиентов

Kusto Explorer автоматически подписывает вас в клиент, к которому изначально принадлежит учетная запись пользователя. Чтобы получить доступ к ресурсам в других клиентах с одной и той же учетной записью пользователя, `tenantId` необходимо явно указать в строке подключения: `Data Source=https://ade.applicationinsights.io/subscriptions/SubscriptionId/resourcegroups/ResourceGroupName;Initial Catalog=NetDefaultDB;AAD Federated Security=True;Authority ID=` **TenantId**

## <a name="next-steps"></a>Дальнейшие шаги
* [Написание запросов](https://docs.microsoft.com/azure/data-explorer/write-queries)
* [Запрос данных в Azure Monitor с помощью обозреватель данных Azure](https://docs.microsoft.com/azure/data-explorer/query-monitor-data)
* [Выполнение запросов журнала между ресурсами в Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/log-query/cross-workspace-query)






