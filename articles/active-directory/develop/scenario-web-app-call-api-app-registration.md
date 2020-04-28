---
title: Регистрация веб-приложения, вызывающего веб-API — платформа Microsoft Identity | Службы
description: Узнайте, как зарегистрировать веб-приложение, вызывающее веб-API
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
ms.openlocfilehash: 8cb7d86bd419563363779c499962c81f0c59e3b6
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2020
ms.locfileid: "80881882"
---
# <a name="a-web-app-that-calls-web-apis-app-registration"></a>Веб-приложение, вызывающее веб-API: регистрация приложения

Веб-приложение, вызывающее веб-API, имеет ту же регистрацию, что и веб-приложение, которое подписывает пользователей. Поэтому следуйте инструкциям в [веб-приложении, которое входит в систему пользователей: регистрация приложения](scenario-web-app-sign-user-app-registration.md).

Однако поскольку веб-приложение теперь также вызывает веб-API, оно становится конфиденциальным клиентским приложением. Вот почему требуется еще одна регистрация. Приложение должно совместно использовать учетные данные клиента или *секреты*с платформой Microsoft Identity.

[!INCLUDE [Registration of client secrets](../../../includes/active-directory-develop-scenarios-registration-client-secrets.md)]

## <a name="api-permissions"></a>Разрешения API

Веб-приложения вызывают API от имени пользователя, выполнившего вход. Для этого они должны запросить *делегированные разрешения*. Дополнительные сведения см. [в разделе Добавление разрешений для доступа к веб-API](quickstart-configure-app-access-web-apis.md#add-permissions-to-access-web-apis).

## <a name="next-steps"></a>Дальнейшие действия

> [!div class="nextstepaction"]
> [Веб-приложение, вызывающее веб-API: конфигурация кода](scenario-web-app-call-api-app-configuration.md)
