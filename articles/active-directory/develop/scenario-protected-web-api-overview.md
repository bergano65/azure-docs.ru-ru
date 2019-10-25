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
ms.collection: M365-identity-device-management
ms.openlocfilehash: 64418138f9eff61cc94a57be4dc5e2625e5fdebb
ms.sourcegitcommit: be8e2e0a3eb2ad49ed5b996461d4bff7cba8a837
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/23/2019
ms.locfileid: "72803686"
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
