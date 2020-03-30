---
title: Приобретите токен из кэша (MSAL.NET)
titleSuffix: Microsoft identity platform
description: Узнайте, как получить токен доступа в режиме бесиспользования (из кэша маркеров) с помощью библиотеки подлинности Майкрософт для .NET (MSAL.NET).
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 07/16/2019
ms.author: marsma
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: 90189a1d7fd6421b7a24940e8c6ed615fa0df6d6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77084840"
---
# <a name="get-a-token-from-the-token-cache-using-msalnet"></a>Получите токен из кэша токенов, используя MSAL.NET

При приобретении токена доступа с помощью библиотеки подлинности Майкрософт для .NET (MSAL.NET) токен консеик консучатся. Когда приложению нужен маркер, он `AcquireTokenSilent` должен сначала вызвать метод, чтобы проверить, находится ли приемлемый маркер в кэше. Во многих случаях можно приобрести другой маркер с большим количеством областей, основанных на токене в кэше. Также можно обновить маркер, когда он приближается к истечению (поскольку кэш маркеров также содержит маркер обновления).

Рекомендуемый шаблон заключается `AcquireTokenSilent` в том, чтобы сначала вызвать метод.  Если `AcquireTokenSilent` не удается, то приобретите токен с помощью других методов.

В следующем примере приложение сначала пытается приобрести токен из кэша токенов.  В `MsalUiRequiredException` случае отбрасыва этого исключения приложение приобретает токен в интерактивном режиме. 

```csharp
AuthenticationResult result = null;
var accounts = await app.GetAccountsAsync();

try
{
 result = await app.AcquireTokenSilent(scopes, accounts.FirstOrDefault())
        .ExecuteAsync();
}
catch (MsalUiRequiredException ex)
{
 // A MsalUiRequiredException happened on AcquireTokenSilent.
 // This indicates you need to call AcquireTokenInteractive to acquire a token
 System.Diagnostics.Debug.WriteLine($"MsalUiRequiredException: {ex.Message}");

 try
 {
    result = await app.AcquireTokenInteractive(scopes)
          .ExecuteAsync();
 }
 catch (MsalException msalex)
 {
    ResultText.Text = $"Error Acquiring Token:{System.Environment.NewLine}{msalex}";
 }
}
catch (Exception ex)
{
 ResultText.Text = $"Error Acquiring Token Silently:{System.Environment.NewLine}{ex}";
 return;
}

if (result != null)
{
 string accessToken = result.AccessToken;
 // Use the token
}
```
