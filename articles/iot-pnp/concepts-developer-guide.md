---
title: Руководством для разработчиков. Предварительная версия IoT Plug and Play | Документация Майкрософт
description: Описание Plug and Play IoT для разработчиков
author: rido-min
ms.author: rmpablos
ms.date: 07/16/2020
ms.topic: conceptual
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: ef221ea068f2786a4a84f20a29e80dd7176f06c6
ms.sourcegitcommit: 46f8457ccb224eb000799ec81ed5b3ea93a6f06f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/28/2020
ms.locfileid: "87337421"
---
# <a name="iot-plug-and-play-preview-developer-guide"></a>Предварительная версия для разработчиков IoT Plug and Play

Предварительная версия IoT Plug and Play позволяет создавать интеллектуальные устройства, которые объявляют свои возможности для приложений Интернета вещей Azure. Устройства IoT Plug and Play не нуждаются в ручной настройке, когда клиент подключает их к приложениям с поддержкой Интернета вещей Plug and Play.

В этом руководстве описываются основные шаги, необходимые для создания устройства, которое соответствует [соглашениям об Plug and Play IOT](concepts-convention.md), и доступных интерфейсов API, которые можно использовать для взаимодействия с устройством.

Чтобы создать устройство Plug and Play IoT, выполните следующие действия.

1. Убедитесь, что устройство использует протокол MQTT или MQTT через WebSocket для подключения к центру Интернета вещей Azure.
1. Создайте модель [дтдл (Digital двойников Definition Language)](https://github.com/Azure/opendigitaltwins-dtdl) для описания устройства. Дополнительные сведения см. в разделе [Знакомство с компонентами в Plug and Playных моделях Интернета вещей](concepts-components.md).
1. Обновите устройство, чтобы объявить `model-id` об этом как часть подключения устройства.
1. Реализация телеметрии, свойств и команд с помощью [соглашений Plug and Play IOT](concepts-convention.md)

После того как ваша реализация устройства будет готова, используйте [Обозреватель Azure IOT](howto-use-iot-explorer.md) , чтобы проверить, что устройство соответствует соглашениям об Plug and Play IOT.

> [!Tip]
> Все фрагменты кода в этой статье используют C#, но эти понятия применимы к любому из доступных пакетов SDK для C, Python, Node и Java.

## <a name="model-id-announcement"></a>Объявление идентификатора модели

Чтобы объявить идентификатор модели, устройство должно включить его в сведения о подключении:

```csharp
DeviceClient.CreateFromConnectionString(
  connectionString,
  TransportType.Mqtt,
  new ClientOptions() { ModelId = modelId })
```

Новая `ClientOptions` перегрузка доступна во всех `DeviceClient` методах, используемых для инициализации соединения.

Объявление идентификатора модели Добавлено в следующие версии пакетов SDK

|SDK|Версия|
|---|-------|
|ПАКЕТ SDK ДЛЯ C|1.3.9|
|.NET|1.27.0|
|Java|1.14.0|
|Узел|1.17.0|
|Python|2.1.4|

## <a name="implement-telemetry-properties-and-commands"></a>Реализация телеметрии, свойств и команд

Как описано в разделе [Общие сведения о компонентах Plug and Play моделей Интернета вещей](concepts-components.md), построители устройств должны решить, хотят ли они использовать компоненты для описания своих устройств. При использовании компонентов устройства должны соответствовать правилам, описанным в этом разделе.

### <a name="telemetry"></a>Телеметрия

Модели без компонентов не нуждаются в специальных свойствах.

При использовании компонентов устройства должны задать свойство Message с именем компонента:

```c#
public async Task SendComponentTelemetryValueAsync(string componentName, string serializedTelemetry)
{
  var message = new Message(Encoding.UTF8.GetBytes(serializedTelemetry));
  message.Properties.Add("$.sub", componentName);
  message.ContentType = "application/json";
  message.ContentEncoding = "utf-8";
  await deviceClient.SendEventAsync(message);
}
```

### <a name="read-only-properties"></a>Свойства только для чтения

Модели без компонентов не нуждаются в специальной конструкции:

```csharp
TwinCollection reportedProperties = new TwinCollection();
reportedProperties["maxTemperature"] = 38.7;
await client.UpdateReportedPropertiesAsync(reportedProperties);
```

Двойника устройства обновляется со следующим сообщаемым свойством:

```json
{
  "reported": {
      "maxTemperature" : 38.7
  }
}
```

При использовании компонентов необходимо создать свойства в имени компонента:

```csharp
TwinCollection reportedProperties = new TwinCollection();
TwinCollection component = new TwinCollection();
component["maxTemperature"] = 38.7;
component["__t"] = "c"; // marker to identify a component
reportedProperties["thermostat1"] = component;
await client.UpdateReportedPropertiesAsync(reportedProperties);
```

Двойника устройства обновляется со следующим сообщаемым свойством:

```json
{
  "reported": {
    "thermostat1" : {  
      "__t" : "c",  
      "maxTemperature" : 38.7
     } 
  }
}
```

### <a name="writable-properties"></a>Доступные для записи свойства

Эти свойства можно задать на устройстве или обновить с помощью решения. Если решение обновляет свойство, клиент получает уведомление в качестве обратного вызова в `DeviceClient` . Чтобы следовать правилам Plug and Play IoT, устройство должно информировать службу о том, что свойство было успешно получено.

#### <a name="report-a-writable-property"></a>Сообщить о свойстве, доступном для записи

Когда устройство сообщает о доступном для записи свойстве, оно должно включать `ack` значения, определенные в соглашениях.

Чтобы сообщить о свойстве, доступном для записи, без компонентов:

```csharp
TwinCollection reportedProperties = new TwinCollection();
TwinCollection ackProps = new TwinCollection();
ackProps["value"] = 23.2;
ackProps["ac"] = 200; // using HTTP status codes
ackProps["av"] = 0; // not readed from a desired property
ackProps["ad"] = "reported default value";
reportedProperties["targetTemperature"] = ackProps;
await client.UpdateReportedPropertiesAsync(reportedProperties);
```

Двойника устройства обновляется со следующим сообщаемым свойством:

```json
{
  "reported": {
      "targetTemperature": {
          "value": 23.2,
          "ac": 200,
          "av": 3,
          "ad": "complete"
      }
  }
}
```

Чтобы сообщить доступное для записи свойство из компонента, двойника должен включать маркер:

```csharp
TwinCollection reportedProperties = new TwinCollection();
TwinCollection component = new TwinCollection();
TwinCollection ackProps = new TwinCollection();
component["__t"] = "c"; // marker to identify a component
ackProps["value"] = 23.2;
ackProps["ac"] = 200; // using HTTP status codes
ackProps["av"] = 0; // not read from a desired property
ackProps["ad"] = "reported default value";
component["targetTemperature"] = ackProps;
reportedProperties["thermostat1"] = component;
await client.UpdateReportedPropertiesAsync(reportedProperties);
```

Двойника устройства обновляется со следующим сообщаемым свойством:

```json
{
  "reported": {
    "thermostat1": {
      "__t" : "c",
      "targetTemperature": {
          "value": 23.2,
          "ac": 200,
          "av": 3,
          "ad": "complete"
      }
    }
  }
}
```

#### <a name="subscribe-to-desired-property-updates"></a>Подпишитесь на обновления требуемых свойств

Службы могут обновлять требуемые свойства, которые инициируют уведомление на подключенных устройствах. Это уведомление содержит обновленные требуемые свойства, включая номер версии, определяющий обновление. Устройства должны отвечать с тем же `ack` сообщением, что и сообщаемые свойства.

Модели без компонентов см. одно свойство и создайте отчет `ack` с полученной версией.

```csharp
await client.SetDesiredPropertyUpdateCallbackAsync(async (desired, ctx) => 
{
  JValue targetTempJson = desired["targetTemperature"];
  double targetTemperature = targetTempJson.Value<double>();

  TwinCollection reportedProperties = new TwinCollection();
  TwinCollection ackProps = new TwinCollection();
  ackProps["value"] = targetTemperature;
  ackProps["ac"] = 200;
  ackProps["av"] = desired.Version; 
  ackProps["ad"] = "desired property received";
  reportedProperties["targetTemperature"] = ackProps;

  await client.UpdateReportedPropertiesAsync(reportedProperties);
}, null);
```

Двойника устройства показывает свойство в нужных и сообщаемых разделах:

```json
{
  "desired" : {
    "targetTemperature": 23.2,
    "$version" : 3
  },
  "reported": {
      "targetTemperature": {
          "value": 23.2,
          "ac": 200,
          "av": 3,
          "ad": "complete"
      }
  }
}
```

Модели с компонентами получают требуемые свойства, заключенные в оболочку с именем компонента, и должны сообщать о `ack` сообщаемом свойстве:

```csharp
await client.SetDesiredPropertyUpdateCallbackAsync(async (desired, ctx) =>
{
  JObject thermostatComponent = desired["thermostat1"];
  JToken targetTempProp = thermostatComponent["targetTemperature"];
  double targetTemperature = targetTempProp.Value<double>();

  TwinCollection reportedProperties = new TwinCollection();
  TwinCollection component = new TwinCollection();
  TwinCollection ackProps = new TwinCollection();
  component["__t"] = "c"; // marker to identify a component
  ackProps["value"] = targetTemperature;
  ackProps["ac"] = 200; // using HTTP status codes
  ackProps["av"] = desired.Version; // not readed from a desired property
  ackProps["ad"] = "desired property received";
  component["targetTemperature"] = ackProps;
  reportedProperties["thermostat1"] = component;

  await client.UpdateReportedPropertiesAsync(reportedProperties);
}, null);
```

В двойникае устройства для компонентов отображаются нужные и сообщаемые разделы следующим образом:

```json
{
  "desired" : {
    "thermostat1" : {
        "__t" : "c",
        "targetTemperature": 23.2,
    }
    "$version" : 3
  },
  "reported": {
    "thermostat1" : {
        "__t" : "c",
      "targetTemperature": {
          "value": 23.2,
          "ac": 200,
          "av": 3,
          "ad": "complete"
      }
    }
  }
}
```

### <a name="commands"></a>Команды

Модели без компонентов получают имя команды, как оно было вызвано службой.

Модели с компонентами получат имя команды с префиксом, а также с помощью компонента и `*` разделителя.

```csharp
await client.SetMethodHandlerAsync("themostat*reboot", (MethodRequest req, object ctx) =>
{
  Console.WriteLine("REBOOT");
  return Task.FromResult(new MethodResponse(200));
},
null);
```

#### <a name="request-and-response-payloads"></a>Полезные данные запросов и ответов

Команды используют типы для определения полезных данных запроса и ответа. Устройство должно десериализовать входящий входной параметр и сериализовать ответ. В следующем примере показано, как реализовать команду со сложными типами, определенными в полезных данных:

```json
{
  "@type": "Command",
  "name": "start",
  "request": {
    "name": "startRequest",
    "schema": {
      "@type": "Object",
      "fields": [
        {
          "name": "startPriority",
          "schema": "integer"
        },
        {
          "name": "startMessage",
          "schema" : "string"
        }
      ]
    }
  },
  "response": {
    "name": "startReponse",
    "schema": {
      "@type": "Object",
      "fields": [
        {
            "name": "startupTime",
            "schema": "integer" 
        },
        {
          "name": "startupMessage",
          "schema": "string"
        }
      ]
    }
  }
}
```

В следующих фрагментах кода показано, как устройство реализует это определение команды, включая типы, используемые для включения сериализации и десериализации:

```csharp
class startRequest
{
  public int startPriority { get; set; }
  public string startMessage { get; set; }
}

class startResponse
{
  public int startupTime { get; set; }
  public string startupMessage { get; set; }
}

// ... 

await client.SetMethodHandlerAsync("start", (MethodRequest req, object ctx) =>
{
  var startRequest = JsonConvert.DeserializeObject<startRequest>(req.DataAsJson);
  Console.WriteLine($"Received start command with priority ${startRequest.startPriority} and ${startRequest.startMessage}");

  var startResponse = new startResponse
  {
    startupTime = 123,
    startupMessage = "device started with message " + startRequest.startMessage
  };

  string responsePayload = JsonConvert.SerializeObject(startResponse);
  MethodResponse response = new MethodResponse(Encoding.UTF8.GetBytes(responsePayload), 200);
  return Task.FromResult(response);
},null);
```

> [!Tip]
> Имена запросов и ответов отсутствуют в сериализованных полезных данных, передаваемых по сети.

## <a name="interact-with-the-device"></a>Взаимодействие с устройством 

IoT Plug and Play позволяет использовать устройства, которые объявили идентификатор модели с помощью центра Интернета вещей. Например, можно напрямую обращаться к свойствам и командам устройства.

Чтобы использовать устройство IoT Plug and Play, подключенное к центру Интернета вещей, используйте центр Интернета вещей REST API или один из пакетов SDK для языка Интернета вещей. В следующих примерах используется центр Интернета вещей REST API. Текущая версия API — `2020-05-31-preview` . Присоединитесь `?api-version=2020-05-31` к вызовам остальных PI.

При вызове устройства термостата `t-123` вы получаете все свойства всех интерфейсов, реализованных устройством, с помощью вызова REST API Get:

```REST
GET /digitalTwins/t-123
```

Этот вызов будет включать свойство JSON `$metadata.$model` с идентификатором модели, объявленным устройством.

Доступ ко всем свойствам всех интерфейсов осуществляется с помощью `GET /DigitalTwin/{device-id}` шаблона REST API, где `{device-id}` — это идентификатор устройства:

```REST
GET /digitalTwins/{device-id}
```

Вы можете напрямую вызывать команды для устройств IoT Plug and Play. Если у `Thermostat` компонента на `t-123` устройстве есть `restart` команда, ее можно вызвать с помощью вызова REST API POST:

```REST
POST /digitalTwins/t-123/components/Thermostat/commands/restart
```

В общем случае команды можно вызывать с помощью этого шаблона REST API:

- `device-id`— идентификатор устройства.
- `component-name`: имя интерфейса из раздела Implements модели возможностей устройства.
- `command-name`: имя команды.

```REST
/digitalTwins/{device-id}/components/{component-name}/commands/{command-name}
```

## <a name="next-steps"></a>Дальнейшие действия

Теперь, когда вы узнали о моделировании устройств, ниже приведены некоторые дополнительные ресурсы.

- [Язык определения цифровых двойников (ДТДЛ)](https://github.com/Azure/opendigitaltwins-dtdl)
- [Пакет SDK для устройств для C](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/)
- [REST API IoT](https://docs.microsoft.com/rest/api/iothub/device)
- [Компоненты модели](./concepts-components.md)
