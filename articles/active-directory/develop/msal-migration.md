---
title: Миграция в библиотеку проверки подлинности Майкрософт (MSAL)
titleSuffix: Microsoft identity platform
description: Сведения о различиях между библиотекой проверки подлинности Майкрософт (MSAL) и библиотекой аутентификация Azure AD (ADAL) и о переходе на MSAL.
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2020
ms.locfileid: "78164936"
---
# <a name="migrate-applications-to-microsoft-authentication-library-msal"></a>Перенос приложений в библиотеку проверки подлинности Майкрософт (MSAL)

Как библиотека проверки подлинности Майкрософт (MSAL), так и библиотека аутентификация Azure AD (ADAL) используются для проверки подлинности сущностей Azure AD и маркеров запросов из Azure AD. До настоящего времени большинство разработчиков использовали для аутентификации удостоверений Azure AD (рабочих и учебных учетных записей) платформу Azure AD для разработчиков (версия 1.0), запрашивая маркеры через библиотеку аутентификации Azure AD (ADAL). Использование MSAL:

- Вы можете проверить подлинность более широкого набора удостоверений Майкрософт (удостоверений Azure AD и учетных записей Майкрософт, а также социальных и локальных учетных записей с помощью Azure AD B2C), так как в нем используется конечная точка платформы Microsoft Identity.
- Пользователи получат лучшие возможности единого входа.
- Приложение может включить добавочное согласие, и поддержка условного доступа упрощается.
- Преимущества инноваций.

**Теперь MSAL является рекомендуемой библиотекой проверки подлинности для использования с платформой Microsoft Identity**. Новые функции не будут реализованы в ADAL. Усилия посвящены улучшению MSAL.

В следующих статьях описываются различия между библиотеками MSAL и ADAL и приведены сведения о переходе на MSAL.
- [Миграция в MSAL.NET](msal-net-migration.md)
- [Миграция в MSAL.js](msal-compare-msal-js-and-adal-js.md)
- [Переход на MSAL.Android](migrate-android-adal-msal.md)
- [Миграция в MSAL.iOS или MSAL.macOS](migrate-objc-adal-msal.md)
- [Переход на MSAL для Python](migrate-python-adal-msal.md)
- [Переход на MSAL для Java](migrate-adal-msal-java.md)
- [Перенос приложений Xamarin в MSAL.NET с помощью брокеров](msal-net-migration-ios-broker.md)
