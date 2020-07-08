---
title: Прием данных телеметрии из Центра Интернета вещей
titleSuffix: Azure Digital Twins
description: Узнайте, как получать сообщения телеметрии устройства из центра Интернета вещей.
author: cschormann
ms.author: cschorm
ms.date: 3/17/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 351f7ed131d545d2aa83df753cac3f26e76e4ccb
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: ru-RU
ms.lasthandoff: 07/02/2020
ms.locfileid: "84725857"
---
# <a name="ingest-iot-hub-telemetry-into-azure-digital-twins"></a>Прием данных телеметрии центра Интернета вещей в Azure Digital двойников

Azure Digital двойников управляет данными из устройств IoT и других источников. Общий источник данных устройства для использования в цифровом двойников Azure — это [центр Интернета вещей](../iot-hub/about-iot-hub.md).

Во время действия предварительной версии процесс приема данных в Azure Digital двойников заключается в настройке внешнего ресурса вычислений, например [функции Azure](../azure-functions/functions-overview.md), которая получает данные и использует [API дигиталтвинс](how-to-use-apis-sdks.md) для установки свойств или запуска событий телеметрии в [цифровом двойников](concepts-twins-graph.md) соответственно. 

В этом документе описывается процесс создания функции Azure, которая может принимать данные телеметрии из центра Интернета вещей.

## <a name="example-telemetry-scenario"></a>Пример сценария телеметрии

В этой инструкции показано, как отправить сообщения из центра Интернета вещей в Azure Digital двойников с помощью функции Azure. Существует множество возможных конфигураций и стратегий сопоставления, которые можно использовать для этого, но пример для этой статьи состоит из следующих частей:
* Устройство термометра в центре Интернета вещей с известным ИДЕНТИФИКАТОРом устройства.
* Цифровой двойника для представления устройства с совпадающим ИДЕНТИФИКАТОРом
* Цифровой двойника, представляющий комнату

> [!NOTE]
> В этом примере используется понятный идентификатор, совпадающий с идентификатором устройства и соответствующим ИДЕНТИФИКАТОРом цифрового двойника, но можно предоставить более сложные сопоставления между устройством и его двойника (например, с таблицей сопоставлений).

Всякий раз, когда устройство термометра отправляет событие телеметрии температуры, свойство *температуры* *комнаты* двойника должно обновляться. Чтобы это сделать, вы будете сопоставлять событие телеметрии на устройстве с методом задания свойств в цифровом двойника. Вы будете использовать сведения о топологии из [графа двойника](concepts-twins-graph.md) для поиска *комнаты* двойника, а затем можете задать свойство двойника. В других случаях пользователям может потребоваться задать свойство для соответствующего двойника (в данном примере — двойника с ИДЕНТИФИКАТОРом *123*). Azure Digital двойников предоставляет множество гибкости для принятия решения о сопоставлении данных телеметрии с двойников. 

Этот сценарий описан в схеме ниже:

:::image type="content" source="media/how-to-ingest-iot-hub-data/events.png" alt-text="Устройство центра Интернета вещей отправляет данные телеметрии температуры с помощью центра Интернета вещей, службы "Сетка событий" или системных разделов в функцию Azure, которая обновляет свойство температуры двойников в Azure Digital двойников." border="false":::

## <a name="prerequisites"></a>Предварительные условия

Прежде чем продолжить работу с этим примером, необходимо выполнить следующие предварительные требования.
1. Создайте Центр Интернета вещей. Инструкции см. в разделе " *Создание центра Интернета вещей* " [этого руководства центра Интернета вещей](../iot-hub/quickstart-send-telemetry-cli.md) .
2. Создайте по крайней мере одну функцию Azure для обработки событий из центра Интернета вещей. См. статью [как настроить функцию Azure для обработки данных](how-to-create-azure-function.md) , чтобы создать базовую функцию Azure, которая может подключаться к Azure Digital двойников и вызывать функции API цифровых двойников Azure. Остальная часть этого руководства будет строиться на основе этой функции.
3. Настройте назначение события для данных концентратора. В [портал Azure](https://portal.azure.com/)перейдите к своему экземпляру центра Интернета вещей. В разделе *события*создайте подписку для функции Azure. 

    :::image type="content" source="media/how-to-ingest-iot-hub-data/add-event-subscription.png" alt-text="Портал Azure: Добавление подписки на события":::

4. На странице *Создание подписки на события* заполните поля следующим образом:
   * В разделе *сведения о ПОДписке на события*укажите имя подписки, которую вы хотите
   * В разделе *типы событий*выберите данные *телеметрии устройства* в качестве типа события для фильтрации.
      - При желании добавьте фильтры в другие типы событий.
   * В разделе *сведения о конечной точке*выберите свою функцию Azure в качестве конечной точки.

## <a name="create-an-azure-function-in-visual-studio"></a>Создание функции Azure в Visual Studio

В этом разделе применяются те же действия по запуску Visual Studio и схема функций Azure из [руководства: Настройка функции Azure для обработки данных](how-to-create-azure-function.md). Скелет обрабатывает аутентификацию и создает клиент службы, готовый к обработке данных и вызов интерфейсов API цифровых двойников Azure в ответе. 

Основой функции скелета является следующее:

```csharp
namespace FunctionSample
{
    public static class FooFunction
    {
        const string adtAppId = "https://digitaltwins.azure.net";
        private static string adtInstanceUrl = Environment.GetEnvironmentVariable("ADT_SERVICE_URL");
        private static HttpClient httpClient = new HttpClient();

        [FunctionName("Foo")]
        public static async Task Run([EventGridTrigger]EventGridEvent eventGridEvent, ILogger log)
        {
            DigitalTwinsClient client = null;
            try
            {
                ManagedIdentityCredential cred = new ManagedIdentityCredential(adtAppId);
                DigitalTwinsClientOptions opts = 
                    new DigitalTwinsClientOptions { Transport = new HttpClientTransport(httpClient) });
                client = new DigitalTwinsClient(new Uri(adtInstanceUrl), cred, opts);
                                                
                log.LogInformation($"ADT service client connection created.");
            }
            catch (Exception e)
            {
                log.LogError($"ADT service client connection failed. " + e.ToString());
                return;
            }
            log.LogInformation(eventGridEvent.Data.ToString());
        }
    }
}
```

В следующих шагах вы добавите в него Специальный код для обработки событий телеметрии IoT из центра Интернета вещей.  

## <a name="add-telemetry-processing"></a>Добавление обработки телеметрии

События телеметрии поступают в виде сообщений с устройства. Первым шагом при добавлении кода обработки телеметрии является извлечение соответствующей части сообщения об устройстве из события сетки событий. 

Различные устройства могут структурировать свои сообщения по-разному, поэтому код для этого шага зависит от подключенного устройства. 

В следующем коде показан пример для простого устройства, отправляющего данные телеметрии в формате JSON. В примере извлекается идентификатор устройства, отправившего сообщение, а также значение температуры.

```csharp
JObject job = eventGridEvent.Data as JObject;
string devid = (string)job["systemProperties"].ToObject<JObject>().Property("IoT-hub-connection-device-ID").Value;
double temp = (double)job["body"].ToObject<JObject>().Property("temperature").Value;
```

Напомним, что цель этого упражнения — обновить температуру *комнаты* в графе двойника. Это означает, что наша цель для сообщения не является цифровым двойника, связанным с этим устройством, но двойника *комнаты* , которая является ее родителем. Вы можете найти родительский двойника, используя значение идентификатора устройства, извлеченное из сообщения телеметрии, с помощью приведенного выше кода.

Для этого используйте интерфейсы API цифровых двойников Azure, чтобы получить доступ к входящим связям с устройством, представляющим двойника (в данном случае имеет тот же идентификатор, что и устройство). Из входящего отношения можно найти идентификатор родителя, используя приведенный ниже фрагмент кода.

В следующем фрагменте кода показано, как получить входящие отношения двойника:

```csharp
AsyncPageable<IncomingRelationship> res = client.GetIncomingRelationshipsAsync(twin_id);
await foreach (IncomingRelationship irel in res)
{
    Log.Ok($"Relationship: {irel.RelationshipName} from {irel.SourceId} | {irel.RelationshipId}");
}
```

Родительский объект двойника находится в свойстве *SourceID* связи.

Она довольно распространена для модели двойника, представляющей устройство, чтобы иметь только одну входящую связь. В этом случае вы можете выбрать только первую (и только) возвращаемую связь. Если модели позволяют использовать несколько типов отношений с этим двойника, может потребоваться дополнительно указать один из нескольких входящих отношений. Обычным способом сделать это является выбор связи по `RelationshipName` . 

После получения идентификатора родительского двойника, представляющего *комнату*, можно «patch» (внести обновления), двойника. Для этого используйте следующий код:

```csharp
UpdateOperationsUtility uou = new UpdateOperationsUtility();
uou.AppendAddOp("/Temperature", temp);
try
{
    await client.UpdateDigitalTwinAsync(twin_id, uou.Serialize());
    Log.Ok($"Twin '{twin_id}' updated successfully!");
}
...
```

### <a name="full-azure-function-code"></a>Полный код функции Azure

Используя код из предыдущих примеров, мы рассмотрим всю функцию Azure в контексте:

```csharp
[FunctionName("ProcessHubToDTEvents")]
public async void Run([EventGridTrigger]EventGridEvent eventGridEvent, ILogger log)
{
    // After this is deployed, in order for this function to be authorized on Azure Digital Twins APIs,
    // you'll need to turn the Managed Identity Status to "On", 
    // grab the Object ID of the function, and assign the "Azure Digital Twins Owner (Preview)" role to this function identity.

    DigitalTwinsClient client = null;
    //log.LogInformation(eventGridEvent.Data.ToString());
    // Authenticate on Azure Digital Twins APIs
    try
    {
        
        ManagedIdentityCredential cred = new ManagedIdentityCredential(adtAppId);
        client = new DigitalTwinsClient(new Uri(adtInstanceUrl), cred, new DigitalTwinsClientOptions { Transport = new HttpClientTransport(httpClient) });
        log.LogInformation($"ADT service client connection created.");
    }
    catch (Exception e)
    {
        log.LogError($"ADT service client connection failed. " + e.ToString());
        return;
    }

    if (client != null)
    {
        try
        {
            if (eventGridEvent != null && eventGridEvent.Data != null)
            {
                #region Open this region for message format information
                // Telemetry message format
                //{
                //  "properties": { },
                //  "systemProperties": 
                // {
                //    "iothub-connection-device-id": "thermostat1",
                //    "iothub-connection-auth-method": "{\"scope\":\"device\",\"type\":\"sas\",\"issuer\":\"iothub\",\"acceptingIpFilterRule\":null}",
                //    "iothub-connection-auth-generation-id": "637199981642612179",
                //    "iothub-enqueuedtime": "2020-03-18T18:35:08.269Z",
                //    "iothub-message-source": "Telemetry"
                //  },
                //  "body": "eyJUZW1wZXJhdHVyZSI6NzAuOTI3MjM0MDg3MTA1NDg5fQ=="
                //}
                #endregion

                // Reading deviceId from message headers
                log.LogInformation(eventGridEvent.Data.ToString());
                JObject job = (JObject)JsonConvert.DeserializeObject(eventGridEvent.Data.ToString());
                string deviceId = (string)job["systemProperties"]["iothub-connection-device-id"];
                log.LogInformation($"Found device: {deviceId}");

                // Extracting temperature from device telemetry
                byte[] body = System.Convert.FromBase64String(job["body"].ToString());
                var value = System.Text.ASCIIEncoding.ASCII.GetString(body);
                var bodyProperty = (JObject)JsonConvert.DeserializeObject(value);
                var temperature = bodyProperty["Temperature"];
                log.LogInformation($"Device Temperature is:{temperature}");

                // Update device Temperature property
                await AdtUtilities.UpdateTwinProperty(client, deviceId, "/Temperature", temperature, log);

                // Find parent using incoming relationships
                string parentId = await AdtUtilities.FindParent(client, deviceId, "contains", log);
                if (parentId != null)
                {
                    await AdtUtilities.UpdateTwinProperty(client, parentId, "/Temperature", temperature, log);
                }

            }
        }
        catch (Exception e)
        {
            log.LogError($"Error in ingest function: {e.Message}");
        }
    }
}
```

Функция служебной программы для поиска входящих отношений:
```csharp
public static async Task<string> FindParent(DigitalTwinsClient client, string child, string relname, ILogger log)
{
    // Find parent using incoming relationships
    try
    {
        AsyncPageable<IncomingRelationship> rels = client.GetIncomingRelationshipsAsync(child);

        await foreach (IncomingRelationship ie in rels)
        {
            if (ie.RelationshipName == relname)
                return (ie.SourceId);
        }
    }
    catch (RequestFailedException exc)
    {
        log.LogInformation($"*** Error in retrieving parent:{exc.Status}:{exc.Message}");
    }
    return null;
}
```

И служебная функция для исправления двойника:
```csharp
public static async Task UpdateTwinProperty(DigitalTwinsClient client, string twinId, string propertyPath, object value, ILogger log)
{
    // If the twin does not exist, this will log an error
    try
    {
        // Update twin property
        UpdateOperationsUtility uou = new UpdateOperationsUtility();
        uou.AppendAddOp(propertyPath, value);
        await client.UpdateDigitalTwinAsync(twinId, uou.Serialize());
    }
    catch (RequestFailedException exc)
    {
        log.LogInformation($"*** Error:{exc.Status}/{exc.Message}");
    }
}
```

Теперь у вас есть функция Azure, которая поддерживает чтение и интерпретацию данных сценария, поступающих из центра Интернета вещей.

## <a name="debug-azure-function-apps-locally"></a>Локальная отладка приложений функций Azure

Можно выполнить отладку функций Azure с помощью триггера службы "Сетка событий" локально. Дополнительные сведения об этом см. в разделе [Отладка триггера сетки событий в локальной](../azure-functions/functions-debug-event-grid-trigger-local.md)среде.

## <a name="next-steps"></a>Дальнейшие шаги

Узнайте о поступлении и исходящих данных в Azure Digital двойников:
* [Основные понятия: интеграция с другими службами](concepts-integration.md)
