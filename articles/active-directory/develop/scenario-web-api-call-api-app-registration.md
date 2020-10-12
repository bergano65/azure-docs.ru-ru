---
title: Регистрация веб-API, который вызывает веб-API — платформа Microsoft Identity | Службы
description: Узнайте, как создать веб-API, который вызывает нисходящие веб-API (регистрация приложений).
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/07/2019
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: c0b009d6d53a117aa11d0629fb649b2dd55559af
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/09/2020
ms.locfileid: "89438200"
---
# <a name="a-web-api-that-calls-web-apis-app-registration"></a>Веб-API, который вызывает веб-API: регистрация приложения

Веб-API, который вызывает нисходящие веб-API, имеет ту же регистрацию, что и защищенный веб-API. Поэтому необходимо выполнить инструкции в разделе [защищенный веб-API: регистрация приложения](scenario-protected-web-api-app-registration.md).

Так как веб-приложение теперь вызывает веб-API, оно становится конфиденциальным клиентским приложением. Вот почему требуются дополнительные сведения о регистрации: приложение должно обмениваться секретами (учетные данные клиента) с платформой Microsoft Identity.

[!INCLUDE [Pre-requisites](../../../includes/active-directory-develop-scenarios-registration-client-secrets.md)]

## <a name="api-permissions"></a>Разрешения API

Веб-приложения вызывают интерфейсы API от имени пользователей, которым был получен маркер носителя. Веб-приложениям необходимо запросить делегированные разрешения. Дополнительные сведения см. [в статье Добавление разрешений для доступа к веб-API](quickstart-configure-app-access-web-apis.md#add-permissions-to-access-your-web-api).

## <a name="next-steps"></a>Дальнейшие шаги

> [!div class="nextstepaction"]
> [Веб-API, вызывающий веб-API: конфигурация кода](scenario-web-api-call-api-app-configuration.md)
