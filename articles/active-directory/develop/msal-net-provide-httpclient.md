---
title: Укажите прокси-сервер HttpClient & (MSAL.NET) | Службы
titleSuffix: Microsoft identity platform
description: Дополнительные сведения об определении собственных HttpClient и прокси-сервера для подключения к Azure AD с помощью библиотеки проверки подлинности Майкрософт для .NET (MSAL.NET).
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 04/23/2019
ms.author: jmprieur
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 80822c2a745d7c9c5b3f56b7921fcc83c5c807d1
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/10/2019
ms.locfileid: "74963245"
---
# <a name="providing-your-own-httpclient-and-proxy-using-msalnet"></a>Определение собственных HttpClient и прокси-сервера с помощью MSAL.NET
С помощью `.WithHttpClientFactory method` при [инициализации общедоступного клиентского приложения](msal-net-initializing-client-applications.md) можно указать собственное значение для HttpClient.  Определение собственного HttpClient позволяет использовать более сложные сценарии для точного управления прокси-сервером HTTP, настройки заголовков агентов пользователя или настройки принудительного использования определенного клиента HttpClient в MSAL (например, в веб-приложениях ASP.NET Core или интерфейсах API).

## <a name="initialize-with-httpclientfactory"></a>Инициализация с использованием HttpClientFactory
В следующем примере показано, как создать `HttpClientFactory`, а затем инициализировать общедоступное клиентское приложение с использованием этой фабрики:

```csharp
IMsalHttpClientFactory httpClientFactory = new MyHttpClientFactory();

var pca = PublicClientApplicationBuilder.Create(MsalTestConstants.ClientId) 
                                        .WithHttpClientFactory(httpClientFactory)
                                        .Build();
```

## <a name="httpclient-and-xamarin-ios"></a>HttpClient и Xamarin iOS
При использовании Xamarin iOS рекомендуется создать клиент `HttpClient`, явным образом использующий обработчик на основе `NSURLSession` для iOS 7 и более поздних версий. MSAL.NET автоматически создает клиент `HttpClient`, использующий `NSURLSessionHandler` для iOS 7 и более поздних версий. Дополнительные сведения см. в [документации по Xamarin iOS для HttpClient](/xamarin/cross-platform/macios/http-stack).