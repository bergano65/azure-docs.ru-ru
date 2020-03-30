---
title: Мигрировать приложения и AA в b2clogin.com
titleSuffix: Azure AD B2C
description: Сведения об использовании b2clogin.com в качестве URL-адреса перенаправления для Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 12/04/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 64b440054795670b99a22e37dec7188f3e1cd74c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78189996"
---
# <a name="set-redirect-urls-to-b2clogincom-for-azure-active-directory-b2c"></a>Установка b2clogin.com в качестве URL-адреса перенаправления для Azure Active Directory B2C

При настройке поставщика идентификационных данных для регистрации и вхлечения в приложение Azure Active Directory B2C (Azure AD B2C) необходимо указать URL-адрес перенаправления. Вы больше не должны ссылаться *на login.microsoftonline.com* в приложениях и AIS. Вместо этого используйте *b2clogin.com* для всех новых приложений и перенося существующие приложения из *login.microsoftonline.com* в *b2clogin.com.*

## <a name="deprecation-of-loginmicrosoftonlinecom"></a>Износ login.microsoftonline.com

04 декабря 2019 года мы объявили о запланированном выходе на пенсию login.microsoftonline.com поддержки в Azure AD **B2C на 04 декабря 2020 года**:

[Активный каталог Azure B2C унижает login.microsoftonline.com](https://azure.microsoft.com/updates/b2c-deprecate-msol/)

Амортизация login.microsoftonline.com вступает в силу для всех арендаторов Azure AD B2C 04 декабря 2020 года, обеспечивая существующим арендаторам один (1) год для перехода в b2clogin.com. Новые арендаторы, созданные после 04 декабря 2019 года, не будут принимать запросы от login.microsoftonline.com. Вся функциональность остается прежней на b2clogin.com конечную точку.

Амортизацию login.microsoftonline.com не влияет на арендаторов Azure Active Directory. Это изменение затрагивает только наиболее активных клиентов Каталог b2C.

## <a name="benefits-of-b2clogincom"></a>Преимущества b2clogin.com

При использовании *b2clogin.com* в качестве URL-адреса перенаправления:

* Пространство, используемое службами Майкрософт в заголовке файлов cookie, уменьшается.
* Ваши ренапрямые URL-адреса больше не должны включать ссылку на корпорацию Майкрософт.
* Код клиента JavaScript поддерживается (в настоящее время в [предварительном просмотре)](user-flow-javascript-overview.md)на настраиваемых страницах. Из-за ограничений безопасности, код JavaScript и HTML-элементы формы удаляются с пользовательских страниц, если вы используете *login.microsoftonline.com.*

## <a name="overview-of-required-changes"></a>Обзор необходимых изменений

Возможно, потребуется внести несколько изменений для переноса приложений в *b2clogin.com:*

* Измените URL-адрес перенаправления в приложениях поставщика идентификационных данных для ссылки *b2clogin.com.*
* Обновите приложения Azure AD B2C, чтобы использовать *b2clogin.com* в их пользовательском потоке и ссылках на конечные знаки токенов.
* Обновление любых **разрешенных истоков,** которые вы определили в настройках CORS для [настройки пользовательского интерфейса.](custom-policy-ui-customization.md)

## <a name="change-identity-provider-redirect-urls"></a>Поставщик изменений в области идентификации перенаправывает URL-адреса

На веб-сайте каждого поставщика идентификационных данных, на котором вы создали `your-tenant-name.b2clogin.com` приложение, измените все доверенные URL-адреса, чтобы перенаправить на приложения вместо *login.microsoftonline.com.*

Есть два формата, которые можно использовать для b2clogin.com перенаправлять URL-адреса. Первый предоставляет преимущество, если не отображаться "Microsoft" в URL-адресе с помощью идентификатора арендатора (GUID) вместо доменного имени арендатора:

```
https://{your-tenant-name}.b2clogin.com/{your-tenant-id}/oauth2/authresp
```

Второй вариант использует ваше доменное имя `your-tenant-name.onmicrosoft.com`арендатора в виде . Пример:

```
https://{your-tenant-name}.b2clogin.com/{your-tenant-name}.onmicrosoft.com/oauth2/authresp
```

Для обоих форматов:

* Замените `{your-tenant-name}` именем вашего клиента Azure AD B2C.
* Удалить, `/te` если он существует в URL.

## <a name="update-your-applications-and-apis"></a>Обновление приложений и AA

Код в приложениях с поддержкой Azure AD B2C и AA могут относиться в `login.microsoftonline.com` нескольких местах. Например, ваш код может иметь ссылки на потоки пользователей и конечные точки токенов. Обновление следующих вместо `your-tenant-name.b2clogin.com`ссылки :

* Конечная точка авторизации
* Конечная точка маркера
* Эмитент токенов

Например, конечная точка полномочий для политики регистрации/регистрации Contoso теперь будет:

```
https://contosob2c.b2clogin.com/00000000-0000-0000-0000-000000000000/B2C_1_signupsignin1
```

Для получения информации о миграции веб-приложений на основе OWIN в b2clogin.com см [b2clogin.com.](multiple-token-endpoints.md)

Для миграции API-иноуправления API, защищенных Azure AD B2C, см. раздел [«Мигрировать в b2clogin.com](secure-api-management.md#migrate-to-b2clogincom) разделе [Secure aPI-наик управления Azure с Azure AD B2C».](secure-api-management.md)

## <a name="microsoft-authentication-library-msal"></a>Библиотека проверки подлинности Майкрософт (MSAL)

### <a name="validateauthority-property"></a>Свойство ValidateAuthority

Если вы используете [MSAL.NET][msal-dotnet] v2 или раньше, `false` установите свойство **ValidateAuthority** на мгновенное клиента, чтобы позволить перенаправления *b2clogin.com.* Эта настройка не требуется для MSAL.NET v3 и выше.

```csharp
ConfidentialClientApplication client = new ConfidentialClientApplication(...); // Can also be PublicClientApplication
client.ValidateAuthority = false; // MSAL.NET v2 and earlier **ONLY**
```

Если вы используете [MSAL для JavaScript:][msal-js]

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

## <a name="next-steps"></a>Дальнейшие действия

Для получения информации о миграции веб-приложений на основе OWIN в b2clogin.com см [b2clogin.com.](multiple-token-endpoints.md)

Для миграции API-иноуправления API, защищенных Azure AD B2C, см. раздел [«Мигрировать в b2clogin.com](secure-api-management.md#migrate-to-b2clogincom) разделе [Secure aPI-наик управления Azure с Azure AD B2C».](secure-api-management.md)

<!-- LINKS - External -->
[msal-dotnet]: https://github.com/AzureAD/microsoft-authentication-library-for-dotnet
[msal-dotnet-b2c]: https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/AAD-B2C-specifics
[msal-js]: https://github.com/AzureAD/microsoft-authentication-library-for-js
[msal-js-b2c]: ../active-directory/develop/msal-b2c-overview.md
