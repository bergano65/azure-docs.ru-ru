---
title: Регистрация в приложениях MSAL Azure
titleSuffix: Microsoft identity platform
description: Сведения о ведении журналов в приложениях MSAL (библиотека аутентификации Майкрософт).
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 11/11/2019
ms.author: marsma
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: 58697cc535357710c6889f05060b5e04e129ae7d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77084893"
---
# <a name="logging-in-msal-applications"></a>Регистрация в приложениях MSAL

Приложения Библиотеки подлинности Майкрософт (MSAL) генерируют сообщения журналов, которые могут помочь диагностировать проблемы. Для настройки ведения журнала в приложении требуется всего несколько строк кода. Также можно настроить пользовательский элемент управления уровнем детализации и указать, нужно ли регистрировать персональные данные или данные организации. Мы рекомендуем создать обратный вызов для регистрации MSAL и предоставить пользователям возможность отправлять журналы при возникновении проблем с аутентификацией.

## <a name="logging-levels"></a>Уровни ведения журнала

MSAL предоставляет несколько уровней деталей регистрации:

- Ошибка: Указывает на то, что что-то пошло не так и была сгенерирована ошибка. Используется для отладки и выявления проблем.
- Предупреждение: Не обязательно была ошибка или сбой, но предназначены для диагностики и выявления проблем.
- Информация: MSAL будет регистрировать события, предназначенные для информационных целей, не обязательно предназначенных для отладки.
- Verbose: По умолчанию. MSAL регистрирует полную информацию о поведении библиотеки.

## <a name="personal-and-organizational-data"></a>Персональные данные и данные организации

По умолчанию регистратор MSAL не записывается с высокочувствительными личными или организационными данными. Библиотека предоставляет возможность регистрации личных и организационных данных, если вы решите это сделать.

Для получения подробной информации о регистрации MSAL на определенном языке выберите вкладку, соответствующую вашему языку:

## <a name="net"></a>[.NET](#tab/dotnet)

## <a name="logging-in-msalnet"></a>Ведение журнала в MSAL.NET

 > [!NOTE]
 > Смотрите [MSAL.NET вики](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki) для образцов MSAL.NET журналов и многое другое.

В MSAL 3.x ведение журналов настраивается с помощью модификатора построителя `.WithLogging` при создании каждого приложения. Этот метод принимает необязательные параметры:

- `Level`позволяет решить, какой уровень регистрации вы хотите. Если указать Errors, будут регистрироваться только ошибки.
- `PiiLoggingEnabled`позволяет регистрировать личные и организационные данные, если они верны. По умолчанию задано значение false, чтобы приложение не регистрировало в журнал персональные данные.
- `LogCallback`устанавливается делегату, который делает журнал. Если `PiiLoggingEnabled` это правда, то этот метод будет `containsPii` получать сообщения дважды: один раз с параметром `containsPii` равно ложным и сообщение без персональных данных, и второй раз с параметром, равным истинному и сообщение может содержать личные данные. В некоторых случаях (если сообщение не содержит персональные данные) сообщения будут одинаковыми.
- `DefaultLoggingEnabled`позволяет записываться по умолчанию для платформы. Значение по умолчанию — false. Если задано значение true, используется трассировка событий в классических приложениях или приложениях UWP, NSLog в iOS и Logcat в Android.

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

## <a name="android"></a>[Android](#tab/android)

## <a name="logging-in-msal-for-android-using-java"></a>Регистрация в MSAL для Android с помощью Java

Включите вход при создании приложения, создав обратный вызов. Обратный вызов использует следующие параметры:

- `tag`— строка, передаваемые библиотекой на обратный вызов. Он связан со записью журнала и может использоваться для сортировки сообщений журнала.
- `logLevel`позволяет решить, какой уровень регистрации вы хотите. Поддерживаемые уровни `Error`журнала: `Info`, `Verbose` `Warning`, и .
- `message`является содержанием записи журнала.
- `containsPII`определяет, регистрируются ли сообщения, содержащие персональные данные, или организационные данные. По умолчанию это установлено на ложное, так что приложение не регистрирует личные данные. Если `containsPII` `true`есть, этот метод будет получать сообщения `containsPII` дважды: один раз с набором параметра `false` и `message` без персональных данных, и во второй раз с `containsPii` набором параметра `true` и сообщение может содержать личные данные. В некоторых случаях (если сообщение не содержит персональные данные) сообщения будут одинаковыми.

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

По умолчанию регистратор MSAL не будет фиксировать личную идентифицируемую информацию или организационную идентифицируемую информацию.
Для обеспечения регистрации личной идентифицируемой информации или организационной идентифицируемой информации:

```java
Logger.getInstance().setEnablePII(true);
```

Чтобы отключить регистрацию персональных данных и организационных данных:

```java
Logger.getInstance().setEnablePII(false);
```

По умолчанию журнал для журнала отключен. Для включения:

```java
Logger.getInstance().setEnableLogcatLog(true);
```

## <a name="javascript"></a>[Javascript](#tab/javascript)

 Включите журнал в MSAL.js (JavaScript), передав объект `UserAgentApplication` регистратора во время конфигурации для создания экземпляра. Этот объект средства ведения журнала имеет следующие свойства.

- `localCallback`экземпляр обратного вызова, который может быть предоставлен разработчиком для потребления и публикации журналов на заказ. Реализуйте метод localCallback в зависимости от желаемого способа перенаправления журналов.
- `level`(необязательно): настраиваемый уровень журнала. Поддерживаемые уровни `Error`журнала: `Info`, `Verbose` `Warning`, и . Значение по умолчанию — `Info`.
- `piiLoggingEnabled`(необязательно): если установлен на истину, журналы личных и организационных данных. По умолчанию это неверно, так что приложение не регистрирует личные данные. Журналы персональных данных никогда не записываются в выходные данные по умолчанию, например в Console, Logcat или NSLog.
- `correlationId`(необязательно): уникальный идентификатор, используемый для картирования запроса с ответом для целей отладки. По умолчанию используется GUID RFC4122 версии 4 (128 бит).

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

## <a name="objective-c"></a>[Objective-C](#tab/objc)

## <a name="msal-for-ios-and-macos-logging-objc"></a>MSAL для iOS и macOS лесозаготовки-ObjC

Установите обратный вызов, чтобы захватить MSAL регистрации и включить его в журнал вашего собственного приложения. Подпись для обратного вызова выглядит следующим образом:

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

### <a name="personal-data"></a>Персональные данные

По умолчанию MSAL не захватывает и не регистрирует никаких персональных данных (PII). Библиотека позволяет разработчикам приложений включить это через свойство в классе MSALLogger. Включив `pii.Enabled`приложение, приложение берет на себя ответственность за безопасную обработку высокочувствительных данных и соблюдение нормативных требований.

```objc
// By default, the `MSALLogger` doesn't capture any PII

// PII will be logged
MSALGlobalConfig.loggerConfig.piiEnabled = YES;

// PII will NOT be logged
MSALGlobalConfig.loggerConfig.piiEnabled = NO;
```

### <a name="logging-levels"></a>Уровни ведения журнала

Чтобы установить уровень регистрации при входе в систему с помощью MSAL для iOS и macOS, используйте одно из следующих значений:

|Level  |Описание |
|---------|---------|
| `MSALLogLevelNothing`| Отключить все лесозаготовки |
| `MSALLogLevelError` | Уровень по умолчанию, распечатывает информацию только при возникновении ошибок |
| `MSALLogLevelWarning` | Предупреждения |
| `MSALLogLevelInfo` |  Пункты входа в библиотеку с параметрами и различными операциями по брелоку |
|`MSALLogLevelVerbose`     |  Трассировка API |

Пример:

```objc
MSALGlobalConfig.loggerConfig.logLevel = MSALLogLevelVerbose;
 ```

 ### <a name="log-message-format"></a>Формат сообщения журнала

Часть сообщений сообщений сообщений msAL находится в формате`TID = <thread_id> MSAL <sdk_ver> <OS> <OS_ver> [timestamp - correlation_id] message`

Пример:

`TID = 551563 MSAL 0.2.0 iOS Sim 12.0 [2018-09-24 00:36:38 - 36764181-EF53-4E4E-B3E5-16FE362CFC44] acquireToken returning with error: (MSALErrorDomain, -42400) User cancelled the authorization session.`

Предоставление идентию корреляции и меток времени полезно для отслеживания проблем. Информация timestamp и идентификационный относясь корреляции доступна в сообщении журнала. Единственное надежное место для их получения — сообщения о регистрации MSAL.

## <a name="swift"></a>[Swift](#tab/swift)

## <a name="msal-for-ios-and-macos-logging-swift"></a>MSAL для iOS и macOS лесозаготовки-Swift

Установите обратный вызов, чтобы захватить MSAL регистрации и включить его в журнал вашего собственного приложения. Подпись (представленная в Objective-C) для обратного вызова выглядит следующим образом:

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

### <a name="personal-data"></a>Персональные данные

По умолчанию MSAL не захватывает и не регистрирует никаких персональных данных (PII). Библиотека позволяет разработчикам приложений включить это через свойство в классе MSALLogger. Включив `pii.Enabled`приложение, приложение берет на себя ответственность за безопасную обработку высокочувствительных данных и соблюдение нормативных требований.

```swift
// By default, the `MSALLogger` doesn't capture any PII

// PII will be logged
MSALGlobalConfig.loggerConfig.piiEnabled = true

// PII will NOT be logged
MSALGlobalConfig.loggerConfig.piiEnabled = false
```

### <a name="logging-levels"></a>Уровни ведения журнала

Чтобы установить уровень регистрации при входе в систему с помощью MSAL для iOS и macOS, используйте одно из следующих значений:

|Level  |Описание |
|---------|---------|
| `MSALLogLevelNothing`| Отключить все лесозаготовки |
| `MSALLogLevelError` | Уровень по умолчанию, распечатывает информацию только при возникновении ошибок |
| `MSALLogLevelWarning` | Предупреждения |
| `MSALLogLevelInfo` |  Пункты входа в библиотеку с параметрами и различными операциями по брелоку |
|`MSALLogLevelVerbose`     |  Трассировка API |

Пример:

```swift
MSALGlobalConfig.loggerConfig.logLevel = .verbose
 ```

### <a name="log-message-format"></a>Формат сообщения журнала

Часть сообщений сообщений сообщений msAL находится в формате`TID = <thread_id> MSAL <sdk_ver> <OS> <OS_ver> [timestamp - correlation_id] message`

Пример:

`TID = 551563 MSAL 0.2.0 iOS Sim 12.0 [2018-09-24 00:36:38 - 36764181-EF53-4E4E-B3E5-16FE362CFC44] acquireToken returning with error: (MSALErrorDomain, -42400) User cancelled the authorization session.`

Предоставление идентию корреляции и меток времени полезно для отслеживания проблем. Информация timestamp и идентификационный относясь корреляции доступна в сообщении журнала. Единственное надежное место для их получения — сообщения о регистрации MSAL.

## <a name="java"></a>[Java](#tab/java)

## <a name="msal-for-java-logging"></a>MSAL для регистрации Java

MSAL для Java позволяет использовать библиотеку регистрации, которую вы уже используете с вашим приложением, до тех пор, пока она совместима с SLF4J. MSAL для Java использует [простой лесозаготовительный фасад для Java](http://www.slf4j.org/) (SLF4J) в качестве простого фасада или абстракции для различных платформ журналов, таких как [java.util.logging,](https://docs.oracle.com/javase/7/docs/api/java/util/logging/package-summary.html) [Logback](http://logback.qos.ch/) и [Log4j](https://logging.apache.org/log4j/2.x/). SLF4J позволяет пользователю подключить нужную платформу журнала во время развертывания.

Например, чтобы использовать Logback в качестве платформы входа в приложение, добавьте зависимость logback в файл Maven pom для приложения:

```xml
<dependency>
    <groupId>ch.qos.logback</groupId>
    <artifactId>logback-classic</artifactId>
    <version>1.2.3</version>
</dependency>
```

Затем добавьте файл конфигурации Logback:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration debug="true">

</configuration>
```

SLF4J автоматически привязывается к Logback во время развертывания. На консоль будут записаны журналы MSAL.

Для получения инструкций о том, как привязаться к другим средам регистрации, см. [SLF4J manual](http://www.slf4j.org/manual.html)

### <a name="personal-and-organization-information"></a>Личная и организационная информация

По умолчанию журнал MSAL не захватывает и не регистрирует личные или организационные данные. В следующем примере журнал личных или организационных данных не выполняется по умолчанию:

```java
    PublicClientApplication app2 = PublicClientApplication.builder(PUBLIC_CLIENT_ID)
            .authority(AUTHORITY)
            .build();
```

Включите регистрацию персональных `logPii()` и организационных данных, установив на разработчика клиентских приложений. Если вы включите регистрацию персональных или организационных данных, ваше приложение должно взять на себя ответственность за безопасную обработку высокочувствительных данных и соблюдение любых нормативных требований.

В следующем примере включена регистрация личных или организационных данных:

```java
PublicClientApplication app2 = PublicClientApplication.builder(PUBLIC_CLIENT_ID)
        .authority(AUTHORITY)
        .logPii(true)
        .build();
```

## <a name="python"></a>[Python](#tab/python)

## <a name="msal-for-python-logging"></a>MSAL для лесозаготовок Python

Регистрация в MSAL Python использует стандартный `logging.info("msg")` механизм регистрации Python, например, вы можете настроить MSAL регистрации следующим образом (и увидеть его в действии в [username_password_sample):](https://github.com/AzureAD/microsoft-authentication-library-for-python/blob/1.0.0/sample/username_password_sample.py#L31L32)

### <a name="enable-debug-logging-for-all-modules"></a>Включить отладку регистрации для всех модулей

По умолчанию запись в любом скрипте Python отключена. Если требуется включить отладку регистрации для всех модулей во всем скрипте Python, используйте:

```python
logging.basicConfig(level=logging.DEBUG)
```

### <a name="silence-only-msal-logging"></a>Молчание только MSAL лесозаготовки

Чтобы заставить замолчать только журнал ыms-библиотеки MSAL, позволяя отладить вход во все другие модули в скрипте Python, выключите регистратор, используемый MSAL Python:

```Python
logging.getLogger("msal").setLevel(logging.WARN)
```

### <a name="personal-and-organizational-data-in-python"></a>Личные и организационные данные в Python

MSAL для Python не регистрирует персональные данные или организационные данные. Нет никакого свойства для входиных или выключаемых персональных или организационных данных.

Вы можете использовать стандартные журналы Python для регистрации любых, которые вы хотите, но вы несете ответственность за безопасную обработку конфиденциальных данных и соблюдение нормативных требований.

Для получения дополнительной информации о входе в Python, пожалуйста, обратитесь к Python [в журнале HOWTO](https://docs.python.org/3/howto/logging.html#logging-basic-tutorial).

---
