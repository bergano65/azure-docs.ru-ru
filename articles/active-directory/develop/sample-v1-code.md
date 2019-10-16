---
title: Примеры кода для Azure Active Directory v 1.0 | Документация Майкрософт
description: Предоставляет список примеров кода Azure Active Directory (конечная точка версии 1.0), сгруппированных по сценариям.
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
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
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 41ff9bce6135936a4b23096fb18efecf7be1e1e2
ms.sourcegitcommit: 0576bcb894031eb9e7ddb919e241e2e3c42f291d
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/15/2019
ms.locfileid: "72374115"
---
# <a name="azure-active-directory-code-samples-v10-endpoint"></a>Примеры кода Azure Active Directory (конечная точка версии 1.0)

[!INCLUDE [active-directory-develop-applies-v1-adal](../../../includes/active-directory-develop-applies-v1-adal.md)]

Для добавления проверки подлинности и авторизации в веб-приложения и веб-интерфейсы API можно использовать Microsoft Azure Active Directory (Azure AD).

Этот раздел содержит ссылки на примеры, с помощью которых вы сможете узнать о конечной точке Azure AD версии 1.0. В этих примерах показано, как это можно сделать. Также приведены фрагменты кода, которые можно использовать в приложениях. На странице примеров кода приведены ссылки на статьи с подробными сведениями о требованиях, установке и настройке. Код указан с комментариями, чтобы можно было понять важные разделы.

> [!NOTE]
> Если вас интересуют примеры кода Azure AD версии 2, см. раздел [Примеры кода версии 2.0 для сценариев](sample-v2-code.md).

Сведения о базовых сценариях для каждого типа примера см. в разделе [Сценарии проверки подлинности в Azure AD](v1-authentication-scenarios.md).

Вы также можете поделиться своими примерами на сайте GitHub. Чтобы узнать, как это сделать, обратитесь к разделу [Примеры и документация Microsoft Azure Active Directory](https://github.com/Azure-Samples?page=3&query=active-directory).

## <a name="single-page-applications"></a>Одностраничные приложения

В этом примере показано, как создать одностраничное приложение, защищенное с помощью Azure AD.

 платформа | Вызывает собственный API | Вызывает другой веб-API
 -------- |  --------------------- | ------------------ 
![На этом рисунке показан логотип JavaScript](media/sample-v2-code/logo_js.png) | [javascript-singlepageapp](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-dotnet-webapi) |
![На этом изображении показан логотип угловой JS](media/sample-v2-code/logo_angular.png) | [angularjs-singlepageapp](https://github.com/Azure-Samples/active-directory-angularjs-singlepageapp) | [angularjs-singlepageapp-cors](https://github.com/Azure-Samples/active-directory-angularjs-singlepageapp-dotnet-webapi)

## <a name="web-applications"></a>Веб-приложения

### <a name="web-applications-signing-in-users-calling-microsoft-graph-or-a-web-api-with-the-users-identity"></a>Веб-приложения, обрабатывающие вход пользователей и вызывающие Microsoft Graph или веб-API с удостоверением пользователя

В следующем примере показаны веб-приложения для входа пользователей. Некоторые из этих приложений также обращаются к Microsoft Graph или к веб-API от имени вошедшего в систему пользователя.

 платформа | Только вход пользователей | Вызывает Microsoft Graph или AAD Graph| Вызывает другой веб-API ASP.NET или ASP.NET Core 2.0
 -------- | ------------------- | --------------------- | -------------------------
![На этом рисунке показан логотип ASP.NET](media/sample-v2-code/logo_NETcore.png)</p>ASP.NET Core 2.0 | [dotnet-webapp-openidconnect-aspnetcore](https://github.com/Azure-Samples/active-directory-dotnet-webapp-openidconnect-aspnetcore) | [webapp-webapi-multitenant-openidconnect-aspnetcore](https://github.com/Azure-Samples/active-directory-webapp-webapi-multitenant-openidconnect-aspnetcore/) </p>(AAD Graph) | [dotnet-webapp-webapi-openidconnect-aspnetcore](https://github.com/Azure-Samples/active-directory-dotnet-webapp-webapi-openidconnect-aspnetcore)
![На этом рисунке показан логотип ASP.NET](media/sample-v2-code/logo_NETframework.png)</p> ASP.NET 4.5 | [webApp-openidconnect-dotnet](quickstart-v1-aspnet-webapp.md) </p> [webapp-WSFederation-dotNet](https://github.com/Azure-Samples/active-directory-dotnet-webapp-wsfederation) </p> [dotnet-webapp-webapi-oauth2-useridentity](https://github.com/Azure-Samples/active-directory-dotnet-webapp-webapi-oauth2-useridentity) | [dotnet-webapp-multitenant-openidconnect](https://github.com/Azure-Samples/active-directory-dotnet-webapp-multitenant-openidconnect)</p> (AAD Graph) |
![На этом рисунке показан логотип Python](media/sample-v2-code/logo_python.png) | | [python-webapp-graphapi](https://github.com/Azure-Samples/active-directory-python-webapp-graphapi)  |
![На этом рисунке показан журнал Java](media/sample-v2-code/logo_java.png)  | | [java-webapp-openidconnect](https://github.com/azure-samples/active-directory-java-webapp-openidconnect)  |
![На этом рисунке показан логотип PHP](media/sample-v2-code/logo_php.png) | | [php-graphapi-web](https://github.com/Azure-Samples/active-directory-php-graphapi-web)  |

### <a name="web-applications-demonstrating-role-based-access-control-authorization"></a>Веб-приложения, демонстрирующие управление доступом на основе ролей (авторизация)

В примерах ниже показано, как реализовать управление доступом на основе ролей (RBAC). RBAC используется, чтобы ограничить разрешения определенных пользователей на доступ к некоторым функциям в веб-приложении. Эти пользователи проходят авторизацию в зависимости от того, принадлежат ли они **группе Azure AD** или имеют определенную **роль** приложения.

платформа | Пример |
 -------- | ------------------- |
![На этом рисунке показан логотип ASP.NET](media/sample-v2-code/logo_NETframework.png)</p> ASP.NET 4.5 | [dotnet-webapp-groupclaims](https://github.com/Azure-Samples/active-directory-dotnet-webapp-groupclaims) </p>  [dotnet-webapp-roleclaims](https://github.com/Azure-Samples/active-directory-dotnet-webapp-roleclaims) | Веб-приложение MVC .NET 4.5, использующее для проверки подлинности **роли** Azure AD

## <a name="desktop-and-mobile-public-client-applications-calling-microsoft-graph-or-a-web-api"></a>Классические и мобильные клиентские приложения, вызывающие Microsoft Graph или веб-API

В следующих примерах показаны общедоступные клиентские приложения (приложения дескто и пмобиле), которые обращаются к Microsoft Graph или веб-API в имени пользователя. В зависимости от устройств и платформ, пользователи могут входить в приложения различными способами (последовательности или предоставление):

- Интерактивном режиме
- В автоматическом режиме (с встроенной проверкой подлинности Windows в Windows или именем пользователя и паролем);
- Делегируя интерактивный вход на другое устройство (поток кода устройства используется на устройствах, которые не предоставляют веб-элементы управления)

Клиентское приложение | платформа | Поток или предоставление | Вызывает Microsoft Graph | Вызывает веб-API ASP.NET или ASP.NET Core 2.x
------------------ | -------- | ---------- | -------------------- | -------------------------
Классическое приложение (WPF)           | ![На этом изображении показан .NETC# /Logo](media/sample-v2-code/logo_NET.png)  | Интерактивный режим | Часть [dotnet-native-multitarget](https://github.com/azure-samples/active-directory-dotnet-native-multitarget) | [Dotnet-native-desktop](https://github.com/Azure-Samples/active-directory-dotnet-native-desktop) </p> [dotnet-native-aspnetcore](https://azure.microsoft.com/resources/samples/active-directory-dotnet-native-aspnetcore/)</p> [dotnet-webapi-manual-jwt-validation](https://github.com/azure-samples/active-directory-dotnet-webapi-manual-jwt-validation)
Мобильное приложение (UWP)            | .![На этом рисунке показано .NET/C#/УВП](media/sample-v2-code/logo_Windows.png)   | Интерактивный режим | [dotnet-native-uwp-wam](https://github.com/azure-samples/active-directory-dotnet-native-uwp-wam) </p> В этом примере используется [WAM](/windows/uwp/security/web-account-manager), а не [ADAL.NET](https://aka.ms/adalnet)|  [dotnet-windows-store](https://github.com/Azure-Samples/active-directory-dotnet-windows-store) (приложение UWP, использующее ADAL.NET для обращения к веб-API одного клиента) </p> [dotnet-webapi-multitenant-windows-store](https://github.com/Azure-Samples/active-directory-dotnet-webapi-multitenant-windows-store) (приложение UWP, использующее ADAL.NET для обращения к веб-API нескольких клиентов)|
Мобильное приложение (Android, iOS, UWP)   | ![На этом рисунке показано .NET/C# (Xamarin)](media/sample-v2-code/logo_xamarin.png) | Интерактивный режим | [dotnet-native-multitarget](https://github.com/azure-samples/active-directory-dotnet-native-multitarget) |
Мобильное приложение (Android)           | ![На этом рисунке показан логотип Android](media/sample-v2-code/logo_Android.png) | Интерактивный режим |   [android](https://github.com/Azure-Samples/active-directory-android) |
Мобильное приложение (iOS)           | ![На этом рисунке показана iOS/объектив C или SWIFT](media/sample-v2-code/logo_iOS.png) | Интерактивный режим |   [nativeClient-iOS](https://github.com/azureadquickstarts/nativeclient-ios) |
Классическое приложение (консольное)          | ![На этом изображении показан .NETC# /Logo](media/sample-v2-code/logo_NET.png) | Имя пользователя и пароль </p>  Встроенная проверка подлинности Windows | | [dotnet-native-headless](https://github.com/azure-samples/active-directory-dotnet-native-headless)
Классическое приложение (консольное)          | ![На этом рисунке показан логотип Java](media/sample-v2-code/logo_Java.png) | Имя пользователя и пароль | | [java-native-headless](https://github.com/Azure-Samples/active-directory-java-native-headless)
Классическое приложение (консольное)           | ![На этом рисунке показана платформа .NETC# Core/логотип](media/sample-v2-code/logo_NETcore.png) | Поток кода устройства | | [dotnet-deviceprofile](https://github.com/Azure-Samples/active-directory-dotnet-deviceprofile)

## <a name="daemon-applications-accessing-web-apis-with-the-applications-identity"></a>Управляющие приложения (доступ к веб-API с удостоверением приложения)

В следующих примерах показаны классические приложения и веб-приложения, которые обращаются к Microsoft Graph или веб-API без указания пользователя (с удостоверением приложения).

Клиентское приложение | платформа | Поток или предоставление | Вызывает веб-API ASP.NET или ASP.NET Core 2.0
------------------ | -------- | ---------- | -------------------- 
Управляющая программа (консольная)          | ![На этом рисунке показан логотип .NET](media/sample-v2-code/logo_NETframework.png) | Учетные данные клиента с секретом приложения или сертификатом | [dotnet-daemon](https://github.com/azure-samples/active-directory-dotnet-daemon)</p> [dotnet-daemon-certificate-credential](https://github.com/azure-samples/active-directory-dotnet-daemon-certificate-credential)
Управляющая программа (консольная)         | ![На этом рисунке показан логотип .NET](media/sample-v2-code/logo_NETcore.png) | Учетные данные клиента с сертификатом| [dotnetcore-daemon-certificate-credential](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-certificate-credential)
Веб-приложение ASP.NET  | ![На этом рисунке показан логотип .NET](media/sample-v2-code/logo_NETframework.png) | Учетные данные клиента | [dotnet-webapp-webapi-oauth2-appidentity](https://github.com/Azure-Samples/active-directory-dotnet-webapp-webapi-oauth2-appidentity)

## <a name="web-apis"></a>Веб-API

### <a name="web-api-protected-by-azure-active-directory"></a>Веб-API, защищенный с помощью Azure Active Directory

В следующем примере показано, как защитить веб-API node.js с помощью Azure AD.

В предыдущих разделах этой статьи можно также найти другие примеры, в которых показано клиентское приложение, **обращающееся** к **веб-API** ASP.NET или ASP.NET Core. Эти примеры не упоминаются повторно в этом разделе, но их можно найти в последнем столбце таблиц выше или ниже.

| платформа | Пример |
|--------|-------------------|
| ![На этом рисунке показан логотип Node. js](media/sample-v2-code/logo_nodejs.png)  | [node-webapi](https://github.com/Azure-Samples/active-directory-node-webapi) |

### <a name="web-api-calling-microsoft-graph-or-another-web-api"></a>Веб-API, вызывающий Microsoft Graph или другой веб-API

В следующих примерах показан веб-API, который вызывает другой веб-API. Во втором примере показано, как управлять условным доступом.

| платформа |  Вызывает Microsoft Graph | Вызывает другой веб-API ASP.NET или ASP.NET Core 2.0 |
| -------- |  --------------------- | ------------------------- |
| ![На этом рисунке показан логотип ASP.NET](media/sample-v2-code/logo_NETframework.png)</p> ASP.NET 4.5 | [dotnet-webapi-onbehalfof](https://github.com/azure-samples/active-directory-dotnet-webapi-onbehalfof) </p> [dotnet-webapi-onbehalfof-ca](https://github.com/azure-samples/active-directory-dotnet-webapi-onbehalfof-ca) | [dotnet-webapi-onbehalfof](https://github.com/azure-samples/active-directory-dotnet-webapi-onbehalfof) </p> [dotnet-webapi-onbehalfof-ca](https://github.com/azure-samples/active-directory-dotnet-webapi-onbehalfof-ca) |

## <a name="other-microsoft-graph-samples"></a>Другие примеры Microsoft Graph

Дополнительные сведения о примерах и учебниках, в которых приводятся различные шаблоны использования API Microsoft Graph, включая аутентификацию в Azure AD, см. в статье [Примеры и учебники сообщества Microsoft Graph](https://github.com/microsoftgraph/msgraph-community-samples).

## <a name="see-also"></a>Дополнительные материалы

[Руководство разработчика по Azure Active Directory](v1-overview.md)

[Библиотеки аутентификации Azure Active Directory](active-directory-authentication-libraries.md)

[Основные понятия и справочные материалы по Azure AD API Graph](https://msdn.microsoft.com/library/azure/hh974476.aspx)

[Библиотека вспомогательных методов для Azure AD Graph API](https://www.nuget.org/packages/Microsoft.Azure.ActiveDirectory.GraphClient)
