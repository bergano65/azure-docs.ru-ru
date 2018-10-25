---
title: Установка b2clogin.com в качестве URL-адреса перенаправления для Azure Active Directory B2C | Документация Майкрософт
description: Сведения об использовании b2clogin.com в качестве URL-адреса перенаправления для Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 10/04/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 8e06cf1a443d4fd158e29ef4b53206a83800dfe9
ms.sourcegitcommit: 9eaf634d59f7369bec5a2e311806d4a149e9f425
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/05/2018
ms.locfileid: "48803058"
---
# <a name="set-redirect-urls-to-b2clogincom-for-azure-active-directory-b2c"></a>Установка b2clogin.com в качестве URL-адреса перенаправления для Azure Active Directory B2C

В процессе настройки поставщика удостоверений для регистрации и входа в приложение Azure Active Directory (Azure AD) B2C, вам потребуется указать URL-адрес переадресации. В прошлом использовался login.microsoftonline.com, а сейчас вместо него следует использовать b2clogin.com.

Использование b2clogin.com дает следующие преимущества:

- Файлы cookie больше не используются совместно с другими службами Майкрософт.
- URL-адреса больше не содержат ссылку на корпорацию Майкрософт. Например, `https://your-tenant-name.b2clogin.com/tfp/your-tenant-ID/policyname/v2.0/.well-known/openid-configuration`.

Чтобы использовать b2clogin.com, установите его в качестве URL-адрес перенаправления в приложении поставщика удостоверений. Также настройте приложении Azure AD B2C на использование b2clogin.com для указанных политик и маркеров конечных точек. При использовании MSAL, значение свойства **ValidateAuthority** следует установить на `false`.

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

Скорее всего приложение Azure AD B2C ссылается на `login.microsoftonline.com` в нескольких местах, например, в указанных политиках и маркерах утверждений.  Убедитесь, что конечная точка авторизации, конечная точка маркера и издателя были обновлены для использования `your-tenant-name.b2clogin.com`.  

## <a name="set-the-validateauthority-property"></a>Установка свойства ValidateAuthority

При использовании MSAL, значение свойства **ValidateAuthority** следует установить на `false`. Далее приведен пример установки свойства.

```
this.clientApplication = new UserAgentApplication(
  env.auth.clientId,
  env.auth.loginAuthority,
  this.authCallback.bind(this),
  {
    validateAuthority: false
  }
);
```

 Дополнительные сведения см. в статье [ClientApplicationBase Class](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.clientapplicationbase?view=azure-dotnet) (Класс ClientApplicationBase).