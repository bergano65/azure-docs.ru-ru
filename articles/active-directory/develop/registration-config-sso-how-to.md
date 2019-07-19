---
title: Настройка единого входа приложения | Документация Майкрософт
description: В этой статье описано, как настроить единый вход для пользовательского приложения, которое вы разрабатываете, и зарегистрировать это приложение в Azure AD.
services: active-directory
documentationcenter: ''
author: rwike77
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: develop
ms.custom: aaddev
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/15/2019
ms.author: ryanwi
ms.collection: M365-identity-device-management
ms.openlocfilehash: fbc9f20903d65fbde161d26ed3e194e63d341ccb
ms.sourcegitcommit: 4b431e86e47b6feb8ac6b61487f910c17a55d121
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/18/2019
ms.locfileid: "68320957"
---
# <a name="how-to-configure-single-sign-on-for-an-application"></a>Настройка единого входа для приложения

При настройке федерации с помощью Azure AD для OpenID Connect, SAML 2.0 или WS-Fed федеративный единый вход для вашего приложения включается автоматически. Если конечным пользователям приходится снова входить в систему несмотря на то, что сеанс Azure AD уже существует, возможно, приложение настроено неправильно.

* Если вы используете ADAL и MSAL, убедитесь, что параметр **PromptBehavior** имеет значение **Auto**, а не **Always**.

* При создании мобильного приложения для включения единого входа с использованием или без использования брокера может потребоваться дополнительная настройка.

Для Android см. раздел [Включение единого входа для нескольких приложений в Android](https://docs.microsoft.com/azure/active-directory/develop/active-directory-sso-android).<br>

Для iOS см. раздел [Включение единого входа для нескольких приложений в iOS](https://docs.microsoft.com/azure/active-directory/develop/active-directory-sso-ios).

## <a name="next-steps"></a>Следующие шаги

[Единый вход в Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)<br>

[Включение единого входа для нескольких приложений в Android](https://docs.microsoft.com/azure/active-directory/develop/active-directory-sso-android)<br>

[Включение единого входа для нескольких приложений в iOS](https://docs.microsoft.com/azure/active-directory/develop/active-directory-sso-ios)<br>

[Интеграция приложений с Azure AD](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications)<br>

[Согласие и разрешения для конвергированных приложений в Azure AD версии 2.0](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-scopes)<br>

[StackOverflow в AzureAD](https://stackoverflow.com/questions/tagged/azure-active-directory)
