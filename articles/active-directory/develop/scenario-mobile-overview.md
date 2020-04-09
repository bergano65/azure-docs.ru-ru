---
title: Создайте мобильное приложение, которое вызывает веб-AI(ru) Azure
titleSuffix: Microsoft identity platform | Azure
description: Узнайте, как создать мобильное приложение, которое вызывает веб-AIS (обзор)
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/07/2019
ms.author: jmprieur
ms.reviewer: brandwe
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: 1f90f7f23fbdf10b91d8dfc7cd00cca83cd32fbc
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/08/2020
ms.locfileid: "80882579"
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
- **Реализация режима общего устройства**: Включить приложение для использования в сценариях общих устройств, например в больницах, производстве, розничной торговле и финансах. [Подробнее о поддержке режима общего устройства.](msal-shared-devices.md)

## <a name="specifics"></a>Специфики

Имейте в виду следующие соображения при создании мобильного приложения на платформе идентификации Майкрософт:

- В зависимости от платформы может потребоваться некоторое взаимодействие с пользователем при первом входе в систему. Например, iOS требует, чтобы приложения отображали взаимодействие с пользователем при использовании SSO в первые разы через Microsoft Authenticator (и портал Intune Company на Android).
- На iOS и Android MSAL может использовать внешний браузер для регистрации пользователей. Внешний браузер может отображаться в верхней части приложения.
- Никогда не используйте секрет в мобильном приложении. В этих приложениях секреты доступны для всех пользователей.

## <a name="next-steps"></a>Дальнейшие действия

> [!div class="nextstepaction"]
> [Регистрация приложения](scenario-mobile-app-registration.md)
