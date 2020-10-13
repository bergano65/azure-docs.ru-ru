---
title: Дополнительные сведения о MSAL | Службы
titleSuffix: Microsoft identity platform
description: Библиотека проверки подлинности Майкрософт (MSAL) позволяет разработчикам приложений получать маркеры для вызова защищенных веб-API. Этими веб-API могут быть Microsoft Graph, другие API Майкрософт, сторонние веб-API или собственный веб-API. MSAL поддерживает несколько архитектур и платформ приложений.
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 10/30/2019
ms.author: marsma
ms.reviewer: saeeda
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: 0c4da177644a1cdb648c00e8309c18031a905d7f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/09/2020
ms.locfileid: "91825947"
---
# <a name="overview-of-microsoft-authentication-library-msal"></a>Обзор библиотеки аутентификации Майкрософт (MSAL)
Библиотека проверки подлинности Майкрософт (MSAL) позволяет разработчикам получать [токены](developer-glossary.md#security-token) из конечной точки платформы Microsoft Identity, чтобы получить доступ к защищенным веб-API. Этими веб-API могут быть Microsoft Graph, другие API Майкрософт, сторонние веб-API или собственный веб-API. MSAL доступен для .NET, JavaScript, Java, Python, Android и iOS, поддерживающих множество различных архитектур приложений и платформ.

MSAL предоставляет множество способов для получения маркеров с использованием согласованного API для нескольких платформ. Использование MSAL обеспечивает следующие преимущества:

* Не требуется напрямую использовать библиотеки OAuth или код для соответствующего протокола в приложении.
* Получение маркеров от имени пользователя или приложения (когда применимо к платформе).
* Поддерживает кэш маркеров и обновляет маркеры, срок действия которых скоро истечет. Нет необходимости вручную регулировать истечение срока действия маркера.
* Помогает вам определить аудиторию, которая будет выполнять вход в ваше приложение (ваша организация, несколько организаций, рабочие, школьные и личные учетные записи Майкрософт, удостоверения социальных сетей с помощью Azure AD B2C, пользователи в независимых и национальных облаках).
* Помогает настроить приложение из файлов конфигурации.
* Поможет вам устранять неполадки, выявляя действенные исключения, ведя журналы и телеметрию.

## <a name="application-types-and-scenarios"></a>Типы приложений и сценариев
С помощью MSAL вы можете получить маркер из нескольких типов приложений: веб-приложений, веб-API, одностраничных приложений (JavaScript), мобильных и собственных приложений, управляющих программ и серверных приложений.

MSAL можно использовать во многих сценариях приложений, включая следующие:

* [Одностраничные приложения (JavaScript)](scenario-spa-overview.md)
* [веб-приложения, в которые могут входить пользователи;](scenario-web-app-sign-user-overview.md)
* [веб-приложения, в которые могут входить пользователи и которые могут вызывать веб-API от имени пользователя;](scenario-web-app-call-api-overview.md)
* [защита веб-API, чтобы доступ к нему могли получить только прошедшие аутентификацию пользователи;](scenario-protected-web-api-overview.md)
* [веб-API, вызывающий другой подчиненный веб-API от имени выполнившего вход пользователя;](scenario-web-api-call-api-overview.md)
* [Классическое приложение, вызывающее веб-API от имени пользователя, выполнившего вход](scenario-desktop-overview.md)
* [Мобильное приложение, вызывающее веб-API от имени пользователя, выполнившего вход в интерактивном режиме](scenario-mobile-overview.md).
* [классическая управляющая программа / управляющая программа службы, вызывающая веб-API от своего имени.](scenario-daemon-overview.md)

## <a name="languages-and-frameworks"></a>Языки и платформы

| Библиотека | Поддерживаемые платформы и среды|
| --- | --- |
| [MSAL.NET](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet)| Платформа .NET Framework, .NET Core, Xamarin Android, Xamarin iOS, универсальная платформа Windows|
| [MSAL.js](https://github.com/AzureAD/microsoft-authentication-library-for-js)| Платформы JavaScript/TypeScript, такие как AngularJS, Ember.js или Durandal.js|
| [MSAL для Android](https://github.com/AzureAD/microsoft-authentication-library-for-android)|Android|
| [MSAL для iOS и MacOS](https://github.com/AzureAD/microsoft-authentication-library-for-objc)|iOS и macOS|
| [MSAL Java](https://github.com/AzureAD/microsoft-authentication-library-for-java)|Windows, macOS, Linux|
| [MSAL Python](https://github.com/AzureAD/microsoft-authentication-library-for-python)|Windows, macOS, Linux|

## <a name="differences-between-adal-and-msal"></a>Различия между ADAL и MSAL

Библиотека проверки подлинности Active Directory (ADAL) интегрируется с конечной точкой Azure AD для разработчиков (версия 1.0), а MSAL интегрируется с конечной точкой платформы удостоверений Майкрософт (версия 2.0). Конечная точка версии 1.0 поддерживает рабочие учетные записи, но не личные учетные записи. Конечная точка Azure AD версии 2.0 — это объединение личных учетных записей Майкрософт и рабочих учетных записей Майкрософт в единую систему аутентификации. Кроме того, с помощью MSAL можно выполнить аутентификацию для Azure AD B2C.

Дополнительные сведения см. в статье о [миграции в MSAL.NET из ADAL.NET](msal-net-migration.md) и [миграции в MSAL.js из ADAL.js](msal-compare-msal-js-and-adal-js.md).
