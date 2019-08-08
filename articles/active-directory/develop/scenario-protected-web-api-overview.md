---
title: Защищенный веб-API — обзор | Службы
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
ms.openlocfilehash: 02bd4b84cc7542714f6db45c12c4b5b13a7fb449
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/08/2019
ms.locfileid: "68852581"
---
# <a name="scenario-protected-web-api"></a>Сценарий: Защищенный веб-API

В этом сценарии мы покажем, как можно предоставить веб-API и как защитить его, чтобы только пользователи, прошедшие проверку подлинности, могли получить доступ к API. Чтобы использовать веб-API, необходимо включить пользователей, прошедших проверку подлинности, с помощью рабочих и учебных учетных записей или личных личных учетных записей Майкрософт.

## <a name="prerequisites"></a>предварительные требования

[!INCLUDE [Pre-requisites](../../../includes/active-directory-develop-scenarios-prerequisites.md)]

## <a name="specifics"></a>Особенности

Ниже приведены некоторые особенности, которые необходимо знать для защиты веб-API.

- Регистрация приложения должна предоставлять по крайней мере одну область. Версия токена, принимаемая веб-API, зависит от аудитории для входа.
- Конфигурация кода для веб-API должна проверять токен, используемый при вызове веб-API.

## <a name="next-steps"></a>Следующие шаги

> [!div class="nextstepaction"]
> [Интеграция приложений с Azure Active Directory](scenario-protected-web-api-app-registration.md)
