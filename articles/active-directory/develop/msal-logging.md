---
title: Ведение журнала в приложениях библиотеки проверки подлинности Майкрософт (MSAL)
titleSuffix: Microsoft identity platform
description: Сведения о ведении журналов в приложениях MSAL (библиотека аутентификации Майкрософт).
services: active-directory
documentationcenter: dev-center-name
author: TylerMSFT
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/05/2019
ms.author: twhitney
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: e12badd84bd929bdeb7b60ad6e99d6b3169e5022
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/30/2019
ms.locfileid: "73150447"
---
# <a name="logging-in-msal-applications"></a>Ведение журнала в приложениях MSAL

Приложения библиотеки проверки подлинности (Майкрософт) (MSAL) создают сообщения журнала, которые могут помочь в диагностике проблем. Для настройки ведения журнала в приложении требуется всего несколько строк кода. Также можно настроить пользовательский элемент управления уровнем детализации и указать, нужно ли регистрировать персональные данные или данные организации. Рекомендуется создать обратный вызов для ведения журнала MSAL и предоставить пользователям возможность отправлять журналы при возникновении проблем с проверкой подлинности.

## <a name="logging-levels"></a>Уровни ведения журнала

MSAL предоставляет несколько уровней детализации ведения журнала:

- Ошибка: указывает, что что-то пошло не так, и была сформирована ошибка. Используется для отладки и выявления проблем.
- Внимание! не всегда произошла ошибка или сбой, но они предназначены для диагностики и выявления проблем.
- INFO: MSAL будет регистрировать события, предназначенные для информационных целей, которые не обязательно предназначены для отладки.
- Verbose: по умолчанию. MSAL записывает в журнал все сведения о поведении библиотеки.

## <a name="personal-and-organizational-data"></a>Персональные данные и данные организации

По умолчанию средство ведения журнала MSAL не захватывает данные личного или организационного уровня. В библиотеке предусмотрен параметр для включения ведения журнала личных данных и организации, если вы решите это сделать.

## <a name="logging-in-msalnet"></a>Ведение журнала в MSAL.NET

 > [!NOTE]
 > Примеры ведения журнала MSAL.NET и многое другое см. на [вики-сайте MSAL.NET](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki) .

В MSAL 3.x ведение журналов настраивается с помощью модификатора построителя `.WithLogging` при создании каждого приложения. Этот метод принимает необязательные параметры:

- `Level` позволяет выбрать необходимый уровень ведения журнала. Если указать Errors, будут регистрироваться только ошибки.
- `PiiLoggingEnabled` позволяет регистрировать личные и организационные данные, если задано значение true. По умолчанию задано значение false, чтобы приложение не регистрировало в журнал персональные данные.
- `LogCallback` задается делегатом, который выполняет ведение журнала. Если `PiiLoggingEnabled` имеет значение true, этот метод получит сообщения дважды: один раз с параметром `containsPii` равно false и сообщением без персональных данных, а второй раз с параметром `containsPii` равно true, а сообщение может содержать персональные данные. В некоторых случаях (если сообщение не содержит персональные данные) сообщения будут одинаковыми.
- `DefaultLoggingEnabled` включает ведение журнала по умолчанию для платформы. Значение по умолчанию — false. Если задано значение true, используется трассировка событий в классических приложениях или приложениях UWP, NSLog в iOS и Logcat в Android.

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
                                             .ExecuteAsync().Result;
  }
 }
 ```

## <a name="logging-in-msal-for-android-using-java"></a>Вход в MSAL для Android с помощью Java

Чтобы включить ведение журнала при создании приложения, создайте обратный вызов для ведения журнала. Обратный вызов принимает следующие параметры:

- `tag` — это строка, передаваемая обратному вызову библиотекой. Он связан с записью журнала и может использоваться для сортировки сообщений журнала.
- `logLevel` позволяет выбрать необходимый уровень ведения журнала. Поддерживаются следующие уровни журнала: `Error`, `Warning`, `Info`и `Verbose`.
- `message` — это содержимое записи журнала.
- `containsPII` указывает, заносятся ли в журнал сообщения, содержащие персональные данные или данные Организации. По умолчанию задано значение false, чтобы приложение не заносить персональные данные в журнал. Если `containsPII` имеет `true`, этот метод получит сообщения дважды: один раз, если для параметра `containsPII` задано значение `false` и `message` без персональных данных, а во второй раз с параметром `containsPii` задано значение `true` и сообщение может содержать персональные данные. В некоторых случаях (если сообщение не содержит персональные данные) сообщения будут одинаковыми.

```java
private StringBuilder mLogs;

mLogs = new StringBuilder();
Logger.getInstance().setExternalLogger(new ILoggerCallback()
{
   @Override
   public void log(String tag, Logger.LogLevel logLevel, String message, boolean containsPII)
   {
      mLogs.append(message).append('\n');
   }
});
```

По умолчанию средство ведения журнала MSAL не захватывает личные сведения или сведения, предоставляемые Организацией.
Чтобы включить ведение журнала личных сведений или сведений, предоставляемых Организацией, выполните следующие действия.

```java
Logger.getInstance().setEnablePII(true);
```

Отключение ведения журнала персональных данных и данных Организации.

```java
Logger.getInstance().setEnablePII(false);
```

По умолчанию ведение журнала в logcat отключено. Чтобы включить: 
```java
Logger.getInstance().setEnableLogcatLog(true);
```

## <a name="logging-in-msaljs"></a>Ведение журнала в MSAL.js

 Включите ведение журнала в MSAL. js, передав объект средства ведения журнала во время настройки для создания экземпляра `UserAgentApplication`. Этот объект средства ведения журнала имеет следующие свойства.

- `localCallback`: экземпляр обратного вызова, который разработчик может предоставить для использования и публикации журналов особым образом. Реализуйте метод localCallback в зависимости от желаемого способа перенаправления журналов.
- `level` (необязательно): настраиваемый уровень ведения журнала. Поддерживаются следующие уровни журнала: `Error`, `Warning`, `Info`и `Verbose`. Значение по умолчанию — `Info`.
- `piiLoggingEnabled` (необязательно). Если задано значение true, записываются данные личного и организационного данных. По умолчанию это значение false, чтобы приложение не заносить персональные данные. Журналы персональных данных никогда не записываются в выходные данные по умолчанию, например в Console, Logcat или NSLog.
- `correlationId` (необязательно) — уникальный идентификатор, используемый для отображения запроса с ответом в целях отладки. По умолчанию используется GUID RFC4122 версии 4 (128 бит).

```javascript
function loggerCallback(logLevel, message, containsPii) {
   console.log(message);
}

var msalConfig = {
    auth: {
        clientId: "<Enter your client id>",
    },
     system: {
             logger: new Msal.Logger(
                                loggerCallback ,{
                                     level: Msal.LogLevel.Verbose,
                                     piiLoggingEnabled: false,
                                     correlationId: '1234'
                                }
                        )
     }
}

var UserAgentApplication = new Msal.UserAgentApplication(msalConfig);
```

## <a name="logging-in-msal-for-ios-and-macos"></a>Вход в MSAL для iOS и macOS

Настройте обратный вызов для записи журнала MSAL и его включения в журнал своего приложения. Сигнатура для обратного вызова выглядит следующим образом:

```objc
/*!
    The LogCallback block for the MSAL logger
 
    @param  level           The level of the log message
    @param  message         The message being logged
    @param  containsPII     If the message might contain Personally Identifiable Information (PII)
                            this will be true. Log messages possibly containing PII will not be
                            sent to the callback unless PIllLoggingEnabled is set to YES on the
                            logger.

 */
typedef void (^MSALLogCallback)(MSALLogLevel level, NSString *message, BOOL containsPII);
```

Пример.

Objective-C
```objc
[MSALGlobalConfig.loggerConfig setLogCallback:^(MSALLogLevel level, NSString *message, BOOL containsPII)
    {
        if (!containsPII)
        {
#if DEBUG
            // IMPORTANT: MSAL logs may contain sensitive information. Never output MSAL logs with NSLog, or print, directly unless you're running your application in debug mode. If you're writing MSAL logs to file, you must store the file securely.
            NSLog(@"MSAL log: %@", message);
#endif
        }
    }];
```

Swift
```swift
MSALGlobalConfig.loggerConfig.setLogCallback { (level, message, containsPII) in
    if let message = message, !containsPII
    {
#if DEBUG
    // IMPORTANT: MSAL logs may contain sensitive information. Never output MSAL logs with NSLog, or print, directly unless you're running your application in debug mode. If you're writing MSAL logs to file, you must store the file securely.
    print("MSAL log: \(message)")
#endif
    }
}
```

### <a name="personal-identifiable-information-pii"></a>Личные идентифицирующие сведения (PII)

По умолчанию MSAL не записывает персональные данные и не регистрирует их. Библиотека позволяет разработчикам приложений включать это через свойство в классе Мсаллогжер. Включив персональные данные, приложение несет ответственность за безопасную обработку данных с высоким уровнем конфиденциальности и соблюдение нормативных требований.

Objective-C
```objc
// By default, the `MSALLogger` doesn't capture any PII

// PII will be logged
MSALGlobalConfig.loggerConfig.piiEnabled = YES;

// PII will NOT be logged
MSALGlobalConfig.loggerConfig.piiEnabled = NO;
```

Swift
```swift
// By default, the `MSALLogger` doesn't capture any PII

// PII will be logged
MSALGlobalConfig.loggerConfig.piiEnabled = true

// PII will NOT be logged
MSALGlobalConfig.loggerConfig.piiEnabled = false
```

### <a name="logging-levels"></a>Уровни ведения журнала

Чтобы задать уровень ведения журнала при использовании MSAL для iOS и macOS, используйте одно из следующих значений:

|уровень  |Описание |
|---------|---------|
| `MSALLogLevelNothing`| Отключить ведение журнала |
| `MSALLogLevelError` | Уровень по умолчанию, выводит сведения только при возникновении ошибок |
| `MSALLogLevelWarning` | Предупреждения |
| `MSALLogLevelInfo` |  Точки входа библиотеки с параметрами и различными операциями цепочки ключей |
|`MSALLogLevelVerbose`     |  Трассировка API       |

Пример.

Objective-C
```objc
MSALGlobalConfig.loggerConfig.logLevel = MSALLogLevelVerbose;
 ```
 
 Swift
```swift
MSALGlobalConfig.loggerConfig.logLevel = .verbose
 ```

### <a name="log-message-format"></a>Формат сообщения журнала

Часть сообщения журнала MSAL имеет формат `TID = <thread_id> MSAL <sdk_ver> <OS> <OS_ver> [timestamp - correlation_id] message`

Пример.

`TID = 551563 MSAL 0.2.0 iOS Sim 12.0 [2018-09-24 00:36:38 - 36764181-EF53-4E4E-B3E5-16FE362CFC44] acquireToken returning with error: (MSALErrorDomain, -42400) User cancelled the authorization session.`

Предоставление идентификаторов корреляции и отметок времени полезно для отслеживания проблем. Сведения о метке времени и ИДЕНТИФИКАТОРе корреляции доступны в сообщении журнала. Единственное надежное место для извлечения — это MSAL сообщения ведения журнала.
