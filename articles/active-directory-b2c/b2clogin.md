---
title: Задайте для URL-адресов перенаправления значение b2clogin.com — Azure Active Directory B2C
description: Сведения об использовании b2clogin.com в качестве URL-адреса перенаправления для Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/17/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: dbc366daac89f44d4b084081590124f81ff9cc9c
ms.sourcegitcommit: 040abc24f031ac9d4d44dbdd832e5d99b34a8c61
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/16/2019
ms.locfileid: "69533744"
---
# <a name="set-redirect-urls-to-b2clogincom-for-azure-active-directory-b2c"></a>Установка b2clogin.com в качестве URL-адреса перенаправления для Azure Active Directory B2C

При настройке поставщика удостоверений для регистрации и входа в приложение Azure Active Directory B2C (Azure AD B2C) необходимо указать URL-адрес перенаправления. Больше не следует ссылаться на *Login.microsoftonline.com* в приложениях и API. Вместо этого используйте *b2clogin.com* для всех новых приложений и перенесите существующие приложения из *Login.microsoftonline.com* в *b2clogin.com*.

## <a name="benefits-of-b2clogincom"></a>Преимущества b2clogin.com

При использовании *b2clogin.com* в качестве URL-адреса перенаправления:

* Пространство, используемое службами Майкрософт в заголовке файлов cookie, уменьшается.
* URL-адреса перенаправления больше не должны включать ссылку на Майкрософт.
* Код на стороне клиента JavaScript поддерживается (в настоящее время находится на [этапе предварительной версии](user-flow-javascript-overview.md)) на настраиваемых страницах. Из-за ограничений безопасности код JavaScript и HTML-элементы формы удаляются из пользовательских страниц, если используется *Login.microsoftonline.com*.

## <a name="overview-of-required-changes"></a>Общие сведения о необходимых изменениях

Существует несколько изменений, которые может потребоваться выполнить для переноса приложений в *b2clogin.com*:

* Измените URL-адрес перенаправления в приложениях поставщика удостоверений, чтобы он ссылался на *b2clogin.com*.
* Обновите приложения Azure AD B2C, чтобы использовать *b2clogin.com* в своих потоках пользователей и ссылках на конечные точки маркеров.
* Обновите все **Разрешенные источники** , определенные в параметрах CORS для [настройки пользовательского интерфейса](active-directory-b2c-ui-customization-custom-dynamic.md).

## <a name="change-identity-provider-redirect-urls"></a>Изменение URL-адресов перенаправления поставщика удостоверений

На каждом веб-сайте поставщика удостоверений, в котором вы создали приложение, измените все доверенные URL- `your-tenant-name.b2clogin.com` адреса, чтобы они перенаправлялись на *Login.microsoftonline.com*.

Для URL-адресов перенаправления b2clogin.com можно использовать два формата. Первый предоставляет преимущества отсутствия "Майкрософт" в URL-адресе, используя идентификатор клиента (GUID) вместо имени домена клиента:

```
https://{your-tenant-name}.b2clogin.com/{your-tenant-id}/oauth2/authresp
```

Во втором варианте используется доменное имя клиента в формате `your-tenant-name.onmicrosoft.com`. Пример:

```
https://{your-tenant-name}.b2clogin.com/{your-tenant-name}.onmicrosoft.com/oauth2/authresp
```

Для обоих форматов:

* Замените `{your-tenant-name}` именем вашего клиента Azure AD B2C.
* Удалите `/te` , если он существует в URL-адресе.

## <a name="update-your-applications-and-apis"></a>Обновление приложений и API-интерфейсов

Код в приложениях с поддержкой Azure AD B2C и API-интерфейсах может ссылаться `login.microsoftonline.com` в нескольких местах. Например, код может иметь ссылки на потоки пользователей и конечные точки маркеров. Вместо этого обновите следующие ссылки `your-tenant-name.b2clogin.com`:

* Конечная точка авторизации
* Конечная точка токена
* Издатель токенов

Например, конечная точка центра для политики регистрации и входа в Contoso теперь будет выглядеть так:

```
https://contosob2c.b2clogin.com/00000000-0000-0000-0000-000000000000/B2C_1_signupsignin1
```

## <a name="microsoft-authentication-library-msal"></a>Библиотека проверки подлинности Майкрософт (MSAL)

### <a name="validateauthority-property"></a>Валидатеаусорити, свойство

Если вы используете [MSAL.NET][msal-dotnet] v2 или более раннюю версию, установите свойство `false` валидатеаусорити в значение On Client, чтобы разрешить перенаправление в *b2clogin.com*. Этот параметр не является обязательным для MSAL.NET v3 и более поздних версий.

```CSharp
ConfidentialClientApplication client = new ConfidentialClientApplication(...); // Can also be PublicClientApplication
client.ValidateAuthority = false; // MSAL.NET v2 and earlier **ONLY**
```

Если вы используете [MSAL для JavaScript][msal-js]:

```JavaScript
this.clientApplication = new UserAgentApplication(
  env.auth.clientId,
  env.auth.loginAuthority,
  this.authCallback.bind(this),
  {
    validateAuthority: false
  }
);
```

<!-- LINKS - External -->
[msal-dotnet]: https://github.com/AzureAD/microsoft-authentication-library-for-dotnet
[msal-dotnet-b2c]: https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/AAD-B2C-specifics
[msal-js]: https://github.com/AzureAD/microsoft-authentication-library-for-js
[msal-js-b2c]: ../active-directory/develop/msal-b2c-overview.md