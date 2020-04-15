---
title: Локальная конфигурация агента безопасности (C)
description: Узнайте больше о Центре безопасности Azure для службы безопасности IoT, локальном файле конфигурации агента безопасности для C'.
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
ms.openlocfilehash: adf0d72763e0cb1892d64c68a6dce05abbf6f582
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/14/2020
ms.locfileid: "81311665"
---
# <a name="understanding-the-local-configuration-file-c-agent"></a>Понимание локального файла конфигурации (агент C')

Центр безопасности Azure для агента безопасности IoT использует конфигурации из локального файла конфигурации.

Агент безопасности считывает файл конфигурации один раз при запуске агента. Конфигурации, найденные в файле локальной конфигурации, содержат как конфигурацию проверки подлинности, так и другие конфигурации, связанные с агентом.

Агент безопасности СЗ использует несколько файлов конфигурации:

- **General.config** - Конфигурации, связанные с агентом.
- **Authentication.config** - Конфигурация, связанная с аутентификацией (включая данные аутентификации).
- **SecurityIotInterface.config** - Конфигурации, связанные с IoT.

Файлы конфигурации содержат конфигурацию по умолчанию. Конфигурация аутентификации заполняется во время установки агента, и при перезапущени агенте вносятся изменения в файл конфигурации.

## <a name="configuration-file-location"></a>Расположение файла конфигурации

Для Linux:

- Файлы конфигурации операционной системы расположены в `/var/ASCIoTAgent`.

Для Windows:

- Файлы конфигурации операционной системы находятся в каталоге агента безопасности.

### <a name="generalconfig-configurations"></a>Конфигурации General.config

| Имя конфигурации | Возможные значения | Сведения |
|:-----------|:---------------|:--------|
| agentId | GUID | Уникальный идентификатор агента |
| readRemoteConfigurationTimeout | TimeSpan | Период времени для получения удаленной конфигурации из Концентратора IoT. Если агент не может получить конфигурацию в течение указанного времени, операция будет тайм-аутом.|
| планировщикInterval | TimeSpan | Внутренний интервал планировщика. |
| ПроизводительInterval | TimeSpan | Интервал работы производителя событий. |
| consumerInterval | TimeSpan | Интервал событий ногой работника с потребителем. |
| highPriorityКикиПроцент | 0 < номер < 1 | Часть общего кэша, предназначенная для сообщений с высоким приоритетом. |
| LogLevel | "Off", "Fatal", "Ошибка", "Предупреждение", "Информация", "Ошибка"  | Сообщения журнала равны и выше этой серьезности регистрируются для отладки консоли (Syslog в Linux). |
| fileLogLevel |  "Off", "Fatal", "Ошибка", "Предупреждение", "Информация", "Ошибка"| Сообщения журнала равны и выше этой серьезности регистрируются для файла (Syslog в Linux). |
| диагностическийVerbosityLevel | "Нет", "Некоторые", "Все", | Уровень многообразия диагностических мероприятий. Ни один - диагностические события не отправляются, некоторые - Только диагностические события с высокой важностью отправляются, все - все журналы также отправляются в качестве диагностических событий. |
| logFilePath | Путь к файлу | Если fileLogLevel > Off, журналы записываются в этот файл. |
| defaultEventPriority | "Высокий", "Низкий", "Off" | Приоритет события по умолчанию. |

### <a name="generalconfig-example"></a>Пример General.config

```XML
<?xml version="1.0" encoding="utf-8"?>
<General>
  <add key="agentId" value="da00006c-dae9-4273-9abc-bcb7b7b4a987" />
  <add key="readRemoteConfigurationTimeout" value="00:00:30" />
  <add key="schedulerInterval" value="00:00:01" />
  <add key="producerInterval" value="00:02:00" />
  <add key="consumerInterval" value="00:02:00" />
  <add key="highPriorityQueueSizePercentage" value="0.5" />
  <add key="logLevel" value="Information" />
  <add key="fileLogLevel" value="Off"/>
  <add key="diagnosticVerbosityLevel" value="Some" />
  <add key="logFilePath" value="IotAgentLog.log" />
  <add key="defaultEventPriority" value="Low"/>
</General>
```

### <a name="authenticationconfig"></a>Authentication.config

| Имя конфигурации | Возможные значения | Сведения |
|:-----------|:---------------|:--------|
| moduleName | строка | Имя иждивежающего модуля безопасности. Это имя должно соответствовать названию модуля в устройстве. |
| deviceId | строка | Идентификатор устройства (зарегистрирован в Azure IoT Hub). || планировщикInterval | Строка TimeSpan | Внутренний интервал планировщика. |
| GatewayHostname | строка | Название хост-центра Azure Iot. Обычно <мой концентратор>.azure-devices.net |
| filePath | строка - путь к файлу | Путь к файлу, содержащего секрет проверки подлинности.|
| type | "SymmetricKey", "SelfSignedCertificate" | Секрет пользователя для проверки подлинности. Выберите *SymmetricKey,* если секрет пользователя является симметричным ключом, выберите *сертификат, подписанный самостоятельно,* если секретом является сертификат, подписанный самостоятельно. |
| identity | "ДПС", "Модуль", "Прибор" | Идентификация подлинности - DPS, если аутентификация производится через DPS, Модуль, если аутентификация производится с использованием учетных данных модуля, или устройства, если аутентификация производится с использованием учетных данных устройства.
| сертификатLocationKind |  "LocalFile", "Store" | LocalFile, если сертификат хранится в файле, храните, если сертификат находится в магазине сертификатов. |
| idScope | строка | Область идентификации DPS |
| registrationId | строка  | Идентификатор регистрации устройства DPS. |
|

### <a name="authenticationconfig-example"></a>Пример аутентификации.конфигурация

```XML
<?xml version="1.0" encoding="utf-8"?>
<Authentication>
  <add key="moduleName" value="azureiotsecurity"/>
  <add key="deviceId" value="d1"/>
  <add key="gatewayHostname" value=""/>
  <add key="filePath" value="c:\p-dps-d1.pfx"/>
  <add key="type" value="SelfSignedCertificate" />                     <!-- SymmetricKey, SelfSignedCertificate-->
  <add key="identity" value="DPS" />                 <!-- Device, Module, DPS -->
  <add key="certificateLocationKind" value="LocalFile" />  <!-- LocalFile, Store -->
  <add key="idScope" value="0ne0005335B"/>
  <add key="registrationId" value="d1"/>
</Authentication>
```

### <a name="securityiotinterfaceconfig"></a>SecurityIotInterface.config

| Имя конфигурации | Возможные значения | Сведения |
|:-----------|:---------------|:--------|
| транспортТип | "Ampq" "Mqtt" | Тип транспорта Концентраторio. |
|

### <a name="securityiotinterfaceconfig-example"></a>Пример SecurityIotInterface.config

```XML
<ExternalInterface>
  <add key="facadeType"  value="Microsoft.Azure.Security.IoT.Agent.Common.SecurityIoTHubInterface, Security.Common" />
  <add key="transportType" value="Amqp"/>
</ExternalInterface>
```

## <a name="next-steps"></a>Следующие шаги

- Прочтите [обзор](overview.md) службы безопасности Azure для обслуживания IoT
- Узнайте больше о Центре безопасности Azure для [IoT-архитектуры](architecture.md)
- Включить Центр безопасности Azure для [службы](quickstart-onboard-iot-hub.md) IoT
- Прочтите Центр безопасности Azure для [часто задаваемых вопросов](resources-frequently-asked-questions.md) ioT
- Узнайте, как получить доступ к [необработанным данным безопасности](how-to-security-data-access.md).
- Общие сведения о [рекомендациях](concept-recommendations.md).
- Понимание [предупреждений безопасности](concept-security-alerts.md)
