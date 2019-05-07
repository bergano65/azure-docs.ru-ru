---
title: Мобильное приложение, что вызовы веб-интерфейсы API — конфигурации кода приложения | Платформы удостоверений Microsoft
description: Сведения о создании мобильного приложения, вызовы веб-API-интерфейсы (конфигурации кода приложения)
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
ms.openlocfilehash: 9eac9d1dfce79ac4ad9d49a6cfe6b7dee7f6681a
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/06/2019
ms.locfileid: "65075165"
---
# <a name="mobile-app-that-calls-web-apis---app-registration"></a>Мобильное приложение, что вызовы веб-интерфейсы API — Регистрация приложения

Эта статья содержит инструкции регистрации приложения для создания мобильных приложений.

## <a name="supported-account-types"></a>Поддерживаемые типы учетных записей

Типы учетных записей, поддерживаемые в мобильных приложениях зависят от качества, которые вы хотите включить и пользователей, предназначен для приложения.

## <a name="platform-configuration-and-redirect-uris"></a>Конфигурация платформы и URI перенаправления  

При создании мобильного приложения, наиболее важным этапом регистрации — URI перенаправления. Это можно задать с помощью [конфигурации платформы, в колонке проверки подлинности](https://aka.ms/MobileAppReg).

Эта возможность позволит вашему приложение для получения единого входа (SSO) через Microsoft Authenticator (и корпоративный портал Intune для Android) и поддержку политик управления устройствами.

Если вы хотите вручную настроить URI перенаправления, это можно сделать с помощью манифест приложения. Ниже приводится рекомендуемый формат:

- ***iOS***: `msauth.<BUNDLE_ID>://auth`
- ***Android***: `msauth://<PACKAGE_NAME>/<SIGNATURE_HASH>`
  - Хэша подписи Android можно создавать с помощью окончательную или отладочную ключи через команду KeyTool.

## <a name="api-permissions"></a>Разрешения API

Мобильные приложения вызовите API от имени выполнившего вход пользователя. Приложение должно запрашивать делегированные разрешения, называемый также областей. В зависимости от требуемой работы это можно сделать статически через портал Azure, или динамически во время выполнения. Статически регистрации разрешения позволяет администраторам легко утвердить приложения и рекомендуется.

## <a name="next-steps"></a>Дальнейшие действия

> [!div class="nextstepaction"]
> [Получение токена](scenario-mobile-acquire-token.md)
