---
title: Веб-API, который вызывает нисходящие веб-API (обзор) — платформа Microsoft Identity
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
ms.openlocfilehash: 1ef9fc121b16d81eed932d1ab55ca38d2a2f1057
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/08/2019
ms.locfileid: "68852501"
---
# <a name="scenario-web-api-that-calls-web-apis"></a>Сценарий: Веб-API, которые вызывают веб-API

Изучите все, что нужно для создания веб-API, который вызывает веб-API.

## <a name="prerequisites"></a>предварительные требования

Этот сценарий, защищенный веб-API, который вызывает веб-API, строится на основе сценария "Защита веб-интерфейса API". Дополнительные сведения об этом базовом сценарии см. в статье о [защищенном веб-API —](scenario-protected-web-api-overview.md) Предварительная ситуация.

## <a name="overview"></a>Обзор

- Клиент (веб-приложение, настольное, мобильное или одностраничное), не представленное на приведенной ниже схеме, вызывает защищенный веб-API и предоставляет токен носителя JWT в своем HTTP-заголовке "Authorization".
- Защищенный веб-API проверяет маркер и использует метод MSAL `AcquireTokenOnBehalfOf` для запроса (из Azure AD) другого маркера, чтобы он мог сам вызывать второй веб-API (именуемый нисходящим веб-API) от имени пользователя.
- Защищенный веб-API использует этот токен для вызова подчиненного API. Он также может вызываться `AcquireTokenSilent`позже для запроса маркеров для других нисходящих API (но по-прежнему от имени того же пользователя). `AcquireTokenSilent`обновляет токен при необходимости.

![Веб-API, вызывающий веб-API](media/scenarios/web-api.svg)

## <a name="specifics"></a>Особенности

Часть регистрации приложения, связанная с разрешениями API, является классической. Конфигурация приложения включает использование потока OAuth 2,0 от имени пользователя для обмена токеном носителя JWT с маркером для подчиненного API. Этот маркер добавляется в кэш маркеров, где он доступен на контроллерах веб-API, и может получать маркер без уведомления для вызова нисходящих интерфейсов API.

## <a name="next-steps"></a>Следующие шаги

> [!div class="nextstepaction"]
> [Интеграция приложений с Azure Active Directory](scenario-web-api-call-api-app-registration.md)
