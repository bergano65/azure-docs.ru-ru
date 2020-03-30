---
title: Учетные данные сертификата идентификационных данных платформы Майкрософт
titleSuffix: Microsoft identity platform
description: В этой статье обсуждается регистрация и использование учетных данных сертификатов для проверки подлинности приложения.
services: active-directory
author: rwike77
manager: CelesteDG
ms.assetid: 88f0c64a-25f7-4974-aca2-2acadc9acbd8
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 12/18/2019
ms.author: ryanwi
ms.reviewer: nacanuma, jmprieur
ms.custom: aaddev
ms.openlocfilehash: 26030c12d98d796ceb1f66f198aede6e40eebd94
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78399011"
---
# <a name="microsoft-identity-platform-application-authentication-certificate-credentials"></a>Учетные данные сертификата аутентификации приложения платформы идентификации Майкрософт

Платформа идентификации Майкрософт позволяет приложению использовать свои собственные учетные данные для проверки подлинности, например, в [OAuth 2.0 Клиентские учетные данные Grant flowv2.0](v2-oauth2-client-creds-grant-flow.md) и [потоке On-Behalf-Of).](v2-oauth2-on-behalf-of-flow.md)

Одной из форм учетных данных, которые приложение может использовать для аутентификации, является утверждение JSON Web Token (JWT), подписанное с помощью сертификата приложения.

## <a name="assertion-format"></a>Формат утверждения
Идентификационная платформа Майкрософт для вычисления утверждения можно использовать одну из многочисленных библиотек [JSON Web Token](https://jwt.ms/) на языке по вашему выбору. Маркер содержит следующие сведения:

### <a name="header"></a>Заголовок

| Параметр |  Комментарий |
| --- | --- |
| `alg` | Должен иметь значение **RS256** |
| `typ` | Должен иметь значение **JWT** |
| `x5t` | Это должен быть отпечаток SHA-1 сертификата X.509 |

### <a name="claims-payload"></a>Утверждения (полезные данные)

| Параметр |  Remarks |
| --- | --- |
| `aud` | Аудитория: должна быть ** https://login.microsoftonline.com/ *tenant_Id*/oauth2/token** |
| `exp` | Срок действия: дата, когда истекает срок действия маркера. Время представлено как количество секунд с 1 января 1970 года (1970-01-01T0:0:0Z) в формате UTC до истечения срока действия маркера.|
| `iss` | Издатель: параметр должен иметь значение client_id (идентификатор приложения службы клиента) |
| `jti` | GUID: идентификатор JWT |
| `nbf` | Не ранее: дата, до которой маркер не может использоваться. Время представлено как количество секунд с 1 января 1970 года (1970-01-01T0:0:0Z) в формате UTC до времени выдачи маркера. |
| `sub` | Субъект: параметр должен иметь значение client_id (идентификатор приложения службы клиента), как и `iss` |

### <a name="signature"></a>Сигнатура

Подпись формируется через применение сертификата, как описано в [документе RFC7519 о спецификации JSON Web Token](https://tools.ietf.org/html/rfc7519).

## <a name="example-of-a-decoded-jwt-assertion"></a>Пример декодированного утверждения JWT

```JSON
{
  "alg": "RS256",
  "typ": "JWT",
  "x5t": "gx8tGysyjcRqKjFPnd7RFwvwZI0"
}
.
{
  "aud": "https: //login.microsoftonline.com/contoso.onmicrosoft.com/oauth2/token",
  "exp": 1484593341,
  "iss": "97e0a5b7-d745-40b6-94fe-5f77d35c6e05",
  "jti": "22b3bb26-e046-42df-9c96-65dbd72c1c81",
  "nbf": 1484592741,
  "sub": "97e0a5b7-d745-40b6-94fe-5f77d35c6e05"
}
.
"Gh95kHCOEGq5E_ArMBbDXhwKR577scxYaoJ1P{a lot of characters here}KKJDEg"
```

## <a name="example-of-an-encoded-jwt-assertion"></a>Пример закодированного утверждения JWT

Ниже приводится строка, которая является примером закодированного утверждения. Если внимательно ее изучить, то можно заметить, что она состоит из трех разделов, разделенных точками (.).
* В первом разделе закодирован заголовок.
* Во втором разделе закодированы полезные данные.
* Последний раздел является подписью, вычисленной с помощью сертификатов из содержимого первых двух разделов.

```
"eyJhbGciOiJSUzI1NiIsIng1dCI6Imd4OHRHeXN5amNScUtqRlBuZDdSRnd2d1pJMCJ9.eyJhdWQiOiJodHRwczpcL1wvbG9naW4ubWljcm9zb2Z0b25saW5lLmNvbVwvam1wcmlldXJob3RtYWlsLm9ubWljcm9zb2Z0LmNvbVwvb2F1dGgyXC90b2tlbiIsImV4cCI6MTQ4NDU5MzM0MSwiaXNzIjoiOTdlMGE1YjctZDc0NS00MGI2LTk0ZmUtNWY3N2QzNWM2ZTA1IiwianRpIjoiMjJiM2JiMjYtZTA0Ni00MmRmLTljOTYtNjVkYmQ3MmMxYzgxIiwibmJmIjoxNDg0NTkyNzQxLCJzdWIiOiI5N2UwYTViNy1kNzQ1LTQwYjYtOTRmZS01Zjc3ZDM1YzZlMDUifQ.
Gh95kHCOEGq5E_ArMBbDXhwKR577scxYaoJ1P{a lot of characters here}KKJDEg"
```

## <a name="register-your-certificate-with-microsoft-identity-platform"></a>Регистрация сертификата на платформе идентификации Майкрософт

Учетные данные сертификата можно связать с клиентским приложением в идентификационной платформе Майкрософт через портал Azure, используя любой из следующих методов:

### <a name="uploading-the-certificate-file"></a>Передача файла сертификата

При регистрации приложения Azure для клиентского приложения сделайте следующее.
1. Выберите **Сертификаты и секреты**.
2. Нажмите на **сертификат Загрузки** и выберите файл сертификата для загрузки.
3. Нажмите кнопку **Добавить**.
  После загрузки сертификата отображаются отпечаток пальца, дата начала и значения истечения срока действия.

### <a name="updating-the-application-manifest"></a>Обновление манифеста приложения

Получив сертификат, необходимо вычислить следующие значения:

- `$base64Thumbprint` — хэш сертификата в кодировке base64;
- `$base64Value` — необработанные данные сертификата в кодировке base64.

Также необходимо предоставить идентификатор GUID для определения ключа в манифесте приложения (`$keyId`).

При регистрации приложения Azure для клиентского приложения сделайте следующее.
1. Выберите **манифест,** чтобы открыть манифест приложения.
2. Замените свойство *keyCredentials* данными нового сертификата, используя приведенную ниже схему.

   ```JSON
   "keyCredentials": [
       {
           "customKeyIdentifier": "$base64Thumbprint",
           "keyId": "$keyid",
           "type": "AsymmetricX509Cert",
           "usage": "Verify",
           "value":  "$base64Value"
       }
   ]
   ```
3. Сохраните свои права в манифесте приложения, а затем загрузите манифест на идентификационную платформу Майкрософт.

   Свойство `keyCredentials` является многозначным, поэтому для расширенного управления ключами можно передать несколько сертификатов.

## <a name="code-sample"></a>Пример кода

> [!NOTE]
> Вы можете вычислить заголовок X5T, преобразовав его в строку базы 64 с помощью хэша сертификата. Код для выполнения этого в `System.Convert.ToBase64String(cert.GetCertHash());`C q .

Образец кода [консольного приложения .NET Core daemon с помощью платформы идентификации Microsoft](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2) показывает, как приложение использует свои собственные учетные данные для проверки подлинности. В не также показано, как [создать самозаверяющий сертификат](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2/tree/master/1-Call-MSGraph#optional-use-the-automation-script) с помощью команды PowerShell `New-SelfSignedCertificate`. Можно также воспользоваться преимуществами [сценариев создания приложений](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2/blob/master/1-Call-MSGraph/AppCreationScripts-withCert/AppCreationScripts.md) для создания сертификатов, вычисления отпечатков и т. д.
