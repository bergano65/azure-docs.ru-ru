---
title: Создайте мобильное приложение, которое вызывает веб-AI(ru) Azure
titleSuffix: Microsoft identity platform
description: Узнайте, как создать мобильное приложение, которое вызывает веб-AIS (обзор)
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/07/2019
ms.author: jmprieur
ms.reviwer: brandwe
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: 6675d67299091325fcc3e12572a906716bf5b88d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77132421"
---
# <a name="scenario-mobile-application-that-calls-web-apis"></a>Сценарий: Мобильное приложение, которое вызывает веб-AIS

Узнайте, как создать мобильное приложение, которое вызывает web-апоттестаты.

## <a name="prerequisites"></a>Предварительные требования

[!INCLUDE [Prerequisites](../../../includes/active-directory-develop-scenarios-prerequisites.md)]

## <a name="getting-started"></a>Начало работы

Создайте свое первое мобильное приложение и попробуйте быстрый запуск.

> [!div class="nextstepaction"]
> [Быстрый запуск: Приобрести токен и вызов Microsoft Graph API из приложения Android](./quickstart-v2-android.md)
>
> [Быстрый запуск: Приобрести токен и вызов Microsoft Graph API из приложения iOS](./quickstart-v2-ios.md)
>
> [Быстрый запуск: Приобрести токен и вызов Microsoft Graph API из приложения Xamarin iOS и Android](https://github.com/Azure-Samples/active-directory-xamarin-native-v2)

## <a name="overview"></a>Обзор

Персонализированный, бесшовный пользовательский интерфейс необходим для мобильных приложений.  Идентификационная платформа Microsoft позволяет мобильным разработчикам создавать этот опыт для пользователей iOS и Android. Приложение может подписаться в Пользователях Active Directory (Azure AD), персональных пользователях учетных записей Майкрософт и пользователях Azure AD B2C. Он также может приобрести токены для вызова веб-API от их имени. Для реализации этих потоков мы используем библиотеку подлинности Майкрософт (MSAL). MSAL реализует отраслевой стандарт [кода авторизации OAuth2.0.](v2-oauth2-auth-code-flow.md)

![Управляющие программы](./media/scenarios/mobile-app.svg)

Рассмотрение мобильных приложений:

- **Пользовательский опыт является ключевым:** Разрешить пользователям, чтобы увидеть значение вашего приложения, прежде чем попросить о входе в систему. Запрашивайте только необходимые разрешения.
- **Поддержка всех пользовательских конфигураций:** Многие мобильные бизнес-пользователи должны придерживаться политик условного доступа и политик соответствия устройствам. Обязательно поддерживайте эти ключевые сценарии.
- **Реализация одного включения (SSO)**: С помощью платформы MSAL и Microsoft, вы можете включить один очный входе через браузер устройства или Microsoft Authenticator (и Intune Company Portal на Android).

## <a name="specifics"></a>Специфики

Имейте в виду следующие соображения при создании мобильного приложения на платформе идентификации Майкрософт:

- В зависимости от платформы может потребоваться некоторое взаимодействие с пользователем при первом входе в систему. Например, iOS требует, чтобы приложения отображали взаимодействие с пользователем при использовании SSO в первые разы через Microsoft Authenticator (и портал Intune Company на Android).
- На iOS и Android MSAL может использовать внешний браузер для регистрации пользователей. Внешний браузер может отображаться в верхней части приложения. Вы можете настроить конфигурацию для использования в приложении WebViews вместо этого.
- Никогда не используйте секрет в мобильном приложении. В этих приложениях секреты доступны для всех пользователей.

## <a name="next-steps"></a>Дальнейшие действия

> [!div class="nextstepaction"]
> [Регистрация приложения](scenario-mobile-app-registration.md)
