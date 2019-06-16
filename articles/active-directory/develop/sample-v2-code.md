---
title: Примеры кода для платформы удостоверений Microsoft | Документация Майкрософт
description: Предоставляет список доступных удостоверений Microsoft примеры кода для платформ (конечной точки версии 2), сгруппированных по сценарию.
services: active-directory
documentationcenter: dev-center-name
author: rwike77
manager: CelesteDG
editor: ''
ms.assetid: a242a5ff-7300-40c2-ba83-fb6035707433
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 10/26/2018
ms.author: ryanwi
ms.reviewer: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 327111182a4ad8958f2d852a88ba0608d8510c51
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/13/2019
ms.locfileid: "67055905"
---
# <a name="microsoft-identity-platform-code-samples-v20-endpoint"></a>Примеры кода платформы удостоверений Microsoft (конечная точка версии 2.0)

[!INCLUDE [active-directory-develop-applies-v2-msal](../../../includes/active-directory-develop-applies-v2-msal.md)]

Возможности платформы удостоверений Майкрософт:

- добавление возможностей аутентификации и авторизации в веб-приложения и веб-API;
- требование маркера доступа для доступа к защищенным веб-API.

В этой статье Краткое описание и содержит ссылки на примеры для конечной точки платформы Microsoft identity. Ссылки перенаправят вас к примерам, в которых показано, как это можно сделать, и фрагментам кода, которые можно использовать в приложениях. На странице примеров кода приведены ссылки на статьи с подробными сведениями о требованиях, установке и настройке. Код указан с комментариями, чтобы можно было понять важные разделы.

> [!NOTE]
> Если вас интересуют примеры для версии 1.0, ознакомьтесь со статьей с [примерами кода Azure AD (конечная точка версии 1.0)](sample-v1-code.md).

Чтобы понять основной сценарий для каждого типа примера, см. в разделе [типы приложений для конечной точки платформы удостоверений Microsoft](v2-app-types.md).

Вы также можете поделиться своими примерами на сайте GitHub. Чтобы узнать, как это сделать, обратитесь к разделу [Примеры и документация Microsoft Azure Active Directory](https://github.com/Azure-Samples?page=3&query=active-directory).

## <a name="single-page-applications-spa"></a>Одностраничные приложения (SPA)

В этих примерах показано, как написать одностраничных приложений, защищенных с помощью платформы удостоверений Microsoft. В этих примерах используется одна из разновидностей MSAL.js:

| платформа | Описание | Ссылка |
| -------- | --------------------- | -------- |
| ![JavaScript](media/sample-v2-code/logo_js.png) [JavaScript (msal.js)](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-core) | Вызывает Microsoft Graph |[javascript-graphapi-web-v2](https://github.com/Azure-Samples/active-directory-javascript-graphapi-web-v2) |
| ![JavaScript](media/sample-v2-code/logo_js.png) [JavaScript (msal.js)](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-core) | Вызывает B2C |[b2c-javascript-msal-singlepageapp](https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp) |
| ![JavaScript](media/sample-v2-code/logo_js.png) [JavaScript (msal.js)](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-core) | Собственные веб-вызовы API |[javascript-singlepageapp-dotnet-webapi-v2](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-dotnet-webapi-v2) |
| ![Использование Angularjs](media/sample-v2-code/logo_angular.png) [JavaScript (MSAL AngularJS)](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-angularjs)| Вызывает Microsoft Graph  | [MsalAngularjsDemoApp](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-angularjs/samples/MsalAngularjsDemoApp)
| ![Angular](media/sample-v2-code/logo_angular.png) [JavaScript (MSAL Angular)](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-angular)| Вызывает Microsoft Graph  | [MSALAngularDemoApp](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-angular/samples/MSALAngularDemoApp) |

## <a name="web-applications"></a>Веб-приложения

В следующем примере показаны веб-приложения для входа пользователей. В некоторых примерах приложение вызывает Microsoft Graph или веб-API с использованием удостоверения пользователя.

| платформа | Только вход пользователей | Выполняет вход пользователей и вызывает Microsoft Graph |
| -------- | ------------------- | --------------------------------- |
| ![ASP.NET Core](media/sample-v2-code/logo_NETcore.png)</p>ASP.NET Core 2.2 | [Руководство по ASP.NET Core веб-приложение выполняет вход пользователей](https://aka.ms/aspnetcore-webapp-sign-in) | Так же образец в [веб-приложения ASP.NET Core вызывает Microsoft Graph](https://aka.ms/aspnetcore-webapp-call-msgraph) этапа |
| ![ASP.NET:](media/sample-v2-code/logo_NETframework.png)</p> ASP.NET: | [Краткое руководство по ASP.NET](https://github.com/AzureAdQuickstarts/AppModelv2-WebApp-OpenIDConnect-DotNet) </p> [dotnet-webapp-openidconnect-v2](https://github.com/azure-samples/active-directory-dotnet-webapp-openidconnect-v2)  |  [dotnet-admin-restricted-scopes-v2](https://github.com/azure-samples/active-directory-dotnet-admin-restricted-scopes-v2) </p> |[msgraph-training-aspnetmvcapp](https://github.com/microsoftgraph/msgraph-training-aspnetmvcapp)
| ![Node.js](media/sample-v2-code/logo_nodejs.png)  |                   | [Краткое руководство по Node.js](https://github.com/azureadquickstarts/appmodelv2-webapp-openidconnect-nodejs) |
| ![Ruby](media/sample-v2-code/logo_ruby.png) |                   | [msgraph-training-rubyrailsapp](https://github.com/microsoftgraph/msgraph-training-rubyrailsapp) |

## <a name="desktop-and-mobile-public-client-apps"></a>Общедоступные клиентские приложения — классические и мобильные

В следующих примерах показаны общедоступные клиентские приложения (классические и мобильные), которые обращаются к API Microsoft Graph или веб-API от имени пользователя. Все эти клиентские приложения используют библиотеки аутентификации Майкрософт (MSAL).

| Клиентское приложение | платформа | Поток или предоставление | Вызывает Microsoft Graph | Вызывает веб-API ASP.NET Core 2.0 |
| ------------------ | -------- |  ----------| ---------- | ------------------------- |
| Классическое приложение (WPF)      | ![.NET и C#](media/sample-v2-code/logo_NET.png) | [интерактивный](msal-authentication-flows.md#interactive)| [dotnet-desktop-msgraph-v2](https://github.com/azure-samples/active-directory-dotnet-desktop-msgraph-v2) | [dotnet-native-aspnetcore-v2](https://aka.ms/msidentity-aspnetcore-webapi) |
| Классическое приложение (консольное)   | ![.NET /C# (классическое приложение)](media/sample-v2-code/logo_NET.png) | [Встроенная аутентификация Windows](msal-authentication-flows.md#integrated-windows-authentication) | [dotnet-iwa-v2](https://github.com/azure-samples/active-directory-dotnet-iwa-v2) |  |
| Классическое приложение (консольное)   | ![.NET /C# (классическое приложение)](media/sample-v2-code/logo_NETcore.png) | [Имя пользователя или пароль](msal-authentication-flows.md#usernamepassword) |[dotnetcore-up-v2](https://github.com/azure-samples/active-directory-dotnetcore-console-up-v2) |  |
| Мобильное приложение (Android, iOS, UWP)   | ![.NET и C# (Xamarin)](media/sample-v2-code/logo_xamarin.png) | [интерактивный](msal-authentication-flows.md#interactive) |[xamarin-native-v2](https://github.com/azure-samples/active-directory-xamarin-native-v2) |  |
| Мобильное приложение (iOS)       | ![iOS, Objective-C или swift](media/sample-v2-code/logo_iOS.png) | [интерактивный](msal-authentication-flows.md#interactive) |[ios-swift-native-v2](https://github.com/azure-samples/active-directory-ios-swift-native-v2) </p> [ios-native-nxoauth2-v2](https://github.com/azure-samples/active-directory-ios-native-nxoauth2-v2) |  |
| Мобильное приложение (Android)   | ![Android и Java](media/sample-v2-code/logo_Android.png) | [интерактивный](msal-authentication-flows.md#interactive) |  [android-native-v2](https://github.com/azure-samples/active-directory-android-native-v2 ) |  |

## <a name="daemon-applications"></a>Управляющие программы

В следующем примере показано приложение, которое обращается к Microsoft Graph с использованием собственного удостоверения (без пользователя).

| Клиентское приложение | платформа | Поток или предоставление | Вызывает Microsoft Graph |
| ------------------ | -------- | ---------- | -------------------- |
| Консоль | ![.NET Core](media/sample-v2-code/logo_NETcore.png)</p> ASP.NET:  | [Учетные данные клиента](msal-authentication-flows.md#client-credentials) | [dotnetcore-daemon-v2](https://github.com/azure-samples/active-directory-dotnetcore-daemon-v2) |
| Веб-приложение | ![ASP.NET:](media/sample-v2-code/logo_NETframework.png)</p> ASP.NET:  | [Учетные данные клиента](msal-authentication-flows.md#client-credentials) | [dotnet-daemon-v2](https://github.com/azure-samples/active-directory-dotnet-daemon-v2) |

## <a name="headless-applications"></a>Приложения удаленного управления

В следующем примере показано общедоступное клиентское приложение, запущенное на устройстве без веб-браузера. Это приложение может быть программой командной строки, приложением для Linux или Mac либо приложением Интернета вещей. В примере представлено приложение, которое обращается к API Microsoft Graph от имени пользователя, который в интерактивном режиме выполняет вход на другом устройстве (например, на мобильном телефоне). Это клиентское приложение использует библиотеки аутентификации Майкрософт (MSAL).

| Клиентское приложение | платформа | Поток или предоставление | Вызывает Microsoft Graph |
| ------------------ | -------- |  ----------| ---------- |
| Классическое приложение (консольное)   | ![.NET /C# (классическое приложение)](media/sample-v2-code/logo_NETcore.png) | [Поток кода устройства](msal-authentication-flows.md#device-code) |[dotnetcore-devicecodeflow-v2](https://github.com/azure-samples/active-directory-dotnetcore-devicecodeflow-v2) |

## <a name="web-apis"></a>Веб-API

В следующем примере демонстрируются как защитить веб-API с конечной точкой платформы Microsoft identity, а также как вызывать API нижнего уровня из веб-API.

| платформа | Образец |
| -------- | ------------------- |
| ![ASP.NET Core](media/sample-v2-code/logo_NETcore.png)</p>ASP.NET Core 2.2 | ASP.NET Core веб-API (служба) из [dotnet-машинный код aspnetcore-v2](https://aka.ms/msidentity-aspnetcore-webapi-calls-msgraph)  |
| ![ASP.NET:](media/sample-v2-code/logo_NET.png)</p>ASP.NET MVC 3 | Веб-API (служба) [ms-identity-aspnet-webapi-onbehalfof](https://github.com/Azure-Samples/ms-identity-aspnet-webapi-onbehalfof) |

## <a name="other-microsoft-graph-samples"></a>Другие примеры Microsoft Graph

Дополнительные сведения о [примерах](https://github.com/microsoftgraph/msgraph-community-samples/tree/master/samples#aspnet) и учебниках, в которых приводятся различные шаблоны использования API Microsoft Graph, включая аутентификацию в Azure AD, см. в статье [Microsoft Graph Community samples & tutorials](https://github.com/microsoftgraph/msgraph-community-samples) (Примеры и учебники сообщества Microsoft Graph).

## <a name="see-also"></a>См. также

- [Active Directory (версия 1.0) Руководство разработчика по Azure](v1-overview.md)
- [Основные понятия и справочные материалы по Azure AD API Graph](https://msdn.microsoft.com/library/azure/hh974476.aspx)
- [Библиотека вспомогательных методов для Azure AD Graph API](https://www.nuget.org/packages/Microsoft.Azure.ActiveDirectory.GraphClient)
