---
title: Защищенный веб-API — обзор
titleSuffix: Microsoft identity platform
description: Узнайте, как создать защищенный веб-API (обзор).
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
ms.openlocfilehash: abe4ecf77e7a65251830ff822b15f79291471202
ms.sourcegitcommit: 984c5b53851be35c7c3148dcd4dfd2a93cebe49f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/28/2020
ms.locfileid: "76773332"
---
# <a name="scenario-protected-web-api"></a>Сценарий: защищенный веб-API

В этом сценарии вы узнаете, как предоставить веб-API. Вы также узнаете, как защитить веб-API таким образом, чтобы доступ к нему могли получить только пользователи, прошедшие проверку подлинности.

Чтобы использовать веб-API, необходимо либо включить прошедшие проверку подлинности пользователей с помощью рабочих и учебных учетных записей, либо включить личные учетные записи Майкрософт.

## <a name="prerequisites"></a>Технические условия

[!INCLUDE [Pre-requisites](../../../includes/active-directory-develop-scenarios-prerequisites.md)]

## <a name="specifics"></a>Особенности

Ниже приведены конкретные сведения, которые необходимо знать для защиты веб-API.

- Регистрация приложения должна предоставлять по крайней мере одну область. Версия токена, принимаемая веб-API, зависит от аудитории для входа.
- Конфигурация кода для веб-API должна проверять токен, используемый при вызове веб-API.

## <a name="next-steps"></a>Дальнейшие действия

> [!div class="nextstepaction"]
> [Интеграция приложений с Azure Active Directory](scenario-protected-web-api-app-registration.md)
