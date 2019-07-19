---
title: Мобильное приложение, вызывающее веб-API — обзор | Платформа Microsoft Identity
description: Узнайте, как создать мобильное приложение, вызывающее веб-API (обзор)
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
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: dfc5e12bf1633c32a61e03c7df6d6c0320263d6e
ms.sourcegitcommit: 4b431e86e47b6feb8ac6b61487f910c17a55d121
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/18/2019
ms.locfileid: "68325318"
---
# <a name="scenario-mobile-application-that-calls-web-apis"></a>Сценарий. Мобильное приложение, вызывающее веб-API

Узнайте все, что нужно знать, чтобы создать мобильное приложение, вызывающее веб-API.

## <a name="prerequisites"></a>предварительные требования

[!INCLUDE [Prerequisites](../../../includes/active-directory-develop-scenarios-prerequisites.md)]

## <a name="getting-started"></a>Начало работы

Создайте свое первое мобильное приложение и попробуйте воспользоваться кратким руководством.

> [!div class="nextstepaction"]
> [Краткое руководство Получение маркера и вызов Microsoft Graph API из приложения Android](./quickstart-v2-android.md)
>
> [Краткое руководство Получение маркера и вызов Microsoft Graph API из приложения iOS](./quickstart-v2-ios.md)
>
> [Краткое руководство Получение маркера и вызов Microsoft Graph API из приложения Xamarin iOS & Android](https://github.com/Azure-Samples/active-directory-xamarin-native-v2)

## <a name="overview"></a>Обзор

Для мобильных приложений очень важно персонализированный и удобный пользовательский интерфейс.  Платформа Microsoft Identity позволяет разработчикам мобильных приложений создавать такие возможности для пользователей iOS и Android. Приложение может выполнять вход Azure Active Directory пользователей (Azure AD), персональные учетная запись Майкрософт пользователей и Azure AD B2C пользователей, а также получать маркеры для вызова веб-API от их имени. Для реализации этих потоков мы будем использовать библиотеку проверки подлинности Майкрософт (MSAL), которая реализует [поток кода авторизации OAuth 2.0](v2-oauth2-auth-code-flow.md)отраслевого стандарта.

![Управляющие программы](./media/scenarios/mobile-app.svg)

Рекомендации для мобильных приложений:

- **Взаимодействие с пользователем — это ключ**: Разрешить пользователям видеть значение приложения перед запросом на вход и запросить только необходимые разрешения.
- **Поддержка всех конфигураций пользователей**: Многие мобильные бизнес-пользователи работают под управлением условного доступа и политик соответствия устройств. Не забудьте обеспечить поддержку этих ключевых сценариев.
- **Реализация единого входа (SSO)** : MSAL и платформа идентификации Майкрософт делают возможным простое использование единого входа через браузер устройства или Microsoft Authenticator (и Корпоративный портал Intune на Android).

## <a name="specifics"></a>Особенности

При создании мобильного приложения на платформе Microsoft Identity следует учитывать следующие моменты.

- В зависимости от платформы может потребоваться некоторое взаимодействие с пользователем при первом входе. Например, для iOS требуется, чтобы приложения отображали взаимодействие с пользователем при первом использовании единого входа с помощью Microsoft Authenticator (и Корпоративный портал Intune на Android).
- В iOS и Android MSAL может использовать внешний браузер (который может отображаться поверх вашего приложения) для входа пользователей. Вместо этого можно настроить конфигурацию для использования представлений в приложении.
- Никогда не используйте секрет в мобильном приложении. Он будет доступен всем пользователям.

## <a name="next-steps"></a>Следующие шаги

> [!div class="nextstepaction"]
> [Интеграция приложений с Azure Active Directory](scenario-mobile-app-registration.md)
