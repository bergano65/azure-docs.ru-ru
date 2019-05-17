---
title: Рекомендации по универсальной платформе Windows (библиотека проверки подлинности Майкрософт для .NET) | Azure
description: Дополнительные сведения о специальные рекомендации при использовании универсальной платформы Windows с помощью библиотеки проверки подлинности Майкрософт для .NET (MSAL.NET).
services: active-directory
documentationcenter: dev-center-name
author: rwike77
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/24/2019
ms.author: ryanwi
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 83fb999b0cf66cfd8d96e82d23ed43626352a8aa
ms.sourcegitcommit: f6c85922b9e70bb83879e52c2aec6307c99a0cac
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/11/2019
ms.locfileid: "65544141"
---
# <a name="universal-windows-platform-specific-considerations-with-msalnet"></a>Универсальной платформой Windows вопросы, связанные с MSAL.NET
В Xamarin iOS существует несколько факторов, которые следует принять во внимание при использовании MSAL.NET.

## <a name="the-usecorporatenetwork-property"></a>Свойство UseCorporateNetwork
В платформе WinRT `PublicClientApplication` имеет следующее логическое свойство ``UseCorporateNetwork``. Это свойство позволяет приложениям Win8.1 и универсальной платформы Windows можно было использовать встроенную проверку подлинности Windows (и поэтому ЕДИНЫЙ вход пользователя вошедшего в систему с операционной системой) Если пользователь является вошедшего в систему с учетной записью в федерации Azure AD клиента. Этот вариант использует WAB (веб-брокер проверки подлинности). 

> [!IMPORTANT]
> Этому свойству присвоить значение true предполагает, что разработчик приложения включил встроенную проверку подлинности Windows (IWA) в приложении. Для этого:
> - В ``Package.appxmanifest`` для приложения универсальной платформы Windows в **возможности** вкладке, включите следующие возможности:
>   - Корпоративная проверка подлинности
>   - Частные сети (клиент и сервер)
>   - Сертификат общего пользователя

IWA не включена по умолчанию, так как для приложений, запрашивающих возможности корпоративной аутентификации или общие сертификаты пользователей требуется более высокий уровень проверки принятия в Windows Store, а не всем разработчикам может потребоваться более поздней версии уровень проверки. 

Базовая реализация на основе uwp (WAB) не работает в корпоративных сценариях, где был включен условный доступ. Признаком этого является пользователь пытается войти с помощью Windows hello что предлагается выбрать сертификат, но сертификат для ПИН-код не найден, или пользователь нажимает ее, но никогда не приглашение на ввод ПИН-код. Решение — использовать альтернативный метод (имя пользователя и пароль и телефон проверки подлинности), но интерфейс не очень хорошо. 

## <a name="next-steps"></a>Дальнейшие действия
Дополнительные сведения приведены в следующих примерах:

Образец | платформа | Описание 
|------ | -------- | -----------|
|[active-directory-dotnet-native-uwp-v2](https://github.com/azure-samples/active-directory-dotnet-native-uwp-v2) | UWP | Клиентское приложение универсальной платформы Windows с помощью msal.net, доступ к Microsoft Graph для пользователей, проверка подлинности с помощью конечной точки версии 2.0 Azure AD. <br>![Топология](media/msal-net-uwp-considerations/topology-native-uwp.png)|
|[https://github.com/Azure-Samples/active-directory-xamarin-native-v2](https://github.com/Azure-Samples/active-directory-xamarin-native-v2) | Xamarin iOS, Android, UWP; | Простое приложение Xamarin Forms, с отображением использование MSAL для проверки подлинности MSA и Azure AD через конечную точку версии 2.0 AAD и получить доступ к Microsoft Graph с помощью полученного маркера. <br>![Топология](media/msal-net-uwp-considerations/topology-xamarin-native.png)|