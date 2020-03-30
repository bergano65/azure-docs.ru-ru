---
title: MSAL для iOS & macOS различия Azure
titleSuffix: Microsoft identity platform
description: Описывает различия в использовании библиотеки подлинности Microsoft (MSAL) между iOS и macOS.
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 08/28/2019
ms.author: marsma
ms.reviewer: oldalton
ms.custom: aaddev
ms.openlocfilehash: 62b79ee7398286b8e6c8ed8612bd001595e1f6ea
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77084981"
---
# <a name="microsoft-authentication-library-for-ios-and-macos-differences"></a>Отличия библиотеки проверки подлинности Майкрософт для iOS и macOS

В этой статье объясняется различия в функциональности между библиотекой подлинности Microsoft (MSAL) для iOS и macOS.

> [!NOTE]
> На Mac MSAL поддерживает только приложения macOS.

## <a name="general-differences"></a>Общие различия

MSAL для macOS — это подмножество функций, доступных для iOS.

MSAL для macOS не поддерживает:

- различные типы `ASWebAuthenticationSession` `SFAuthenticationSession`браузеров, такие как , . `SFSafariViewController`
- при посредничестве аутентификации через приложение Microsoft Authenticator не поддерживается для macOS.

Обмен ключей между приложениями от одного и того же издателя более ограничен на macOS 10.14 и ранее. Используйте [списки управления доступом,](https://developer.apple.com/documentation/security/keychain_services/access_control_lists?language=objc) чтобы указать пути к приложениям, которые должны совместно использовать брелок. Пользователь может видеть дополнительные подсказки брелока.

На macOS 10.15 "поведение MSAL является одинаковым между iOS и macOS. MSAL использует [группы доступа к брелоку](https://developer.apple.com/documentation/security/keychain_services/keychain_items/sharing_access_to_keychain_items_among_a_collection_of_apps?language=objc) для совместного использования брелоков. 

### <a name="conditional-access-authentication-differences"></a>Условные различия в аутентификации доступа

Для сценариев условного доступа при использовании MSAL для iOS будет меньше запросов пользователей. Это связано с тем, что iOS использует приложение брокера (Microsoft Authenticator), которое сводит на нет необходимость запроса пользователя в некоторых случаях.

### <a name="project-setup-differences"></a>Различия в настройках проекта

**macOS**

- При настройке проекта на macOS убедитесь, что ваше приложение подписано действительным сертификатом разработки или производства. MSAL по-прежнему работает в неподписанном режиме, но он будет вести себя по-разному в отношении сохранения кэша. Приложение должно быть запущено только без подписи для целей отладки. Если вы распространяете приложение без подписи, оно будет:
1. На 10.14 и ранее, MSAL будет подсказывать пользователю для keychain пароль каждый раз, когда они перезапускают приложение.
2. На 10.15, MSAL подскажет пользователю учетные данные для каждого приобретения токенов. 

- Приложения macOS не должны выполнять вызов AppDelegate.

**iOS**

- Существуют дополнительные шаги для настройки проекта для поддержки потока брокера по аутентификации. Шаги называются в учебнике.
- Проекты iOS должны регистрировать пользовательские схемы в info.plist. Это не требуется на macOS.
