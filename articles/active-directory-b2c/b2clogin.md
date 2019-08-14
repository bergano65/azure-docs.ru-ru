---
title: Установка b2clogin.com в качестве URL-адреса перенаправления — Azure Active Directory B2C | Документация Майкрософт
description: Сведения об использовании b2clogin.com в качестве URL-адреса перенаправления для Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 01/28/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 080c1933f88d9e824969a42212de2eacd0f62e14
ms.sourcegitcommit: 13a289ba57cfae728831e6d38b7f82dae165e59d
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/09/2019
ms.locfileid: "68927281"
---
# <a name="set-redirect-urls-to-b2clogincom-for-azure-active-directory-b2c"></a>Установка b2clogin.com в качестве URL-адреса перенаправления для Azure Active Directory B2C

В процессе настройки поставщика удостоверений для регистрации и входа в приложение Azure Active Directory (Azure AD) B2C, вам потребуется указать URL-адрес переадресации. В прошлом использовался login.microsoftonline.com, а сейчас вместо него следует использовать b2clogin.com.

> [!NOTE]
> Вы можете использовать код на стороне клиента JavaScript (в настоящее время находится на этапе предварительной версии) в b2clogin.com. При использовании login.microsoftonline.com код JavaScript будет удален из настраиваемой страницы. К login.microsoftonline.com также применяются дополнительные ограничения безопасности, например удаление HTML-элементов формы с пользовательской страницы. 

Использование b2clogin.com дает следующие преимущества:

- Пространство, используемое службами Майкрософт в заголовке файлов cookie, уменьшается.
- URL-адреса больше не содержат ссылку на корпорацию Майкрософт. Например, `https://your-tenant-name.b2clogin.com/tenant-id/oauth2/authresp`.

> [!NOTE]
> Вы можете использовать как имя клиента, так и GUID клиента следующим образом:
> * `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com`(по-прежнему ссылается `onmicrosoft.com`на)
> * `https://your-tenant-name.b2clogin.com/your-tenant-guid`(в этом случае ссылка на корпорацию Майкрософт отсутствует)
>
> Однако нельзя использовать _личный домен_ для клиента Azure Active Directory B2C, например `https://your-tenant-name.b2clogin.com/your-custom-domain-name` , _не_ будет работать.

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

При использовании MSAL для свойства **ValidateAuthority** следует установить значение `false`. Если свойству **ValidateAuthority** присвоено значение `false`, для b2clogin.com разрешены перенаправления. 

Далее приведен пример установки свойства.

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
