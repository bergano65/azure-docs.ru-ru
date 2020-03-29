---
title: Защищенный Web API - обзор
titleSuffix: Microsoft identity platform
description: Узнайте, как создать защищенный веб-aPI (обзор).
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76773332"
---
# <a name="scenario-protected-web-api"></a>Сценарий: Защищенный веб-aPI

В этом сценарии вы узнаете, как разоблачить веб-API. Вы также узнаете, как защитить веб-API, чтобы получить к нему доступ только для аутентифицированных пользователей.

Чтобы использовать web-aPI, необходимо либо включить аутентифицированных пользователей с рабочими и школьными учетными записями, либо включить личные учетные записи Майкрософт.

## <a name="prerequisites"></a>Предварительные требования

[!INCLUDE [Pre-requisites](../../../includes/active-directory-develop-scenarios-prerequisites.md)]

## <a name="specifics"></a>Специфики

Вот конкретная информация, которую вы должны знать для защиты веб-AIS:

- Регистрация приложения должна предоставить по крайней мере одну область. Версия токенов, принятая вашим веб-API, зависит от аудитории, вписавшей.
- Конфигурация кода для веб-API должна проверить маркер, используемый при вызове веб-API.

## <a name="next-steps"></a>Дальнейшие действия

> [!div class="nextstepaction"]
> [Регистрация приложения](scenario-protected-web-api-app-registration.md)
