---
title: Регистрация веб-API, который вызывает веб-API — платформа Microsoft Identity | Службы
description: Узнайте, как создать веб-API, который вызывает нисходящие веб-API (регистрация приложений).
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
ms.openlocfilehash: cb5f4763e13935b99564bfcb6d8b6e7f463ed59e
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/08/2019
ms.locfileid: "74919806"
---
# <a name="web-api-that-calls-web-apis---app-registration"></a>Веб-API, вызывающий веб-API — регистрация приложения

Веб-API, который вызывает нисходящие веб-API, имеет ту же регистрацию, что и защищенный веб-API. Поэтому необходимо выполнить инструкции в разделе [защищенный веб-API — регистрация приложений](scenario-protected-web-api-app-registration.md).

Однако поскольку веб-приложение теперь вызывает веб-API, оно становится конфиденциальным клиентским приложением. Вот почему требуются дополнительные сведения о регистрации: приложение должно обмениваться секретами (учетные данные клиента) с платформой Microsoft Identity.

[!INCLUDE [Pre-requisites](../../../includes/active-directory-develop-scenarios-registration-client-secrets.md)]

## <a name="api-permissions"></a>Разрешения API

Веб-приложения вызывают API от имени пользователя, для которого был получен маркер носителя. Они должны запрашивать делегированные разрешения. Дополнительные сведения см. [в разделе Добавление разрешений для доступа к веб-API](quickstart-configure-app-access-web-apis.md#add-permissions-to-access-web-apis).

## <a name="next-steps"></a>Дальнейшие действия

> [!div class="nextstepaction"]
> [Конфигурация кода приложения](scenario-web-api-call-api-app-configuration.md)
