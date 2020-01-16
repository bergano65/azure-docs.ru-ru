---
title: Создание веб-API, вызывающего веб-API — платформа Microsoft Identity | Службы
description: Узнайте, как создать веб-API, который вызывает нисходящие веб-API (обзор).
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
ms.custom: aaddev, identityplatformtop40
ms.collection: M365-identity-device-management
ms.openlocfilehash: b67507daf8005f3f9a299b778f1fba4ce67d46d4
ms.sourcegitcommit: 05cdbb71b621c4dcc2ae2d92ca8c20f216ec9bc4
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/16/2020
ms.locfileid: "76044160"
---
# <a name="scenario-a-web-api-that-calls-web-apis"></a>Сценарий: веб-API, который вызывает веб-API

Узнайте, что нужно знать для создания веб-API, который вызывает веб-API.

## <a name="prerequisites"></a>Технические условия

Этот сценарий, в котором защищенный веб-API вызывает веб-API, строится поверх сценария "Защита веб-интерфейса API". Дополнительные сведения об этом базовом сценарии см. в статье [сценарий: защищенный веб-API](scenario-protected-web-api-overview.md).

## <a name="overview"></a>Обзор

- Клиентское, настольное, мобильное или одностраничное приложение (не представленное в сопровождающей схеме) вызывает защищенный веб-API и предоставляет маркер носителя JSON Web Token (JWT) в своем HTTP-заголовке "Authorization".
- Защищенный веб-API проверяет маркер и использует метод `AcquireTokenOnBehalfOf` (MSAL) для запроса другого маркера из Azure Active Directory (Azure AD), чтобы защищенный веб-API мог вызывать второй веб-API или нисходящий веб-API от имени пользователя.
- Защищенный веб-API также может вызвать `AcquireTokenSilent`позже, чтобы запросить маркеры для других нисходящих интерфейсов API от имени того же пользователя. `AcquireTokenSilent` обновляет маркер при необходимости.

![Схема веб-API, вызывающего веб-API](media/scenarios/web-api.svg)

## <a name="specifics"></a>Особенности

Часть регистрации приложения, связанная с разрешениями API, является классической. Конфигурация приложения включает использование потока OAuth 2,0 от имени пользователя для обмена токеном носителя JWT с маркером для подчиненного API. Этот маркер добавляется в кэш маркеров, где он доступен на контроллерах веб-API, и затем может получить маркер без уведомления для вызова интерфейсов API нисходящей связи.

## <a name="next-steps"></a>Дальнейшие действия

> [!div class="nextstepaction"]
> [Интеграция приложений с Azure Active Directory](scenario-web-api-call-api-app-registration.md)
