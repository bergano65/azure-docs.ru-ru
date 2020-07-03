---
title: Локальная конфигурация агента безопасности (C#)
description: Узнайте больше о центре безопасности Azure для службы безопасности IoT, локальном файле конфигурации агента безопасности для C#.
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2020
ms.locfileid: "81311665"
---
# <a name="understanding-the-local-configuration-file-c-agent"></a>Основные сведения о локальном файле конфигурации (агент C#)

В центре безопасности Azure для агента безопасности IoT используются конфигурации из локального файла конфигурации.

Агент безопасности считывает файл конфигурации один раз при запуске агента. Конфигурации, найденные в локальном файле конфигурации, содержат конфигурацию проверки подлинности и другие конфигурации, связанные с агентом.

Агент безопасности C# использует несколько файлов конфигурации:

- **General. config** — конфигурации, связанные с агентом.
- **Authentication. config** — конфигурация, связанная с аутентификацией (включая сведения о проверке подлинности).
- **Секуритиотинтерфаце. config** — конфигурации, связанные с IOT.

Файлы конфигурации содержат конфигурацию по умолчанию. Конфигурация проверки подлинности заполняется во время установки агента, и изменения в файле конфигурации вносятся при перезапуске агента.

## <a name="configuration-file-location"></a>Расположение файла конфигурации

Для Linux:

- Файлы конфигурации операционной системы находятся в `/var/ASCIoTAgent`папке.

Для Windows:

- Файлы конфигурации операционной системы находятся в каталоге агента безопасности.

### <a name="generalconfig-configurations"></a>Конфигурации General. config

| Имя конфигурации | Возможные значения | Сведения |
|:-----------|:---------------|:--------|
| agentId | Идентификатор GUID | Уникальный идентификатор агента |
| реадремотеконфигуратионтимеаут | TimeSpan | Период времени для выборки удаленной конфигурации из центра Интернета вещей. Если агент не может получить конфигурацию в течение заданного времени, операция завершится с истечением времени ожидания.|
| счедулеринтервал | TimeSpan | Внутренний интервал планировщика. |
| продуцеринтервал | TimeSpan | Интервал рабочей роли производителя события. |
| консумеринтервал | TimeSpan | Интервал рабочего процесса потребителя событий. |
| хигхприоритикуеуесизеперцентаже | 0 < число < 1 | Часть общего кэша, выделенная для сообщений с высоким приоритетом. |
| LogLevel | "OFF", "неустранимые", "Error", "warning", "Information", "Debug"  | Сообщения журнала, равные и превышающие эту серьезность, регистрируются в консоли отладки (системный журнал в Linux). |
| филелоглевел |  "OFF", "неустранимые", "Error", "warning", "Information", "Debug"| Сообщения журнала, равные и превышающие эту серьезность, записываются в файл (системный журнал в Linux). |
| диагностиквербоситилевел | «None», «some», «ALL», | Уровень детализации событий диагностики. None — события диагностики не отправляются, отправляются только диагностические события с высоким уровнем важности, все — все журналы также отправляются как диагностические события. |
| logFilePath | Путь к файлу | Если Филелоглевел >, журналы записываются в этот файл. |
| дефаултевентприорити | "Высокий", "низкий", "Выкл" | Приоритет события по умолчанию. |

### <a name="generalconfig-example"></a>Пример файла General. config

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

### <a name="authenticationconfig"></a>Файл Authentication. config

| Имя конфигурации | Возможные значения | Сведения |
|:-----------|:---------------|:--------|
| moduleName | строка | Имя удостоверения модуля безопасности. Это имя должно соответствовать имени удостоверения модуля на устройстве. |
| deviceId | строка | ИДЕНТИФИКАТОР устройства (зарегистрированный в центре Интернета вещей Azure). || счедулеринтервал | Строка TimeSpan | Внутренний интервал планировщика. |
| гатевайхостнаме | строка | Имя узла центра Интернета вещей Azure. Обычно <My-Hub>. azure-devices.net |
| filePath | String — путь к файлу | Путь к файлу, содержащему секрет проверки подлинности.|
| type | "SymmetricKey", "SelfSignedCertificate" | Секрет пользователя для проверки подлинности. Выберите *SymmetricKey* , если секрет пользователя является симметричным ключом, выберите *самозаверяющий сертификат* , если секрет представляет собой самозаверяющий сертификат. |
| identity | "DPS", "Module", "Device" | Удостоверение проверки подлинности — DP, если проверка подлинности выполняется с помощью DPS, модуля, если проверка подлинности выполняется с помощью учетных данных модуля, или устройства, если проверка подлинности выполняется
| цертификателокатионкинд |  "Локальный_файл", "Store" | Локальный_файл если сертификат хранится в файле, сохраните его, если сертификат находится в хранилище сертификатов. |
| idScope | строка | Область ИДЕНТИФИКАТОРов DPS |
| registrationId | строка  | ИДЕНТИФИКАТОР регистрации устройства DPS. |
|

### <a name="authenticationconfig-example"></a>Пример проверки подлинности. config

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

### <a name="securityiotinterfaceconfig"></a>Секуритиотинтерфаце. config

| Имя конфигурации | Возможные значения | Сведения |
|:-----------|:---------------|:--------|
| transportType | "AMPQ" "Mqtt" | Тип транспорта центра Интернета вещей. |
|

### <a name="securityiotinterfaceconfig-example"></a>Пример Секуритиотинтерфаце. config

```XML
<ExternalInterface>
  <add key="facadeType"  value="Microsoft.Azure.Security.IoT.Agent.Common.SecurityIoTHubInterface, Security.Common" />
  <add key="transportType" value="Amqp"/>
</ExternalInterface>
```

## <a name="next-steps"></a>Дальнейшие шаги

- Ознакомьтесь с [обзором](overview.md) центра безопасности Azure для службы IOT
- Узнайте больше о центре безопасности Azure для [архитектуры](architecture.md) IOT
- Включение центра безопасности Azure для [службы](quickstart-onboard-iot-hub.md) IOT
- [Вопросы и ответы](resources-frequently-asked-questions.md) о центре безопасности Azure для службы IOT
- Узнайте, как получить доступ к [необработанным данным безопасности](how-to-security-data-access.md).
- Общие сведения о [рекомендациях](concept-recommendations.md).
- Общие сведения об [оповещениях](concept-security-alerts.md) системы безопасности
