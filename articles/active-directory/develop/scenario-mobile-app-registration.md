---
title: Мобильное приложение, вызывающее веб-API — конфигурация кода приложения | Платформа Microsoft Identity
description: Узнайте, как создать мобильное приложение, вызывающее веб-API (конфигурация кода приложения).
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
ms.openlocfilehash: f43ae9da51f68c9765a36d27c993d1c9935d61fa
ms.sourcegitcommit: 4b431e86e47b6feb8ac6b61487f910c17a55d121
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/18/2019
ms.locfileid: "68326121"
---
# <a name="mobile-app-that-calls-web-apis---app-registration"></a>Мобильное приложение, вызывающее веб-API — регистрация приложений

Эта статья содержит инструкции по регистрации приложений для создания мобильного приложения.

## <a name="supported-account-types"></a>Поддерживаемые типы учетных записей

Типы учетных записей, поддерживаемые в мобильных приложениях, зависят от интерфейса, который вы хотите включить, и пользователей, для которых предназначено приложение.

## <a name="platform-configuration-and-redirect-uris"></a>Конфигурация платформы и URI перенаправления  

При создании мобильного приложения наиболее важным этапом регистрации является URI перенаправления. Это можно настроить с помощью [конфигурации платформы в колонке проверка](https://aka.ms/MobileAppReg)подлинности.

Этот интерфейс позволит приложению получить единый вход (SSO) через Microsoft Authenticator (и Корпоративный портал Intune на Android), а также поддерживать политики управления устройствами.

Если вы предпочитаете вручную настроить URI перенаправления, это можно сделать с помощью манифеста приложения. Рекомендуется использовать следующий формат:

- ***iOS***:`msauth.<BUNDLE_ID>://auth`
- ***Android***:`msauth://<PACKAGE_NAME>/<SIGNATURE_HASH>`
  - Хэш подписи Android можно создать с помощью ключей выпуска или отладки с помощью команды KeyTool.

## <a name="api-permissions"></a>Разрешения API

Мобильные приложения вызывают API от имени пользователя, выполнившего вход. Приложение должно запрашивать делегированные разрешения, также называемые областями. В зависимости от требуемого интерфейса это можно сделать статически с помощью портал Azure или динамически во время выполнения. Статическая регистрация разрешений позволяет администраторам легко утверждать ваше приложение и рекомендуется.

## <a name="next-steps"></a>Следующие шаги

> [!div class="nextstepaction"]
> [Получение маркера](scenario-mobile-acquire-token.md)
