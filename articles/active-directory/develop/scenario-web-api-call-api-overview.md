---
title: Создание веб-API, вызывающего веб-API — платформа Microsoft Identity | Службы
description: Узнайте, как создать веб-API, который вызывает нисходящие веб-API (обзор).
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/07/2019
ms.author: jmprieur
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: 88a0177755fbd913bdaaf0ecf3e12c62dee294c1
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2020
ms.locfileid: "80885078"
---
# <a name="scenario-a-web-api-that-calls-web-apis"></a>Сценарий: веб-API, который вызывает веб-API

Узнайте, что нужно знать для создания веб-API, который вызывает веб-API.

## <a name="prerequisites"></a>Предварительные требования

Этот сценарий, в котором защищенный веб-API вызывает веб-API, строится поверх сценария "Защита веб-интерфейса API". Дополнительные сведения об этом базовом сценарии см. в статье [сценарий: защищенный веб-API](scenario-protected-web-api-overview.md).

## <a name="overview"></a>Обзор

- Клиентское, настольное, мобильное или одностраничное приложение (не представленное в сопровождающей схеме) вызывает защищенный веб-API и предоставляет маркер носителя JSON Web Token (JWT) в своем HTTP-заголовке "Authorization".
- Защищенный веб-API проверяет маркер и использует метод библиотеки проверки подлинности Майкрософт ( `AcquireTokenOnBehalfOf` MSAL) для запроса другого маркера из Azure Active Directory (Azure AD), чтобы защищенный веб-API мог вызвать второй веб-API или нисходящий веб-API от имени пользователя.
- Защищенный веб-API также может `AcquireTokenSilent`вызываться позже для запроса маркеров для других нисходящих API от имени того же пользователя. `AcquireTokenSilent`обновляет токен при необходимости.

![Схема веб-API, вызывающего веб-API](media/scenarios/web-api.svg)

## <a name="specifics"></a>Особенности

Часть регистрации приложения, связанная с разрешениями API, является классической. Конфигурация приложения включает использование потока OAuth 2,0 от имени пользователя для обмена токеном носителя JWT с маркером для подчиненного API. Этот маркер добавляется в кэш маркеров, где он доступен на контроллерах веб-API, и затем может получить маркер без уведомления для вызова интерфейсов API нисходящей связи.

## <a name="next-steps"></a>Дальнейшие действия

> [!div class="nextstepaction"]
> [Регистрация приложения](scenario-web-api-call-api-app-registration.md)
