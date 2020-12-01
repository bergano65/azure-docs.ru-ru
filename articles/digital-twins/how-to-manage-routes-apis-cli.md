---
title: Управление конечными точками и маршрутами (API и интерфейс командной строки)
titleSuffix: Azure Digital Twins
description: Узнайте, как настроить конечные точки и маршруты событий для данных Azure Digital двойников и управлять ими.
author: alexkarcher-msft
ms.author: alkarche
ms.date: 11/18/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 6b767a2cf4739a0b36b9f5c5c960e3e3ead58262
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/30/2020
ms.locfileid: "96353091"
---
# <a name="manage-endpoints-and-routes-in-azure-digital-twins-apis-and-cli"></a>Управление конечными точками и маршрутами в Azure Digital двойников (API и CLI)

[!INCLUDE [digital-twins-route-selector.md](../../includes/digital-twins-route-selector.md)]

В Azure Digital двойников можно маршрутизировать [уведомления о событиях](how-to-interpret-event-data.md) в подчиненные службы или подключенные ресурсы вычислений. Для этого сначала необходимо настроить **конечные точки** , которые могут принимать события. Затем можно создать  [**маршруты событий**](concepts-route-events.md) , указывающие, какие события, создаваемые Azure Digital двойников, доставляются в конечные точки.

В этой статье описывается процесс создания конечных точек и маршрутов с помощью [API маршрутов событий](/rest/api/digital-twins/dataplane/eventroutes), [пакета SDK для .NET (C#)](/dotnet/api/overview/azure/digitaltwins/client?view=azure-dotnet&preserve-view=true)и [интерфейса командной строки Azure Digital двойников](how-to-use-cli.md).

Кроме того, можно также управлять конечными точками и маршрутами с помощью [портал Azure](https://portal.azure.com). Версию этой статьи, которая использует портал, см. [*в разделе руководство. Управление конечными точками и маршрутами (портал)*](how-to-manage-routes-portal.md).

## <a name="prerequisites"></a>Предварительные требования

* Вам потребуется **учетная запись Azure** (вы можете настроить ее [здесь](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)бесплатно).
* Вам потребуется **экземпляр Azure Digital двойников** в подписке Azure. Если у вас еще нет экземпляра, его можно создать, выполнив действия, описанные в разделе [*инструкции. Настройка экземпляра и проверки подлинности*](how-to-set-up-instance-cli.md). Используйте следующие значения из программы установки, которые можно использовать далее в этой статье:
    - Имя экземпляра
    - Группа ресурсов
    
## <a name="create-an-endpoint-for-azure-digital-twins"></a>Создание конечной точки для Azure Digital двойников

Ниже приведены поддерживаемые типы конечных точек, которые можно создать для экземпляра.
* [Сетка событий](../event-grid/overview.md)
* [Центры событий](../event-hubs/event-hubs-about.md)
* [Служебная шина](../service-bus-messaging/service-bus-messaging-overview.md)

Дополнительные сведения о различных типах конечных точек см. в статье [*Выбор между службами обмена сообщениями Azure*](../event-grid/compare-messaging-services.md).

Чтобы связать конечную точку с цифровым двойников Azure, необходимо уже существовать раздел "Сетка событий", концентратор событий или служебная шина, которые вы используете для этой конечной точки. 

### <a name="create-an-event-grid-endpoint"></a>Создание конечной точки сетки событий

В следующем примере показано, как создать конечную точку типа "Сетка событий" с помощью Azure CLI. Вы можете использовать [Azure Cloud Shell](https://shell.azure.com)или [установить интерфейс командной строки локально](/cli/azure/install-azure-cli?preserve-view=true&view=azure-cli-latest).

Сначала создайте раздел "Сетка событий". Вы можете использовать следующую команду или просмотреть действия более подробно, перейдя [к разделу *Создание пользовательского раздела*](../event-grid/custom-event-quickstart-portal.md#create-a-custom-topic) краткого руководства по *событиям* сетки событий.

```azurecli-interactive
az eventgrid topic create -g <your-resource-group-name> --name <your-topic-name> -l <region>
```

> [!TIP]
> Чтобы получить список имен регионов Azure, которые можно передать в команды в Azure CLI, выполните следующую команду:
> ```azurecli-interactive
> az account list-locations -o table
> ```

После создания раздела можно связать его с Azure Digital двойников, используя следующую [команду Azure Digital ДВОЙНИКОВ CLI](how-to-use-cli.md):

```azurecli-interactive
az dt endpoint create eventgrid --endpoint-name <Event-Grid-endpoint-name> --eventgrid-resource-group <Event-Grid-resource-group-name> --eventgrid-topic <your-Event-Grid-topic-name> -n <your-Azure-Digital-Twins-instance-name>
```

Теперь раздел "Сетка событий" доступен в качестве конечной точки в Azure Digital двойников с именем, указанным в `--endpoint-name` аргументе. Обычно это имя используется в качестве цели для **маршрута события**, который будет создан [Далее в этой статье](#create-an-event-route) с помощью API службы Digital двойников.

### <a name="create-an-event-hubs-or-service-bus-endpoint"></a>Создание концентраторов событий или конечной точки служебной шины

Процесс создания концентраторов событий или конечных точек служебной шины аналогичен процессу сетки событий, показанному выше.

Сначала создайте ресурсы, которые будут использоваться в качестве конечной точки. Вот что требуется:
* Служебная шина: _пространство имен служебной шины_, _раздел служебной шины_, _правило авторизации_
* Концентраторы событий: _пространство имен концентраторов событий_, _концентратор событий_, _правило авторизации_

Затем используйте следующие команды для создания конечных точек в Azure Digital двойников: 

* Добавить конечную точку раздела служебной шины (требуется предварительно созданный ресурс служебной шины)
```azurecli-interactive 
az dt endpoint create servicebus --endpoint-name <Service-Bus-endpoint-name> --servicebus-resource-group <Service-Bus-resource-group-name> --servicebus-namespace <Service-Bus-namespace> --servicebus-topic <Service-Bus-topic-name> --servicebus-policy <Service-Bus-topic-policy> -n <your-Azure-Digital-Twins-instance-name>
```

* Добавить конечную точку концентраторов событий (требуется предварительно созданный ресурс концентраторов событий)
```azurecli-interactive
az dt endpoint create eventhub --endpoint-name <Event-Hub-endpoint-name> --eventhub-resource-group <Event-Hub-resource-group> --eventhub-namespace <Event-Hub-namespace> --eventhub <Event-Hub-name> --eventhub-policy <Event-Hub-policy> -n <your-Azure-Digital-Twins-instance-name>
```

### <a name="create-an-endpoint-with-dead-lettering"></a>Создание конечной точки с недоставленным письмом

Если конечная точка не может доставить событие в течение определенного периода времени или после попытки доставить событие определенное количество раз, оно может отправить недоставленное событие в учетную запись хранения. Этот процесс называется **недоставленным**.

Чтобы создать конечную точку с поддержкой недоставленных сообщений, необходимо использовать [API ARM](/rest/api/digital-twins/controlplane/endpoints/digitaltwinsendpoint_createorupdate) для создания конечной точки. 

Прежде чем задать параметры расположения недоставленных сообщений, необходимо создать учетную запись хранения с контейнером. При создании конечной точки вы предоставляете URL-адрес для этого контейнера. Недоставленная буква предоставляется как URL-адрес контейнера с маркером SAS. Этот маркер должен иметь только `write` разрешение для целевого контейнера в пределах учетной записи хранения. Полностью сформированный URL-адрес будет иметь формат: `https://<storageAccountname>.blob.core.windows.net/<containerName>?<SASToken>`

Дополнительные сведения о маркерах SAS см. в статье [предоставление ограниченного доступа к ресурсам службы хранилища Azure с помощью подписанных URL-адресов (SAS)](../storage/common/storage-sas-overview.md) .

Дополнительные сведения о недоставленных сообщениях см. в разделе [*Основные понятия: маршруты событий*](concepts-route-events.md#dead-letter-events).

#### <a name="configuring-the-endpoint"></a>Настройка конечной точки

При создании конечной точки добавьте в `deadLetterSecret` `properties` объект в тексте запроса, который содержит URL-адрес контейнера и маркер SAS для вашей учетной записи хранения.

```json
{
  "properties": {
    "endpointType": "EventGrid",
    "TopicEndpoint": "https://contosoGrid.westus2-1.eventgrid.azure.net/api/events",
    "accessKey1": "xxxxxxxxxxx",
    "accessKey2": "xxxxxxxxxxx",
    "deadLetterSecret":"https://<storageAccountname>.blob.core.windows.net/<containerName>?<SASToken>"
  }
}
```

Дополнительные сведения см. в документации по службе Digital двойников REST API: [Endpoints-Дигиталтвинсендпоинт CreateOrUpdate](/rest/api/digital-twins/controlplane/endpoints/digitaltwinsendpoint_createorupdate).

### <a name="message-storage-schema"></a>Схема хранилища сообщений

Недоставленные сообщения будут храниться в следующем формате в учетной записи хранения:

`{container}/{endpointName}/{year}/{month}/{day}/{hour}/{eventId}.json`

Недоставленные сообщения будут соответствовать схеме исходного события, предназначенного для доставки в исходную конечную точку.

Ниже приведен пример недоставленного сообщения для [уведомления о создании двойника](how-to-interpret-event-data.md#digital-twin-life-cycle-notifications).

```json
{
  "specversion": "1.0",
  "id": "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "type": "Microsoft.DigitalTwins.Twin.Create",
  "source": "<yourInstance>.api.<yourregion>.da.azuredigitaltwins-test.net",
  "data": {
    "$dtId": "<yourInstance>xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxxxxx",
    "$etag": "W/\"xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxxxxx\"",
    "TwinData": "some sample",
    "$metadata": {
      "$model": "dtmi:test:deadlettermodel;1",
      "room": {
        "lastUpdateTime": "2020-10-14T01:11:49.3576659Z"
      }
    }
  },
  "subject": "<yourInstance>xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "time": "2020-10-14T01:11:49.3667224Z",
  "datacontenttype": "application/json",
  "traceparent": "00-889a9094ba22b9419dd9d8b3bfe1a301-f6564945cb20e94a-01"
}
```

## <a name="create-an-event-route"></a>Создание маршрута события

Чтобы фактически передавать данные из Azure Digital двойников в конечную точку, необходимо определить **маршрут события**. **API-интерфейсы** Azure Digital двойников евентраутес позволяют разработчикам связывать потоки событий во всей системе и в подчиненных службах. Дополнительные сведения о маршрутах событий см. в статье [*Маршрутизация событий цифровых двойников Azure*](concepts-route-events.md).

В примерах в этом разделе используется [пакет SDK для .NET (C#)](/dotnet/api/overview/azure/digitaltwins/client?view=azure-dotnet&preserve-view=true).

**Предварительные требования**. прежде чем можно будет перейти к созданию маршрута, необходимо создать конечные точки, как описано ранее в этой статье. После завершения настройки конечных точек можно перейти к созданию маршрута события.

> [!NOTE]
> Если вы недавно развернули конечные точки, убедитесь, что они завершили развертывание, **прежде чем** пытаться использовать их для нового маршрута событий. Если развертывание маршрута завершается сбоем из-за неготовности конечных точек, подождите несколько минут и повторите попытку.
>
> Если вы создаете скрипт для этого потока, это можно сделать, создав период времени ожидания в 2-3 минут до завершения развертывания службы конечной точки перед переходом к настройке маршрута.

### <a name="creation-code-with-apis-and-the-c-sdk"></a>Создание кода с помощью API и пакета SDK для C#

Маршруты событий определяются с помощью [API-интерфейсов плоскости данных](how-to-use-apis-sdks.md#overview-data-plane-apis). 

Определение маршрута может содержать следующие элементы:
* Имя маршрута, которое вы хотите использовать
* Имя конечной точки, которую вы хотите использовать.
* Фильтр, который определяет, какие события отправляются в конечную точку 

Если имя маршрута отсутствует, сообщения не направляются за пределы Azure Digital двойников. Если имеется имя маршрута и фильтр имеет значение `true` , все сообщения направляются в конечную точку. Если имеется имя маршрута и добавлен другой фильтр, сообщения будут отфильтрованы на основе фильтра.

Один маршрут должен разрешать выбор нескольких уведомлений и типов событий. 

`CreateOrReplaceEventRouteAsync` — Это вызов пакета SDK, который используется для добавления маршрута события. Ниже приведен пример использования.

```csharp
string eventFilter = "$eventType = 'DigitalTwinTelemetryMessages' or $eventType = 'DigitalTwinLifecycleNotification'";
var er = new DigitalTwinsEventRoute("<your-endpointName>", eventFilter);
await CreateOrReplaceEventRouteAsync(client, "routeName", er);
```
    
> [!TIP]
> Все функции пакета SDK имеют синхронные и асинхронные версии.

### <a name="event-route-sample-code"></a>Пример кода для маршрута события

В следующем примере метода показано, как создать, перечислить и удалить маршрут события.
```csharp
private async static Task CreateEventRoute(DigitalTwinsClient client, String routeName, DigitalTwinsEventRoute er)
{
  try
  {
    Console.WriteLine("Create a route: testRoute1");
            
    // Make a filter that passes everything
    er.Filter = "true";
    await client.CreateOrReplaceEventRouteAsync(routeName, er);
    Console.WriteLine("Create route succeeded. Now listing routes:");
    Pageable<DigitalTwinsEventRoute> result = client.GetEventRoutes();
    foreach (DigitalTwinsEventRoute r in result)
    {
        Console.WriteLine($"Route {r.Id} to endpoint {r.EndpointName} with filter {r.Filter} ");
    }
    Console.WriteLine("Deleting routes:");
    foreach (DigitalTwinsEventRoute r in result)
    {
        Console.WriteLine($"Deleting route {r.Id}:");
        client.DeleteEventRoute(r.Id);
    }
  }
    catch (RequestFailedException e)
    {
        Console.WriteLine($"*** Error in event route processing ({e.ErrorCode}):\n${e.Message}");
    }
  }
```

## <a name="filter-events"></a>События фильтра

Без фильтрации конечные точки получают различные события из Azure Digital двойников:
* Данные телеметрии, созданные [цифровым двойников](concepts-twins-graph.md) с помощью API службы Digital двойников
* Двойника уведомления об изменении свойств для любых двойника в экземпляре цифрового двойников Azure
* События жизненного цикла, которые срабатывают при создании или удалении двойников или связей

Вы можете ограничить отправляемые события, добавив **Фильтр** для конечной точки в маршруте события.

Чтобы добавить фильтр, можно использовать запрос на размещение *https://{The-Azure-Digital-двойников-имя_узла}/евентраутес/{Event-Route-Name}? API-Version = 2020-10-31* со следующим текстом:

```json  
{
    "endpointName": "<endpoint-name>",
    "filter": "<filter-text>"
}
``` 
Ниже приведены поддерживаемые фильтры маршрутов. Используйте сведения в столбце *схема текста фильтра* , чтобы заменить `<filter-text>` заполнитель в тексте запроса выше.

[!INCLUDE [digital-twins-route-filters](../../includes/digital-twins-route-filters.md)]

## <a name="manage-endpoints-and-routes-with-cli"></a>Управление конечными точками и маршрутами с помощью интерфейса командной строки

Также можно управлять конечными точками и маршрутами с помощью интерфейса командной строки Azure Digital двойников. Дополнительные сведения об использовании интерфейса командной строки и доступных команд см. в разделе [*как использовать интерфейс командной строки Azure Digital двойников*](how-to-use-cli.md).

[!INCLUDE [digital-twins-route-metrics](../../includes/digital-twins-route-metrics.md)]

## <a name="next-steps"></a>Дальнейшие действия

Сведения о различных типах сообщений о событиях, которые можно получить:
* [*Пошаговое руководство. анализ данных события*](how-to-interpret-event-data.md)