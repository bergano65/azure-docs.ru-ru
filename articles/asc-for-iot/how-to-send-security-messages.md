---
title: Отправка сообщений безопасности в центре безопасности Azure для предварительной версии Интернета вещей | Документация Майкрософт
description: Узнайте, как отправлять сообщения безопасности с помощью центра безопасности Azure для Интернета вещей.
services: ascforiot
documentationcenter: na
author: mlottner
manager: barbkess
editor: ''
ms.assetid: c611bb5c-b503-487f-bef4-25d8a243803d
ms.service: ascforiot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/26/2019
ms.author: mlottner
ms.openlocfilehash: be17c5bb4d09e0868af0c6fd9b31f7653b614735
ms.sourcegitcommit: 09bb15a76ceaad58517c8fa3b53e1d8fec5f3db7
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/01/2019
ms.locfileid: "58762775"
---
# <a name="send-security-messages-sdk"></a>Отправка сообщения о безопасности пакета SDK

> [!IMPORTANT]
> Центр безопасности Azure для Интернета вещей находится в общедоступной предварительной версии.
> Эта предварительная версия предоставляется без соглашения об уровне обслуживания и не рекомендуется для производственных рабочих нагрузок. Некоторые функции могут не поддерживаться или их возможности могут быть ограничены. Дополнительные сведения см. в статье [Дополнительные условия использования предварительных выпусков Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

В этом практическом руководстве объясняется центр безопасности Azure (ASC) для возможности службы Интернета вещей, при выборе для сбора и отправки сообщения о безопасности устройства без использования ASC для агента Интернета вещей и объясняется, как сделать это.  

Из этого руководства вы узнаете, как выполнить следующие задачи: 
> [!div class="checklist"]
> * С помощью безопасности сообщения API отправки дляC#
> * Использование безопасности отправка сообщений API для C

## <a name="asc-for-iot-capabilities"></a>ASC для возможности Интернета вещей

ASC для Интернета вещей позволяют обрабатывать и анализировать любые данные безопасности сообщений, до тех пор, пока данные, отправляемые соответствует [ASC для Интернета вещей схемы](https://aka.ms/iot-security-schemas) и сообщения является сообщение системы безопасности.

## <a name="security-message"></a>Сообщение о безопасности

ASC для Интернета вещей определяет сообщение безопасности, используя следующие критерии:
- Если сообщение было отправлено с помощью Azure IoT C /C# SDK
- Если сообщение соответствует [схема безопасности сообщения](https://aka.ms/iot-security-schemas)
- Если сообщение было задано как сообщение безопасности перед отправкой

Каждое сообщение безопасности входят метаданные отправителя, такие как `AgentId`, `AgentVersion`, `MessageSchemaVersion` и список событий безопасности.
Схема определяет допустимые и требуемые свойства на сообщение безопасности, включая типы событий.

[!NOTE]
> Отправлять сообщения, не соответствуют схеме учитываются. Не забудьте проверить схему перед началом отправки данных, как пропущенные сообщения хранятся не в настоящее время. 
> Отправлять сообщения, не были заданы как безопасность сообщений с помощью Azure IoT C /C# SDK не будут направляться к ASC для Интернета вещей конвейера

## <a name="valid-message-example"></a>Пример допустимых сообщений

В приведенном ниже примере показан объект допустимый безопасности сообщения. В примере содержатся метаданные сообщения и один `ProcessCreate` событий безопасности.

После установки как сообщение безопасности и отправки, это сообщение будет обрабатываться с помощью ASC для Интернета вещей.

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
                    "Executable": "/usr/bin/echo",
                    "ProcessId": 11750,
                    "ParentProcessId": 1593,
                    "UserName": "nginx",
                    "CommandLine": "./backup .htaccess"
                }
            ]
    }
]
```

## <a name="send-security-messages"></a>Отправка сообщения о безопасности 

Отправить сообщения о безопасности, не используя ASC для агента IoT с помощью [Интернета вещей Azure C# пакет SDK для устройств](https://github.com/Azure/azure-iot-sdk-csharp/tree/preview) или [пакет SDK для устройств Azure IoT C](https://github.com/Azure/azure-iot-sdk-c/tree/public-preview).

Чтобы отправить данные устройства с устройств для обработки в ASC для Интернета вещей, используйте один из следующих API-интерфейсов для маркировки сообщений для маршрутизации ASC для конвейера обработки Интернета вещей. Сообщения, отправляемые таким образом будет обработано и отображается как сведениями о безопасности в ASC для Интернета вещей в оба центра Интернета вещей или в центре безопасности Azure. 

Все данные, которые отправляются, даже если помечен атрибутом правильный заголовок, также должны соответствовать требованиям [ASC для схемы сообщений Интернета вещей](https://aka.ms/iot-security-schemas). 

### <a name="send-security-message-api"></a>Безопасность сообщений API отправки

**Отправлять сообщения о безопасности** API в настоящее время доступен в C и C#.  

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

#### <a name="c-api"></a>ИНТЕРФЕЙС C API

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

## <a name="next-steps"></a>Дальнейшие действия
- Чтение ASC для службы Интернета вещей [Обзор](overview.md)
- Дополнительные сведения о ASC для Интернета вещей [архитектуры](architecture.md)
- Включить [службы](quickstart-onboard-iot-hub.md)
- Чтение [часто задаваемые вопросы](resources-frequently-asked-questions.md)
- Узнайте, как получить доступ к [необработанной безопасности данных](how-to-security-data-access.md)
- Понять [рекомендации](concept-recommendations.md)
- Понять [оповещения](concept-security-alerts.md)
