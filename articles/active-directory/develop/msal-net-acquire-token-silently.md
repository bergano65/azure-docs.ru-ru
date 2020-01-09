---
title: Получение маркера из кэша (MSAL.NET)
titleSuffix: Microsoft identity platform
description: Узнайте, как получить маркер доступа автоматически (из кэша маркеров) с помощью библиотеки проверки подлинности Microsoft для .NET (MSAL.NET).
services: active-directory
author: TylerMSFT
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 07/16/2019
ms.author: twhitney
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 67a72b295005e328723be5d3b577d15330c05134
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/25/2019
ms.locfileid: "75424279"
---
# <a name="get-a-token-from-the-token-cache-using-msalnet"></a>Получение маркера из кэша маркеров с помощью MSAL.NET

При получении маркера доступа с помощью библиотеки проверки подлинности Майкрософт для .NET (MSAL.NET) маркер кэшируется. Когда приложению требуется токен, сначала необходимо вызвать метод `AcquireTokenSilent`, чтобы проверить, находится ли допустимый маркер в кэше. Во многих случаях можно получить другой токен с дополнительными областями на основе маркера в кэше. Можно также обновить маркер, когда он поступает в начало срока действия (так как кэш маркеров также содержит маркер обновления).

Рекомендуемый шаблон — сначала вызвать метод `AcquireTokenSilent`.  Если `AcquireTokenSilent` не удается, получите маркер с помощью других методов.

В следующем примере приложение сначала пытается получить маркер из кэша маркеров.  Если создается исключение `MsalUiRequiredException`, приложение получает маркер в интерактивном режиме. 

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
