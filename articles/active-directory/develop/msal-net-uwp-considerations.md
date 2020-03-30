---
title: Соображения UWP (MSAL.NET) Azure
titleSuffix: Microsoft identity platform
description: Узнайте о соображениях использования универсальной платформы Windows (UWP) в библиотеке подлинности Microsoft для .NET (MSAL.NET).
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
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2eeec28569cf31af4542d6cd7aca1fb27d77b1e0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77132525"
---
# <a name="considerations-for-using-universal-windows-platform-with-msalnet"></a>Рассмотрение вопроса об использовании универсальной платформы Windows с MSAL.NET
Разработчики приложений, которые используют Универсальную платформу Windows (UWP) с MSAL.NET должны рассмотреть концепции, представленные в этой статье.

## <a name="the-usecorporatenetwork-property"></a>Свойство UseCorporateNetwork
На платформе Windows Runtime (WinRT) `PublicClientApplication` имеет `UseCorporateNetwork`свойство Boolean. Это свойство позволяет приложениям Windows 8.1 и UWP получать выгоду от интегрированной аутентификации Windows (IWA), если пользователь вписался в учетную запись с федеративным клиентом Active Directory (Azure AD). Пользователи, всданные в операционную систему, также могут использовать один развлек (SSO). При настройке `UseCorporateNetwork` свойства MSAL.NET использует веб-брокера аутентификации (WAB).

> [!IMPORTANT]
> Установка `UseCorporateNetwork` свойства на истину предполагает, что разработчик приложения включил IWA в приложение. Для включения IWA:
> - В приложении UWP `Package.appxmanifest`на вкладке **«Возможности»** включите следующие возможности:
>   - **Корпоративная проверка подлинности**
>   - **Частные сети (клиент и сервер)**
>   - **Общий пользовательский сертификат**

IWA не включен по умолчанию, потому что Microsoft Store требует высокого уровня проверки, прежде чем он принимает приложения, которые запрашивает возможности корпоративной аутентификации или общие сертификаты пользователей. Не все разработчики хотят сделать этот уровень проверки.

На платформе UWP базовая реализация WAB не работает правильно в корпоративных сценариях, где включен условный доступ. Пользователи видят симптомы этой проблемы, когда они пытаются войти в систему с помощью Windows Hello. Когда пользователю предлагается выбрать сертификат:

- Сертификат для PIN-кода не найден.
- После того, как пользователь выбирает сертификат, он не подсказывается для PIN-кода.

Вы можете попытаться избежать этой проблемы с помощью альтернативного метода, такого как имя пользователя-пароль и проверка подлинности телефона, но опыт не очень хорош.

## <a name="troubleshooting"></a>Устранение неполадок

Некоторые клиенты сообщили о следующей ошибке ввходе в конкретных корпоративных средах, в которых они знают, что у них есть подключение к Интернету и что соединение работает с общедоступной сетью.

```Text
We can't connect to the service you need right now. Check your network connection or try this again later.
```

Вы можете избежать этой проблемы, убедившись, что WAB (основной компонент Windows) позволяет частную сеть. Вы можете сделать это, установив ключ реестра:

```Text
HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Image File Execution Options\authhost.exe\EnablePrivateNetwork = 00000001
```

Для получения дополнительной информации, смотрите [веб-брокер аутентификации - Скрипач](https://docs.microsoft.com/windows/uwp/security/web-authentication-broker#fiddler).

## <a name="next-steps"></a>Дальнейшие действия
Следующие примеры предоставляют более подробную информацию.

Пример | Платформа | Описание 
|------ | -------- | -----------|
|[активный-каталог-dotnet-родной-uwp-v2](https://github.com/azure-samples/active-directory-dotnet-native-uwp-v2) | UWP | Клиентское приложение UWP, используюеееееееMSAL.NET. Он получает доступ к Microsoft Graph для пользователя, который проверяет подлинность с помощью точки azure AD 2.0. <br>![Топология](media/msal-net-uwp-considerations/topology-native-uwp.png)|
|[активный каталог-xamarin-родной-v2](https://github.com/Azure-Samples/active-directory-xamarin-native-v2) | Xamarin iOS, Android, UWP; | Простое приложение Xamarin Forms, которое показывает, как использовать MSAL для проверки подлинности личных учетных записей Майкрософт и Azure AD через конечную точку Azure AD 2.0. Он также показывает, как получить доступ к Microsoft Graph и показывает полученный токен. <br>![Топология](media/msal-net-uwp-considerations/topology-xamarin-native.png)|
