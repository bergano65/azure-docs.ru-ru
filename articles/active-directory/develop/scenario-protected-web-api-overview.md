---
title: Защищенные веб-API — Обзор | Azure
description: Узнайте, как создать защищенный веб-API (Обзор).
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
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: c5bc0075e6604bd1f94531040f2e4a0628e70667
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/13/2019
ms.locfileid: "65074895"
---
# <a name="scenario-protected-web-api"></a>Сценарий. Защищенный веб-API

В этом случае мы покажем вам, как вы можете опубликовать веб-API и как можно защитить его, чтобы только прошедшие проверку пользователи могут получить доступ к API. Необходимо задействовать прошедшего проверку подлинности сотрудников с помощью рабочих и учебных учетных записей или личных личные учетные записи Майкрософт на веб-API.

## <a name="prerequisites"></a>Технические условия

[!INCLUDE [Pre-requisites](../../../includes/active-directory-develop-scenarios-prerequisites.md)]

## <a name="specifics"></a>Особенности

Ниже приведены некоторые особенности, которые необходимо знать для защиты веб-API.

- Регистрации вашего приложения должен предоставлять по крайней мере одну область. Маркер версии, принимаемых веб-API зависит от знака в аудитории.
- Конфигурация кода для веб-API необходимо проверить маркер, который используется при вызове веб-API.

## <a name="next-steps"></a>Дальнейшие действия

> [!div class="nextstepaction"]
> [Интеграция приложений с Azure Active Directory](scenario-protected-web-api-app-registration.md)
