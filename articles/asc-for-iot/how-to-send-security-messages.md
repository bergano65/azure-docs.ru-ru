---
title: Отправка сообщений безопасности в центр безопасности Azure для Интернета вещей | Документация Майкрософт
description: Узнайте, как отправить сообщения безопасности с помощью центра безопасности Azure для Интернета вещей.
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
ms.date: 07/27/2019
ms.author: mlottner
ms.openlocfilehash: c780eea15b9f064d3279c75ac2f967e8b6099ecb
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/29/2019
ms.locfileid: "68596208"
---
# <a name="send-security-messages-sdk"></a>Пакет SDK для отправки сообщений системы безопасности

В этом пошаговом руководство описывается центр безопасности Azure для возможностей службы IoT, если вы решили получать и отсылать сообщения безопасности устройства без использования центра безопасности Azure для агента IoT, а также объясняется, как это сделать.  

Из этого руководства вы узнаете, как выполнить следующие задачи: 
> [!div class="checklist"]
> * Использование API отправки сообщений системы безопасности для C#
> * Использование API отправки сообщений системы безопасности для C

## <a name="azure-security-center-for-iot-capabilities"></a>Центр безопасности Azure для возможностей Интернета вещей

Центр безопасности Azure для Интернета вещей может обрабатывать и анализировать любые данные сообщений системы безопасности при условии, что данные, отправляемые в [Центр безопасности Azure для схемы IOT](https://aka.ms/iot-security-schemas) , задаются в виде сообщения безопасности.

## <a name="security-message"></a>Сообщение безопасности

Центр безопасности Azure для Интернета вещей определяет сообщение безопасности, используя следующие критерии.
- Если сообщение было отправлено с помощью Azure IoT CC# /SDK
- Если сообщение соответствует [схеме сообщения безопасности](https://aka.ms/iot-security-schemas)
- Если сообщение было задано как сообщение безопасности перед отправкой

Каждое сообщение безопасности содержит метаданные отправителя `AgentId` `MessageSchemaVersion` , такие как, `AgentVersion`и список событий безопасности.
Схема определяет допустимые и обязательные свойства сообщения безопасности, включая типы событий.

>[!Note]
> Отправленные сообщения, которые не соответствуют схеме, игнорируются. Не забудьте проверить схему, прежде чем отправлять данные, так как игнорируемые сообщения в данный момент не хранятся. 

>[!Note]
> Отправленные сообщения, которые не были заданы в качестве сообщения безопасности с помощью пакетаC# Azure IOT C/SDK, не будут направляться в центр безопасности Azure для конвейера IOT.

## <a name="valid-message-example"></a>Пример допустимого сообщения

В приведенном ниже примере показан допустимый объект сообщения безопасности. В примере содержатся метаданные сообщения и одно `ProcessCreate` событие безопасности.

После настройки в качестве сообщения безопасности и отправки это сообщение будет обработано центром безопасности Azure для Интернета вещей.

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

Отправка сообщений безопасности без использования центра безопасности Azure для агента IoT с помощью [пакета SDK для устройств Azure C# IOT](https://github.com/Azure/azure-iot-sdk-csharp/tree/preview) или [пакета SDK для устройств с Azure IOT C](https://github.com/Azure/azure-iot-sdk-c/tree/public-preview).

Чтобы отправить данные устройства с устройств для обработки с помощью центра безопасности Azure для Интернета вещей, используйте один из следующих интерфейсов API, чтобы пометить сообщения для правильной маршрутизации в центр безопасности Azure для конвейера обработки IoT. 

Все отправляемые данные, даже если они отмечены правильным заголовком, также должны соответствовать [схеме сообщений центра безопасности Azure для Интернета вещей](https://aka.ms/iot-security-schemas). 

### <a name="send-security-message-api"></a>API для отправки сообщений системы безопасности

API-интерфейс **для отправки сообщений системы безопасности** в настоящее время доступен на языках C и C#.  

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

## <a name="next-steps"></a>Следующие шаги
- Ознакомьтесь с [обзором](overview.md) центра безопасности Azure для службы IOT
- Узнайте больше о центре безопасности Azure для [архитектуры](architecture.md) IOT
- Включите [службу](quickstart-onboard-iot-hub.md).
- Просмотрите [часто задаваемые вопросы](resources-frequently-asked-questions.md).
- Узнайте, как получить доступ к [необработанным данным безопасности](how-to-security-data-access.md).
- Общие сведения о [рекомендациях](concept-recommendations.md).
- Ознакомьтесь со сведениями об [оповещениях](concept-security-alerts.md).
