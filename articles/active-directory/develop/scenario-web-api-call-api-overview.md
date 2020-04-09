---
title: Создайте веб-aPI, который вызывает веб-API - идентификационную платформу Microsoft (ru) Azure
description: Узнайте, как создать веб-aPI, который вызывает ниже по течению веб-API (обзор).
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
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/08/2020
ms.locfileid: "80885078"
---
# <a name="scenario-a-web-api-that-calls-web-apis"></a>Сценарий: веб-aPI, который вызывает веб-API

Узнайте, что вам нужно знать, чтобы создать веб-aPI, который вызывает web-aPI.

## <a name="prerequisites"></a>Предварительные требования

Этот сценарий, в котором защищенный веб-API вызывает веб-API, строится поверх сценария "Защита веб-API". Чтобы узнать больше об этом основополагающем сценарии, [см.](scenario-protected-web-api-overview.md)

## <a name="overview"></a>Обзор

- Клиент веб-, настольных, мобильных или одностраничных приложений (не представленный на сопроводительной диаграмме) вызывает защищенный веб-API и предоставляет токен на предъявителя JSON Web (JWT) в заголовке HTTP "Авторизация".
- Защищенный веб-API проверяет токен и использует метод Библиотеки `AcquireTokenOnBehalfOf` подлинности Майкрософт (MSAL) для запроса другого маркера из Azure Active Directory (Azure AD), чтобы защищенный web API мог вызывать второй web API или вниз по течению web API от имени пользователя.
- Защищенный веб-API `AcquireTokenSilent`также может позвонить позже, чтобы запросить токены для других нижепоподней API от имени того же пользователя. `AcquireTokenSilent`обновляет токен, когда это необходимо.

![Диаграмма веб-API вызова веб-API](media/scenarios/web-api.svg)

## <a name="specifics"></a>Специфики

Часть регистрации приложения, связанная с разрешениями API, является классической. Конфигурация приложения включает в себя использование потока OAuth 2.0 On-Behalf-Of для обмена токена на предъявителя JWT на токен ниже по течению. Этот маркер добавляется в кэш маркеров, где он доступен в контроллерах Web API, и затем он может получить токен молча, чтобы вызвать ниже по течению API.

## <a name="next-steps"></a>Дальнейшие действия

> [!div class="nextstepaction"]
> [Регистрация приложения](scenario-web-api-call-api-app-registration.md)
