---
title: Образцы кода для платформы идентификации Майкрософт Документы Майкрософт
description: Обеспечивает индекс доступных образцов кода платформы идентификации Майкрософт (v2.0 endpoint), организованный по сценарию.
services: active-directory
documentationcenter: dev-center-name
author: rwike77
manager: CelesteDG
editor: ''
ms.assetid: a242a5ff-7300-40c2-ba83-fb6035707433
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/15/2019
ms.author: ryanwi
ms.reviewer: jmprieur
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: 2420c98a95eb9e5cdfee36bbd9bec20d22ad24ab
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79460608"
---
# <a name="microsoft-identity-platform-code-samples-v20-endpoint"></a>Образцы кода идентификационной платформы Майкрософт (конечная точка v2.0)

Возможности платформы удостоверений Майкрософт:

- добавление возможностей аутентификации и авторизации в веб-приложения и веб-API;
- требование маркера доступа для доступа к защищенным веб-API.

В этой статье кратко описывается и предоставляется ссылки на образцы для конечных точек платформы идентификации Майкрософт. Эти примеры показывают, как это делается, а также предоставляют фрагменты кода, которые можно использовать в приложениях. На странице образца кода вы найдете подробные темы чтения, которые помогут с требованиями, установкой и настройкой. Комментарии в коде помогут вам понять критические разделы.

> [!NOTE]
> Если вас интересуют образцы v1.0, [см.](../azuread-dev/sample-v1-code.md)

Чтобы понять базовый сценарий для каждого типа образца, см. [типы приложений для конечных точек платформы идентификации Майкрософт.](v2-app-types.md)

Вы также можете поделиться своими примерами на сайте GitHub. Чтобы узнать, как это сделать, обратитесь к разделу [Примеры и документация Microsoft Azure Active Directory](https://github.com/Azure-Samples?page=3&query=active-directory).

## <a name="single-page-applications"></a>Одностраничные приложения

Эти примеры показывают, как написать одностраничное приложение, защищенное платформой идентификации Майкрософт. Эти образцы используют один из вкусов MSAL.js.

| Платформа | Описание | Ссылка |
| -------- | --------------------- | -------- |
| ![На этом изображении](media/sample-v2-code/logo_js.png) показан логотип JavaScript [JavaScript (msal.js)](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-core) | Вызывает Microsoft Graph |[javascript-graphapi-v2](https://github.com/Azure-Samples/active-directory-javascript-graphapi-v2) |
| ![На этом изображении](media/sample-v2-code/logo_js.png) показан логотип JavaScript [JavaScript (msal.js)](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-core) | Вызовы B2C |[b2c-javascript-msal-singlepageapp](https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp) |
| ![На этом изображении показан](media/sample-v2-code/logo_angular.png) логотип Angular JS [JavaScript (MSAL AngularJS)](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-angularjs)| Вызывает Microsoft Graph  | [MsalAngularjsDemoApp](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/samples/MsalAngularjsDemoApp)
| ![На этом изображении](media/sample-v2-code/logo_angular.png) изображен логотип Angular [JavaScript (MSAL Angular)](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-angular)| Вызывает Microsoft Graph  | [javascript-singlepageapp-угловой](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-angular) |
| ![На этом изображении](media/sample-v2-code/logo_angular.png) изображен логотип Angular [JavaScript (MSAL Angular)](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-core) | Вызовы B2C |[активный каталог-b2c-javascript-угловой-спа](https://github.com/Azure-Samples/active-directory-b2c-javascript-angular-spa) |

## <a name="web-applications"></a>Веб-приложения

В следующем примере показаны веб-приложения для входа пользователей. В некоторых примерах приложение вызывает Microsoft Graph или веб-API с использованием удостоверения пользователя.

| Платформа | Только вход пользователей | Выполняет вход пользователей и вызывает Microsoft Graph |
| -------- | ------------------- | --------------------------------- |
| ![На этом изображении изображен логотип ASP.NET Core](media/sample-v2-code/logo_NETcore.png)</p>ASP.NET ядро 2.2 | [ASP.NET Core WebApp подписи в пользователей учебник](https://aka.ms/aspnetcore-webapp-sign-in) | Тот же пример в [ASP.NET Core Web App называет этап Microsoft Graph](https://aka.ms/aspnetcore-webapp-call-msgraph) |
| ![На этом изображении изображен логотип ASP.NET](media/sample-v2-code/logo_NETframework.png)</p> ASP.NET | [Краткое руководство по ASP.NET](https://github.com/AzureAdQuickstarts/AppModelv2-WebApp-OpenIDConnect-DotNet) </p> [dotnet-webapp-openidconnect-v2](https://github.com/azure-samples/active-directory-dotnet-webapp-openidconnect-v2)  |  [dotnet-admin-restricted-scopes-v2](https://github.com/azure-samples/active-directory-dotnet-admin-restricted-scopes-v2) </p> |[msgraph-training-aspnetmvcapp](https://github.com/microsoftgraph/msgraph-training-aspnetmvcapp)
| ![На этом изображении изображен логотип Java](media/sample-v2-code/logo_java.png)  |                   | [ms-identity-java-webapp](https://github.com/Azure-Samples/ms-identity-java-webapp) |
| ![На этом изображении изображен логотип Python](media/sample-v2-code/logo_python.png)  |                   | [ms-identity-python-webapp](https://github.com/Azure-Samples/ms-identity-python-webapp) |
| ![На этом изображении изображен логотип Ruby](media/sample-v2-code/logo_ruby.png) |                   | [msgraph-training-rubyrailsapp](https://github.com/microsoftgraph/msgraph-training-rubyrailsapp) |

## <a name="desktop-and-mobile-public-client-apps"></a>Общедоступные клиентские приложения — классические и мобильные

Следующие примеры показывают общедоступные клиентские приложения (настольные или мобильные приложения), которые получают доступ к API Microsoft Graph, или ваш собственный веб-aPI от имени пользователя. Все эти клиентские приложения используют библиотеку подлинности Майкрософт (MSAL).

| Клиентское приложение | Платформа | Поток или предоставление | Вызывает Microsoft Graph | Вызывает ASP.NET Web API Core 2.0 |
| ------------------ | -------- |  ----------| ---------- | ------------------------- |
| Классическое приложение (WPF)      | ![На этом снимке изображен логотип .NET/C](media/sample-v2-code/logo_NET.png) | [Интерактивная](msal-authentication-flows.md#interactive)| [dotnet-desktop-msgraph-v2](https://github.com/azure-samples/active-directory-dotnet-desktop-msgraph-v2) | [dotnet-native-aspnetcore-v2](https://aka.ms/msidentity-aspnetcore-webapi) |
| Классическое приложение (консольное)   | ![На этом снимке изображен логотип .NET/C е (Desktop)](media/sample-v2-code/logo_NET.png) | [Встроенная проверка подлинности Windows](msal-authentication-flows.md#integrated-windows-authentication) | [dotnet-iwa-v2](https://github.com/azure-samples/active-directory-dotnet-iwa-v2) |  |
| Классическое приложение (консольное)   | ![На этом изображении изображен логотип Java](media/sample-v2-code/logo_java.png) | [Встроенная проверка подлинности Windows](msal-authentication-flows.md#integrated-windows-authentication) |[ms-identity-java-desktop](https://github.com/Azure-Samples/ms-identity-java-desktop/) |  |
| Классическое приложение (консольное)   | ![На этом снимке изображен логотип .NET/C е (Desktop)](media/sample-v2-code/logo_NETcore.png) | [Имя пользователя или пароль](msal-authentication-flows.md#usernamepassword) |[dotnetcore-up-v2](https://github.com/azure-samples/active-directory-dotnetcore-console-up-v2) |  |
| Рабочий стол (консоль) с WAM  | ![На этом снимке изображен логотип .NET/C е (Desktop)](media/sample-v2-code/logo_NETcore.png) | [интерактивный с WAM](msal-authentication-flows.md#interactive) |[dotnet-native-uwp-wam](https://github.com/azure-samples/active-directory-dotnet-native-uwp-wam) |  |
| Классическое приложение (консольное)   | ![На этом изображении изображен логотип Java](media/sample-v2-code/logo_java.png) | [Имя пользователя или пароль](msal-authentication-flows.md#usernamepassword) |[ms-identity-java-desktop](https://github.com/Azure-Samples/ms-identity-java-desktop/) |  |
| Классическое приложение (консольное)   | ![На этом изображении изображен логотип Python](media/sample-v2-code/logo_python.png) | [Имя пользователя или пароль](msal-authentication-flows.md#usernamepassword) |[ms-identity-python-desktop](https://github.com/Azure-Samples/ms-identity-python-desktop) |  |
| Мобильное приложение (Android, iOS, UWP)   | ![На этом снимке изображен логотип .NET/Cе (Xamarin)](media/sample-v2-code/logo_xamarin.png) | [Интерактивная](msal-authentication-flows.md#interactive) |[xamarin-native-v2](https://github.com/azure-samples/active-directory-xamarin-native-v2) |  |
| Мобильное приложение (iOS)       | ![На этом снимке показаны iOS/Objective-C или Swift](media/sample-v2-code/logo_iOS.png) | [Интерактивная](msal-authentication-flows.md#interactive) |[ios-swift-objc-родной-v2](https://github.com/azure-samples/active-directory-ios-swift-native-v2) </p> [ios-native-nxoauth2-v2](https://github.com/azure-samples/active-directory-ios-native-nxoauth2-v2) |  |
| Рабочий стол (macOS)       | macOS | [Интерактивная](msal-authentication-flows.md#interactive) |[macOS-swift-objc-native-v2](https://github.com/Azure-Samples/ms-identity-macOS-swift-objc) |  |
| Мобильный (Android-Java)   | ![На этом изображении изображен логотип Android](media/sample-v2-code/logo_Android.png) | [Интерактивная](msal-authentication-flows.md#interactive) |  [андроид-Ява](https://github.com/Azure-Samples/ms-identity-android-java) |  |
| Мобильный (Android-Котлин)   | ![На этом изображении изображен логотип Android](media/sample-v2-code/logo_Android.png) | [Интерактивная](msal-authentication-flows.md#interactive) |  [андроид-Котлин](https://github.com/Azure-Samples/ms-identity-android-kotlin) |  |

## <a name="daemon-applications"></a>Управляющие программы

В следующем примере показано приложение, которое обращается к Microsoft Graph с использованием собственного удостоверения (без пользователя).

| Клиентское приложение | Платформа | Поток или предоставление | Вызывает Microsoft Graph |
| ------------------ | -------- | ---------- | -------------------- |
| Консоль | ![На этом изображении изображен логотип .NET Core](media/sample-v2-code/logo_NETcore.png)</p> ASP.NET  | [Учетные данные клиентов](msal-authentication-flows.md#client-credentials) | [dotnetcore-daemon-v2](https://github.com/azure-samples/active-directory-dotnetcore-daemon-v2) |
| Веб-приложение | ![На этом изображении изображен логотип ASP.NET](media/sample-v2-code/logo_NETframework.png)</p> ASP.NET  | [Учетные данные клиентов](msal-authentication-flows.md#client-credentials) | [dotnet-daemon-v2](https://github.com/azure-samples/active-directory-dotnet-daemon-v2) |
| Консоль | ![На этом изображении изображен логотип Java](media/sample-v2-code/logo_java.png) | [Учетные данные клиентов](msal-authentication-flows.md#client-credentials) | [ms-identity-java-daemon](https://github.com/Azure-Samples/ms-identity-java-daemon) |
| Консоль | ![На этом изображении изображен логотип Python](media/sample-v2-code/logo_python.png) | [Учетные данные клиентов](msal-authentication-flows.md#client-credentials) | [ms-identity-python-daemon](https://github.com/Azure-Samples/ms-identity-python-daemon) |

## <a name="headless-applications"></a>Приложения удаленного управления

В следующем примере показано общедоступное клиентское приложение, запущенное на устройстве без веб-браузера. Приложение может быть инструментом командной строки, приложением, работающим на Linux или Mac, или приложением IoT. В примере есть приложение, доступ к Microsoft Graph API, от имени пользователя, который вписывается в интерактивно на другом устройстве (например, мобильный телефон). Это клиентское приложение использует библиотеку подлинности Майкрософт (MSAL).

| Клиентское приложение | Платформа | Поток или предоставление | Вызывает Microsoft Graph |
| ------------------ | -------- |  ----------| ---------- |
| Классическое приложение (консольное)   | ![На этом снимке изображен логотип .NET/C е (Desktop)](media/sample-v2-code/logo_NETcore.png) | [Поток кода устройства](msal-authentication-flows.md#device-code) |[dotnetcore-devicecodeflow-v2](https://github.com/azure-samples/active-directory-dotnetcore-devicecodeflow-v2) |
| Классическое приложение (консольное)   | ![На этом изображении изображен логотип Java](media/sample-v2-code/logo_java.png) | [Поток кода устройства](msal-authentication-flows.md#device-code) |[ms-identity-java-devicecodeflow](https://github.com/Azure-Samples/ms-identity-java-devicecodeflow) |
| Классическое приложение (консольное)   | ![На этом изображении изображен логотип Python](media/sample-v2-code/logo_python.png) | [Поток кода устройства](msal-authentication-flows.md#device-code) |[ms-identity-python-devicecodeflow](https://github.com/Azure-Samples/ms-identity-python-devicecodeflow) |

## <a name="web-apis"></a>Веб-API

Следующие примеры показывают, как защитить веб-API с конечным пунктом платформы идентификации Майкрософт, и как вызвать aPI ниже по течению от Web API.

| Платформа | Пример |
| -------- | ------------------- |
| ![На этом изображении изображен логотип ASP.NET Core](media/sample-v2-code/logo_NETcore.png)</p>ASP.NET ядро 2.2 | ASP.NET Core web API (сервис) [dotnet-родной-aspnetcore-v2](https://aka.ms/msidentity-aspnetcore-webapi-calls-msgraph)  |
| ![На этом изображении изображен логотип ASP.NET](media/sample-v2-code/logo_NET.png)</p>ASP.NET MVC 3 | Web API (услуга) [ms-identity-aspnet-webapi-onbehalfof](https://github.com/Azure-Samples/ms-identity-aspnet-webapi-onbehalfof) |
| ![На этом изображении изображен логотип Java](media/sample-v2-code/logo_java.png) | Веб-API (услуга) [ms-identity-java-webapi](https://github.com/Azure-Samples/ms-identity-java-webapi) |
| ![На этом изображении изображен логотип Node.js](media/sample-v2-code/logo_nodejs.png) | Web API (сервис) [активного каталога-javascript-nodejs-webapi-v2](https://github.com/Azure-Samples/active-directory-javascript-nodejs-webapi-v2) |
| ![На этом изображении изображен логотип Node.js](media/sample-v2-code/logo_nodejs.png) | B2C Web API (сервис) [активного каталога-b2c-javascript-nodejs-webapi](https://github.com/Azure-Samples/active-directory-b2c-javascript-nodejs-webapi) |

## <a name="azure-functions-as-web-apis"></a>Функции Azure как веб-AA

Следующие примеры показывают, как защитить функцию Azure с помощью HttpTrigger и разоблачить веб-API с конечным пунктом платформы идентификации Майкрософт, а также как вызвать API ниже по течению от Web API.

| Платформа | Пример |
| -------- | ------------------- |
| ![На этом изображении изображен логотип ASP.NET Core](media/sample-v2-code/logo_NETcore.png)</p>ASP.NET ядро 2.2 | ASP.NET Core Web API (сервис) Azure Функция [Dotnet-родной-aspnetcore-v2](https://github.com/Azure-Samples/ms-identity-dotnet-webapi-azurefunctions)  |
| ![На этом изображении изображен логотип Node.js](media/sample-v2-code/logo_nodejs.png)</p>Node.js | Web API (услуга) [NodeJS и паспортно-azure-ad](https://github.com/Azure-Samples/ms-identity-nodejs-webapi-azurefunctions) |
| ![На этом изображении изображен логотип Python](media/sample-v2-code/logo_python.png)</p>Python | Веб-API (услуга) [Python](https://github.com/Azure-Samples/ms-identity-python-webapi-azurefunctions) |
| ![На этом изображении изображен логотип Node.js](media/sample-v2-code/logo_nodejs.png)</p>Node.js | Web API (услуга) [NodeJS и паспортно-azure-ad с использованием от имени](https://github.com/Azure-Samples/ms-identity-nodejs-webapi-onbehalfof-azurefunctions) |

## <a name="other-microsoft-graph-samples"></a>Другие примеры Microsoft Graph

Дополнительные сведения о [примерах](https://github.com/microsoftgraph/msgraph-community-samples/tree/master/samples#aspnet) и учебниках, в которых приводятся различные шаблоны использования API Microsoft Graph, включая аутентификацию в Azure AD, см. в статье [Microsoft Graph Community samples & tutorials](https://github.com/microsoftgraph/msgraph-community-samples) (Примеры и учебники сообщества Microsoft Graph).

## <a name="see-also"></a>См. также

- [Руководство разработчика Azure Active Directory (v1.0)](../azuread-dev/v1-overview.md)
- [Концептуальный и справочный](https://docs.microsoft.com/graph/use-the-api?context=graph%2Fapi%2Fbeta&view=graph-rest-beta)
