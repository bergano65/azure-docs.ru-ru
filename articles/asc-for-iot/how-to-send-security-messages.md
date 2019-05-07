---
title: Отправка сообщений безопасности в центре безопасности Azure для предварительной версии Интернета вещей | Документация Майкрософт
description: Узнайте, как отправлять сообщения безопасности с помощью центра безопасности Azure для Интернета вещей.
services: asc-for-iot
ms.service: ascforiot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.assetid: c611bb5c-b503-487f-bef4-25d8a243803d
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/26/2019
ms.author: mlottner
ms.openlocfilehash: a91a3538a9c176e3c76e351eb53eb84decc85938
ms.sourcegitcommit: 0568c7aefd67185fd8e1400aed84c5af4f1597f9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/06/2019
ms.locfileid: "65200534"
---
# <a name="send-security-messages-sdk"></a>Пакет SDK для отправки сообщений системы безопасности

> [!IMPORTANT]
> Центр безопасности Azure для Интернета вещей сейчас предоставляется в общедоступной предварительной версии.
> Предварительная версия предоставляется без соглашения об уровне обслуживания. Мы не рекомендуем использовать ее в рабочей среде. Некоторые функции могут не поддерживаться или их возможности могут быть ограничены. Дополнительные сведения см. в статье [Дополнительные условия использования предварительных выпусков Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

В этом практическом руководстве объясняется центр безопасности Azure (ASC) для возможности службы Интернета вещей, при выборе для сбора и отправки сообщения о безопасности устройства без использования ASC для агента Интернета вещей и объясняется, как сделать это.  

Из этого руководства вы узнаете, как выполнить следующие задачи: 
> [!div class="checklist"]
> * Использование API отправки сообщений системы безопасности для C#
> * Использование API отправки сообщений системы безопасности для C

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
> Отправленные сообщения, которые не соответствуют схеме, игнорируются. Не забудьте проверить схему, прежде чем отправлять данные, так как игнорируемые сообщения в данный момент не хранятся. 
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

## <a name="send-security-messages"></a>Отправка сообщений системы безопасности 

Отправить сообщения о безопасности, не используя ASC для агента IoT с помощью [Интернета вещей Azure C# пакет SDK для устройств](https://github.com/Azure/azure-iot-sdk-csharp/tree/preview) или [пакет SDK для устройств Azure IoT C](https://github.com/Azure/azure-iot-sdk-c/tree/public-preview).

Для отправки данных c устройств для обработки с помощью ASC для Интернета вещей, используйте один из следующих API-интерфейсов, чтобы пометить сообщения для их правильной маршрутизации в конвейер обработки ASC для Интернета вещей. Сообщения, отправляемые таким образом, будут обрабатываться и отображаться в ASC для Интернета вещей как в Центре Интернета вещей, так и в Центре безопасности Azure в виде сведений о безопасности. 

Все отправляемые данные, даже если они помечены с использованием правильных заголовков, также должны соответствовать [схеме сообщений ASC для Интернета вещей](https://aka.ms/iot-security-schemas). 

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

## <a name="next-steps"></a>Дальнейшие действия
- Просмотрите [обзор](overview.md) службы ASC для Интернета вещей.
- Ознакомьтесь с дополнительными сведениями об [архитектуре](architecture.md) ASC для Интернета вещей.
- Включите [службу](quickstart-onboard-iot-hub.md).
- Просмотрите [часто задаваемые вопросы](resources-frequently-asked-questions.md).
- Узнайте, как получить доступ к [необработанным данным безопасности](how-to-security-data-access.md).
- Общие сведения о [рекомендациях](concept-recommendations.md).
- Ознакомьтесь со сведениями об [оповещениях](concept-security-alerts.md).
