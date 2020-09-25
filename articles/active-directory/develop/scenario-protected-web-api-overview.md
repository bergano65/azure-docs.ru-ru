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
ms.date: 07/14/2020
ms.author: jmprieur
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: c9ff9ae811a29685937b922f04a277e663e26f1f
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/25/2020
ms.locfileid: "91257370"
---
# <a name="scenario-protected-web-api"></a>Сценарий: защищенный веб-API

В этом сценарии вы узнаете, как предоставить веб-API. Вы также узнаете, как защитить веб-API таким образом, чтобы доступ к нему могли получить только пользователи, прошедшие проверку подлинности.

Чтобы использовать веб-API, необходимо либо включить прошедшие проверку подлинности пользователей с помощью рабочих и учебных учетных записей, либо включить личные учетные записи Майкрософт.

## <a name="specifics"></a>Особенности

Ниже приведены конкретные сведения, которые необходимо знать для защиты веб-API.

- Регистрация приложения должна предоставлять по крайней мере одну *область* или одну *роль приложения*.
  - Области предоставляются веб-API, которые вызываются от имени пользователя.
  - Роли приложений предоставляются веб-API, которые вызываются приложениями управляющей программы (которые вызывают ваш веб-API от своего имени).
- Если вы создаете новую регистрацию приложения веб-API, выберите [версию маркера доступа](reference-app-manifest.md#accesstokenacceptedversion-attribute) , которая принимает ваш веб-API `2` . Для устаревших веб-API допустимой версией токена может быть `null` , но это значение запрещает доступ только для организаций, а личные учетные записи Майкрософт (MSA) не поддерживаются.
- Конфигурация кода для веб-API должна проверять токен, используемый при вызове веб-API.
- Код в действиях контроллера должен проверять роли или области в маркере.

## <a name="recommended-reading"></a>Рекомендуемые материалы

[!INCLUDE [recommended-topics](../../../includes/active-directory-develop-scenarios-prerequisites.md)]

## <a name="next-steps"></a>Дальнейшие действия

> [!div class="nextstepaction"]
> [Регистрация приложения](scenario-protected-web-api-app-registration.md)
