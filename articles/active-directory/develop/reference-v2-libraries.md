---
title: Библиотеки проверки подлинности платформы Microsoft identity | Документация Майкрософт
description: Совместимые клиентские библиотеки и серверные библиотеки по промежуточного слоя и связанные библиотеки, источник и ссылки на примеры, для конечной точки платформы Microsoft identity.
services: active-directory
documentationcenter: ''
author: negoe
manager: CelesteDG
editor: ''
ms.assetid: 19cec615-e51f-4141-9f8c-aaf38ff9f746
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/07/2019
ms.author: negoe
ms.reviewer: jmprieur, saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 19718dc884685f6c1e1a588eea6a5de36e446fb1
ms.sourcegitcommit: f6c85922b9e70bb83879e52c2aec6307c99a0cac
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/11/2019
ms.locfileid: "65545536"
---
# <a name="microsoft-identity-platform-authentication-libraries"></a>Библиотеки проверки подлинности платформы Microsoft identity

[Конечная точка платформы удостоверений Microsoft](active-directory-v2-compare.md) поддерживает стандартные отраслевые протоколы OAuth 2.0 и OpenID Connect 1.0. Библиотеки аутентификации Майкрософт (MSAL) предназначен для работы с конечной точкой платформы Microsoft identity. Можно также использовать библиотеки с открытым кодом, которые поддерживают OAuth 2.0 и OpenID Connect 1.0.

Рекомендуется использовать библиотеки, написанные специалистами в области протоколов, которые придерживаются методов жизненного цикла разработки защищенных приложений (SDL), [таких, каким следуют в корпорации Майкрософт][Microsoft-SDL]. Если вы решили написать код для протоколов, следуйте методам SDL (например тем, которых придерживаются в корпорации Майкрософт) и внимательно прочитайте рекомендации по обеспечению безопасности в спецификациях стандартов для каждого из протоколов.

> [!NOTE]
> Ищете библиотеку аутентификации Azure AD (ADAL)? Ознакомьтесь с [руководством по библиотекам ADAL](active-directory-authentication-libraries.md).

## <a name="types-of-libraries"></a>Типы библиотек

Конечная точка платформы удостоверений Microsoft поддерживает два типа библиотек:

* **Клиентские библиотеки**. Собственные клиенты и серверы используют клиентские библиотеки, чтобы получить маркер доступа для вызова ресурса, например Microsoft Graph.
* **Серверные библиотеки ПО промежуточного слоя**. Веб-приложения используют серверные библиотеки ПО промежуточного слоя для входа пользователей. Веб-API используют серверные библиотеки ПО промежуточного слоя для проверки маркеров, отправляемых собственными клиентами или другими серверами.

## <a name="library-support"></a>Поддержка библиотек

Для библиотек предусмотрено две категории поддержки:

* **Библиотеки, поддерживаемые корпорацией Майкрософт**. Корпорация Майкрософт предоставляет исправления для этих библиотек, к тому же она провела для них комплексную экспертизу жизненного цикла разработки защищенных приложений.
* **Совместимые библиотеки**. Корпорация Майкрософт протестировала такие библиотеки в основных сценариях и подтвердила их совместимость с конечной точкой платформы Microsoft identity. Корпорация Майкрософт не предоставляет исправления для этих библиотек и не выполняла их проверку. Проблемы и запросы возможностей следует отправлять в проекты с открытым кодом библиотеки.

Список библиотек, совместимых с конечной точкой платформа удостоверений Майкрософт см. в следующих разделах этой статьи.

## <a name="microsoft-supported-client-libraries"></a>Клиентские библиотеки, поддерживаемые корпорацией Майкрософт

Клиентские библиотеки аутентификации используются, чтобы получить маркер для вызова защищенного веб-API

| платформа | Библиотека | Download (Скачать) | Исходный код | Образец | Справочные материалы | Основная документация | Стратегия |
| --- | --- | --- | --- | --- | --- | --- | ---|
| ![JavaScript](media/sample-v2-code/logo_js.png) | MSAL.js  | [NPM](https://www.npmjs.com/package/msal) |[GitHub](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-angularjs/README.md) |  [Одностраничное приложение](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-dotnet-webapi-v2) | [Справочные материалы](https://htmlpreview.github.io/?https://raw.githubusercontent.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-core/docs/classes/_useragentapplication_.useragentapplication.html) | [Общие документы](msal-overview.md)| [План выхода продуктов](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki#roadmap)
|![Angular JS](media/sample-v2-code/logo_angular.png) | MSAL для Angular JS | [NPM](https://www.npmjs.com/package/@azure/msal-angularjs) | [GitHub](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-angularjs/README.md) |  |  | |
![Angular](media/sample-v2-code/logo_angular.png) | MSAL для Angular (предварительная версия) | [NPM](https://www.npmjs.com/package/@azure/msal-angular) |[GitHub](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-angular/README.md) | | | |
| ![.NET Framework](media/sample-v2-code/logo_NET.png) ![UWP](media/sample-v2-code/logo_windows.png) ![Xamarin](media/sample-v2-code/logo_xamarin.png) | MSAL .NET  |[NuGet](https://www.nuget.org/packages/Microsoft.Identity.Client) |[GitHub](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet) | [Классическое приложение](guidedsetups/active-directory-mobileanddesktopapp-windowsdesktop-intro.md) | [MSAL для .NET](https://docs.microsoft.com/dotnet/api/microsoft.identity.client?view=azure-dotnet-preview) |[Вики-сайт](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki#conceptual-documentation) | [План выхода продуктов](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki#roadmap)
| ![Python](media/sample-v2-code/logo_python.png) | MSAL Python (Предварительная версия) | [PyPI](https://pypi.org/project/msal) | [Github](https://github.com/AzureAD/microsoft-authentication-library-for-python) | [Примеры](https://github.com/AzureAD/microsoft-authentication-library-for-python/tree/dev/sample) | [— ReadTheDocs](https://msal-python.rtfd.io/) | [Вики-сайт](https://github.com/AzureAD/microsoft-authentication-library-for-python/wiki) | [План выхода продуктов](https://github.com/AzureAD/microsoft-authentication-library-for-python/wiki/Roadmap)
| ![Java](media/sample-v2-code/logo_java.png) | Java MSAL (Предварительная версия) | [Maven](https://mvnrepository.com/artifact/com.microsoft.azure/msal4j) | [Github](https://github.com/AzureAD/microsoft-authentication-library-for-java) | [Примеры](https://github.com/AzureAD/microsoft-authentication-library-for-java/tree/dev/src/samples) | | | [План выхода продуктов](https://github.com/AzureAD/microsoft-authentication-library-for-java/wiki)
| ![iOS, Objective-C или swift](media/sample-v2-code/logo_iOS.png) | MSAL для obj_c (предварительная версия) | [GitHub](https://github.com/AzureAD/microsoft-authentication-library-for-objc) |[GitHub](https://github.com/AzureAD/microsoft-authentication-library-for-objc) | [Приложение iOS](https://github.com/Azure-Samples/active-directory-msal-ios-swift) |  |
|![Android и Java](media/sample-v2-code/logo_Android.png) | MSAL (предварительная версия) | [Центральный репозиторий](https://repo1.maven.org/maven2/com/microsoft/identity/client/msal/) |[GitHub](https://github.com/AzureAD/microsoft-authentication-library-for-android) | [Приложение Android](quickstart-v2-android.md) | [Javadocs](https://javadoc.io/doc/com.microsoft.identity.client/msal) | | |

## <a name="microsoft-supported-server-middleware-libraries"></a>Серверные библиотеки ПО промежуточного слоя, поддерживаемые корпорацией Майкрософт

Библиотеки ПО промежуточного слоя используются для защиты веб-приложений и веб-API. Для веб-приложений или веб-API, написанных с помощью ASP.NET или ASP.NET Core, эти библиотеки используются ASP.NET и ASP.NET Core.

| платформа | Библиотека | Download (Скачать) | Исходный код | Образец | Справочные материалы
| --- | --- | --- | --- | --- | --- |
| ![.NET](media/sample-v2-code/logo_NET.png) ![.NET Core](media/sample-v2-code/logo_NETcore.png) | Безопасность ASP.NET |[NuGet](https://www.nuget.org/packages/Microsoft.AspNet.Mvc/) |[GitHub](https://github.com/aspnet/AspNetCore) |[Приложение MVC](quickstart-v2-aspnet-webapp.md) |[Справочник по API для ASP.NET](https://docs.microsoft.com/dotnet/api/?view=aspnetcore-2.0) |
| ![.NET](media/sample-v2-code/logo_NET.png)| Расширения модели идентификации для .NET| |[GitHub](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet) | [Приложение MVC](quickstart-v2-aspnet-webapp.md) |[Справочные материалы](https://docs.microsoft.com/dotnet/api/overview/azure/activedirectory/client?view=azure-dotnet) |
| ![Node.js](media/sample-v2-code/logo_nodejs.png) | Azure AD Passport |[NPM](https://www.npmjs.com/package/passport-azure-ad) |[GitHub](https://github.com/AzureAD/passport-azure-ad) | [Веб-приложение](https://github.com/AzureADQuickStarts/AppModelv2-WebApp-OpenIDConnect-nodejs) | |

## <a name="compatible-client-libraries"></a>Совместимые клиентские библиотеки

| платформа | Имя библиотеки | Проверенные версии | Исходный код | Образец |
|:---:|:---:|:---:|:---:|:---:|
|![JavaScript](media/sample-v2-code/logo_js.png)|[Hello.js](https://adodson.com/hello.js/) | 1.13.5 |[Hello.js](https://github.com/MrSwitch/hello.js) |[Безопасная проверка пароля](https://github.com/Azure-Samples/active-directory-javascript-graphapi-web-v2) |
| ![Java](media/sample-v2-code/logo_java.png) | [Scribe Java](https://github.com/scribejava/scribejava) | [Версия 3.2.0](https://github.com/scribejava/scribejava/releases/tag/scribejava-3.2.0) | [ScribeJava](https://github.com/scribejava/scribejava/) | |
| ![Java](media/sample-v2-code/logo_java.png) | [Библиотеку Gluu OpenID Connect](https://github.com/GluuFederation/oxAuth) | [Версия 3.0.2](https://github.com/GluuFederation/oxAuth/releases/tag/3.0.2) | [Библиотеку Gluu OpenID Connect](https://github.com/GluuFederation/oxAuth) | |
| ![Python](media/sample-v2-code/logo_python.png) | [Requests-OAuthlib](https://github.com/requests/requests-oauthlib) | [Версия 1.2.0](https://github.com/requests/requests-oauthlib/releases/tag/v1.2.0) | [Requests-OAuthlib](https://github.com/requests/requests-oauthlib) | |
| ![Node.js](media/sample-v2-code/logo_nodejs.png) | [Клиент openid](https://github.com/panva/node-openid-client) | [Версия 2.4.5](https://github.com/panva/node-openid-client/releases/tag/v2.4.5) | [Клиент openid](https://github.com/panva/node-openid-client) | |
| ![PHP](media/sample-v2-code/logo_php.png) | [The PHP League (oauth2-client)](https://github.com/thephpleague/oauth2-client) | [Версия 1.4.2](https://github.com/thephpleague/oauth2-client/releases/tag/1.4.2) | [oauth2-client](https://github.com/thephpleague/oauth2-client/) | |
| ![Ruby](media/sample-v2-code/logo_ruby.png) |[OmniAuth](https://github.com/omniauth/omniauth/wiki) |omniauth:1.3.1<br />omniauth-oauth2:1.4.0 |[OmniAuth](https://github.com/omniauth/omniauth)<br />[OmniAuth OAuth2](https://github.com/intridea/omniauth-oauth2) |  |
| ![iOS](media/sample-v2-code/logo_iOS.png) ![Android](media/sample-v2-code/logo_Android.png) | [REACT Native проверки подлинности приложения](https://github.com/FormidableLabs/react-native-app-auth) | [Версии 4.2.0](https://github.com/FormidableLabs/react-native-app-auth/releases/tag/v4.2.0) | [REACT Native проверки подлинности приложения](https://github.com/FormidableLabs/react-native-app-auth) | |

Для всех библиотек, соответствующих стандартам воспользуйтесь конечной точкой платформа удостоверений Майкрософт, поэтому очень важно знать, куда обратиться за поддержкой.

* Проблемы и запросы на новые возможности в коде библиотеки отправляются владельцу библиотеки.
* Проблемы и запросы на новые возможности в реализации протокола на стороне службы отправляются в корпорацию Майкрософт.
* [Зарегистрируйте запрос функции](https://feedback.azure.com/forums/169401-azure-active-directory) для дополнительных компонентов, которые вы хотите добавить в протокол.
* [Создайте запрос в службу поддержки](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request) при обнаружении проблемы, где конечная точка платформы Microsoft identity не соответствует стандарту OAuth 2.0 или OpenID Connect 1.0.

## <a name="related-content"></a>Связанная информация

Дополнительные сведения о конечной точке платформы удостоверений Microsoft см. в разделе [обзор платформы удостоверений Microsoft][AAD-App-Model-V2-Overview].

<!--Image references-->

<!--Reference style links -->
[AAD-App-Model-V2-Overview]: v2-overview.md
[ClientLib-NET-Lib]: https://www.nuget.org/packages/Microsoft.Identity.Client
[ClientLib-NET-Repo]: https://github.com/AzureAD/microsoft-authentication-library-for-dotnet
[ClientLib-NET-Sample]: active-directory-v2-devquickstarts-wpf.md
[ClientLib-Node-Lib]: https://www.npmjs.com/package/passport-azure-ad
[ClientLib-Node-Repo]: https://github.com/AzureAD/passport-azure-ad
[ClientLib-Node-Sample]:/
[ClientLib-Iosmac-Lib]:/
[ClientLib-Iosmac-Repo]:/
[ClientLib-Iosmac-Sample]:/
[ClientLib-Android-Lib]:/
[ClientLib-Android-Repo]:/
[ClientLib-Android-Sample]:/
[ClientLib-Js-Lib]:/
[ClientLib-Js-Repo]:/
[ClientLib-Js-Sample]:/

[Microsoft-SDL]: https://www.microsoft.com/sdl/default.aspx
[ServerLib-Net4-Owin-Oidc-Lib]: https://www.nuget.org/packages/Microsoft.Owin.Security.OpenIdConnect/
[ServerLib-Net4-Owin-Oidc-Repo]: https://katanaproject.codeplex.com/
[ServerLib-Net4-Owin-Oidc-Sample]: active-directory-v2-devquickstarts-dotnet-web.md
[ServerLib-Net4-Owin-Oauth-Lib]: https://www.nuget.org/packages/Microsoft.Owin.Security.OAuth/
[ServerLib-Net4-Owin-Oauth-Repo]: https://katanaproject.codeplex.com/
[ServerLib-Net4-Owin-Oauth-Sample]: https://azure.microsoft.com/documentation/articles/active-directory-v2-devquickstarts-dotnet-api/
[ServerLib-Net-Jwt-Lib]: https://www.nuget.org/packages/System.IdentityModel.Tokens.Jwt
[ServerLib-Net-Jwt-Repo]: https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet
[ServerLib-Net-Jwt-Sample]:/
[ServerLib-NetCore-Owin-Oidc-Lib]: https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.OpenIdConnect/
[ServerLib-NetCore-Owin-Oidc-Repo]: https://github.com/aspnet/Security
[ServerLib-NetCore-Owin-Oidc-Sample]: https://github.com/Azure-Samples/active-directory-dotnet-webapp-openidconnect-aspnetcore-v2
[ServerLib-NetCore-Owin-Oauth-Lib]: https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.OAuth/
[ServerLib-NetCore-Owin-Oauth-Repo]: https://github.com/aspnet/Security
[ServerLib-NetCore-Owin-Oauth-Sample]:/
[ServerLib-Node-Lib]: https://www.npmjs.com/package/passport-azure-ad
[ServerLib-Node-Repo]: https://github.com/AzureAD/passport-azure-ad/
[ServerLib-Node-Sample]: https://azure.microsoft.com/documentation/articles/active-directory-v2-devquickstarts-node-web/
