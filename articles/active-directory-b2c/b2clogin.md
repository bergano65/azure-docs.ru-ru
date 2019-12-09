---
title: Перенос приложений и API в b2clogin.com — Azure AD B2C
description: Сведения об использовании b2clogin.com в качестве URL-адреса перенаправления для Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 12/04/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 426a8cedb365a3abdcef3e1eac5a64498099feb0
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/08/2019
ms.locfileid: "74913386"
---
# <a name="set-redirect-urls-to-b2clogincom-for-azure-active-directory-b2c"></a>Установка b2clogin.com в качестве URL-адреса перенаправления для Azure Active Directory B2C

При настройке поставщика удостоверений для регистрации и входа в приложение Azure Active Directory B2C (Azure AD B2C) необходимо указать URL-адрес перенаправления. Больше не следует ссылаться на *Login.microsoftonline.com* в приложениях и API. Вместо этого используйте *b2clogin.com* для всех новых приложений и перенесите существующие приложения из *Login.microsoftonline.com* в *b2clogin.com*.

## <a name="deprecation-of-loginmicrosoftonlinecom"></a>Нерекомендуемость login.microsoftonline.com

На 04 декабря 2019 мы объявили о запланированном прекращении поддержки login.microsoftonline.com в Azure AD B2C **4 декабря 2020**:

[Azure Active Directory B2C не является устаревшим login.microsoftonline.com](https://azure.microsoft.com/updates/b2c-deprecate-msol/)

Прекращение использования login.microsoftonline.com действует для всех клиентов Azure AD B2C на 04 декабря 2020, предоставляя существующие клиенты в один (1) год для миграции на b2clogin.com. Новые клиенты, созданные после 04 декабря 2019, не будут принимать запросы от login.microsoftonline.com. Все функциональные возможности остаются неизменными в конечной точке b2clogin.com.

Нерекомендуемое значение login.microsoftonline.com не влияет на Azure Active Directory клиентов. Это изменение затрагивает только Azure Active Directory B2C клиентов.

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

На каждом веб-сайте поставщика удостоверений, в котором вы создали приложение, измените все надежные URL-адреса, чтобы они перенаправлялись на `your-tenant-name.b2clogin.com` вместо *Login.microsoftonline.com*.

Для URL-адресов перенаправления b2clogin.com можно использовать два формата. Первый предоставляет преимущества отсутствия "Майкрософт" в URL-адресе, используя идентификатор клиента (GUID) вместо имени домена клиента:

```
https://{your-tenant-name}.b2clogin.com/{your-tenant-id}/oauth2/authresp
```

Второй вариант использует доменное имя клиента в виде `your-tenant-name.onmicrosoft.com`. Пример.

```
https://{your-tenant-name}.b2clogin.com/{your-tenant-name}.onmicrosoft.com/oauth2/authresp
```

Для обоих форматов:

* Замените `{your-tenant-name}` именем вашего клиента Azure AD B2C.
* Удалите `/te`, если он существует в URL-адресе.

## <a name="update-your-applications-and-apis"></a>Обновление приложений и API-интерфейсов

Код в приложениях с поддержкой Azure AD B2C и API-интерфейсах может ссылаться на `login.microsoftonline.com` в нескольких местах. Например, код может иметь ссылки на потоки пользователей и конечные точки маркеров. Вместо этого обновите следующие ссылки на `your-tenant-name.b2clogin.com`:

* Конечная точка авторизации
* Конечная точка маркера
* Поставщик токена

Например, конечная точка центра для политики регистрации и входа в Contoso теперь будет выглядеть так:

```
https://contosob2c.b2clogin.com/00000000-0000-0000-0000-000000000000/B2C_1_signupsignin1
```

Сведения о миграции веб-приложений на основе OWIN в b2clogin.com см. в статье [Миграция веб-API на основе OWIN в b2clogin.com](multiple-token-endpoints.md).

Сведения о переносе API-интерфейсов службы управления API Azure, защищенных Azure AD B2C, см. в разделе " [Миграция в b2clogin.com](secure-api-management.md#migrate-to-b2clogincom) " статьи [Защита API управления API azure с помощью Azure AD B2C](secure-api-management.md).

## <a name="microsoft-authentication-library-msal"></a>Библиотека проверки подлинности Майкрософт (MSAL)

### <a name="validateauthority-property"></a>Валидатеаусорити, свойство

Если вы используете [MSAL.NET][msal-dotnet] v2 или более раннюю версию, задайте для свойства **валидатеаусорити** значение `false` при создании экземпляра клиента, чтобы разрешить перенаправление на *b2clogin.com*. Этот параметр не является обязательным для MSAL.NET v3 и более поздних версий.

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

## <a name="next-steps"></a>Дальнейшие действия

Сведения о миграции веб-приложений на основе OWIN в b2clogin.com см. в статье [Миграция веб-API на основе OWIN в b2clogin.com](multiple-token-endpoints.md).

Сведения о переносе API-интерфейсов службы управления API Azure, защищенных Azure AD B2C, см. в разделе " [Миграция в b2clogin.com](secure-api-management.md#migrate-to-b2clogincom) " статьи [Защита API управления API azure с помощью Azure AD B2C](secure-api-management.md).

<!-- LINKS - External -->
[msal-dotnet]: https://github.com/AzureAD/microsoft-authentication-library-for-dotnet
[msal-dotnet-b2c]: https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/AAD-B2C-specifics
[msal-js]: https://github.com/AzureAD/microsoft-authentication-library-for-js
[msal-js-b2c]: ../active-directory/develop/msal-b2c-overview.md