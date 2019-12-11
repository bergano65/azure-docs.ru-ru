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
ms.openlocfilehash: 4b357def86b77d4bbb294e2253dacfbd129998ec
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/10/2019
ms.locfileid: "74965132"
---
# <a name="scenario-web-api-that-calls-web-apis"></a>Сценарий: веб-API, который вызывает веб-API

Изучите все, что нужно для создания веб-API, который вызывает веб-API.

## <a name="prerequisites"></a>Технические условия

Этот сценарий, защищенный веб-API, который вызывает веб-API, строится на основе сценария "Защита веб-интерфейса API". Дополнительные сведения об этом базовом сценарии см. в статье о [защищенном веб-API —](scenario-protected-web-api-overview.md) Предварительная ситуация.

## <a name="overview"></a>Краткое описание

- Клиент (веб-приложение, настольное, мобильное или одностраничное), не представленное на приведенной ниже схеме, вызывает защищенный веб-API и предоставляет токен носителя JWT в своем HTTP-заголовке "Authorization".
- Защищенный веб-API проверяет маркер и использует метод MSAL `AcquireTokenOnBehalfOf` для запроса (из Azure AD) другого маркера, чтобы он мог сам вызывать второй веб-API (именуемый нисходящим веб-API) от имени пользователя.
- Защищенный веб-API использует этот токен для вызова подчиненного API. Он также может вызвать `AcquireTokenSilent`позже, чтобы запросить маркеры для других нижестоящих API (но по-прежнему от имени того же пользователя). `AcquireTokenSilent` обновляет маркер при необходимости.

![Веб-API, вызывающий веб-API](media/scenarios/web-api.svg)

## <a name="specifics"></a>Особенности

Часть регистрации приложения, связанная с разрешениями API, является классической. Конфигурация приложения включает использование потока OAuth 2,0 от имени пользователя для обмена токеном носителя JWT с маркером для подчиненного API. Этот маркер добавляется в кэш маркеров, где он доступен на контроллерах веб-API, и может получать маркер без уведомления для вызова нисходящих интерфейсов API.

## <a name="next-steps"></a>Дальнейшие действия

> [!div class="nextstepaction"]
> [Интеграция приложений с Azure Active Directory](scenario-web-api-call-api-app-registration.md)
