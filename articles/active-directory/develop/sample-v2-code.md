---
title: Примеры кода Azure Active Directory | Документы Майкрософт
description: Список доступных примеров кода Azure Active Directory (конечная точка версии 2), сгруппированных по сценарию.
services: active-directory
documentationcenter: dev-center-name
author: CelesteDG
manager: mtillman
editor: ''
ms.assetid: a242a5ff-7300-40c2-ba83-fb6035707433
ms.service: active-directory
ms.component: develop
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 10/26/2018
ms.author: celested
ms.reviewer: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 4a2cb6fa310cc56b4c5ccb1cb625b88431f70783
ms.sourcegitcommit: 0b7fc82f23f0aa105afb1c5fadb74aecf9a7015b
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/14/2018
ms.locfileid: "51633858"
---
# <a name="azure-active-directory-code-samples-v20-endpoint"></a>Примеры кода Azure Active Directory (конечная точка версии 2.0)

[!INCLUDE [active-directory-develop-applies-v2-msal](../../../includes/active-directory-develop-applies-v2-msal.md)]

Возможности платформы удостоверений Майкрософт:

- добавление возможностей аутентификации и авторизации в веб-приложения и веб-API;
- требование маркера доступа для доступа к защищенным веб-API.

В этой статье приводится краткое описание и ссылки на примеры для конечной точки Azure AD версии 2.0. Ссылки перенаправят вас к примерам, в которых показано, как это можно сделать, и фрагментам кода, которые можно использовать в приложениях. На странице примеров кода приведены ссылки на статьи с подробными сведениями о требованиях, установке и настройке. Код указан с комментариями, чтобы можно было понять важные разделы.

> [!NOTE]
> Если вас интересуют примеры для версии 1.0, ознакомьтесь со статьей с [примерами кода Azure AD (конечная точка версии 1.0)](sample-v1-code.md).

Чтобы понять основной сценарий для каждого типа примера, см. раздел о [типах приложений для конечной точки Azure Active Directory версии 2.0](v2-app-types.md).

Вы также можете поделиться своими примерами на сайте GitHub. Чтобы узнать, как это сделать, обратитесь к разделу [Примеры и документация Microsoft Azure Active Directory](https://github.com/Azure-Samples?page=3&query=active-directory).

## <a name="single-page-applications-spa"></a>Одностраничные приложения (SPA)

В этих примерах показано, как создать одностраничное приложение, защищенное с помощью Azure AD. В этих примерах используется одна из разновидностей MSAL.js:

* [библиотека аутентификации Майкрософт для JavaScript](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-core);
* [библиотека аутентификации Майкрософт для Angular](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-angular);
* [библиотека аутентификации Майкрософт для AngularJS](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-angularjs).

 платформа |  Вызывает Microsoft Graph
 -------- |  ---------------------
![JavaScript](media/sample-v2-code/logo_js.png) JavaScript (msal.js)  | [javascript-graphapi-web-v2](https://github.com/Azure-Samples/active-directory-javascript-graphapi-web-v2)
![Angular JS](media/sample-v2-code/logo_angular.png) JavaScript (MSAL AngularJS) | [MsalAngularjsDemoApp](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-angularjs/samples/MsalAngularjsDemoApp)
![Angular](media/sample-v2-code/logo_angular.png) JavaScript (MSAL Angular) | [MSALAngularDemoApp](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-angular/samples/MSALAngularDemoApp)

## <a name="web-applications"></a>Веб-приложения

В следующем примере показаны веб-приложения для входа пользователей. В некоторых примерах приложение вызывает Microsoft Graph или веб-API с использованием удостоверения пользователя.

 платформа | Только вход пользователей | Выполняет вход пользователей и вызывает Microsoft Graph
 -------- | ------------------- | ---------------------------------
![ASP.NET Core;](media/sample-v2-code/logo_NETcore.png)<p/>ASP.NET Core 2.1 | [aspnetcore-webapp-openidconnect-v2](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2) | Тот же образец в ветви [aspnetcore2-2-signInAndCallGraph](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/aspnetcore2-2-signInAndCallGraph)
![ASP.NET:](media/sample-v2-code/logo_NETframework.png)<p/> ASP.NET: | [Краткое руководство по ASP.NET](https://GitHub.com/AzureAdQuickstarts/AppModelv2-WebApp-OpenIDConnect-DotNet) <p/> [dotnet-webapp-openidconnect-v2](https://GitHub.com/azure-samples/active-directory-dotnet-webapp-openidconnect-v2)  |  [dotnet-admin-restricted-scopes-v2](https://github.com/azure-samples/active-directory-dotnet-admin-restricted-scopes-v2) <p/>[msgraph-training-aspnetmvcapp](https://github.com/microsoftgraph/msgraph-training-aspnetmvcapp)
![Node.js](media/sample-v2-code/logo_nodejs.png)  |                   | [Краткое руководство по Node.js](https://github.com/azureadquickstarts/appmodelv2-webapp-openidconnect-nodejs)
![Ruby](media/sample-v2-code/logo_ruby.png) |                   | [msgraph-training-rubyrailsapp](https://github.com/microsoftgraph/msgraph-training-rubyrailsapp)

## <a name="desktop-and-mobile-public-client-apps"></a>Общедоступные клиентские приложения — классические и мобильные

В следующих примерах показаны общедоступные клиентские приложения (классические и мобильные), которые обращаются к API Microsoft Graph или веб-API от имени пользователя. Все эти клиентские приложения используют библиотеки аутентификации Майкрософт (MSAL).

Клиентское приложение | платформа | Поток или предоставление | Вызывает Microsoft Graph | Вызывает веб-API ASP.NET Core 2.0
------------------ | -------- |  ----------| ---------- | -------------------------
Классическое приложение (WPF)      | ![.NET и C#](media/sample-v2-code/logo_NET.png) | интерактивный | [dotnet-desktop-msgraph-v2](http://github.com/azure-samples/active-directory-dotnet-desktop-msgraph-v2) | [dotnet-native-aspnetcore-v2](https://GitHub.com/azure-samples/active-directory-dotnet-native-aspnetcore-v2)
Классическое приложение (консольное)   | ![.NET /C# (классическое приложение)](media/sample-v2-code/logo_NET.png) | Встроенная проверка подлинности Windows |[dotnet-iwa-v2](https://github.com/azure-samples/active-directory-dotnet-iwa-v2)
Классическое приложение (консольное)   | ![.NET /C# (классическое приложение)](media/sample-v2-code/logo_NETcore.png) | Имя пользователя или пароль |[dotnetcore-up-v2](https://github.com/azure-samples/active-directory-dotnetcore-console-up-v2)
Мобильное приложение (UWP)   | ![.NET и C# (UWP)](media/sample-v2-code/logo_windows.png) | интерактивный |[dotnet-native-uwp-v2](https://github.com/azure-samples/active-directory-dotnet-native-uwp-v2) |
Мобильное приложение (Android, iOS, UWP)   | ![.NET и C# (Xamarin)](media/sample-v2-code/logo_xamarin.png) | интерактивный |[xamarin-native-v2](https://Github.com/azure-samples/active-directory-xamarin-native-v2) |
Мобильное приложение (iOS)       | ![iOS, Objective-C или swift](media/sample-v2-code/logo_iOS.png) | интерактивный |[ios-swift-native-v2](https://github.com/azure-samples/active-directory-ios-swift-native-v2) <p/> [ios-native-nxoauth2-v2](https://github.com/azure-samples/active-directory-ios-native-nxoauth2-v2) |
Мобильное приложение (Android)   | ![Android и Java](media/sample-v2-code/logo_Android.png) | интерактивный |  [android-native-v2](https://github.com/azure-samples/active-directory-android-native-v2 ) |

## <a name="daemon-applications"></a>Управляющие программы

В следующем примере показано приложение, которое обращается к Microsoft Graph с использованием собственного удостоверения (без пользователя).

Клиентское приложение | платформа | Поток или предоставление | Вызывает Microsoft Graph
------------------ | -------- | ---------- | --------------------
Консоль | ![.NET Core](media/sample-v2-code/logo_NETcore.png)<p/> ASP.NET:  | Учетные данные клиента | [dotnetcore-daemon-v2](https://github.com/azure-samples/active-directory-dotnetcore-daemon-v2)
Веб-приложение | ![ASP.NET:](media/sample-v2-code/logo_NETframework.png)<p/> ASP.NET:  | Учетные данные клиента | [dotnet-daemon-v2](https://github.com/azure-samples/active-directory-dotnet-daemon-v2)

## <a name="headless-applications"></a>Приложения удаленного управления

В следующем примере показано общедоступное клиентское приложение, запущенное на устройстве без веб-браузера. Это приложение может быть программой командной строки, приложением для Linux или Mac либо приложением Интернета вещей. В примере представлено приложение, которое обращается к API Microsoft Graph от имени пользователя, который в интерактивном режиме выполняет вход на другом устройстве (например, на мобильном телефоне). Это клиентское приложение использует библиотеки аутентификации Майкрософт (MSAL).

Клиентское приложение | платформа | Поток или предоставление | Вызывает Microsoft Graph
------------------ | -------- |  ----------| ----------
Классическое приложение (консольное)   | ![.NET /C# (классическое приложение)](media/sample-v2-code/logo_NETcore.png) | Последовательность кода устройства |[dotnetcore-devicecodeflow-v2](https://github.com/azure-samples/active-directory-dotnetcore-devicecodeflow-v2)

## <a name="web-apis"></a>Веб-API

В следующем примере показано, как защитить веб-API с помощью конечной точки Azure AD версии 2.0. Этот API выполняется приложением WPF, но может вызываться любым приложением.

платформа | Образец
 -------- | -------------------
![.NET и C#](media/sample-v2-code/logo_NET.png) | Веб-API (служба) [dotnet-native-aspnetcore-v2](https://GitHub.com/azure-samples/active-directory-dotnet-native-aspnetcore-v2)

## <a name="other-microsoft-graph-samples"></a>Другие примеры Microsoft Graph

Дополнительные сведения о [примерах](https://github.com/microsoftgraph/msgraph-community-samples/tree/master/samples#aspnet) и учебниках, в которых приводятся различные шаблоны использования API Microsoft Graph, включая аутентификацию в Azure AD, см. в статье [Microsoft Graph Community samples & tutorials](https://github.com/microsoftgraph/msgraph-community-samples) (Примеры и учебники сообщества Microsoft Graph).

## <a name="see-also"></a>См. также

[Руководство разработчика по Azure Active Directory](v1-overview.md)

[Основные понятия и справочные материалы по Azure AD API Graph](https://msdn.microsoft.com/library/azure/hh974476.aspx)

[Библиотека вспомогательных методов для Azure AD Graph API](https://www.nuget.org/packages/Microsoft.Azure.ActiveDirectory.GraphClient)
