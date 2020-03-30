---
title: Центр безопасности Azure для руководства по расследованию IoT устройств Документы Майкрософт
description: Таким образом, как руководство в руководстве объясняет, как использовать Центр безопасности Azure для IoT для исследования подозрительного устройства IoT с помощью Log Analytics.
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.assetid: b18b48ae-b445-48f8-9ac0-365d6e065b64
ms.subservice: asc-for-iot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/23/2019
ms.author: mlottner
ms.openlocfilehash: 8d2fe8d63c7ece6f3b3426d8fc5a3454a61826f8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "68596248"
---
# <a name="investigate-a-suspicious-iot-device"></a>Исследование подозрительных устройств Интернета вещей

Центр безопасности Azure для оповещений служб IoT дает четкие указания, когда устройства IoT подозреваются в причастности к подозрительным действиям или когда имеются признаки того, что устройство скомпрометировано. 

В этом руководстве используйте предложения по расследованию, предоставляемые для определения потенциальных рисков для вашей организации, решить, как исправить, и найти наилучшие способы предотвращения подобных атак в будущем.  

> [!div class="checklist"]
> * Поиск данных об устройстве
> * Анализ с использованием запросов kql


## <a name="how-can-i-access-my-data"></a>Как получить доступ к своим данным?

По умолчанию Центр безопасности Azure security Center для IoT хранит ваши оповещения и рекомендации по безопасности в рабочем пространстве log Analytics. Можно также хранить необработанные данные системы безопасности.

Чтобы найти рабочее пространство для анализа журналов для хранения данных:

1. Откройте Центр Интернета вещей. 
1. Под **безопасностью,** нажмите **Обзор**, а затем выберите **Настройки.**
1. Измените сведения о конфигурации рабочей области Log Analytics. 
1. Нажмите **Сохранить**. 

После этого для доступа к данным, хранящимся в рабочей области Log Analytics, сделайте следующее:

1. Выберите и нажмите на Центр безопасности Azure для оповещения IoT в вашем концентраторе IoT. 
1. Щелкните **Further investigation** (Дальнейшие исследования). 
1. Выберите **To see which devices have this alert click here and view the DeviceId column** (Чтобы узнать, какие устройства имеют это предупреждение, щелкните здесь и просмотрите столбец DeviceId).

## <a name="investigation-steps-for-suspicious-iot-devices"></a>Исследование подозрительных устройств Интернета вещей

Чтобы просмотреть информацию и необработанные данные о ваших устройствах IoT, перейдите в рабочее пространство Log Analytics [для доступа к вашим данным.](#how-can-i-access-my-data)

Смотрите пример kql запросы ниже, чтобы начать с расследования оповещений и действий на вашем устройстве.

### <a name="related-alerts"></a>Похожие оповещения

Чтобы узнать, были ли в это же время активированы другие оповещения, используйте следующий запрос kql:

  ```
  let device = "YOUR_DEVICE_ID";
  let hub = "YOUR_HUB_NAME";
  SecurityAlert
  | where ExtendedProperties contains device and ResourceId contains tolower(hub)
  | project TimeGenerated, AlertName, AlertSeverity, Description, ExtendedProperties
  ```

### <a name="users-with-access"></a>Пользователи с доступом

Чтобы узнать, какие пользователи имеют доступ к этому устройству, используйте следующий запрос kql: 

 ```
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
 ```
Используйте следующие данные для исследования: 
- Какие пользователи имеют доступ к устройству?
- Имеют ли пользователи с доступом ожидаемые уровни разрешений?

### <a name="open-ports"></a>Открытие портов

Чтобы узнать, какие порты на устройстве в настоящее время используются или используются, используйте следующий запрос kql: 

 ```
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
 ```

Используйте следующие данные для исследования:
- Какие прослушивающие сокеты сейчас активны на устройстве?
- Следует ли разрешить прослушивающие розетки, которые в настоящее время активны?
- Есть ли подозрительные удаленные адреса, подключенные к устройству?

### <a name="user-logins"></a>Логины пользователей

Чтобы найти пользователей, вошедших в устройство, используйте следующий запрос kql: 
 
 ```
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
 ```

Используйте следующие результаты запроса для исследования:
- Какие пользователи входили на устройство?
- Должны ли пользователи, вошедших в систему, войти в систему?
- Подключались ли вошедшие пользователи с ожидаемых или неожидаемых IP-адресов?
  
### <a name="process-list"></a>Список процессов

Чтобы узнать, является ли список процессов ожидаемым, используйте следующий запрос kql: 

 ```
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
```

Используйте следующие результаты запроса для исследования:

- Выполнялись ли подозрительные процессы на устройстве?
- Запускались ли такие процессы санкционированным пользователем?
- Вводились ли в командную строку правильные и ожидаемые аргументы?

## <a name="next-steps"></a>Дальнейшие действия

После изучения устройства и получения сведений о рисках вы можете [настроить получаемые оповещения](quickstart-create-custom-alerts.md), чтобы повысить уровень безопасности решения Интернета вещей. Если у вас еще нет агента безопасности устройства, вы можете [развернуть его](how-to-deploy-agent.md) или [изменить его конфигурацию](how-to-agent-configuration.md) для улучшения результатов. 
