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
ms.openlocfilehash: 4e530f76c8301dc74f73b675befa6f0710aedab7
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/23/2020
ms.locfileid: "87026634"
---
# <a name="scenario-protected-web-api"></a>Сценарий: защищенный веб-API

В этом сценарии вы узнаете, как предоставить веб-API. Вы также узнаете, как защитить веб-API таким образом, чтобы доступ к нему могли получить только пользователи, прошедшие проверку подлинности.

Чтобы использовать веб-API, необходимо либо включить прошедшие проверку подлинности пользователей с помощью рабочих и учебных учетных записей, либо включить личные учетные записи Майкрософт.

## <a name="prerequisites"></a>Обязательные условия

[!INCLUDE [Pre-requisites](../../../includes/active-directory-develop-scenarios-prerequisites.md)]

## <a name="specifics"></a>Особенности

Ниже приведены конкретные сведения, которые необходимо знать для защиты веб-API.

- Регистрация приложения должна предоставлять по крайней мере одну *область* или одну *роль приложения*.
  - Области предоставляются веб-API, которые вызываются от имени пользователя.
  - Роли приложений предоставляются веб-API, которые вызываются приложениями управляющей программы (которые вызывают ваш веб-API от своего имени).
- Если вы создаете новую регистрацию приложения веб-API, выберите [версию маркера доступа](reference-app-manifest.md#accesstokenacceptedversion-attribute) , которая принимает ваш веб-API `2` . Для устаревших веб-API допустимой версией токена может быть `null` , но это значение запрещает доступ только для организаций, а личные учетные записи Майкрософт (MSA) не поддерживаются.
- Конфигурация кода для веб-API должна проверять токен, используемый при вызове веб-API.
- Код в действиях контроллера должен проверять роли или области в маркере.

## <a name="next-steps"></a>Дальнейшие действия

> [!div class="nextstepaction"]
> [Регистрация приложения](scenario-protected-web-api-app-registration.md)
