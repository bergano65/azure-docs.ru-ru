---
title: Библиотеки проверки подлинности платформы Microsoft Identity Документация Майкрософт
description: Совместимые клиентские библиотеки и библиотеки промежуточного слоя сервера, а также связанные библиотеки, источник и образцы ссылок для конечной точки платформы Microsoft Identity.
services: active-directory
documentationcenter: ''
author: negoe
manager: CelesteDG
editor: ''
ms.assetid: 19cec615-e51f-4141-9f8c-aaf38ff9f746
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/25/2019
ms.author: negoe
ms.reviewer: jmprieur, saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 924143d7275e9670ebce3c244263c5210dcb5d62
ms.sourcegitcommit: 263a69b70949099457620037c988dc590d7c7854
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/25/2019
ms.locfileid: "71268444"
---
# <a name="microsoft-identity-platform-authentication-libraries"></a>Библиотеки проверки подлинности платформы Microsoft Identity

[Конечная точка платформы Microsoft Identity](active-directory-v2-compare.md) поддерживает стандартные протоколы OAuth 2,0 и openid connect Connect 1,0. Библиотека проверки подлинности Microsoft (MSAL) предназначена для работы с конечной точкой платформы Microsoft Identity. Также можно использовать библиотеки с открытым исходным кодом, поддерживающие OAuth 2,0 и OpenID Connect Connect 1,0.

Рекомендуется использовать библиотеки, написанные экспертами по домену протокола, которые соответствуют методологии жизненного цикла разработки безопасности (SDL). К таким библиотекам относится [тот, который следует корпорации Майкрософт][Microsoft-SDL]. При наличии кода для протоколов следует следовать такой методологии, как Microsoft SDL. Обратите особое внимание на вопросы безопасности в спецификациях стандартов для каждого протокола.

> [!NOTE]
> Вы ищете библиотеку проверки подлинности Azure Active Directory (ADAL)? Ознакомьтесь с [руководством по библиотеке ADAL](active-directory-authentication-libraries.md).

## <a name="types-of-libraries"></a>Типы библиотек

Конечная точка платформы Microsoft Identity работает с двумя типами библиотек:

* **Клиентские библиотеки**. Собственные клиенты и серверы используют клиентские библиотеки для получения маркеров доступа для вызова ресурса, например Microsoft Graph.
* **Серверные библиотеки ПО промежуточного слоя**. Веб-приложения используют серверные библиотеки по промежуточного слоя для входа пользователя. Веб-API используют серверные библиотеки ПО промежуточного слоя для проверки маркеров, отправляемых собственными клиентами или другими серверами.

## <a name="library-support"></a>Поддержка библиотек

Для библиотек предусмотрено две категории поддержки:

* **Библиотеки, поддерживаемые корпорацией Майкрософт**. Корпорация Майкрософт предоставляет исправления для этих библиотек, к тому же она провела для них комплексную экспертизу жизненного цикла разработки защищенных приложений.
* **Совместимые библиотеки**. Корпорация Майкрософт проверила эти библиотеки в основных сценариях и подтвердила, что они работают с конечной точкой платформы Microsoft Identity. Корпорация Майкрософт не предоставляет исправления для этих библиотек и не выполнила проверку этих библиотек. Проблемы и запросы возможностей следует отправлять в проекты с открытым кодом библиотеки.

Список библиотек, которые работают с конечной точкой платформы идентификации Майкрософт, см. в следующих разделах.

## <a name="microsoft-supported-client-libraries"></a>Клиентские библиотеки, поддерживаемые корпорацией Майкрософт

Используйте библиотеки проверки подлинности клиента, чтобы получить маркер для вызова защищенного веб-API.

| Платформа | Библиотека | Загрузить | Исходный код | Пример | Ссылка | Основная документация | Стратегия |
| --- | --- | --- | --- | --- | --- | --- | --- |
| JavaScript | MSAL.js  | [NPM](https://www.npmjs.com/package/msal) |[GitHub](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/README.md) |  [Одностраничное приложение](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-dotnet-webapi-v2) | [Справочные материалы](https://htmlpreview.github.io/?https://raw.githubusercontent.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-core/docs/classes/_useragentapplication_.useragentapplication.html) | [Основные документы](msal-overview.md)| [План выхода продуктов](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki#roadmap)
|Angular JS | MSAL для Angular JS | [NPM](https://www.npmjs.com/package/@azure/msal-angularjs) | [GitHub](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-angularjs/README.md) |  |  | |
Angular | MSALный угловой (Предварительная версия) | [NPM](https://www.npmjs.com/package/@azure/msal-angular) |[GitHub](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-angular/README.md) | | | |
| .NET Framework <br> UWP <br> Xamarin  | MSAL.NET  |[NuGet](https://www.nuget.org/packages/Microsoft.Identity.Client) |[GitHub](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet) | [Классическое приложение](guidedsetups/active-directory-mobileanddesktopapp-windowsdesktop-intro.md) | [MSAL для .NET](https://docs.microsoft.com/dotnet/api/microsoft.identity.client?view=azure-dotnet-preview) |[Основные документы](msal-overview.md) | [План выхода продуктов](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki#roadmap)
| Python | MSAL Python (Предварительная версия) | [PyPI](https://pypi.org/project/msal) | [GitHub](https://github.com/AzureAD/microsoft-authentication-library-for-python) | [Примеры](https://github.com/AzureAD/microsoft-authentication-library-for-python/tree/dev/sample) | [реадседокс](https://msal-python.rtfd.io/) | [Вики-сайт](https://github.com/AzureAD/microsoft-authentication-library-for-python/wiki) | [План выхода продуктов](https://github.com/AzureAD/microsoft-authentication-library-for-python/wiki/Roadmap)
| Java | MSAL Java (Предварительная версия) | [Maven](https://mvnrepository.com/artifact/com.microsoft.azure/msal4j) | [GitHub](https://github.com/AzureAD/microsoft-authentication-library-for-java) | [Примеры](https://github.com/AzureAD/microsoft-authentication-library-for-java/tree/dev/src/samples) | | [Вики-сайт](https://github.com/AzureAD/microsoft-authentication-library-for-java/wiki) | [План выхода продуктов](https://github.com/AzureAD/microsoft-authentication-library-for-java/wiki)
| iOS <br> macOS | MSAL iOS и macOS | [GitHub](https://github.com/AzureAD/microsoft-authentication-library-for-objc) |[GitHub](https://github.com/AzureAD/microsoft-authentication-library-for-objc) | [приложение iOS](https://github.com/Azure-Samples/ms-identity-mobile-apple-swift-objc), [приложение macOS](https://github.com/Azure-Samples/ms-identity-macOS-swift-objc) | [Справочные материалы](https://azuread.github.io/microsoft-authentication-library-for-objc/index.html)  | [Основные документы](msal-overview.md) | |
|Android и Java  | MSAL (предварительная версия) | [Центральный репозиторий](https://repo1.maven.org/maven2/com/microsoft/identity/client/msal/) |[GitHub](https://github.com/AzureAD/microsoft-authentication-library-for-android) | [Приложение Android](quickstart-v2-android.md) | [Javadocs](https://javadoc.io/doc/com.microsoft.identity.client/msal) | [Вики-сайт](https://github.com/AzureAD/microsoft-authentication-library-for-android/wiki) |

## <a name="microsoft-supported-server-middleware-libraries"></a>Серверные библиотеки ПО промежуточного слоя, поддерживаемые корпорацией Майкрософт

Используйте библиотеки по промежуточного слоя для защиты веб-приложений и веб-API. Веб-приложения или веб-API, написанные с помощью ASP.NET или ASP.NET Core, используют библиотеки по промежуточного слоя.

| Платформа | Библиотека | Загрузить | Исходный код | Пример | Ссылка
| --- | --- | --- | --- | --- | --- |
| .NET <br> .NET Core | Безопасность ASP.NET |[NuGet](https://www.nuget.org/packages/Microsoft.AspNet.Mvc/) |[GitHub](https://github.com/aspnet/AspNetCore) |[Приложение MVC](quickstart-v2-aspnet-webapp.md) |[Справочник по API для ASP.NET](https://docs.microsoft.com/dotnet/api/?view=aspnetcore-2.0) |
| .NET | Расширения модели идентификации для .NET| |[GitHub](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet) | [Приложение MVC](quickstart-v2-aspnet-webapp.md) |[Справочные материалы](https://docs.microsoft.com/dotnet/api/overview/azure/activedirectory/client?view=azure-dotnet) |
| Node.js  | Azure AD Passport |[NPM](https://www.npmjs.com/package/passport-azure-ad) |[GitHub](https://github.com/AzureAD/passport-azure-ad) | [Веб-приложение](https://github.com/AzureADQuickStarts/AppModelv2-WebApp-OpenIDConnect-nodejs) | |

## <a name="microsoft-supported-libraries-by-os--language"></a>Библиотеки, поддерживаемые Майкрософт, по ОС и языку

В терминах поддерживаемых операционных систем и языков используется следующее сопоставление:

|             | Windows    | Linux      | macOS      | iOS | Android    |
|-------------|------------|------------|------------|------------|------------|
| JavaScript  |  MSAL.js | MSAL.js | MSAL.js | MSAL.js |  MSAL.js |
| C# | ASP.NET, ASP.NET Core, MSAL.Net (.NET FW, Core, UWP)| ASP.NET Core MSAL.Net (.NET Core) | ASP.NET Core, MSAL.Net (macOS) | MSAL.Net (Xamarin. iOS) | MSAL.Net (Xamarin. Android)|
| Код SWIFT <br> Objective-C |            |            | [MSAL для iOS и macOS](msal-overview.md) | [MSAL для iOS и macOS](msal-overview.md) |            |
| Java       | msal4j       | msal4j       | msal4j  |    | MSAL.Android |
| Python     | MSAL Python | MSAL Python | MSAL Python |
|  Node.JS     | Паспорт. Node | Паспорт. Node | Паспорт. Node |

См. также [сценарии поддерживаемых платформ и языков](authentication-flows-app-scenarios.md#scenarios-and-supported-platforms-and-languages)

## <a name="compatible-client-libraries"></a>Совместимые клиентские библиотеки

| Платформа | Имя библиотеки | Проверенные версии | Исходный код | Пример |
|:---:|:---:|:---:|:---:|:---:|
|JavaScript|[Hello.js](https://adodson.com/hello.js/) | Версия 1.13.5 |[Hello.js](https://github.com/MrSwitch/hello.js) |[Безопасная проверка пароля](https://github.com/Azure-Samples/active-directory-javascript-graphapi-web-v2) |
| Java | [Scribe Java](https://github.com/scribejava/scribejava) | [Версия 3.2.0](https://github.com/scribejava/scribejava/releases/tag/scribejava-3.2.0) | [ScribeJava](https://github.com/scribejava/scribejava/) | |
| Java | [Библиотека глуу OpenID Connect Connect](https://github.com/GluuFederation/oxAuth) | [Версия 3.0.2](https://github.com/GluuFederation/oxAuth/releases/tag/3.0.2) | [Библиотека глуу OpenID Connect Connect](https://github.com/GluuFederation/oxAuth) | |
| Python | [Запросы — Оауслиб](https://github.com/requests/requests-oauthlib) | [Версия 1.2.0](https://github.com/requests/requests-oauthlib/releases/tag/v1.2.0) | [Запросы — Оауслиб](https://github.com/requests/requests-oauthlib) | |
| Node.js  | [OpenID Connect — клиент](https://github.com/panva/node-openid-client) | [Версия 2.4.5](https://github.com/panva/node-openid-client/releases/tag/v2.4.5) | [OpenID Connect — клиент](https://github.com/panva/node-openid-client) | |
| PHP | [The PHP League (oauth2-client)](https://github.com/thephpleague/oauth2-client) | [Версия 1.4.2](https://github.com/thephpleague/oauth2-client/releases/tag/1.4.2) | [oauth2-client](https://github.com/thephpleague/oauth2-client/) | |
| Ruby |[OmniAuth](https://github.com/omniauth/omniauth/wiki) |омниаус: 1.3.1<br />омниаус — OAuth2: 1.4.0 |[OmniAuth](https://github.com/omniauth/omniauth)<br />[OmniAuth OAuth2](https://github.com/intridea/omniauth-oauth2) |  |
| iOS и Android  | [Отреагировать на проверку подлинности собственного приложения](https://github.com/FormidableLabs/react-native-app-auth) | [Версия 4.2.0](https://github.com/FormidableLabs/react-native-app-auth/releases/tag/v4.2.0) | [Отреагировать на проверку подлинности собственного приложения](https://github.com/FormidableLabs/react-native-app-auth) | |

Для любой библиотеки, совместимой со стандартами, можно использовать конечную точку платформы Microsoft Identity. Важно иметь представление о том, где следует обращаться в службу поддержки:

* Проблемы и запросы на новые возможности в коде библиотеки отправляются владельцу библиотеки.
* Проблемы и запросы на новые возможности в реализации протокола на стороне службы отправляются в корпорацию Майкрософт.
* Отправьте [запрос функции](https://feedback.azure.com/forums/169401-azure-active-directory) для дополнительных функций, которые вы хотите увидеть в протоколе.
* [Создайте запрос в службу поддержки](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request) , если вы нашли ошибку, когда конечная точка платформы Microsoft Identity не соответствует требованиям OAuth 2,0 или openid connect Connect 1,0.

## <a name="related-content"></a>Связанная информация

Дополнительные сведения о конечной точке платформы идентификации Майкрософт см. в [статье Обзор платформы идентификации Майкрософт][AAD-App-Model-V2-Overview].

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
