---
title: Учетные данные сертификата платформы удостоверений Microsoft Identity
titleSuffix: Microsoft identity platform
description: В этой статье обсуждается регистрация и использование учетных данных сертификата для проверки подлинности приложения.
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
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7a44d89e19a1efc54e2c3c49053ec9badc91ba97
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/25/2019
ms.locfileid: "75424718"
---
# <a name="microsoft-identity-platform-application-authentication-certificate-credentials"></a>Учетные данные сертификата проверки подлинности приложения платформы Microsoft Identity

Платформа Microsoft Identity позволяет приложению использовать собственные учетные данные для проверки подлинности, например, в [учетных данных клиента OAuth 2,0 предоставьте фловв 2.0](v2-oauth2-client-creds-grant-flow.md) и поток "от [имени](v2-oauth2-on-behalf-of-flow.md)".

Одной из форм учетных данных, которые приложение может использовать для аутентификации, является утверждение JSON Web Token (JWT), подписанное с помощью сертификата приложения.

## <a name="assertion-format"></a>Формат утверждения
Платформа Microsoft Identity. чтобы вычислить утверждение, можно использовать одну из многих [JSON Web Token](https://jwt.ms/) библиотек на выбранном языке. Маркер содержит следующие сведения:

### <a name="header"></a>Заголовок

| Параметр |  Комментарий |
| --- | --- |
| `alg` | Должен иметь значение **RS256** |
| `typ` | Должен иметь значение **JWT** |
| `x5t` | Это должен быть отпечаток SHA-1 сертификата X.509 |

### <a name="claims-payload"></a>Утверждения (полезные данные)

| Параметр |  Remarks |
| --- | --- |
| `aud` | Аудитория: должно быть значение **https://login.microsoftonline.com/*tenant_Id*/oauth2/token** |
| `exp` | Срок действия: дата, когда истекает срок действия маркера. Время представлено как количество секунд с 1 января 1970 года (1970-01-01T0:0:0Z) в формате UTC до истечения срока действия маркера.|
| `iss` | Издатель: параметр должен иметь значение client_id (идентификатор приложения службы клиента) |
| `jti` | GUID: идентификатор JWT |
| `nbf` | Не ранее: дата, до которой маркер не может использоваться. Время представлено как количество секунд с 1 января 1970 года (1970-01-01T0:0:0Z) в формате UTC до времени выдачи маркера. |
| `sub` | Субъект: параметр должен иметь значение client_id (идентификатор приложения службы клиента), как и `iss` |

### <a name="signature"></a>Сигнатура

Подпись формируется через применение сертификата, как описано в [документе RFC7519 о спецификации JSON Web Token](https://tools.ietf.org/html/rfc7519).

## <a name="example-of-a-decoded-jwt-assertion"></a>Пример декодированного утверждения JWT

```
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

## <a name="register-your-certificate-with-microsoft-identity-platform"></a>Регистрация сертификата на платформе Microsoft Identity

Учетные данные сертификата можно связать с клиентским приложением на платформе Microsoft Identity с помощью портал Azure одним из следующих способов.

### <a name="uploading-the-certificate-file"></a>Передача файла сертификата

При регистрации приложения Azure для клиентского приложения сделайте следующее.
1. Выберите **Сертификаты и секреты**. 
2. Щелкните **отправить сертификат** и выберите файл сертификата для отправки.
3. Нажмите кнопку **Добавить**.
  После отправки сертификата отображаются отпечаток, Дата начала и срок действия. 

### <a name="updating-the-application-manifest"></a>Обновление манифеста приложения

Получив сертификат, необходимо вычислить следующие значения:

- `$base64Thumbprint` — хэш сертификата в кодировке base64;
- `$base64Value` — необработанные данные сертификата в кодировке base64.

Также необходимо предоставить идентификатор GUID для определения ключа в манифесте приложения (`$keyId`).

При регистрации приложения Azure для клиентского приложения сделайте следующее.
1. Выберите **Манифест** , чтобы открыть манифест приложения.
2. Замените свойство *keyCredentials* данными нового сертификата, используя приведенную ниже схему.

   ```
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
3. Сохраните изменения в манифесте приложения, а затем отправьте манифест на платформу удостоверений Майкрософт. 

   Свойство `keyCredentials` является многозначным, поэтому для расширенного управления ключами можно передать несколько сертификатов.
   
## <a name="code-sample"></a>Пример кода

> [!NOTE]
> Необходимо вычислить заголовок X5T, используя хэш сертификата, и преобразовать его в строку Base64. В C# этом случае будет выглядеть примерно так: `System.Convert.ToBase64String(cert.GetCertHash());`

Пример кода для проверки подлинности [на платформе Microsoft Identity в приложениях управляющей программы с сертификатами](https://github.com/Azure-Samples/active-directory-dotnet-daemon-certificate-credential) показывает, как приложение использует собственные учетные данные для аутентификации. В не также показано, как [создать самозаверяющий сертификат](https://github.com/Azure-Samples/active-directory-dotnet-daemon-certificate-credential#create-a-self-signed-certificate) с помощью команды PowerShell `New-SelfSignedCertificate`. Можно также воспользоваться преимуществами [сценариев создания приложений](https://github.com/Azure-Samples/active-directory-dotnet-daemon-certificate-credential/blob/master/AppCreationScripts/AppCreationScripts.md) для создания сертификатов, вычисления отпечатков и т. д.
