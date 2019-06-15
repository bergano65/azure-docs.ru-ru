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
ms.author: jmprieur
ms.reviwer: brandwe
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0f007ad1d5bf99136328ec5706f7ccbb5f6593c8
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/13/2019
ms.locfileid: "67111228"
---
# <a name="scenario-mobile-application-that-calls-web-apis"></a>Сценарий. Мобильных приложений, что вызовы веб-API

Узнайте все что нужно знать для создания мобильное приложение, которое вызывает веб-API.

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

Персонализированные простым пользовательским интерфейсом, очень важно для мобильных приложений.  Мобильные разработчики могут создавать этот опыт для iOS и Android пользователей платформой Microsoft identity. Приложение можно войти пользователей Azure Active Directory (Azure AD), личные пользователям учетных записей Майкрософт и пользователей Azure AD B2C и получить маркеры для вызова веб-API от их имени. Чтобы реализовать эти потоки, мы будем использовать библиотеку проверки подлинности Майкрософт (MSAL), который реализует стандартные для отрасли [потока кода авторизации OAuth 2.0](v2-oauth2-auth-code-flow.md).

![Управляющие программы](./media/scenarios/mobile-app.svg)

Замечания в отношении мобильных приложений.

- **Взаимодействие с пользователем является ключом**: Разрешить пользователям видеть значение приложения перед запросом для входа в систему и запросить только необходимые разрешения.
- **Поддерживает все конфигурации пользователя**: Многие мобильные корпоративные пользователи находятся под условного доступа и политики соответствия устройств. Убедитесь в том, что для поддержки следующих ключевых сценариев.
- **Реализовать единый вход (SSO)** : MSAL и платформе Microsoft identity упрощают Включение единого входа через браузер на устройстве или проверка подлинности Microsoft (и корпоративный портал Intune для Android).

## <a name="specifics"></a>Особенности

При создании мобильного приложения на платформе Microsoft identity учитывайте эти аспекты:

- В зависимости от платформы взаимодействие пользователя может потребоваться при первом входе пользователей. Например iOS требуется для приложений, чтобы показать взаимодействия с пользователем при использовании единого входа в первый раз с помощью проверки подлинности Microsoft (и корпоративный портал Intune для Android).
- В iOS и Android MSAL может выполнять вход пользователей во внешнем браузере (который может появляться поверх приложения). Можно настроить конфигурацию, чтобы вместо этого используйте любимую в приложении.
- Никогда не используйте секреты в мобильном приложении. Он будет доступен всем пользователям.

## <a name="next-steps"></a>Дальнейшие действия

> [!div class="nextstepaction"]
> [Интеграция приложений с Azure Active Directory](scenario-mobile-app-registration.md)
