---
title: Рекомендации по универсальная платформа Windows (Библиотека проверки подлинности Microsoft для .NET) | Службы
description: Ознакомьтесь с конкретными соображениями при использовании универсальная платформа Windows с библиотекой проверки подлинности Майкрософт для .NET (MSAL.NET).
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
ms.date: 07/16/2019
ms.author: ryanwi
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2d78a64ee41e37fe53eba20eab6753c0b6eb8389
ms.sourcegitcommit: 9a699d7408023d3736961745c753ca3cec708f23
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/16/2019
ms.locfileid: "68277921"
---
# <a name="universal-windows-platform-specific-considerations-with-msalnet"></a>Вопросы, связанные с универсальная платформа Windows MSAL.NET
В UWP существует несколько аспектов, которые необходимо учитывать при использовании MSAL.NET.

## <a name="the-usecorporatenetwork-property"></a>Свойство Усекорпоратенетворк
В платформе `PublicClientApplication` WinRT имеет следующее логическое свойство ``UseCorporateNetwork``. Это свойство позволяет приложениям Win 8.1 и UWP использовать преимущества встроенной проверки подлинности Windows (и, следовательно, единый вход с пользовательским входом с операционной системой), если пользователь вошел в систему с помощью учетной записи в Федеративной клиенте Azure AD. При задании этого свойства MSAL.NET использует WAB (посредник веб-проверки подлинности).

> [!IMPORTANT]
> Присвоение этому свойству значения true предполагает, что разработчик приложения включил встроенную проверку подлинности Windows (IWA) в приложении. Для этого:
> - В приложении UWP на вкладке возможности включите следующие возможности.  ``Package.appxmanifest``
>   - Корпоративная проверка подлинности
>   - Частные сети (клиент и сервер)
>   - Сертификат общего пользователя

IWA не включен по умолчанию, так как приложения, запрашивающие возможности Enterprise или Общие сертификаты пользователей, нуждаются в более высоком уровне проверки в магазине Windows, и не все разработчики могут работать с более высоким уровень проверки.

Базовая реализация на платформе UWP (WAB) не работает правильно в корпоративных сценариях, где включен условный доступ. Симптомом является то, что пользователь пытается войти в Windows Hello и предложил выбрать сертификат, но:

- не найден сертификат для ПИН-кода,
- или пользователь выбирает его, но не получает запрос на ввод ПИН-кода.

Обходной путь заключается в использовании альтернативного метода (имя пользователя, пароль и телефонная проверка подлинности), но неплохой опыт.

## <a name="troubleshooting"></a>Устранение неполадок

Некоторые клиенты сообщили о том, что в некоторых конкретных корпоративных средах произошла следующая ошибка входа:

```Text
We can't connect to the service you need right now. Check your network connection or try this again later
```

в то время как они узнают, что они имеют подключение к Интернету и работают с общедоступной сетью.

Для решения этой проблемы необходимо убедиться, что WAB (базовый компонент Windows) разрешает использовать частную сеть. Это можно сделать, задав раздел реестра:

```Text
HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Image File Execution Options\authhost.exe\EnablePrivateNetwork = 00000001
```

Дополнительные сведения см. в разделе [посредник веб-проверки подлинности — Fiddler](https://docs.microsoft.com/windows/uwp/security/web-authentication-broker#fiddler).

## <a name="next-steps"></a>Следующие шаги
Дополнительные сведения приведены в следующих примерах.

Образец | Платформа | Описание 
|------ | -------- | -----------|
|[active-directory-dotnet-native-uwp-v2](https://github.com/azure-samples/active-directory-dotnet-native-uwp-v2) | UWP | Универсальная платформа Windows клиентское приложение, использующее msal.net, доступ к Microsoft Graph для пользователя, выполняющего проверку подлинности с помощью конечной точки Azure AD версии 2.0. <br>![Топология](media/msal-net-uwp-considerations/topology-native-uwp.png)|
|[https://github.com/Azure-Samples/active-directory-xamarin-native-v2](https://github.com/Azure-Samples/active-directory-xamarin-native-v2) | Xamarin iOS, Android, UWP; | Простое приложение Xamarin Forms, в котором показано, как использовать MSAL для проверки подлинности MSA и Azure AD через конечную точку AAD версии 2.0, а также для доступа к Microsoft Graph с результирующим маркером. <br>![Топология](media/msal-net-uwp-considerations/topology-xamarin-native.png)|
