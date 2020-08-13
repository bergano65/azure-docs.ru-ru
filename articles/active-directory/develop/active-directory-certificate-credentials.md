---
title: Учетные данные сертификата платформы удостоверений Майкрософт
titleSuffix: Microsoft identity platform
description: В этой статье рассматривается регистрация и использование учетных данных сертификата для проверки подлинности приложения.
services: active-directory
author: hpsin
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 08/12/2020
ms.author: hirsin
ms.reviewer: nacanuma, jmprieur
ms.custom: aaddev
ms.openlocfilehash: 06f15257148342879a164005a8f4fb302c539e67
ms.sourcegitcommit: c28fc1ec7d90f7e8b2e8775f5a250dd14a1622a6
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/13/2020
ms.locfileid: "88163668"
---
# <a name="microsoft-identity-platform-application-authentication-certificate-credentials"></a>Учетные данные сертификата проверки подлинности приложения платформы удостоверений Майкрософт

Платформа Microsoft Identity позволяет приложению использовать собственные учетные данные для проверки подлинности, например, в потоке [предоставления учетных данных клиента](v2-oauth2-client-creds-grant-flow.md) OAuth 2,0 и потоке "от [имени](v2-oauth2-on-behalf-of-flow.md) " (OBO).

Одним из форм учетных данных, которые приложение может использовать для проверки подлинности, является утверждение [JSON Web Token](./security-tokens.md#json-web-tokens-jwts-and-claims) (JWT), подписанное с помощью сертификата, которому принадлежит приложение.

## <a name="assertion-format"></a>Формат утверждения

Чтобы вычислить утверждение, можно использовать одну из многих библиотек JWT на выбранном языке. Эта информация переносится маркером в [заголовке](#header), [утверждениях](#claims-payload)и [сигнатуре](#signature).

### <a name="header"></a>Заголовок

| Параметр |  Комментарий |
| --- | --- |
| `alg` | Должен иметь значение **RS256** |
| `typ` | Должен иметь значение **JWT** |
| `x5t` | Хэш сертификата X. 509 (также известный как *отпечаток*SHA-1 сертификата) кодируется как строковое значение Base64. Например, если получен хэш сертификата X. 509 `84E05C1D98BCE3A5421D225B140B36E86A3D5534` , `x5t` утверждение будет иметь значение `hOBcHZi846VCHSJbFAs26Go9VTQ` . |

### <a name="claims-payload"></a>Утверждения (полезные данные)

| Параметр |  Remarks |
| --- | --- |
| `aud` | Аудитория: должна быть`https://login.microsoftonline.com/<your-tenant-id>/oauth2/token` |
| `exp` | Дата окончания срока действия. Дата истечения срока действия маркера. Время представлено как количество секунд с 1 января 1970 года (1970-01-01T0:0:0Z) в формате UTC до истечения срока действия маркера. Рекомендуется использовать короткий срок действия-10 минут в течение одного часа.|
| `iss` | Issuer: должен быть client_id (*идентификатор приложения (клиента)* службы клиента) |
| `jti` | GUID: идентификатор JWT. |
| `nbf` | Не ранее: Дата, до которой маркер не может быть использован. Время представляется в виде числа секунд с 1 января 1970 (1970-01-01T0:0: 0Z) в формате UTC до момента создания утверждения. |
| `sub` | Subject: как для `iss` , должен быть client_id (*идентификатор приложения (клиента)* службы клиента) |

### <a name="signature"></a>Подпись

Подпись вычисляются путем применения сертификата, как описано в [спецификации JSON Web Token RFC7519](https://tools.ietf.org/html/rfc7519).

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

Ниже приводится строка, которая является примером закодированного утверждения. При внимательном рассмотрении Обратите внимание на три раздела, разделенные точками ( `.` ):

* Первый раздел кодирует *заголовок*
* Второй раздел кодирует *утверждения* (полезные данные)
* Последний раздел — это *подпись* , вычисленная с использованием сертификатов из содержимого первых двух разделов.

```
"eyJhbGciOiJSUzI1NiIsIng1dCI6Imd4OHRHeXN5amNScUtqRlBuZDdSRnd2d1pJMCJ9.eyJhdWQiOiJodHRwczpcL1wvbG9naW4ubWljcm9zb2Z0b25saW5lLmNvbVwvam1wcmlldXJob3RtYWlsLm9ubWljcm9zb2Z0LmNvbVwvb2F1dGgyXC90b2tlbiIsImV4cCI6MTQ4NDU5MzM0MSwiaXNzIjoiOTdlMGE1YjctZDc0NS00MGI2LTk0ZmUtNWY3N2QzNWM2ZTA1IiwianRpIjoiMjJiM2JiMjYtZTA0Ni00MmRmLTljOTYtNjVkYmQ3MmMxYzgxIiwibmJmIjoxNDg0NTkyNzQxLCJzdWIiOiI5N2UwYTViNy1kNzQ1LTQwYjYtOTRmZS01Zjc3ZDM1YzZlMDUifQ.
Gh95kHCOEGq5E_ArMBbDXhwKR577scxYaoJ1P{a lot of characters here}KKJDEg"
```

## <a name="register-your-certificate-with-microsoft-identity-platform"></a>Зарегистрируйте сертификат на платформе удостоверений Майкрософт

Учетные данные сертификата можно связать с клиентским приложением на платформе удостоверений Майкрософт через портал Azure с помощью любого из следующих методов:

### <a name="uploading-the-certificate-file"></a>Передача файла сертификата

При регистрации приложения Azure для клиентского приложения сделайте следующее.
1. Выберите **Сертификаты и секреты**.
2. Нажмите **Отправить сертификат** и выберите файл сертификата для отправки.
3. Нажмите кнопку **Добавить**.
  После передачи сертификата отображаются значения отпечатка сертификата, даты начала и истечения срока действия.

### <a name="updating-the-application-manifest"></a>Обновление манифеста приложения

Получив сертификат, необходимо вычислить следующие значения:

- `$base64Thumbprint`— Значение хэша сертификата в кодировке Base64
- `$base64Value`— Значение необработанных данных сертификата в кодировке Base64

Также необходимо предоставить идентификатор GUID для определения ключа в манифесте приложения (`$keyId`).

При регистрации приложения Azure для клиентского приложения сделайте следующее.
1. Щелкните **Манифест**, чтобы открыть манифест приложения.
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
3. Сохраните изменения в манифесте приложения и передайте его на платформу удостоверений Майкрософт.

   Свойство `keyCredentials` является многозначным, поэтому для расширенного управления ключами можно передать несколько сертификатов.

## <a name="next-steps"></a>Дальнейшие действия

[Консольное приложение управляющей программы .NET Core, использующее пример кода платформы идентификаторов Microsoft Identity](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2) на GitHub, показывает, как приложение использует собственные учетные данные для проверки подлинности. Здесь также показано, как [создать самозаверяющий сертификат](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2/tree/master/1-Call-MSGraph#optional-use-the-automation-script) с помощью `New-SelfSignedCertificate` командлета PowerShell. Вы также можете использовать [скрипты создания приложений](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2/blob/master/1-Call-MSGraph/AppCreationScripts-withCert/AppCreationScripts.md) в примере репозитория для создания сертификатов, вычислить отпечаток и т. д.
