---
title: Установка b2clogin.com в качестве URL-адреса перенаправления для Azure Active Directory B2C | Документация Майкрософт
description: Сведения об использовании b2clogin.com в качестве URL-адреса перенаправления для Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 11/30/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 147d8e80c66bd47ed56400deca024532fc7e39c7
ms.sourcegitcommit: 8115c7fa126ce9bf3e16415f275680f4486192c1
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/24/2019
ms.locfileid: "54844932"
---
# <a name="set-redirect-urls-to-b2clogincom-for-azure-active-directory-b2c"></a>Установка b2clogin.com в качестве URL-адреса перенаправления для Azure Active Directory B2C

В процессе настройки поставщика удостоверений для регистрации и входа в приложение Azure Active Directory (Azure AD) B2C, вам потребуется указать URL-адрес переадресации. В прошлом использовался login.microsoftonline.com, а сейчас вместо него следует использовать b2clogin.com.

Использование b2clogin.com дает следующие преимущества:

- Файлы cookie больше не используются совместно с другими службами Майкрософт.
- URL-адреса больше не содержат ссылку на корпорацию Майкрософт. Например, `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp`.

Рассмотрите следующие параметры, которые может понадобиться изменить при использовании b2clogin.com:

- Установите URL-адреса перенаправления в приложениях поставщика удостоверений для использования b2clogin.com. 
- Настройте приложении Azure AD B2C для использования b2clogin.com в качестве ссылок на потоки пользователя и конечных точек для получения маркеров. 
- При использовании MSAL, значение свойства **ValidateAuthority** следует установить на `false`.
- Не забудьте изменить все **разрешенные источники**, определенные вами в параметрах CORS, для [настройки пользовательского интерфейса](active-directory-b2c-ui-customization-custom-dynamic.md).  

## <a name="change-redirect-urls"></a>Изменение URL-адреса перенаправления

Чтобы использовать b2clogin.com в настройках приложения поставщика удостоверений, найдите и измените список доверенных URL-адресов, предназначенных для перенаправления в Azure AD B2C.  В настоящее время вы, вероятно уже настроили перенаправление на веб-сайт login.microsoftonline.com. 

URL-адрес перенаправления следует изменить таким образом, чтобы `your-tenant-name.b2clogin.com` стал авторизованным. Убедитесь, что вы заменили `your-tenant-name` на имя клиента Azure AD B2C и удалили `/te` (если он находился в URL-адресе). Для каждого поставщика удостоверений существует несколько небольших вариаций данного URL-адреса, поэтому для получения точного URL-адреса следует проверить соответствующую страницу.

Информацию для настройки поставщиков удостоверений можно найти в следующих статьях.

- [Учетная запись Майкрософт](active-directory-b2c-setup-msa-app.md)
- [Facebook](active-directory-b2c-setup-fb-app.md)
- [Google](active-directory-b2c-setup-goog-app.md)
- [Amazon](active-directory-b2c-setup-amzn-app.md)
- [LinkedIn](active-directory-b2c-setup-li-app.md)
- [Twitter](active-directory-b2c-setup-twitter-app.md)
- [GitHub](active-directory-b2c-setup-github-app.md)
- [Weibo](active-directory-b2c-setup-weibo-app.md)
- [QQ](active-directory-b2c-setup-qq-app.md)
- [WeChat](active-directory-b2c-setup-wechat-app.md)
- [Azure AD](active-directory-b2c-setup-oidc-azure-active-directory.md)
- [Настройка регистрации и входа с помощью OpenID Connect через Azure Active Directory B2C](active-directory-b2c-setup-oidc-idp.md)

## <a name="update-your-application"></a>Обновление приложения

Скорее всего приложение Azure AD B2C ссылается на `login.microsoftonline.com` в нескольких местах, например, в ссылках на потоки пользователя и маркерах утверждений.  Убедитесь, что конечная точка авторизации, конечная точка маркера и издателя были обновлены для использования `your-tenant-name.b2clogin.com`.  

## <a name="set-the-validateauthority-property"></a>Установка свойства ValidateAuthority

При использовании MSAL, значение свойства **ValidateAuthority** следует установить на `false`. Далее приведен пример установки свойства.

В [MSAL для .NET](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet):

```CSharp
 ConfidentialClientApplication client = new ConfidentialClientApplication(...); // can also be PublicClientApplication
 client.ValidateAuthority = false;
```

В [MSAL для JavaScript](https://github.com/AzureAD/microsoft-authentication-library-for-js):

```Javascript
this.clientApplication = new UserAgentApplication(
  env.auth.clientId,
  env.auth.loginAuthority,
  this.authCallback.bind(this),
  {
    validateAuthority: false
  }
);
```
