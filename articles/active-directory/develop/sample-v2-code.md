---
title: Примеры кода для платформы Microsoft Identity
description: Содержит индекс доступных примеров кода платформы Microsoft Identity Platform (версии 2.0), упорядоченных по сценарию.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 07/15/2019
ms.author: ryanwi
ms.reviewer: jmprieur
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: 713fa09319f1b95fe2bcc0d15c973a2096a250ec
ms.sourcegitcommit: c8a0fbfa74ef7d1fd4d5b2f88521c5b619eb25f8
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/05/2020
ms.locfileid: "82801164"
---
# <a name="microsoft-identity-platform-code-samples-v20-endpoint"></a>Примеры кода платформы удостоверений Microsoft Identity (версия 2.0, конечная точка)

Возможности платформы удостоверений Майкрософт:

- добавление возможностей аутентификации и авторизации в веб-приложения и веб-API;
- требование маркера доступа для доступа к защищенным веб-API.

В этой статье кратко описаны ссылки на примеры для конечной точки платформы идентификации Майкрософт. В этих примерах показано, как это делается, а также приведены фрагменты кода, которые можно использовать в приложениях. На странице образец кода вы найдете подробные разделы readme, которые помогут в требованиях, установке и настройке. Комментарии в коде помогают понять критически важные разделы.

> [!NOTE]
> Если вы заинтересованы в примерах версии 1.0, ознакомьтесь с [примерами кода Azure AD (конечная точка версии 1.0)](../azuread-dev/sample-v1-code.md).

Основные сведения о базовом сценарии для каждого типа выборки см. в разделе [типы приложений для конечной точки платформы Microsoft Identity](v2-app-types.md).

Вы также можете поделиться своими примерами на сайте GitHub. Чтобы узнать, как это сделать, обратитесь к разделу [Примеры и документация Microsoft Azure Active Directory](https://github.com/Azure-Samples?page=3&query=active-directory).

## <a name="single-page-applications"></a>Одностраничные приложения

В этих примерах показано, как создать одностраничное приложение, защищенное с помощью платформы идентификации Майкрософт. В этих примерах используется одна из разновидностей MSAL. js.

| Платформа | Описание | Ссылка |
| -------- | --------------------- | -------- |
| ![На этом рисунке показан логотип](media/sample-v2-code/logo_js.png) JavaScript [JavaScript (msal. js)](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-core) . | Вызывает Microsoft Graph |[javascript-graphapi-v2](https://github.com/Azure-Samples/active-directory-javascript-graphapi-v2) |
| ![На этом рисунке показан логотип](media/sample-v2-code/logo_js.png) JavaScript [JavaScript (msal. js)](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-core) . | Вызывает Microsoft Graph с помощью потока кода проверки подлинности w/PKCE |[JavaScript — v2](https://github.com/Azure-Samples/ms-identity-javascript-v2) |
| ![На этом рисунке показан логотип](media/sample-v2-code/logo_js.png) JavaScript [JavaScript (msal. js)](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-core) . | Вызывает B2C |[B2C-JavaScript-msal-singlepageapp](https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp) |
| ![На этом изображении показана](media/sample-v2-code/logo_angular.png) угловая эмблема [JavaScript (MSAL под углом)](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-angular)| Вызывает собственный веб-API | [MS-Identity-JavaScript-угловой-Spa-aspnetcore-webapi](https://github.com/Azure-Samples/ms-identity-javascript-angular-spa-aspnetcore-webapi) |
| ![На этом изображении показана](media/sample-v2-code/logo_angular.png) угловая эмблема [JavaScript (MSAL под углом)](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-angular)| Вызывает Microsoft Graph  | [Active-Directory-JavaScript-singlepageapp-угловой](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-angular) |
| ![На этом рисунке показан логотип](media/sample-v2-code/logo_react.png) [JavaScript (msal. js)](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-core) .| Вызывает собственный веб-API, который, в свою очередь, вызывает Microsoft Graph  | [MS-Identity-JavaScript-реакция — Spa-команда dotnetcore-webapi-OBO](https://github.com/Azure-Samples/ms-identity-javascript-react-spa-dotnetcore-webapi-obo) |
| ![На этом изображении показана](media/sample-v2-code/logo_angular.png) угловая эмблема [JavaScript (MSAL под углом)](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-angular) | Вызывает B2C |[Active-Directory-B2C-JavaScript-угловой — Spa](https://github.com/Azure-Samples/active-directory-b2c-javascript-angular-spa) |

## <a name="web-applications"></a>Веб-приложения

В следующем примере показаны веб-приложения для входа пользователей. В некоторых примерах приложение вызывает Microsoft Graph или веб-API с использованием удостоверения пользователя.

| Платформа | Только вход пользователей | Выполняет вход пользователей и вызывает Microsoft Graph |
| -------- | ------------------- | --------------------------------- |
| ![На этом рисунке показан логотип ASP.NET Core](media/sample-v2-code/logo_NETcore.png)</p>ASP.NET Core | [Руководство пользователя ASP.NET Core WebApp входа в систему](https://aka.ms/aspnetcore-webapp-sign-in) | Тот же пример в [ASP.NET Core веб-приложение вызывает этап Microsoft Graph](https://aka.ms/aspnetcore-webapp-call-msgraph) |
| ![На этом рисунке показан логотип ASP.NET](media/sample-v2-code/logo_NETframework.png)</p> ASP.NET | [Краткое руководство по ASP.NET](https://github.com/AzureAdQuickstarts/AppModelv2-WebApp-OpenIDConnect-DotNet) </p> [dotnet-webapp-openidconnect-v2](https://github.com/azure-samples/active-directory-dotnet-webapp-openidconnect-v2)  |  [dotnet-admin-restricted-scopes-v2](https://github.com/azure-samples/active-directory-dotnet-admin-restricted-scopes-v2) </p> |[msgraph-training-aspnetmvcapp](https://github.com/microsoftgraph/msgraph-training-aspnetmvcapp)
| ![На этом рисунке показан логотип Java](media/sample-v2-code/logo_java.png)  |                   | [MS-Identity-Java-webapp](https://github.com/Azure-Samples/ms-identity-java-webapp) |
| ![На этом рисунке показан логотип Python](media/sample-v2-code/logo_python.png)  |                   | [ms-identity-python-webapp](https://github.com/Azure-Samples/ms-identity-python-webapp) |
| ![На этом изображении показан логотип Ruby](media/sample-v2-code/logo_ruby.png) |                   | [msgraph-training-rubyrailsapp](https://github.com/microsoftgraph/msgraph-training-rubyrailsapp) |

## <a name="desktop-and-mobile-public-client-apps"></a>Общедоступные клиентские приложения — классические и мобильные

В следующих примерах показаны общедоступные клиентские приложения (настольные или мобильные приложения), которые обращаются к Microsoft Graph API, или собственный веб-API в имени пользователя. Все эти клиентские приложения используют библиотеку проверки подлинности Майкрософт (MSAL).

| Клиентское приложение | Платформа | Поток или предоставление | Вызывает Microsoft Graph | Вызывает веб-API ASP.NET Core |
| ------------------ | -------- |  ----------| ---------- | ------------------------- |
| Классическое приложение (WPF)      | ![На этом рисунке показан логотип .NET/C#](media/sample-v2-code/logo_NET.png) | [Интерактивный](msal-authentication-flows.md#interactive)| [dotnet-desktop-msgraph-v2](https://github.com/azure-samples/active-directory-dotnet-desktop-msgraph-v2) | [dotnet-native-aspnetcore-v2](https://aka.ms/msidentity-aspnetcore-webapi) |
| Классическое приложение (консольное)   | ![На этом изображении показан логотип .NET/C# (Desktop)](media/sample-v2-code/logo_NET.png) | [Встроенная проверка подлинности Windows](msal-authentication-flows.md#integrated-windows-authentication) | [dotnet-iwa-v2](https://github.com/azure-samples/active-directory-dotnet-iwa-v2) |  |
| Классическое приложение (консольное)   | ![На этом рисунке показан логотип Java](media/sample-v2-code/logo_java.png) | [Встроенная проверка подлинности Windows](msal-authentication-flows.md#integrated-windows-authentication) |[MS-Identity-Java-Desktop](https://github.com/Azure-Samples/ms-identity-java-desktop/) |  |
| Классическое приложение (консольное)   | ![На этом изображении показан логотип .NET/C# (Desktop)](media/sample-v2-code/logo_NETcore.png) | [Имя пользователя и пароль](msal-authentication-flows.md#usernamepassword) |[dotnetcore-up-v2](https://github.com/azure-samples/active-directory-dotnetcore-console-up-v2) |  |
| Desktop (консоль) с WAM  | ![На этом изображении показан логотип .NET/C# (Desktop)](media/sample-v2-code/logo_NETcore.png) | [Интерактивный с WAM](msal-authentication-flows.md#interactive) |[dotnet-native-uwp-wam](https://github.com/azure-samples/active-directory-dotnet-native-uwp-wam) |  |
| Классическое приложение (консольное)   | ![На этом рисунке показан логотип Java](media/sample-v2-code/logo_java.png) | [Имя пользователя и пароль](msal-authentication-flows.md#usernamepassword) |[MS-Identity-Java-Desktop](https://github.com/Azure-Samples/ms-identity-java-desktop/) |  |
| Классическое приложение (консольное)   | ![На этом рисунке показан логотип Python](media/sample-v2-code/logo_python.png) | [Имя пользователя и пароль](msal-authentication-flows.md#usernamepassword) |[MS-Identity-Python — Настольный компьютер](https://github.com/Azure-Samples/ms-identity-python-desktop) |  |
| Мобильное приложение (Android, iOS, UWP)   | ![На этом рисунке показан логотип .NET/C# (Xamarin).](media/sample-v2-code/logo_xamarin.png) | [Интерактивный](msal-authentication-flows.md#interactive) |[xamarin-native-v2](https://github.com/azure-samples/active-directory-xamarin-native-v2) |  |
| Мобильное приложение (iOS)       | ![На этом рисунке показана iOS/объектив-C или SWIFT.](media/sample-v2-code/logo_iOS.png) | [Интерактивный](msal-authentication-flows.md#interactive) |[iOS-SWIFT-ObjC-Native-v2](https://github.com/azure-samples/active-directory-ios-swift-native-v2) </p> [ios-native-nxoauth2-v2](https://github.com/azure-samples/active-directory-ios-native-nxoauth2-v2) |  |
| Desktop (macOS)       | macOS | [Интерактивный](msal-authentication-flows.md#interactive) |[macOS-SWIFT-ObjC-Native-v2](https://github.com/Azure-Samples/ms-identity-macOS-swift-objc) |  |
| Mobile (Android-Java)   | ![На этом рисунке показан логотип Android](media/sample-v2-code/logo_Android.png) | [Интерактивный](msal-authentication-flows.md#interactive) |  [Android — Java](https://github.com/Azure-Samples/ms-identity-android-java) |  |
| Mobile (Android-Котлин)   | ![На этом рисунке показан логотип Android](media/sample-v2-code/logo_Android.png) | [Интерактивный](msal-authentication-flows.md#interactive) |  [Android — Котлин](https://github.com/Azure-Samples/ms-identity-android-kotlin) |  |

## <a name="daemon-applications"></a>Управляющие программы

В следующем примере показано приложение, которое обращается к Microsoft Graph с использованием собственного удостоверения (без пользователя).

| Клиентское приложение | Платформа | Поток или предоставление | Вызывает Microsoft Graph |
| ------------------ | -------- | ---------- | -------------------- |
| Консоль | ![На этом рисунке показан логотип .NET Core](media/sample-v2-code/logo_NETcore.png)</p> ASP.NET  | [Учетные данные клиента](msal-authentication-flows.md#client-credentials) | [dotnetcore-daemon-v2](https://github.com/azure-samples/active-directory-dotnetcore-daemon-v2) |
| Веб-приложение | ![На этом рисунке показан логотип ASP.NET](media/sample-v2-code/logo_NETframework.png)</p> ASP.NET  | [Учетные данные клиента](msal-authentication-flows.md#client-credentials) | [dotnet-daemon-v2](https://github.com/azure-samples/active-directory-dotnet-daemon-v2) |
| Консоль | ![На этом рисунке показан логотип Java](media/sample-v2-code/logo_java.png) | [Учетные данные клиента](msal-authentication-flows.md#client-credentials) | [MS-Identity-Java-Демон](https://github.com/Azure-Samples/ms-identity-java-daemon) |
| Консоль | ![На этом рисунке показан логотип Python](media/sample-v2-code/logo_python.png) | [Учетные данные клиента](msal-authentication-flows.md#client-credentials) | [MS-Identity-Python-Демон](https://github.com/Azure-Samples/ms-identity-python-daemon) |

## <a name="headless-applications"></a>Приложения удаленного управления

В следующем примере показано общедоступное клиентское приложение, запущенное на устройстве без веб-браузера. Приложение может быть программой командной строки, приложением, работающим в Linux или Mac, или приложением IoT. Пример включает в себя приложение, осуществляющее доступ к Microsoft Graph API, в имени пользователя, который выполняет вход в интерактивном режиме на другом устройстве (например, на мобильном телефоне). Это клиентское приложение использует библиотеку проверки подлинности Майкрософт (MSAL).

| Клиентское приложение | Платформа | Поток или предоставление | Вызывает Microsoft Graph |
| ------------------ | -------- |  ----------| ---------- |
| Классическое приложение (консольное)   | ![На этом изображении показан логотип .NET/C# (Desktop)](media/sample-v2-code/logo_NETcore.png) | [Поток кода устройства](msal-authentication-flows.md#device-code) |[dotnetcore-devicecodeflow-v2](https://github.com/azure-samples/active-directory-dotnetcore-devicecodeflow-v2) |
| Классическое приложение (консольное)   | ![На этом рисунке показан логотип Java](media/sample-v2-code/logo_java.png) | [Поток кода устройства](msal-authentication-flows.md#device-code) |[MS-Identity-Java-девицекодефлов](https://github.com/Azure-Samples/ms-identity-java-devicecodeflow) |
| Классическое приложение (консольное)   | ![На этом рисунке показан логотип Python](media/sample-v2-code/logo_python.png) | [Поток кода устройства](msal-authentication-flows.md#device-code) |[MS-Identity-Python-девицекодефлов](https://github.com/Azure-Samples/ms-identity-python-devicecodeflow) |

## <a name="web-apis"></a>Веб-API

В следующих примерах показано, как защитить веб-API с помощью конечной точки платформы Microsoft Identity и как вызывать подчиненный API из веб-API.

| Платформа | Пример |
| -------- | ------------------- |
| ![На этом рисунке показан логотип ASP.NET Core](media/sample-v2-code/logo_NETcore.png)</p>ASP.NET Core | ASP.NET Core веб-API (служба) [DotNet-Native-aspnetcore-v2](https://aka.ms/msidentity-aspnetcore-webapi-calls-msgraph)  |
| ![На этом рисунке показан логотип ASP.NET](media/sample-v2-code/logo_NET.png)</p>ASP.NET MVC 3 | Веб-API (служба) [MS-Identity-ASPNET-webapi-OnBehalfOf](https://github.com/Azure-Samples/ms-identity-aspnet-webapi-onbehalfof) |
| ![На этом рисунке показан логотип Java](media/sample-v2-code/logo_java.png) | Веб-API (служба) [MS-Identity-Java-webapi](https://github.com/Azure-Samples/ms-identity-java-webapi) |
| ![На этом рисунке показан логотип Node. js](media/sample-v2-code/logo_nodejs.png) | Веб-API (служба) [Active-Directory-JavaScript-NodeJS-webapi-v2](https://github.com/Azure-Samples/active-directory-javascript-nodejs-webapi-v2) |
| ![На этом рисунке показан логотип Node. js](media/sample-v2-code/logo_nodejs.png) | Веб-API B2C (служба) для [Active-Directory-B2C-JavaScript-NodeJS-webapi](https://github.com/Azure-Samples/active-directory-b2c-javascript-nodejs-webapi) |

## <a name="azure-functions-as-web-apis"></a>Функции Azure в качестве веб-API

В следующих примерах показано, как защитить функцию Azure с помощью HttpTrigger и предоставить доступ к веб-API с помощью конечной точки платформы идентификации Майкрософт, а также как вызвать подчиненный API из веб-API.

| Платформа | Пример |
| -------- | ------------------- |
| ![На этом рисунке показан логотип ASP.NET Core](media/sample-v2-code/logo_NETcore.png)</p>ASP.NET Core | ASP.NET Core службы веб-API (служба) Azure [DotNet-Native-aspnetcore-v2](https://github.com/Azure-Samples/ms-identity-dotnet-webapi-azurefunctions)  |
| ![На этом рисунке показан логотип Node. js](media/sample-v2-code/logo_nodejs.png)</p>Node.js | Веб-API (служба) [NodeJS и Passport-Azure-AD](https://github.com/Azure-Samples/ms-identity-nodejs-webapi-azurefunctions) |
| ![На этом рисунке показан логотип Python](media/sample-v2-code/logo_python.png)</p>Python | Веб-API (служба) [Python](https://github.com/Azure-Samples/ms-identity-python-webapi-azurefunctions) |
| ![На этом рисунке показан логотип Node. js](media/sample-v2-code/logo_nodejs.png)</p>Node.js | Веб-API (служба) [NodeJS и Passport-AD, используя от имени](https://github.com/Azure-Samples/ms-identity-nodejs-webapi-onbehalfof-azurefunctions) |

## <a name="other-microsoft-graph-samples"></a>Другие примеры Microsoft Graph

Дополнительные сведения о [примерах](https://github.com/microsoftgraph/msgraph-community-samples/tree/master/samples#aspnet) и учебниках, в которых приводятся различные шаблоны использования API Microsoft Graph, включая аутентификацию в Azure AD, см. в статье [Microsoft Graph Community samples & tutorials](https://github.com/microsoftgraph/msgraph-community-samples) (Примеры и учебники сообщества Microsoft Graph).

## <a name="see-also"></a>См. также раздел

- [Azure Active Directory (версия 1.0): руководством разработчика](../azuread-dev/v1-overview.md)
- [Общие сведения о Microsoft Graph API и справочнике](https://docs.microsoft.com/graph/use-the-api?context=graph%2Fapi%2Fbeta&view=graph-rest-beta)
