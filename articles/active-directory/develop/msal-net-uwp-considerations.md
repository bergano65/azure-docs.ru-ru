---
title: Замечания по UWP (MSAL.NET) | Службы
titleSuffix: Microsoft identity platform
description: Сведения о вопросах использования универсальная платформа Windows (UWP) с помощью библиотеки проверки подлинности Майкрософт для .NET (MSAL.NET).
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
ms.openlocfilehash: 502bbe65cdc0aef768ff4f017b1f6a920815b001
ms.sourcegitcommit: b8702065338fc1ed81bfed082650b5b58234a702
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/11/2020
ms.locfileid: "88118864"
---
# <a name="considerations-for-using-universal-windows-platform-with-msalnet"></a>Рекомендации по использованию универсальная платформа Windows с MSAL.NET
Разработчики приложений, использующих универсальная платформа Windows (UWP) с MSAL.NET, должны учитывать концепции, представленные в этой статье.

## <a name="the-usecorporatenetwork-property"></a>Свойство Усекорпоратенетворк
На платформе среда выполнения Windows (WinRT) `PublicClientApplication` имеет логическое свойство `UseCorporateNetwork` . Это свойство позволяет приложениям Windows 8.1 и приложениям UWP использовать преимущества встроенной проверки подлинности Windows (IWA), если пользователь вошел в учетную запись с клиентом Федерации Azure Active Directory (Azure AD). Пользователи, выполнившие вход в операционную систему, также могут использовать единый вход (SSO). При задании `UseCorporateNetwork` свойства MSAL.NET использует брокер веб-проверки подлинности (WAB).

> [!IMPORTANT]
> Присвоение `UseCorporateNetwork` свойству значения true предполагает, что разработчик приложения ВКЛЮЧИЛ IWA в приложении. Чтобы включить IWA, сделайте следующее:
> - В приложении UWP `Package.appxmanifest` на вкладке **возможности** включите следующие возможности.
>   - **Корпоративная проверка подлинности**
>   - **Частные сети (клиент и сервер)**
>   - **Сертификат общего пользователя**

IWA не включен по умолчанию, так как Microsoft Store требует высокого уровня проверки перед приемом приложений, запрашивающих возможности проверки подлинности предприятия или общих сертификатов пользователей. Не все разработчики хотят выполнить этот уровень проверки.

На платформе UWP Базовая реализация WAB работает неправильно в корпоративных сценариях с включенным условным доступом. Пользователи видят симптомы этой проблемы при попытке входа с помощью Windows Hello. Когда пользователю предлагается выбрать сертификат:

- Не найден сертификат для ПИН-кода.
- После того, как пользователь выберет сертификат, он не запрашивает ввод ПИН-кода.

Вы можете попытаться избежать этой проблемы с помощью альтернативного метода, например имени пользователя, пароля и телефонной проверки подлинности, но неплохой опыт.

## <a name="troubleshooting"></a>Устранение неполадок

Некоторые клиенты сообщили об ошибке входа в определенных корпоративных средах, в которых они узнают, что они имеют подключение к Интернету и что подключение работает с общедоступной сетью.

```Text
We can't connect to the service you need right now. Check your network connection or try this again later.
```

Эту ошибку можно избежать, убедившись, что WAB (базовый компонент Windows) разрешает использовать частную сеть. Это можно сделать, задав раздел реестра:

```Text
HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Image File Execution Options\authhost.exe\EnablePrivateNetwork = 00000001
```

Дополнительные сведения см. в разделе [посредник веб-проверки подлинности — Fiddler](/windows/uwp/security/web-authentication-broker#fiddler).

## <a name="next-steps"></a>Дальнейшие действия
В следующих примерах содержатся дополнительные сведения.

Образец | Платформа | Описание 
|------ | -------- | -----------|
|[Active-Directory-DotNet-Native-UWP-v2](https://github.com/azure-samples/active-directory-dotnet-native-uwp-v2) | UWP | Клиентское приложение UWP, использующее MSAL.NET. Он обращается к Microsoft Graph для пользователя, который выполняет проверку подлинности с помощью конечной точки Azure AD 2,0. <br>![Топология](media/msal-net-uwp-considerations/topology-native-uwp.png)|
|[Active-Directory-Xamarin-Native-v2](https://github.com/Azure-Samples/active-directory-xamarin-native-v2) | Xamarin iOS, Android, UWP; | Простое приложение Xamarin Forms, которое показывает, как использовать MSAL для проверки подлинности личных учетных записей Майкрософт и Azure AD через конечную точку Azure AD 2,0. Здесь также показано, как получить доступ к Microsoft Graph и показать получившийся маркер. <br>![Топология](media/msal-net-uwp-considerations/topology-xamarin-native.png)|