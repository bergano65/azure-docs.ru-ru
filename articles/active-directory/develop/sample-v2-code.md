---
title: Примеры кода для платформы Microsoft Identity | Документация Майкрософт
description: Содержит индекс доступных примеров кода платформы Microsoft Identity Platform (версии 2.0), упорядоченных по сценарию.
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
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6ba9df2aa81111ec28970c28e9c584baf2f8cd93
ms.sourcegitcommit: 6bb98654e97d213c549b23ebb161bda4468a1997
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/03/2019
ms.locfileid: "74766350"
---
# <a name="microsoft-identity-platform-code-samples-v20-endpoint"></a>Примеры кода платформы удостоверений Microsoft Identity (версия 2.0, конечная точка)

Возможности платформы удостоверений Майкрософт:

- добавление возможностей аутентификации и авторизации в веб-приложения и веб-API;
- требование маркера доступа для доступа к защищенным веб-API.

В этой статье кратко описаны ссылки на примеры для конечной точки платформы идентификации Майкрософт. В этих примерах показано, как это делается, а также приведены фрагменты кода, которые можно использовать в приложениях. На странице образец кода вы найдете подробные разделы readme, которые помогут в требованиях, установке и настройке. Комментарии в коде помогают понять критически важные разделы.

> [!NOTE]
> Если вы заинтересованы в примерах версии 1.0, ознакомьтесь с [примерами кода Azure AD (конечная точка версии 1.0)](sample-v1-code.md).

Основные сведения о базовом сценарии для каждого типа выборки см. в разделе [типы приложений для конечной точки платформы Microsoft Identity](v2-app-types.md).

Вы также можете поделиться своими примерами на сайте GitHub. Чтобы узнать, как это сделать, обратитесь к разделу [Примеры и документация Microsoft Azure Active Directory](https://github.com/Azure-Samples?page=3&query=active-directory).

## <a name="single-page-applications"></a>Одностраничные приложения

В этих примерах показано, как создать одностраничное приложение, защищенное с помощью платформы идентификации Майкрософт. В этих примерах используется одна из разновидностей MSAL. js.

| платформа | Описание | Ссылка |
| -------- | --------------------- | -------- |
| ![этом изображении показан логотип JavaScript](media/sample-v2-code/logo_js.png) [JavaScript (msal. js)](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-core) | Вызывает Microsoft Graph |[javascript-graphapi-web-v2](https://github.com/Azure-Samples/active-directory-javascript-graphapi-web-v2) |
| ![этом изображении показан логотип JavaScript](media/sample-v2-code/logo_js.png) [JavaScript (msal. js)](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-core) | Вызывает B2C |[B2C-JavaScript-msal-singlepageapp](https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp) |
| ![этом изображении показан логотип JavaScript](media/sample-v2-code/logo_js.png) [JavaScript (msal. js)](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-core) | Вызывает собственный веб-API |[JavaScript-singlepageapp-DotNet-webapi-v2](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-dotnet-webapi-v2) |
| ![этом изображении показан логотип угловой JS](media/sample-v2-code/logo_angular.png) [JavaScript (MSAL AngularJS)](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-angularjs)| Вызывает Microsoft Graph  | [MsalAngularjsDemoApp](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/samples/MsalAngularjsDemoApp)
| ![этом изображении показан угловой логотип](media/sample-v2-code/logo_angular.png) [JavaScript (MSAL под углом)](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-angular)| Вызывает Microsoft Graph  | [MSALAngularDemoApp](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/samples/MSALAngularDemoApp) |

## <a name="web-applications"></a>Веб-приложения

В следующем примере показаны веб-приложения для входа пользователей. В некоторых примерах приложение вызывает Microsoft Graph или веб-API с использованием удостоверения пользователя.

| платформа | Только вход пользователей | Выполняет вход пользователей и вызывает Microsoft Graph |
| -------- | ------------------- | --------------------------------- |
| ![На этом рисунке показан логотип ASP.NET Core](media/sample-v2-code/logo_NETcore.png)</p>ASP.NET Core 2,2 | [Руководство пользователя ASP.NET Core WebApp входа в систему](https://aka.ms/aspnetcore-webapp-sign-in) | Тот же пример в [ASP.NET Core веб-приложение вызывает этап Microsoft Graph](https://aka.ms/aspnetcore-webapp-call-msgraph) |
| ![На этом рисунке показан логотип ASP.NET](media/sample-v2-code/logo_NETframework.png)</p> ASP.NET: | [Краткое руководство по ASP.NET](https://github.com/AzureAdQuickstarts/AppModelv2-WebApp-OpenIDConnect-DotNet) </p> [dotnet-webapp-openidconnect-v2](https://github.com/azure-samples/active-directory-dotnet-webapp-openidconnect-v2)  |  [dotnet-admin-restricted-scopes-v2](https://github.com/azure-samples/active-directory-dotnet-admin-restricted-scopes-v2) </p> |[msgraph-training-aspnetmvcapp](https://github.com/microsoftgraph/msgraph-training-aspnetmvcapp)
| ![На этом рисунке показан логотип Java](media/sample-v2-code/logo_java.png)  |                   | [MS-Identity-Java-webapp](https://github.com/Azure-Samples/ms-identity-java-webapp) |
| ![На этом рисунке показан логотип Python](media/sample-v2-code/logo_python.png)  |                   | [MS-Identity-Python-webapp](https://github.com/Azure-Samples/ms-identity-python-webapp) |
| ![На этом рисунке показан логотип Node. js](media/sample-v2-code/logo_nodejs.png)  |                   | [Краткое руководство по Node.js](https://github.com/azureadquickstarts/appmodelv2-webapp-openidconnect-nodejs) |
| ![На этом изображении показан логотип Ruby](media/sample-v2-code/logo_ruby.png) |                   | [msgraph-training-rubyrailsapp](https://github.com/microsoftgraph/msgraph-training-rubyrailsapp) |

## <a name="desktop-and-mobile-public-client-apps"></a>Общедоступные клиентские приложения — классические и мобильные

В следующих примерах показаны общедоступные клиентские приложения (настольные или мобильные приложения), которые обращаются к Microsoft Graph API, или собственный веб-API в имени пользователя. Все эти клиентские приложения используют библиотеку проверки подлинности Майкрософт (MSAL).

| Клиентское приложение | платформа | Поток или предоставление | Вызывает Microsoft Graph | Вызывает веб-API ASP.NET Core 2,0 |
| ------------------ | -------- |  ----------| ---------- | ------------------------- |
| Классическое приложение (WPF)      | ![На этом изображении показан .NETC# /Logo](media/sample-v2-code/logo_NET.png) | [интерактивный](msal-authentication-flows.md#interactive)| [dotnet-desktop-msgraph-v2](https://github.com/azure-samples/active-directory-dotnet-desktop-msgraph-v2) | [dotnet-native-aspnetcore-v2](https://aka.ms/msidentity-aspnetcore-webapi) |
| Классическое приложение (консольное)   | ![На этом изображении показан логотипC# .NET/(Desktop)](media/sample-v2-code/logo_NET.png) | [Встроенная аутентификация Windows](msal-authentication-flows.md#integrated-windows-authentication) | [dotnet-iwa-v2](https://github.com/azure-samples/active-directory-dotnet-iwa-v2) |  |
| Классическое приложение (консольное)   | ![На этом изображении показан логотипC# .NET/(Desktop)](media/sample-v2-code/logo_NETcore.png) | [Имя пользователя или пароль](msal-authentication-flows.md#usernamepassword) |[dotnetcore-up-v2](https://github.com/azure-samples/active-directory-dotnetcore-console-up-v2) |  |
| Классическое приложение (консольное)   | ![На этом рисунке показан логотип Java](media/sample-v2-code/logo_java.png) | [Имя пользователя или пароль](msal-authentication-flows.md#usernamepassword) |[MS-Identity-Java-Desktop](https://github.com/Azure-Samples/ms-identity-java-desktop/tree/master/Call-MsGraph-WithUsernamePassword) |  |
| Классическое приложение (консольное)   | ![На этом рисунке показан логотип Python](media/sample-v2-code/logo_python.png) | [Имя пользователя или пароль](msal-authentication-flows.md#usernamepassword) |[MS-Identity-Python — Настольный компьютер](https://github.com/Azure-Samples/ms-identity-python-desktop/tree/master/2-Call-MsGraph-WithUsernamePassword) |  |
| Мобильное приложение (Android, iOS, UWP)   | ![На этом рисунке изображена эмблема .NET/C# (Xamarin).](media/sample-v2-code/logo_xamarin.png) | [интерактивный](msal-authentication-flows.md#interactive) |[xamarin-native-v2](https://github.com/azure-samples/active-directory-xamarin-native-v2) |  |
| Мобильное приложение (iOS)       | ![На этом рисунке показана iOS/объектив-C или SWIFT.](media/sample-v2-code/logo_iOS.png) | [интерактивный](msal-authentication-flows.md#interactive) |[iOS-SWIFT-ObjC-Native-v2](https://github.com/azure-samples/active-directory-ios-swift-native-v2) </p> [ios-native-nxoauth2-v2](https://github.com/azure-samples/active-directory-ios-native-nxoauth2-v2) |  |
| Desktop (macOS)       | MacOS | [интерактивный](msal-authentication-flows.md#interactive) |[macOS-SWIFT-ObjC-Native-v2](https://github.com/Azure-Samples/ms-identity-macOS-swift-objc) |  |
| Mobile (Android-Java)   | ![На этом рисунке показан логотип Android](media/sample-v2-code/logo_Android.png) | [интерактивный](msal-authentication-flows.md#interactive) |  [Android — Java](https://github.com/Azure-Samples/ms-identity-android-java) |  |
| Mobile (Android-Котлин)   | ![На этом рисунке показан логотип Android](media/sample-v2-code/logo_Android.png) | [интерактивный](msal-authentication-flows.md#interactive) |  [Android — Котлин](https://github.com/Azure-Samples/ms-identity-android-kotlin) |  |

## <a name="daemon-applications"></a>Управляющие программы

В следующем примере показано приложение, которое обращается к Microsoft Graph с использованием собственного удостоверения (без пользователя).

| Клиентское приложение | платформа | Поток или предоставление | Вызывает Microsoft Graph |
| ------------------ | -------- | ---------- | -------------------- |
| Console | ![На этом рисунке показан логотип .NET Core](media/sample-v2-code/logo_NETcore.png)</p> ASP.NET:  | [Учетные данные клиента](msal-authentication-flows.md#client-credentials) | [dotnetcore-daemon-v2](https://github.com/azure-samples/active-directory-dotnetcore-daemon-v2) |
| Веб-приложение | ![На этом рисунке показан логотип ASP.NET](media/sample-v2-code/logo_NETframework.png)</p> ASP.NET:  | [Учетные данные клиента](msal-authentication-flows.md#client-credentials) | [dotnet-daemon-v2](https://github.com/azure-samples/active-directory-dotnet-daemon-v2) |
| Console | ![На этом рисунке показан логотип Java](media/sample-v2-code/logo_java.png) | [Учетные данные клиента](msal-authentication-flows.md#client-credentials) | [MS-Identity-Java-Демон](https://github.com/Azure-Samples/ms-identity-java-daemon) |
| Console | ![На этом рисунке показан логотип Python](media/sample-v2-code/logo_python.png) | [Учетные данные клиента](msal-authentication-flows.md#client-credentials) | [MS-Identity-Python-Демон](https://github.com/Azure-Samples/ms-identity-python-daemon) |

## <a name="headless-applications"></a>Приложения удаленного управления

В следующем примере показано общедоступное клиентское приложение, запущенное на устройстве без веб-браузера. Приложение может быть программой командной строки, приложением, работающим в Linux или Mac, или приложением IoT. Пример включает в себя приложение, осуществляющее доступ к Microsoft Graph API, в имени пользователя, который выполняет вход в интерактивном режиме на другом устройстве (например, на мобильном телефоне). Это клиентское приложение использует библиотеку проверки подлинности Майкрософт (MSAL).

| Клиентское приложение | платформа | Поток или предоставление | Вызывает Microsoft Graph |
| ------------------ | -------- |  ----------| ---------- |
| Классическое приложение (консольное)   | ![На этом изображении показан логотипC# .NET/(Desktop)](media/sample-v2-code/logo_NETcore.png) | [Поток кода устройства](msal-authentication-flows.md#device-code) |[dotnetcore-devicecodeflow-v2](https://github.com/azure-samples/active-directory-dotnetcore-devicecodeflow-v2) |
| Классическое приложение (консольное)   | ![На этом рисунке показан логотип Java](media/sample-v2-code/logo_java.png) | [Поток кода устройства](msal-authentication-flows.md#device-code) |[MS-Identity-Java-девицекодефлов](https://github.com/Azure-Samples/ms-identity-java-devicecodeflow) |
| Классическое приложение (консольное)   | ![На этом рисунке показан логотип Python](media/sample-v2-code/logo_python.png) | [Поток кода устройства](msal-authentication-flows.md#device-code) |[MS-Identity-Python-девицекодефлов](https://github.com/Azure-Samples/ms-identity-python-devicecodeflow) |

## <a name="web-apis"></a>Веб-API

В следующих примерах показано, как защитить веб-API с помощью конечной точки платформы Microsoft Identity и как вызывать подчиненный API из веб-API.

| платформа | Пример |
| -------- | ------------------- |
| ![На этом рисунке показан логотип ASP.NET Core](media/sample-v2-code/logo_NETcore.png)</p>ASP.NET Core 2,2 | ASP.NET Core веб-API (служба) [DotNet-Native-aspnetcore-v2](https://aka.ms/msidentity-aspnetcore-webapi-calls-msgraph)  |
| ![На этом рисунке показан логотип ASP.NET](media/sample-v2-code/logo_NET.png)</p>ASP.NET MVC | Веб-API (служба) [MS-Identity-ASPNET-webapi-OnBehalfOf](https://github.com/Azure-Samples/ms-identity-aspnet-webapi-onbehalfof) |
| ![На этом рисунке показан логотип Java](media/sample-v2-code/logo_java.png) | Веб-API (служба) [MS-Identity-Java-webapi](https://github.com/Azure-Samples/ms-identity-java-webapi) |

## <a name="azure-functions-as-web-apis"></a>Функции Azure в качестве веб-API

В следующих примерах показано, как защитить функцию Azure с помощью HttpTrigger и предоставить доступ к веб-API с помощью конечной точки платформы идентификации Майкрософт, а также как вызвать подчиненный API из веб-API.

| платформа | Пример |
| -------- | ------------------- |
| ![На этом рисунке показан логотип ASP.NET Core](media/sample-v2-code/logo_NETcore.png)</p>ASP.NET Core 2,2 | ASP.NET Core службы веб-API (служба) Azure [DotNet-Native-aspnetcore-v2](https://github.com/Azure-Samples/ms-identity-dotnet-webapi-azurefunctions)  |
| ![На этом рисунке показан логотип Node. js](media/sample-v2-code/logo_nodejs.png)</p>NodeJs | Веб-API (служба) [NodeJS и Passport-Azure-AD](https://github.com/Azure-Samples/ms-identity-nodejs-webapi-azurefunctions) |
| ![На этом рисунке показан логотип Python](media/sample-v2-code/logo_python.png)</p>Python | Веб-API (служба) [Python](https://github.com/Azure-Samples/ms-identity-python-webapi-azurefunctions) |
| ![На этом рисунке показан логотип Node. js](media/sample-v2-code/logo_nodejs.png)</p>NodeJs | Веб-API (служба) [NodeJS и Passport-AD, используя от имени](https://github.com/Azure-Samples/ms-identity-nodejs-webapi-onbehalfof-azurefunctions) |

## <a name="other-microsoft-graph-samples"></a>Другие примеры Microsoft Graph

Дополнительные сведения о [примерах](https://github.com/microsoftgraph/msgraph-community-samples/tree/master/samples#aspnet) и учебниках, в которых приводятся различные шаблоны использования API Microsoft Graph, включая аутентификацию в Azure AD, см. в статье [Microsoft Graph Community samples & tutorials](https://github.com/microsoftgraph/msgraph-community-samples) (Примеры и учебники сообщества Microsoft Graph).

## <a name="see-also"></a>Дополнительные материалы

- [Azure Active Directory (версия 1.0): руководством разработчика](v1-overview.md)
- [Основные понятия и справочные материалы по Azure AD API Graph](https://msdn.microsoft.com/library/azure/hh974476.aspx)
- [Библиотека вспомогательных методов для Azure AD Graph API](https://www.nuget.org/packages/Microsoft.Azure.ActiveDirectory.GraphClient)
