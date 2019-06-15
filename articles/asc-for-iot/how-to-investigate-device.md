---
title: Центр безопасности Azure для Интернета вещей устройства исследования руководство Предварительная версия | Документация Майкрософт
description: Это Практическое руководство содержит сведения об использовании центра безопасности Azure для Интернета вещей для расследования подозрительных устройства Интернета вещей с помощью Log Analytics.
services: asc-for-iot
ms.service: ascforiot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.assetid: b18b48ae-b445-48f8-9ac0-365d6e065b64
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/18/2019
ms.author: mlottner
ms.openlocfilehash: 15e65c155a98ae12c156587735d34a16ed2c9109
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/13/2019
ms.locfileid: "65192659"
---
# <a name="investigate-a-suspicious-iot-device"></a>Исследование подозрительных устройств Интернета вещей

> [!IMPORTANT]
> Центр безопасности Azure для Интернета вещей сейчас предоставляется в общедоступной предварительной версии.
> Предварительная версия предоставляется без соглашения об уровне обслуживания. Мы не рекомендуем использовать ее в рабочей среде. Некоторые функции могут не поддерживаться или их возможности могут быть ограничены. Дополнительные сведения см. в статье [Дополнительные условия использования предварительных выпусков Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Центр безопасности Azure (ASC) оповещения службы Интернета вещей и свидетельств предоставляют четко при устройств Интернета вещей, вероятно, участия в подозрительных действий или когда указаний существуют, устройство становится известен посторонним. 

В этом руководстве используйте предложения расследования, помогут определить потенциальные риски для вашей организации, выберите способ устранения и понять, какие возможности для предотвращения подобных атак в будущем.  

> [!div class="checklist"]
> * Поиск данных об устройстве
> * Анализ с использованием запросов kql


## <a name="how-can-i-access-my-data"></a>Как получить мои данные?

По умолчанию ASC для Интернета вещей хранит оповещения системы безопасности и рекомендации в рабочей области Log Analytics. Можно также хранить необработанные данные системы безопасности.

Чтобы найти свою рабочую область Log Analytics для хранения данных, сделайте следующее:

1. Откройте Центр Интернета вещей. 
1. В разделе **безопасности**, нажмите кнопку **Обзор**, а затем выберите **параметры**.
1. Измените сведения о конфигурации рабочей области Log Analytics. 
1. Выберите команду **Сохранить**. 

После этого для доступа к данным, хранящимся в рабочей области Log Analytics, сделайте следующее:

1. Выберите и щелкните оповещение ASC для Интернета вещей в Центре Интернета вещей. 
1. Щелкните **Further investigation** (Дальнейшие исследования). 
1. Выберите **To see which devices have this alert click here and view the DeviceId column** (Чтобы узнать, какие устройства имеют это предупреждение, щелкните здесь и просмотрите столбец DeviceId).

## <a name="investigation-steps-for-suspicious-iot-devices"></a>Исследование подозрительных устройств Интернета вещей

Для доступа к insights и необработанные данные об устройствах Интернета вещей, перейдите в рабочую область Log Analytics [для доступа к данным](#how-can-i-access-my-data).

Проверьте и исследовать данные устройства для действий с помощью следующих запросов kql и следующие сведения.

### <a name="related-alerts"></a>Связанные предупреждения

Чтобы узнать, были ли в это же время активированы другие оповещения, используйте следующий запрос kql:

  ~~~
  let device = "YOUR_DEVICE_ID";
  let hub = "YOUR_HUB_NAME";
  SecurityAlert
  | where ExtendedProperties contains device and ResourceId contains tolower(hub)
  | project TimeGenerated, AlertName, AlertSeverity, Description, ExtendedProperties
  ~~~

### <a name="users-with-access"></a>Пользователи с доступом

Чтобы узнать, какие пользователи имеют доступ к этому устройству, используйте следующий запрос kql: 

  ~~~
  let device = "YOUR_DEVICE_ID";
  let hub = "YOUR_HUB_NAME";
  SecurityIoTRawEvent
  | where
     DeviceId == device and AssociatedResourceId contains tolower(hub)
     and RawEventName == "LocalUsers"
  | project
     TimestampLocal=extractjson("$.TimestampLocal", EventDetails, typeof(datetime)),
     GroupNames=extractjson("$.GroupNames", EventDetails, typeof(string)),
     UserName=extractjson("$.UserName", EventDetails, typeof(string))
  | summarize FirstObserved=min(TimestampLocal) by GroupNames, UserName
  ~~~
Используйте следующие данные для исследования: 
  1. Какие пользователи имеют доступ к устройству?
  2. Есть ли у пользователей с доступом уровни должным образом? 

### <a name="open-ports"></a>Открытие портов

Чтобы узнать, какие порты в устройства в настоящее время используются или были использованы, используйте следующий запрос kql: 

  ~~~
  let device = "YOUR_DEVICE_ID";
  let hub = "YOUR_HUB_NAME";
  SecurityIoTRawEvent
  | where
     DeviceId == device and AssociatedResourceId contains tolower(hub)
     and RawEventName == "ListeningPorts"
     and extractjson("$.LocalPort", EventDetails, typeof(int)) <= 1024 // avoid short-lived TCP ports (Ephemeral)
  | project
     TimestampLocal=extractjson("$.TimestampLocal", EventDetails, typeof(datetime)),
     Protocol=extractjson("$.Protocol", EventDetails, typeof(string)),
     LocalAddress=extractjson("$.LocalAddress", EventDetails, typeof(string)),
     LocalPort=extractjson("$.LocalPort", EventDetails, typeof(int)),
     RemoteAddress=extractjson("$.RemoteAddress", EventDetails, typeof(string)),
     RemotePort=extractjson("$.RemotePort", EventDetails, typeof(string))
  | summarize MinObservedTime=min(TimestampLocal), MaxObservedTime=max(TimestampLocal), AllowedRemoteIPAddress=makeset(RemoteAddress), AllowedRemotePort=makeset(RemotePort) by Protocol, LocalPort
  ~~~

    Use this data to discover:
  1. Какие прослушивающие сокеты сейчас активны на устройстве?
  2. Должно быть разрешено прослушивающих сокетов, которые активны в данный момент?
  3. Существуют ли все подозрительные удаленного адреса, подключенных к устройству?

### <a name="user-logins"></a>Имена входа

Чтобы узнать, пользователей, вошедших в устройство используйте следующий запрос kql: 
 
  ~~~
  let device = "YOUR_DEVICE_ID";
  let hub = "YOUR_HUB_NAME";
  SecurityIoTRawEvent
  | where
     DeviceId == device and AssociatedResourceId contains tolower(hub)
     and RawEventName == "Login"
     // filter out local, invalid and failed logins
     and EventDetails contains "RemoteAddress"
     and EventDetails !contains '"RemoteAddress":"127.0.0.1"'
     and EventDetails !contains '"UserName":"(invalid user)"'
     and EventDetails !contains '"UserName":"(unknown user)"'
     //and EventDetails !contains '"Result":"Fail"'
  | project
     TimestampLocal=extractjson("$.TimestampLocal", EventDetails, typeof(datetime)),
     UserName=extractjson("$.UserName", EventDetails, typeof(string)),
     LoginHandler=extractjson("$.Executable", EventDetails, typeof(string)),
     RemoteAddress=extractjson("$.RemoteAddress", EventDetails, typeof(string)),
     Result=extractjson("$.Result", EventDetails, typeof(string))
  | summarize CntLoginAttempts=count(), MinObservedTime=min(TimestampLocal), MaxObservedTime=max(TimestampLocal), CntIPAddress=dcount(RemoteAddress), IPAddress=makeset(RemoteAddress) by UserName, Result, LoginHandler
  ~~~

    Use the query results to discover:
  1. Какие пользователи входили на устройство?
  2. Пользователям, которые вошли, должен выполнить вход?
  3. Подключались ли вошедшие пользователи с ожидаемых или неожидаемых IP-адресов?
  
### <a name="process-list"></a>Список процессов

Чтобы выяснить, правильно ли список процессов, используйте следующий запрос kql: 

  ~~~
  let device = "YOUR_DEVICE_ID";
  let hub = "YOUR_HUB_NAME";
  SecurityIoTRawEvent
  | where
     DeviceId == device and AssociatedResourceId contains tolower(hub)
     and RawEventName == "ProcessCreate"
  | project
     TimestampLocal=extractjson("$.TimestampLocal", EventDetails, typeof(datetime)),
     Executable=extractjson("$.Executable", EventDetails, typeof(string)),
     UserId=extractjson("$.UserId", EventDetails, typeof(string)),
     CommandLine=extractjson("$.CommandLine", EventDetails, typeof(string))
  | join kind=leftouter (
     // user UserId details
     SecurityIoTRawEvent
     | where
        DeviceId == device and AssociatedResourceId contains tolower(hub)
        and RawEventName == "LocalUsers"
     | project
        UserId=extractjson("$.UserId", EventDetails, typeof(string)),
        UserName=extractjson("$.UserName", EventDetails, typeof(string))
     | distinct UserId, UserName
  ) on UserId
  | extend UserIdName = strcat("Id:", UserId, ", Name:", UserName)
  | summarize CntExecutions=count(), MinObservedTime=min(TimestampLocal), MaxObservedTime=max(TimestampLocal), ExecutingUsers=makeset(UserIdName), ExecutionCommandLines=makeset(CommandLine) by Executable
  ~~~

    Use the query results to discover:

  1. Выполнялись ли подозрительные процессы на устройстве?
  2. Запускались ли такие процессы санкционированным пользователем?
  3. Вводились ли в командную строку правильные и ожидаемые аргументы?

## <a name="next-steps"></a>Дальнейшие действия

После изучения устройства и получения сведений о рисках вы можете [настроить получаемые оповещения](quickstart-create-custom-alerts.md), чтобы повысить уровень безопасности решения Интернета вещей. Если у вас еще нет агента безопасности устройства, вы можете [развернуть его](how-to-deploy-agent.md) или [изменить его конфигурацию](how-to-agent-configuration.md) для улучшения результатов. 