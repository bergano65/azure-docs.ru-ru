---
title: Ведение журналов в приложениях MSAL | Azure
description: Сведения о ведении журналов в приложениях MSAL (библиотека аутентификации Майкрософт).
services: active-directory
documentationcenter: dev-center-name
author: rwike77
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/22/2019
ms.author: ryanwi
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 58f18995d46ca61ae68a7b226bbfc9a286e73a0b
ms.sourcegitcommit: f6c85922b9e70bb83879e52c2aec6307c99a0cac
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/11/2019
ms.locfileid: "65544094"
---
# <a name="logging"></a>Ведение журналов
Приложения MSAL (библиотека аутентификации Майкрософт) используются для создания сообщений журнала, которые помогают диагностировать проблемы и позволяют получить дополнительные сведения. Для настройки ведения журнала в приложении требуется всего несколько строк кода. Также можно настроить пользовательский элемент управления уровнем детализации и указать, нужно ли регистрировать персональные данные или данные организации. Рекомендуется настроить обратный вызов при ведении журнала MSAL и обеспечить отправку журналов, когда у пользователей возникают проблемы с аутентификацией.

## <a name="logging-levels"></a>Уровни ведения журнала

Средство ведения журнала MSAL поддерживает запись данных на нескольких уровнях детализации:

- Ошибка: указывает на наличие проблемы и возникновение ошибки. Используется для отладки и выявления проблем.
- Предупреждение. Подозрительные события, о которых приложению требуется больше информации. Эти события необязательно будут ошибкой или сбоем, но их требуется рассмотреть для диагностики и выявления проблем.
- Info. MSAL будет регистрировать в журнал события для получения дополнительной информации, но не обязательно для выполнения отладки.
- Verbose. По умолчанию. MSAL будет регистрировать большой объем данных и предоставлять полные сведения о поведении библиотеки.

## <a name="personal-and-organizational-data"></a>Персональные данные и данные организации
По умолчанию средство ведения журналов MSAL не регистрирует персональные данные или данные организации с высокой степенью конфиденциальности. Библиотека позволяет включить ведение журналов персональных данных и данных организации при необходимости.

## <a name="logging-in-msalnet"></a>Ведение журнала в MSAL.NET
В MSAL 3.x ведение журналов настраивается с помощью модификатора построителя `.WithLogging` при создании каждого приложения. Этот метод принимает необязательные параметры:

- *Level* позволяет указать требуемый уровень ведения журнала. Если указать Errors, будут регистрироваться только ошибки.
- *PiiLoggingEnabled* позволяет регистрировать в журнал персональные данные и данные организации, если задано значение true. По умолчанию задано значение false, чтобы приложение не регистрировало в журнал персональные данные.
- *LogCallback* присваивается делегат, который выполняет запись в журнал. Если *PiiLoggingEnabled* имеет значение true, этот метод будет получать сообщения дважды: один раз он получит сообщение без персональных данных, если параметр *containsPii* имеет значение false, а второй раз — сообщение с персональными данными, если параметр *containsPii* имеет значение true. В некоторых случаях (если сообщение не содержит персональные данные) сообщения будут одинаковыми.
- *DefaultLoggingEnabled* позволяет включить по умолчанию ведение журнала для платформы. Значение по умолчанию — false. Если задано значение true, используется трассировка событий в классических приложениях или приложениях UWP, NSLog в iOS и Logcat в Android.

```csharp
class Program
 {
  private static void Log(LogLevel level, string message, bool containsPii)
  {
     if (containsPii)
     {
        Console.ForegroundColor = ConsoleColor.Red;
     }
     Console.WriteLine($"{level} {message}");
     Console.ResetColor();
  }

  static void Main(string[] args)
  {
    var scopes = new string[] { "User.Read" };

    var application = PublicClientApplicationBuilder.Create("<clientID>")
                      .WithLogging(Log, LogLevel.Info, true)
                      .Build();

    AuthenticationResult result = application.AcquireTokenInteractive(scopes)
                                             .ExecuteAsnc();
  }
 }
 ```

 ## <a name="logging-in-msaljs"></a>Ведение журнала в MSAL.js

 Вы можете включить ведение журнала в MSAL.js, передав объект средства ведения журнала при выполнении настройки для создания экземпляра `UserAgentApplication`. Этот объект средства ведения журнала имеет следующие свойства.

- *localCallback* — экземпляр обратного вызова, который разработчик может предоставить для использования и публикации журналов особым способом. Реализуйте метод localCallback в зависимости от желаемого способа перенаправления журналов.

- *level* (необязательно) — настраиваемый уровень ведения журналов. Поддерживаются такие уровни: Error, Warning, Info, Verbose. Значение по умолчанию — Info.

- *piiLoggingEnabled* (необязательно) позволяет регистрировать в журнал персональные данные и данные организации, если задано значение true. По умолчанию имеет значение false, чтобы приложение не регистрировало в журнал персональные данные. Журналы персональных данных никогда не записываются в выходные данные по умолчанию, например в Console, Logcat или NSLog. По умолчанию установлено значение false.

- *correlationId* (необязательно) — уникальный идентификатор, используемый для сопоставления запроса с ответом в целях отладки. По умолчанию используется GUID RFC4122 версии 4 (128 бит).

```javascript

function loggerCallback(logLevel, message, containsPii) {
   console.log(message);
}

var msalConfig = {
    auth: {
        clientId: “abcd-ef12-gh34-ikkl-ashdjhlhsdg”,
    },
    system: {
        logger: {
            localCallback: loggerCallback,
            level: Msal.LogLevel.Verbose,
            piiLoggingEnabled: false,
            correlationId: '1234'
        }
    }
}

var UserAgentApplication = new Msal.UserAgentApplication(msalConfig);
```
