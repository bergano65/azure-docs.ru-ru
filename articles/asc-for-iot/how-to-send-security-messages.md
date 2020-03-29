---
title: Отправка сообщений безопасности в Центр безопасности Azure для IoT Документы Майкрософт
description: Узнайте, как отправлять сообщения безопасности с помощью Центра безопасности Azure для IoT.
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.assetid: c611bb5c-b503-487f-bef4-25d8a243803d
ms.subservice: asc-for-iot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 1/30/2020
ms.author: mlottner
ms.openlocfilehash: 8bbbd8248c7418b667e34389cb47bd3f6b4f06ab
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76963824"
---
# <a name="send-security-messages-sdk"></a>Пакет SDK для отправки сообщений системы безопасности

В этом руководстве объясняется возможности службы Службы Безопасности Azure для служб IoT при выборе для сбора и отправки сообщений безопасности устройства без использования Центра безопасности Azure для агента IoT и объясняется, как это сделать.  

Из этого руководства вы узнаете, как выполнить следующие задачи: 
> [!div class="checklist"]
> * Отправка сообщений безопасности с помощью SDK Azure IoT C
> * Отправка сообщений безопасности с помощью SDK Azure IoT C
> * Отправка сообщений безопасности с помощью SDK Azure IoT Python
> * Отправка сообщений безопасности с помощью Azure IoT Node.js SDK
> * Отправка сообщений безопасности с помощью SDK Azure IoT Java


## <a name="azure-security-center-for-iot-capabilities"></a>Центр безопасности Azure для возможностей IoT

Центр безопасности Azure для IoT может обрабатывать и анализировать любые данные о сообщениях безопасности до тех пор, пока отправляемые данные соответствуют [схеме Azure Security Для IoT,](https://aka.ms/iot-security-schemas) и сообщение будет установлено как сообщение безопасности.

## <a name="security-message"></a>Сообщение безопасности

Центр безопасности Azure для IoT определяет сообщение безопасности, используя следующие критерии:
- Если сообщение было отправлено с помощью Azure IoT SDK
- Если сообщение соответствует [схеме сообщения безопасности](https://aka.ms/iot-security-schemas)
- Если сообщение было установлено в качестве сообщения безопасности перед отправкой

Каждое сообщение безопасности включает в себя `AgentId`метаданные отправителя, такие как , `AgentVersion` `MessageSchemaVersion` и список событий безопасности.
Схема определяет действительные и необходимые свойства сообщения безопасности, включая типы событий.

>[!Note]
> Отправленные сообщения, которые не соответствуют схеме, игнорируются. Не забудьте проверить схему, прежде чем отправлять данные, так как игнорируемые сообщения в данный момент не хранятся. 

>[!Note]
> Сообщения, отправленные не в виде сообщения безопасности с помощью SDK Azure IoT, не будут направляться в Центр безопасности Azure для конвейера IoT.

## <a name="valid-message-example"></a>Пример действительного сообщения

В приведенном ниже примере показан допустимый объект сообщения безопасности. Пример содержит метаданные сообщений `ProcessCreate` и одно событие безопасности.

После установки в качестве сообщения безопасности и отправки это сообщение будет обработано Центром безопасности Azure для IoT.

```json
"AgentVersion": "0.0.1",
"AgentId": "e89dc5f5-feac-4c3e-87e2-93c16f010c25",
"MessageSchemaVersion": "1.0",
"Events": [
    {
        "EventType": "Security",
        "Category": "Triggered",
        "Name": "ProcessCreate",
        "IsEmpty": false,
        "PayloadSchemaVersion": "1.0",
        "Id": "21a2db0b-44fe-42e9-9cff-bbb2d8fdf874",
        "TimestampLocal": "2019-01-27 15:48:52Z",
        "TimestampUTC": "2019-01-27 13:48:52Z",
        "Payload":
            [
                {
                    "Executable": "/usr/bin/myApp",
                    "ProcessId": 11750,
                    "ParentProcessId": 1593,
                    "UserName": "aUser",
                    "CommandLine": "myApp -a -b"
                }
            ]
    }
]
```

## <a name="send-security-messages"></a>Отправка сообщений системы безопасности 

Отправка сообщений безопасности *без* использования Azure Security Center для агента IoT, используя [устройство SDK IoT C AzK,](https://github.com/Azure/azure-iot-sdk-c/tree/public-preview) [устройство SDK IoT IoT,](https://github.com/Azure/azure-iot-sdk-csharp/tree/preview) [Azure IoT Node.js SDK](https://github.com/Azure/azure-iot-sdk-node), [Azure IoT Python SDK](https://github.com/Azure/azure-iot-sdk-python), или [Azure IoT Java SDK.](https://github.com/Azure/azure-iot-sdk-java)

Для отправки данных устройства с устройств для обработки ВСУ центром безопасности Azure для IoT используйте один из следующих AI для обозначения сообщений для правильной передачи в Центр безопасности Azure для конвейера обработки IoT. 

Все отправляемые данные, даже если они помечены правильным заголовком, также должны соответствовать [схеме сообщений Azure для ioT-сообщений.](https://aka.ms/iot-security-schemas) 

### <a name="send-security-message-api"></a>API для отправки сообщений системы безопасности 

API сообщений **безопасности Отправка** в настоящее время доступен в C и C, Python, Node.js и Java.  

#### <a name="c-api"></a>API C

```c
bool SendMessageAsync(IoTHubAdapter* iotHubAdapter, const void* data, size_t dataSize) {
 
    bool success = true;
    IOTHUB_MESSAGE_HANDLE messageHandle = NULL;
 
    messageHandle = IoTHubMessage_CreateFromByteArray(data, dataSize);
 
    if (messageHandle == NULL) {
        success = false;
        goto cleanup;
    }
 
    if (IoTHubMessage_SetAsSecurityMessage(messageHandle) != IOTHUB_MESSAGE_OK) {
        success = false;
        goto cleanup;
    }
 
    if (IoTHubModuleClient_SendEventAsync(iotHubAdapter->moduleHandle, messageHandle, SendConfirmCallback, iotHubAdapter) != IOTHUB_CLIENT_OK) {
        success = false;
        goto cleanup;
    }
 
cleanup:
    if (messageHandle != NULL) {
        IoTHubMessage_Destroy(messageHandle);
    }
 
    return success;
}
 
static void SendConfirmCallback(IOTHUB_CLIENT_CONFIRMATION_RESULT result, void* userContextCallback) {
    if (userContextCallback == NULL) {
        //error handling
        return;
    }
 
    if (result != IOTHUB_CLIENT_CONFIRMATION_OK){
        //error handling
    }
}
```
#### <a name="c-api"></a>API C#

```cs

private static async Task SendSecurityMessageAsync(string messageContent)
{
    ModuleClient client = ModuleClient.CreateFromConnectionString("<connection_string>");
    Message  securityMessage = new Message(Encoding.UTF8.GetBytes(messageContent));
    securityMessage.SetAsSecurityMessage();
    await client.SendEventAsync(securityMessage);
}
```
#### <a name="nodejs-api"></a>API для Node.js

```typescript
var Protocol = require('azure-iot-device-mqtt').Mqtt

function SendSecurityMessage(messageContent)
{
  var client = Client.fromConnectionString(connectionString, Protocol);

  var connectCallback = function (err) {
    if (err) {
      console.error('Could not connect: ' + err.message);
    } else {
      var message = new Message(messageContent);
      message.setAsSecurityMessage();
      client.sendEvent(message);
  
      client.on('error', function (err) {
        console.error(err.message);
      });
  
      client.on('disconnect', function () {
        clearInterval(sendInterval);
        client.removeAllListeners();
        client.open(connectCallback);
      });
    }
  };

  client.open(connectCallback);
}
```

#### <a name="python-api"></a>API Python

Для использования Python API необходимо установить пакет [azure-iot-устройство.](https://pypi.org/project/azure-iot-device/)

При использовании Python API можно отправить сообщение безопасности через модуль или через устройство с помощью уникальной строки соединения устройства или модуля. При использовании следующего примера сценария Python, с устройством, используйте **IoTHubDevice,** а с модулем используйте **IoTHubModuleClient.** 

```python
from azure.iot.device.aio import IoTHubDeviceClient, IoTHubModuleClient
from azure.iot.device import Message

async def send_security_message_async(message_content):
    conn_str = os.getenv("<connection_string>")
    device_client = IoTHubDeviceClient.create_from_connection_string(conn_str)
    await device_client.connect()
    security_message = Message(message_content)
    security_message.set_as_security_message()
    await device_client.send_message(security_message)
    await device_client.disconnect()
```

#### <a name="java-api"></a>API Java

```java
public void SendSecurityMessage(string message)
{
    ModuleClient client = new ModuleClient("<connection_string>", IotHubClientProtocol.MQTT);
    Message msg = new Message(message);
    msg.setAsSecurityMessage();
    EventCallback callback = new EventCallback();
    string context = "<user_context>";
    client.sendEventAsync(msg, callback, context);
}
```


## <a name="next-steps"></a>Дальнейшие действия
- Прочтите [обзор](overview.md) службы безопасности Azure для обслуживания IoT
- Узнайте больше о Центре безопасности Azure для [IoT-архитектуры](architecture.md)
- Включите [службу](quickstart-onboard-iot-hub.md).
- Читать [часто задаваемые вопросы](resources-frequently-asked-questions.md)
- Узнайте, как получить доступ к [необработанным данным безопасности](how-to-security-data-access.md).
- Общие сведения о [рекомендациях](concept-recommendations.md).
- Ознакомьтесь со сведениями об [оповещениях](concept-security-alerts.md).
