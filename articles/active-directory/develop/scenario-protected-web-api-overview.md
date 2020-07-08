---
title: Защищенный веб-API — обзор
titleSuffix: Microsoft identity platform
description: Узнайте, как создать защищенный веб-API (обзор).
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
ms.openlocfilehash: cf66757d28a3883664aaacd85baad9cc0dea6956
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: ru-RU
ms.lasthandoff: 07/02/2020
ms.locfileid: "81537208"
---
# <a name="scenario-protected-web-api"></a>Сценарий: защищенный веб-API

В этом сценарии вы узнаете, как предоставить веб-API. Вы также узнаете, как защитить веб-API таким образом, чтобы доступ к нему могли получить только пользователи, прошедшие проверку подлинности.

Чтобы использовать веб-API, необходимо либо включить прошедшие проверку подлинности пользователей с помощью рабочих и учебных учетных записей, либо включить личные учетные записи Майкрософт.

## <a name="prerequisites"></a>Предварительные условия

[!INCLUDE [Pre-requisites](../../../includes/active-directory-develop-scenarios-prerequisites.md)]

## <a name="specifics"></a>Особенности

Ниже приведены конкретные сведения, которые необходимо знать для защиты веб-API.

- Регистрация приложения должна предоставлять по крайней мере одну область. Версия токена, принимаемая веб-API, зависит от аудитории для входа.
- Конфигурация кода для веб-API должна проверять токен, используемый при вызове веб-API.

## <a name="next-steps"></a>Дальнейшие шаги

> [!div class="nextstepaction"]
> [Регистрация приложения](scenario-protected-web-api-app-registration.md)
