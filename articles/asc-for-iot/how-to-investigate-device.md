---
title: ASC для Интернета вещей устройства исследования руководство Предварительная версия | Документация Майкрософт
description: Это Практическое руководство содержит сведения об использовании ASC для Интернета вещей для расследования подозрительных устройства Интернета вещей с помощью Log Analytics.
services: ascforiot
documentationcenter: na
author: mlottner
manager: barbkess
editor: ''
ms.assetid: b18b48ae-b445-48f8-9ac0-365d6e065b64
ms.service: ascforiot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/27/2019
ms.author: mlottner
ms.openlocfilehash: 032315d1b618b9716e23c8433c6ec1bf64b7e77d
ms.sourcegitcommit: c63fe69fd624752d04661f56d52ad9d8693e9d56
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2019
ms.locfileid: "58580543"
---
# <a name="investigate-a-suspicious-iot-device"></a>Исследовать подозрительные устройства Интернета вещей

> [!IMPORTANT]
> ASC для Интернета вещей находится в общедоступной предварительной версии.
> Эта предварительная версия предоставляется без соглашения об уровне обслуживания и не рекомендуется для производственных рабочих нагрузок. Некоторые функции могут не поддерживаться или их возможности могут быть ограничены. Дополнительные сведения см. в статье [Дополнительные условия использования предварительных выпусков Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

ASC оповещения службы Интернета вещей и свидетельств предоставляют четко при устройств Интернета вещей, вероятно, участия в подозрительных действий или когда указаний существуют, устройство становится известен посторонним. 

В этом руководстве используйте предложения расследования, помогут определить потенциальные риски для вашей организации, выберите способ устранения и понять, какие возможности для предотвращения подобных атак в будущем.  

> [!div class="checklist"]
> * Найти данные на устройстве
> * Рассмотрите возможность использования запросов kql


## <a name="how-can-i-access-my-data"></a>Как получить мои данные?

По умолчанию ASC для Интернета вещей хранит оповещения системы безопасности и рекомендации в рабочей области Log Analytics. Можно также хранить данные необработанной безопасности.

Чтобы найти рабочую область Log Analytics для хранения данных:

1. Откройте центр Интернета вещей 
1. Нажмите кнопку **безопасности**, а затем выберите **параметры**.
1. Измените сведениям о конфигурации рабочей области Log Analytics. 
1. Выберите команду **Сохранить**. 

Следующую конфигурацию выполните следующие действия для доступа к данным, хранящиеся в рабочей области Log Analytics.

1. Выберите и щелкните ASC для Интернета вещей оповещения в центре Интернета вещей. 
1. Нажмите кнопку **Дальнейшее изучение**. 
1. Выберите **чтобы увидеть, какие устройства имеют этого предупреждения щелкните здесь и просмотрите столбец DeviceId**.

## <a name="investigation-steps-for-suspicious-iot-devices"></a>Действия расследования подозрительных устройств Интернета вещей

Для доступа к insights и необработанные данные об устройствах Интернета вещей, перейдите в рабочую область Log Analytics [для доступа к данным](#how-can-i-access-my-data).

Проверьте и исследовать данные устройства для действий с помощью следующих запросов kql и следующие сведения.

### <a name="related-alerts"></a>Связанные предупреждения

Чтобы узнать, были ли другие предупреждения запуска вокруг же времени используйте следующий запрос kql:

  ~~~
  let device = "YOUR_DEVICE_ID";
  let hub = "YOUR_HUB_NAME";
  SecurityAlert
  | where ExtendedProperties contains device and ResourceId contains tolower(hub)
  | project TimeGenerated, AlertName, AlertSeverity, Description, ExtendedProperties
  ~~~

### <a name="users-with-access"></a>Пользователи с доступом

Чтобы узнать, какие пользователи имеют доступ к этой устройства используйте следующий запрос kql: 

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
Используйте эти данные для обнаружения. 
  1. Какие пользователи имеют доступ к устройству?
  2. Есть ли у пользователей с доступом уровни должным образом? 

### <a name="open-ports"></a>Открытие портов

Чтобы найти out, какие порты в устройства в настоящее время используются или были использованы используйте следующий запрос kql: 

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
  1. Какие прослушивающих сокетов в настоящее время активны на устройстве?
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
  1. Какие пользователи, вошедшие устройство?
  2. Пользователи, которые зарегистрированы в должны выполнить вход?
  3. Пользователи, которые вошли соединился с ожидаемыми или неожиданными IP-адресов?
  
### <a name="process-list"></a>Список процессов

Чтобы определить список процессов — как предполагаемого использования kql следующий запрос: 

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

  1. Возникали ли все подозрительные процессы, запущенные на устройстве?
  2. Процессы выполнялись с соответствующим пользователям?
  3. Любой командной строки выполнений содержал правильный и ожидаемые аргументы?

## <a name="next-steps"></a>Дальнейшие действия
После изучения устройства и получение лучше понять рисков, может понадобиться рассмотреть [Настройка настраиваемые оповещения](quickstart-create-custom-alerts.md) чтобы повысить уровень безопасности решения Интернета вещей. Если у вас еще нет агент устройства, рассмотрите возможность [развертывание агента безопасности](how-to-deploy-agent.md) или [изменение конфигурации существующего агента устройства](how-to-agent-configuration.md) для улучшения результатов. 