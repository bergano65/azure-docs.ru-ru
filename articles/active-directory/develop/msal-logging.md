---
title: Ведение журнала в приложениях MSAL | Платформа Microsoft Identity
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
ms.date: 08/28/2019
ms.author: twhitney
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4dad8a276cd40b1ff04bbced833b5d70cec4fc87
ms.sourcegitcommit: 263a69b70949099457620037c988dc590d7c7854
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/25/2019
ms.locfileid: "71268579"
---
# <a name="logging-in-msal-applications"></a>Ведение журнала в приложениях MSAL

Приложения библиотеки проверки подлинности (Майкрософт) (MSAL) создают сообщения журнала, которые могут помочь в диагностике проблем. Для настройки ведения журнала в приложении требуется всего несколько строк кода. Также можно настроить пользовательский элемент управления уровнем детализации и указать, нужно ли регистрировать персональные данные или данные организации. Рекомендуется создать обратный вызов для ведения журнала MSAL и предоставить пользователям возможность отправлять журналы при возникновении проблем с проверкой подлинности.

## <a name="logging-levels"></a>Уровни ведения журнала

MSAL предоставляет несколько уровней детализации ведения журнала:

- Ошибка: указывает на наличие проблемы и возникновение ошибки. Используется для отладки и выявления проблем.
- Предупреждение. Ошибка или сбой не существовали, но предназначены для диагностики и выявления проблем.
- Info. MSAL будет регистрировать в журнал события для получения дополнительной информации, но не обязательно для выполнения отладки.
- Verbose. По умолчанию. MSAL записывает в журнал все сведения о поведении библиотеки.

## <a name="personal-and-organizational-data"></a>Персональные данные и данные организации

По умолчанию средство ведения журнала MSAL не захватывает данные личного или организационного уровня. В библиотеке предусмотрен параметр для включения ведения журнала личных данных и организации, если вы решите это сделать.

## <a name="logging-in-msalnet"></a>Ведение журнала в MSAL.NET

 > [!NOTE]
 > Дополнительные сведения о MSAL.NET см. на вики- [сайте MSAL.NET](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki). Получите примеры ведения журнала MSAL.NET и многое другое.
 
В MSAL 3.x ведение журналов настраивается с помощью модификатора построителя `.WithLogging` при создании каждого приложения. Этот метод принимает необязательные параметры:

- *Level* позволяет указать требуемый уровень ведения журнала. Если указать Errors, будут регистрироваться только ошибки.
- *PiiLoggingEnabled* позволяет регистрировать в журнал персональные данные и данные организации, если задано значение true. По умолчанию установлено значение false, чтобы приложение не заносить персональные данные в журнал.
- *LogCallback* присваивается делегат, который выполняет запись в журнал. Если *PiiLoggingEnabled* имеет значение true, этот метод будет получать сообщения дважды: один раз он получит сообщение без персональных данных, если параметр *containsPii* имеет значение false, а второй раз — сообщение с персональными данными, если параметр *containsPii* имеет значение true. В некоторых случаях (если сообщение не содержит персональных данных) сообщение будет таким же.
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
                                             .ExecuteAsync().Result;
  }
 }
 ```

 ## <a name="logging-in-msaljs"></a>Ведение журнала в MSAL.js

 Вы можете включить ведение журнала в MSAL.js, передав объект средства ведения журнала при выполнении настройки для создания экземпляра `UserAgentApplication`. Этот объект средства ведения журнала имеет следующие свойства.

- `localCallback`: экземпляр обратного вызова, который разработчик может предоставить для использования и публикации журналов особым образом. Реализуйте метод localCallback в зависимости от желаемого способа перенаправления журналов.

- `level`(необязательно): настраиваемый уровень ведения журнала. Поддерживаются такие уровни: Error, Warning, Info, Verbose. Значение по умолчанию — Info.

- `piiLoggingEnabled`(необязательно): позволяет регистрировать личные и организационные данные, если задано значение true. По умолчанию установлено значение false, чтобы приложение не заносить персональные данные в журнал. Журналы персональных данных никогда не записываются в выходные данные по умолчанию, например в Console, Logcat или NSLog. По умолчанию установлено значение false.

- `correlationId`(необязательно): уникальный идентификатор, используемый для отображения запроса с ответом для отладки. По умолчанию используется GUID RFC4122 версии 4 (128 бит).

```javascript
function loggerCallback(logLevel, message, containsPii) {
   console.log(message);
}

var msalConfig = {
    auth: {
        clientId: “abcd-ef12-gh34-ikkl-ashdjhlhsdg”,
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

Пример:

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

Код SWIFT
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

Код SWIFT
```swift
// By default, the `MSALLogger` doesn't capture any PII

// PII will be logged
MSALGlobalConfig.loggerConfig.piiEnabled = true

// PII will NOT be logged
MSALGlobalConfig.loggerConfig.piiEnabled = false
```

### <a name="logging-levels"></a>Уровни ведения журнала

Чтобы задать уровень ведения журнала при использовании MSAL для iOS и macOS, используйте одно из следующих значений:

|Уровень  |Описание |
|---------|---------|
| `MSALLogLevelNothing`| Отключить ведение журнала |
| `MSALLogLevelError` | Уровень по умолчанию, выводит сведения только при возникновении ошибок |
| `MSALLogLevelWarning` | Предупреждения |
| `MSALLogLevelInfo` |  Точки входа библиотеки с параметрами и различными операциями цепочки ключей |
|`MSALLogLevelVerbose`     |  Трассировка API       |

Пример:

Objective-C
```objc
MSALGlobalConfig.loggerConfig.logLevel = MSALLogLevelVerbose;
 ```
 
 Код SWIFT
```swift
MSALGlobalConfig.loggerConfig.logLevel = .verbose
 ```

### <a name="log-message-format"></a>Формат сообщения журнала

Часть сообщения журнала MSAL имеет формат`TID = <thread_id> MSAL <sdk_ver> <OS> <OS_ver> [timestamp - correlation_id] message`

Пример:

`TID = 551563 MSAL 0.2.0 iOS Sim 12.0 [2018-09-24 00:36:38 - 36764181-EF53-4E4E-B3E5-16FE362CFC44] acquireToken returning with error: (MSALErrorDomain, -42400) User cancelled the authorization session.`

Предоставление идентификаторов корреляции и отметок времени полезно для отслеживания проблем. Сведения о метке времени и ИДЕНТИФИКАТОРе корреляции доступны в сообщении журнала. Единственное надежное место для извлечения — это MSAL сообщения ведения журнала.
