---
title: Управление конечными точками и маршрутами
titleSuffix: Azure Digital Twins
description: Узнайте, как настроить конечные точки и маршруты событий для данных Azure Digital двойников и управлять ими.
author: alexkarcher-msft
ms.author: alkarche
ms.date: 6/23/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: bc22cf5a21709ccacafe068a60541cc9990d1131
ms.sourcegitcommit: 0e8a4671aa3f5a9a54231fea48bcfb432a1e528c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/24/2020
ms.locfileid: "87132268"
---
# <a name="manage-endpoints-and-routes-in-azure-digital-twins"></a>Управление конечными точками и маршрутами в цифровом двойников Azure

В Azure Digital двойников можно перенаправлять [уведомления о событиях](how-to-interpret-event-data.md) в подчиненные службы или подключаться к ресурсам вычислений. Для этого сначала необходимо настроить **конечные точки** , которые могут получать события, а также [**маршруты событий**](concepts-route-events.md) , указывающие, какие события, созданные Azure Digital двойников, доставляются в конечные точки.

Поддерживаемые типы конечных точек включают:
* [Концентратор событий](../event-hubs/event-hubs-about.md)
* [Сетка событий](../event-grid/overview.md)
* [Служебная шина](../service-bus-messaging/service-bus-messaging-overview.md)

Дополнительные сведения о различных конечных точках см. в статье [*Выбор между службами обмена сообщениями Azure*](https://docs.microsoft.com/azure/event-grid/compare-messaging-services).

Управление конечными точками и маршрутами осуществляется с помощью [**API-интерфейсов евентраутес**](how-to-use-apis-sdks.md), [пакета SDK для .NET (C#)](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/digitaltwins/Azure.DigitalTwins.Core)или [Azure Digital двойников CLI](how-to-use-cli.md). Они также могут управляться с помощью [портал Azure](https://portal.azure.com).

## <a name="create-an-endpoint-for-azure-digital-twins"></a>Создание конечной точки для Azure Digital двойников

Чтобы связать конечную точку с цифровым двойников Azure, концентратор событий, раздел сетки событий или служебную шину, которые вы используете для этой конечной точки, должны уже существовать. 

В следующем примере показано, как создать раздел сетки событий с помощью Azure CLI.

```azurecli
az eventgrid topic create -g <your-resource-group-name> --name <your-topic-name> -l <region>
```

> [!TIP]
> Чтобы получить список имен регионов Azure, которые можно передать в команды в Azure CLI, выполните следующую команду:
> ```azurecli
> az account list-locations -o table
> ```

После создания раздела его можно связать с Azure Digital двойников с помощью следующей команды:

```azurecli
az dt endpoint create eventgrid --endpoint-name <Event-Grid-endpoint-name> --eventgrid-resource-group <Event-Grid-resource-group-name> --eventgrid-topic <your-Event-Grid-topic-name> -n <your-Azure-Digital-Twins-instance-name>
```

Это сделает раздел сетки событий доступным в Azure Digital двойников с именем, указанным в `--endpoint-name` аргументе. Обычно это имя используется в качестве цели для **маршрута события**, который будет создан в следующем разделе с помощью API службы Digital двойников.

Аналогичные команды существуют для концентраторов событий и конечных точек служебной шины:

* Добавить конечную точку раздела служебной шины (требуется предварительно созданный ресурс служебной шины)
```azurecli 
az dt endpoint create servicebus --endpoint-name <Service-Bus-endpoint-name> --servicebus-resource-group <Service-Bus-resource-group-name> --servicebus-namespace <Service-Bus-namespace> --servicebus-topic <Service-Bus-topic-name> --servicebus-policy <Service-Bus-topic-policy> -n <your-Azure-Digital-Twins-instance-name>
```

* Добавить конечную точку концентратора событий (требуется предварительно созданный ресурс концентратора событий)
```azurecli
az dt endpoint create eventhub --endpoint-name <Event-Hub-endpoint-name> --eventhub-resource-group <Event-Hub-resource-group> --eventhub-namespace <Event-Hub-namespace> --eventhub <Event-Hub-name> --eventhub-policy <Event-Hub-policy> -n <your-Azure-Digital-Twins-instance-name>
```

## <a name="manage-event-routes-with-apis"></a>Управление маршрутами событий с помощью API

Чтобы фактически передавать данные из Azure Digital двойников в конечную точку, необходимо определить маршрут события. **API-интерфейсы** Azure Digital двойников евентраутес позволяют разработчикам связывать потоки событий во всей системе и в подчиненных службах. Дополнительные сведения о маршрутах событий см. в статье [*Маршрутизация событий цифровых двойников Azure*](concepts-route-events.md).

После завершения настройки конечных точек можно перейти к созданию маршрута события.

>[!NOTE]
>Если вы недавно развернули конечные точки, убедитесь, что они завершили развертывание, **прежде чем** пытаться использовать их для нового маршрута событий. Если развертывание маршрута завершается сбоем из-за неготовности конечных точек, подождите несколько минут и повторите попытку.
>
> Если вы создаете скрипт для этого потока, это можно сделать, создав период времени ожидания в 2-3 минут до завершения развертывания службы конечной точки перед переходом к настройке маршрута.

В примерах в этой статье используется пакет SDK для C#.

Маршруты событий определяются с помощью API-интерфейсов плоскости данных. Определение маршрута может содержать следующие элементы:
* Идентификатор маршрута, который вы хотите использовать
* Имя конечной точки, которую вы хотите использовать.
* Фильтр, который определяет, какие события отправляются в конечную точку 

Если идентификатор маршрута отсутствует, сообщения не направляются за пределы Azure Digital двойников. Если имеется идентификатор маршрута и фильтр имеет значение `true` , все сообщения направляются в конечную точку. Если имеется идентификатор маршрута и добавлен другой фильтр, сообщения будут отфильтрованы на основе фильтра.

Один маршрут должен разрешать выбор нескольких уведомлений и типов событий. 

`CreateEventRoute`— Это вызов пакета SDK, который используется для добавления маршрута события. Ниже приведен пример использования.

```csharp
EventRoute er = new EventRoute("endpointName");
er.Filter("true"); //Filter allows all messages
await client.CreateEventRoute("routeName", er);
```

> [!TIP]
> Все функции пакета SDK имеют синхронные и асинхронные версии.

### <a name="event-route-sample-code"></a>Пример кода для маршрута события

В следующем примере кода показано, как создать, перечислить и удалить маршрут события.
```csharp
try
{
    Console.WriteLine("Create a route: testRoute1");
    EventRoute er = new EventRoute("< your - endpoint - name >");
    // Make a filter that passes everything
    er.Filter = "true";
    client.CreateEventRoute("< your - route - name >", er);
    Console.WriteLine("Create route succeeded. Now listing routes:");
    Pageable <EventRoute> result = client.GetEventRoutes();
    foreach (EventRoute r in result)
    {
        Console.WriteLine($"Route {r.Id} to endpoint {r.EndpointId} with filter {r.Filter} ");
    }
    Console.WriteLine("Deleting routes:");
    foreach (EventRoute r in result)
    {
        Console.WriteLine($"Deleting route {r.Id}:");
        client.DeleteEventRoute(r.Id);
    }
}
catch (RequestFailedException e)
{
    Console.WriteLine($"*** Error in event route processing ({e.ErrorCode}):\n${e.Message}");
}
```

### <a name="filter-events"></a>События фильтра

Без фильтрации конечные точки получают различные события из Azure Digital двойников:
* Данные телеметрии, созданные [цифровым двойников](concepts-twins-graph.md) с помощью API службы Digital двойников
* Двойника уведомления об изменении свойств для любых двойника в экземпляре цифрового двойников Azure
* События жизненного цикла, которые срабатывают при создании или удалении двойников или связей
* События изменения модели, возникающие при добавлении или удалении [моделей](concepts-models.md) , настроенных в экземпляре Azure Digital двойников

Вы можете ограничить отправляемые события, добавив фильтр к конечной точке.

Чтобы добавить фильтр, можно использовать запрос на размещение *https://{йоурхост}/евентраутес/миневрауте? API-Version = 2020-05 -31-Preview* со следующим текстом:

```json  
{
    "endpointName": "<endpoint-name>",
    "filter": "<filter-text>"
}
``` 

Ниже приведены поддерживаемые фильтры маршрутов.

| Имя фильтра | Описание | Схема фильтра | Поддерживаемые значения | 
| --- | --- | --- | --- |
| Type | [Тип события](./concepts-route-events.md#types-of-event-messages) , переданного через ваш экземпляр Digital двойника | `"filter" : "type = '<eventType>'"` | `Microsoft.DigitalTwins.Twin.Create` <br> `Microsoft.DigitalTwins.Twin.Delete` <br> `Microsoft.DigitalTwins.Twin.Update`<br>`Microsoft.DigitalTwins.Relationship.Create`<br>`Microsoft.DigitalTwins.Relationship.Update`<br> `Microsoft.DigitalTwins.Relationship.Delete` <br> `microsoft.iot.telemetry`  |
| Источник | Имя экземпляра Digital двойников для Azure | `"filter" : "source = '<hostname>'"`|  **Для уведомлений**:`<yourDigitalTwinInstance>.<yourRegion>.azuredigitaltwins.net` <br> **Для телеметрии**:`<yourDigitalTwinInstance>.<yourRegion>.azuredigitaltwins.net/digitaltwins/<twinId>`|
| Тема | Описание события в контексте источника событий выше | `"filter": " subject = '<subject>'"` | **Для уведомлений**: Тема`<twinid>` <br> или формат URI для субъектов, которые уникально идентифицируются несколькими частями или идентификаторами:<br>`<twinid>/relationships/<relationshipid>`<br> **Для телеметрии**: субъект — это путь к компоненту (если данные телеметрии создаются из компонента двойника), например `comp1.comp2` . Если данные телеметрии не выдаются из компонента, то его поле subject будет пустым. |
| Схема данных | Идентификатор модели ДТДЛ | `"filter": "dataschema = 'dtmi:example:com:floor4;2'"` | **Для телеметрии**: Схема данных — это идентификатор модели двойника или компонента, который создает данные телеметрии. <br>**Для уведомлений**: Схема данных не поддерживается|
| Тип содержимого | Тип содержимого значения данных | `"filter": "datacontenttype = '<contentType>'"` | `application/json` |
| Версия спецификации | Используемая версия схемы событий | `"filter": "specversion = '<version>'"` | Этот параметр должен содержать значение `1.0`. Это означает, что схема Клаудевентс версии 1,0 |
| True/false | Разрешает создание маршрута без фильтрации или отключение маршрута | `"filter" : "<true/false>"` | `true`= маршрут включен без фильтрации <br> `false`= маршрут отключен |
<!--
| ID | *Not implemented for public preview* | "filter": "id = '…'" | |
| Schema | *Not implemented for public preview*  | "filter": "dataschema = '…'" | |
-->

Можно также комбинировать фильтры, используя следующие операции:

| Имя фильтра | Схема фильтра | Пример | 
| --- | --- | --- |
| И (ИЛИ) | `"filter": "<filter1> AND <filter2>"` | `"filter": "type != 'microsoft.iot.telemetry' AND datacontenttype = 'application/json'"` |
| И (ИЛИ) | `"filter": "<filter1> OR <filter2>"` | `"filter": "type != 'microsoft.iot.telemetry' OR datacontenttype = 'application/json'"` |
| Вложенные операции | `"filter": "(<Comparison1>) AND (<Comparison2>)"` | `"filter": "(type != 'microsoft.iot.telemetry' OR datacontenttype = 'application/json') OR (specversion != '1.0')"` |

> [!NOTE]
> Во время предварительной версии поддерживается только равенство строк (=,! =).

При реализации или обновлении фильтра может потребоваться несколько минут для отражения в конвейере данных.

## <a name="manage-endpoints-and-routes-with-cli"></a>Управление конечными точками и маршрутами с помощью интерфейса командной строки

Также можно управлять конечными точками и маршрутами с помощью интерфейса командной строки Azure Digital двойников. Команды можно найти в [*этом пошаговом окне. Используйте интерфейс командной строки Azure Digital двойников*](how-to-use-cli.md).

## <a name="monitor-event-routes"></a>Мониторинг маршрутов событий

Метрики маршрутизации, такие как количество, задержка и частота сбоев, можно просмотреть в [портал Azure](https://portal.azure.com/). 

На домашней странице портала найдите свой экземпляр Azure Digital двойников, чтобы получить сведения о нем. Выберите параметр **метрики** в меню экземпляра Azure Digital двойников, чтобы открыть страницу *метрики* .

:::image type="content" source="media/how-to-manage-routes/metrics.png" alt-text="Страница метрики для экземпляра цифрового двойников Azure в портал Azure":::

Здесь можно просмотреть метрики для своего экземпляра и создать пользовательские представления.

## <a name="next-steps"></a>Дальнейшие действия

Сведения о различных типах сообщений о событиях, которые можно получить:
* [*Пошаговое руководство. анализ данных события*](how-to-interpret-event-data.md)
