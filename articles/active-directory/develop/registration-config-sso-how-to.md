---
title: Настройка единого входа приложения
description: В этой статье описано, как настроить единый вход для пользовательского приложения, которое вы разрабатываете, и зарегистрировать это приложение в Azure AD.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.custom: aaddev
ms.workload: identity
ms.topic: conceptual
ms.date: 07/15/2019
ms.author: ryanwi
ms.openlocfilehash: 465adbb71abaa45160399ecba2ebfb692a8307c2
ms.sourcegitcommit: b8702065338fc1ed81bfed082650b5b58234a702
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/11/2020
ms.locfileid: "88120683"
---
# <a name="how-to-configure-single-sign-on-for-an-application"></a>Настройка единого входа для приложения

При настройке федерации с помощью Azure AD для OpenID Connect, SAML 2.0 или WS-Fed федеративный единый вход для вашего приложения включается автоматически. Если конечным пользователям приходится снова входить в систему несмотря на то, что сеанс Azure AD уже существует, возможно, приложение настроено неправильно.

* Если вы используете ADAL и MSAL, убедитесь, что параметр **PromptBehavior** имеет значение **Auto**, а не **Always**.

* При создании мобильного приложения для включения единого входа с использованием или без использования брокера может потребоваться дополнительная настройка.

Для Android см. раздел [Включение единого входа для нескольких приложений в Android](../azuread-dev/howto-v1-enable-sso-android.md).<br>

Для iOS см. раздел [Включение единого входа для нескольких приложений в iOS](../azuread-dev/howto-v1-enable-sso-ios.md).

## <a name="next-steps"></a>Дальнейшие действия

[Единый вход Azure AD](../manage-apps/what-is-single-sign-on.md)<br>

[Включение единого входа для нескольких приложений в Android](../azuread-dev/howto-v1-enable-sso-android.md)<br>

[Включение единого входа для нескольких приложений в iOS](../azuread-dev/howto-v1-enable-sso-ios.md)<br>

[Интеграция приложений с Azure AD](./quickstart-register-app.md)<br>

[Разрешения и согласие для конечной точки платформы удостоверений Майкрософт](./v2-permissions-and-consent.md)<br>

[StackOverflow в AzureAD](https://stackoverflow.com/questions/tagged/azure-active-directory)