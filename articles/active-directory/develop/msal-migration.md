---
title: Мигрировать в библиотеку подлинности Майкрософт (MSAL)
titleSuffix: Microsoft identity platform
description: Узнайте о различиях между Библиотекой аутентификации Майкрософт (MSAL) и Библиотекой аутентификации Azure AD (ADAL) и о том, как перейти на MSAL.
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 02/27/2020
ms.author: jmprieur
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: 3af18eb09fd9906a0caaebda0b786795400467f3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78164936"
---
# <a name="migrate-applications-to-microsoft-authentication-library-msal"></a>Перенести приложения в Библиотеку подлинности Майкрософт (MSAL)

Библиотека аутентификации Майкрософт (MSAL) и Библиотека аутентификации Azure AD (ADAL) используются для аутентификации объектов Azure AD и запроса токенов из Azure AD. До настоящего времени большинство разработчиков использовали для аутентификации удостоверений Azure AD (рабочих и учебных учетных записей) платформу Azure AD для разработчиков (версия 1.0), запрашивая маркеры через библиотеку аутентификации Azure AD (ADAL). Использование MSAL:

- Можно проверить подлинность более широкого набора идентификаторов Майкрософт (идентификаторы Azure AD и учетные записи Майкрософт, а также социальные и локальные учетные записи через Azure AD B2C) при использовании конечной точки платформы идентификации Майкрософт.
- Ваши пользователи получат лучший опыт с одним подписанием.
- Приложение может включить постепенное согласие, а поддержка условного доступа проще.
- Вы получаете выгоду от нововведения.

**MSAL теперь рекомендуемая библиотека auth для использования с платформой идентификации Майкрософт.** Новые функции на ADAL не будут реализованы. Усилия направлены на улучшение MSAL.

В следующих статьях описаны различия между библиотеками MSAL и ADAL и вы можете перейти на MSAL:
- [Миграция в MSAL.NET](msal-net-migration.md)
- [Миграция в MSAL.js](msal-compare-msal-js-and-adal-js.md)
- [Переход на MSAL.Android](migrate-android-adal-msal.md)
- [Мигрировать на MSAL.iOS / macOS](migrate-objc-adal-msal.md)
- [Переход на MSAL для Python](migrate-python-adal-msal.md)
- [Переход на MSAL для Java](migrate-adal-msal-java.md)
- [Перенос приложений Xamarin в MSAL.NET с помощью брокеров](msal-net-migration-ios-broker.md)
