---
title: Локальная конфигурация агента безопасности (C)
description: Узнайте о Центре безопасности Azure для локальных конфигураций агентов для C.
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.assetid: 2cf6a49b-5d35-491f-abc3-63ec24eb4bc2
ms.subservice: asc-for-iot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/26/2019
ms.author: mlottner
ms.openlocfilehash: cd344b9bebb69af210c482f46af6b2dd7edf7816
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/14/2020
ms.locfileid: "81311710"
---
# <a name="understanding-the-localconfigurationjson-file---c-agent"></a>Основные сведения о файле LocalConfiguration.json — агент C

Центр безопасности Azure для агента безопасности IoT использует конфигурации из локального файла конфигурации.
Агент безопасности считывает конфигурацию один раз при запуске агента.
Конфигурация, найденная в файле локальной конфигурации, содержит конфигурацию проверки подлинности и другие конфигурации, связанные с агентом.
Файл содержит конфигурации в парах "Key-Value" в обозначении JSON, и при установке агента конфигурации заполняются.

По умолчанию файл находится по адресу: /var/ASCIoTAgent/LocalConfiguration.json

Изменения в файле конфигурации происходят при перезагрузке агента.

## <a name="security-agent-configurations-for-c"></a>Конфигурации агента безопасности для C

| Имя конфигурации | Возможные значения | Сведения |
|:-----------|:---------------|:--------|
| AgentId | GUID | Уникальный идентификатор агента |
| TriggerdEventsInterval | Строка ISO8601 | Интервал для сбора срабатывающих событий |
| ConnectionTimeout | Строка ISO8601 | Период времени, прежде чем подключение к IoThub получает приурочен |
| Аутентификация | JsonObject | Конфигурация аутентификации. Этот объект содержит всю информацию, необходимую для проверки подлинности против IoTHub |
| Идентификация | "ДПС", "БезопасностьМодуль", "Устройство" | Идентификация подлинности - DPS, если аутентификация производится через DPS, SecurityModule, если аутентификация производится с помощью учетных данных модуля безопасности или устройства, если аутентификация сделана с учетными данными устройства |
| АутентификацияМетод | "SasToken", "SelfSignedCertificate" | секрет пользователя для проверки подлинности - Выберите SasToken, если секрет использования является симметричным ключом, выберите сертификат самостоятельного подписанного, если секретом является самоподписанный сертификат  |
| FilePath | Путь к файлу (строка) | Путь к файлу, содержащего секрет проверки подлинности |
| HostName | строка | Название хоста концентратора azure iot. обычно <мой хаб>.azure-devices.net |
| deviceId | строка | Идентификатор устройства (зарегистрированный в Azure IoT Hub) |
| DPS | JsonObject | Конфигурации, связанные с DPS |
| IDScope | строка | Область идентификации DPS |
| ИД регистрации | строка  | Идентификатор регистрации устройства DPS |
| Logging | JsonObject | Конфигурации, связанные с регистратором агента |
| SystemLoggerМинимальнаястепенья тяжесть | 0 <- число <No 4 | сообщения журнала равны и выше этой серьезности будут зарегистрированы на /var/log/syslog (0 является самой низкой серьезностью) |
| ДиагностическоеСобытиеЕСтии | 0 <- число <No 4 | сообщения журнала равны и выше этой серьезности будут отправлены в качестве диагностических событий (0 является наименьшей степенью тяжести) |

## <a name="security-agent-configurations-code-example"></a>Пример кода агента безопасности

```JSON
{
    "Configuration" : {
        "AgentId" : "b97faf0a-0f57-471f-9dab-46a8e1764946",
        "TriggerdEventsInterval" : "PT2M",
        "ConnectionTimeout" : "PT30S",
        "Authentication" : {
            "Identity" : "Device",
            "AuthenticationMethod" : "SasToken",
            "FilePath" : "/path/to/my/SymmetricKey",
            "DeviceId" : "my-device",
            "HostName" : "my-iothub.azure-devices.net",
            "DPS" : {
                "IDScope" : "",
                "RegistrationId" : ""
            }
        },
        "Logging": {
            "SystemLoggerMinimumSeverity": 0,
            "DiagnoticEventMinimumSeverity": 2
        }
    }
}
```

## <a name="next-steps"></a>Следующие шаги

- Прочтите [обзор](overview.md) службы безопасности Azure для обслуживания IoT
- Узнайте больше о Центре безопасности Azure для [IoT-архитектуры](architecture.md)
- Включить Центр безопасности Azure для [службы](quickstart-onboard-iot-hub.md) IoT
- Прочтите Центр безопасности Azure для [часто задаваемых вопросов](resources-frequently-asked-questions.md) ioT
- Узнайте, как получить доступ к [необработанным данным безопасности](how-to-security-data-access.md).
- Общие сведения о [рекомендациях](concept-recommendations.md).
- Понимание [предупреждений безопасности](concept-security-alerts.md)
