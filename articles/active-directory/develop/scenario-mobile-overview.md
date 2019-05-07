---
title: Мобильное приложение, которое вызывает веб-интерфейсы API — Обзор | Платформы удостоверений Microsoft
description: Сведения о создании мобильного приложения, вызовы веб-API-интерфейсы (Обзор)
services: active-directory
documentationcenter: dev-center-name
author: danieldobalian
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/07/2019
ms.author: dadobali
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: eb0acd1534bab11eb57a7aa0e695f192b5999ed2
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/06/2019
ms.locfileid: "65076500"
---
# <a name="scenario-mobile-application-that-calls-web-apis"></a>Сценарий. Мобильных приложений, что вызовы веб-API

Узнайте все необходимое для создания мобильное приложение, которое вызывает веб-API.

## <a name="prerequisites"></a>Технические условия

[!INCLUDE [Prerequisites](../../../includes/active-directory-develop-scenarios-prerequisites.md)]

## <a name="getting-started"></a>Приступая к работе

Создайте свое первое приложение для мобильных устройств и повторите код быстрого запуска!

> [!div class="nextstepaction"]
> [Краткое руководство Получить маркер и вызвать Microsoft Graph API из приложения Android](./quickstart-v2-android.md)
>
> [Краткое руководство Получить маркер и вызвать Microsoft Graph API из приложения iOS](./quickstart-v2-ios.md)
>
> [Краткое руководство Получить маркер и вызвать Microsoft Graph API в Xamarin iOS и Android приложения](https://github.com/Azure-Samples/active-directory-xamarin-native-v2)

## <a name="overview"></a>Обзор

При создании мобильного приложения, важно персонализированные, простой конечным пользователем.  Мобильные разработчики могут обеспечить эти возможности для iOS и Android пользователей платформой Microsoft identity. Приложение можно войти в Azure AD, в личную учетную запись Майкрософт и пользователи Azure AD B2C и получить маркеры для вызова веб-API от их имени. Чтобы реализовать эти потоки, мы будем использовать библиотеку проверки подлинности в Microsoft (MSAL), который реализует стандартные для отрасли [потока кода авторизации OAuth 2.0](v2-oauth2-auth-code-flow.md).

![Управляющая программа](./media/scenarios/mobile-app.svg)

Аспекты работы с мобильными приложениями:

- ***Взаимодействие с пользователем является ключом***: Разрешить пользователям видеть значение приложения перед запросом для входа в систему и запросить только необходимые разрешения.
- ***Поддерживает все конфигурации пользователя***: Многие мобильные корпоративные пользователи находятся под условного доступа и политики соответствия устройств. Убедитесь в том, что для поддержки следующих ключевых сценариев.
- ***Реализовать единый вход (SSO)***: MSAL и платформе Microsoft identity упрощают Включение единого входа через браузер на устройстве или проверка подлинности Microsoft (и корпоративный портал Intune для Android).

## <a name="specifics"></a>Особенности

При создании мобильного приложения на платформе Microsoft identity, работы с управляемыми имеет некоторые особенности:

- В зависимости от платформы вход без взаимодействия с может оказаться невозможным при первом входе. операций ввода-вывода, например, требуется для приложений, чтобы показать взаимодействия с пользователем, при получении времени первого единого входа с помощью проверки подлинности Microsoft (и корпоративный портал Intune для Android).
- В iOS и Android MSAL может использовать внешние браузеров, (которые могут отображаться в верхней части приложения) для входа в систему. Его можно настроить, чтобы вместо этого используйте любимую в приложении.
- Никогда не используйте секреты в мобильном приложении, он будет доступен всем пользователям.

## <a name="next-steps"></a>Дальнейшие действия

> [!div class="nextstepaction"]
> [Интеграция приложений с Azure Active Directory](scenario-mobile-app-registration.md)
