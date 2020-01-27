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
ms.openlocfilehash: f96393adf0eaed8a28bc1a2ec0def6b0386bd7ac
ms.sourcegitcommit: af6847f555841e838f245ff92c38ae512261426a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/23/2020
ms.locfileid: "76701983"
---
# <a name="scenario-protected-web-api"></a>Сценарий: защищенный веб-API

В этом сценарии мы покажем, как можно предоставить веб-API и как защитить его, чтобы только пользователи, прошедшие проверку подлинности, могли получить доступ к API. Чтобы использовать веб-API, необходимо включить пользователей, прошедших проверку подлинности, с помощью рабочих и учебных учетных записей или личных личных учетных записей Майкрософт.

## <a name="prerequisites"></a>Технические условия

[!INCLUDE [Pre-requisites](../../../includes/active-directory-develop-scenarios-prerequisites.md)]

## <a name="specifics"></a>Особенности

Ниже приведены некоторые особенности, которые необходимо знать для защиты веб-API.

- Регистрация приложения должна предоставлять по крайней мере одну область. Версия токена, принимаемая веб-API, зависит от аудитории для входа.
- Конфигурация кода для веб-API должна проверять токен, используемый при вызове веб-API.

## <a name="next-steps"></a>Дальнейшие действия

> [!div class="nextstepaction"]
> [Интеграция приложений с Azure Active Directory](scenario-protected-web-api-app-registration.md)
