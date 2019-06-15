---
title: Веб-API, который вызывает подчиненными веб-API (Обзор) - платформой Microsoft identity
description: Сведения о создании веб-API, вызывающее подчиненными веб-API-интерфейсы (Обзор).
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
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 497134b7f3cc535f7b3f180db13cd04ef56787db
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/13/2019
ms.locfileid: "65075405"
---
# <a name="scenario-web-api-that-calls-web-apis"></a>Сценарий. Веб-API, которые вызывают веб-API

Узнайте все необходимое для создания веб-API, которые вызывает веб-API-интерфейсы.

## <a name="prerequisites"></a>Технические условия

Этот сценарий, защищенный веб-API, что вызовы веб-API, строится на основе сценария «Защитить веб-API». Дополнительные сведения об этом базовом сценарии, см. в разделе [защищенные веб-API - сценарий](scenario-protected-web-api-overview.md) первого.

## <a name="overview"></a>Обзор

- Клиент (Интернета, рабочего стола, мобильных или одностраничного приложения) — не представлены на схеме ниже - вызывает защищенный веб-API и предоставляет токен носителя JWT в заголовке «Authorization» Http.
- Защищенный веб-API проверяет токен и использует MSAL `AcquireTokenOnBehalfOf` вызов метода для запроса (из Azure AD) другой токен, таким образом, чтобы он, сам по себе может, второй веб-API (с именем нисходящего веб-API) от имени пользователя.
- Защищенный веб-API использует этот маркер для вызова API нижнего уровня. Он также может вызвать `AcquireTokenSilent`более поздней версии, для запроса маркеров для других подчиненных API (но по-прежнему от имени одного пользователя). `AcquireTokenSilent` обновляет маркер при необходимости.

![Веб-API, вызов веб-API](media/scenarios/web-api.svg)

## <a name="specifics"></a>Особенности

В части регистрации приложения, связанных с разрешениями API классической. Конфигурация приложения включает в себя с помощью потока on-behalf-of OAuth 2.0 для обмена токена носителя JWT от маркера для API нижнего уровня. Этот маркер добавляется в кэш маркера, где он доступен в контроллерах веб-интерфейса API, а можно получить токен автоматически, для вызова API подчиненных.

## <a name="next-steps"></a>Дальнейшие действия

> [!div class="nextstepaction"]
> [Интеграция приложений с Azure Active Directory](scenario-web-api-call-api-app-registration.md)
