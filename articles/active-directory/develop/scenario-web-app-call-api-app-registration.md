---
title: Регистрация веб-приложения, вызывающего веб-API — платформа Microsoft Identity | Службы
description: Узнайте, как зарегистрировать веб-приложение, вызывающее веб-API
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
ms.openlocfilehash: 784de823e94aace6f91222c19c1ff8130c3f995f
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/10/2019
ms.locfileid: "74962939"
---
# <a name="web-app-that-calls-web-apis---app-registration"></a>Веб-приложение, вызывающее веб-API — регистрация приложения

Веб-приложения, вызывающие веб-API, имеют ту же регистрацию, что и пользователи для входа в веб-приложение. Поэтому необходимо выполнить инструкции в [веб-приложении, которое входит в систему пользователей — Регистрация приложений](scenario-web-app-sign-user-app-registration.md) .

Однако поскольку веб-приложение теперь вызывает веб-API, оно становится конфиденциальным клиентским приложением. Вот почему требуется немного дополнительной регистрации: необходимо использовать секретные данные клиента с платформой Microsoft Identity.

[!INCLUDE [Pre-requisites](../../../includes/active-directory-develop-scenarios-registration-client-secrets.md)]

## <a name="api-permissions"></a>Разрешения API

Веб-приложения вызывают API от имени пользователя, выполнившего вход. Они должны запрашивать делегированные разрешения. Дополнительные сведения см. [в разделе Добавление разрешений для доступа к веб-API](quickstart-configure-app-access-web-apis.md#add-permissions-to-access-web-apis) .

## <a name="next-steps"></a>Дальнейшие действия

> [!div class="nextstepaction"]
> [Конфигурация кода приложения](scenario-web-app-call-api-app-configuration.md)
