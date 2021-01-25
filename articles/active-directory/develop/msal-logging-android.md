---
title: Ведение журнала ошибок и исключений в MSAL для Android.
titleSuffix: Microsoft identity platform
description: Узнайте, как вести журнал ошибок и исключений в MSAL для Android.
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 01/25/2021
ms.author: marsma
ms.reviewer: saeeda, jmprieur
ms.custom: aaddev
ms.openlocfilehash: b5d11c1bd091e3d7fbe5be87004b91f242e807f5
ms.sourcegitcommit: 3c3ec8cd21f2b0671bcd2230fc22e4b4adb11ce7
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/25/2021
ms.locfileid: "98763500"
---
# <a name="logging-in-msal-for-android"></a>Ведение журналов в MSAL для Android

[!INCLUDE [MSAL logging introduction](../../../includes/active-directory-develop-error-logging-introduction.md)]

## <a name="logging-in-msal-for-android-using-java"></a>Вход в MSAL для Android с помощью Java

Чтобы включить ведение журнала при создании приложения, создайте обратный вызов для ведения журнала. Обратный вызов принимает следующие параметры:

- `tag` — Это строка, передаваемая обратному вызову библиотекой. Он связан с записью журнала и может использоваться для сортировки сообщений журнала.
- `logLevel` позволяет выбрать необходимый уровень ведения журнала. Поддерживаются следующие уровни ведения журнала: `Error` , `Warning` , `Info` и `Verbose` .
- `message` содержимое записи журнала.
- `containsPII` Указывает, заносятся ли в журнал сообщения, содержащие персональные данные или данные Организации. По умолчанию задано значение false, чтобы приложение не заносить персональные данные в журнал. Если `containsPII` имеет значение `true` , этот метод получит сообщения дважды: один раз с `containsPII` параметром, для которого задано значение, `false` и `message` без персональных данных, а второй раз с `containsPii` параметром, для которого задано значение, `true` а сообщение может содержать персональные данные. В некоторых случаях (если сообщение не содержит персональные данные) сообщения будут одинаковыми.

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

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные примеры кода см. в статье [примеры кода платформы удостоверений Microsoft Identity](sample-v2-code.md).