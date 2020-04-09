---
title: Зарегистрируйте веб-приложение, которое вызывает веб-AI - идентификационная платформа Microsoft Azure
description: Узнайте, как зарегистрировать веб-приложение, которое вызывает веб-AIS
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
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/08/2020
ms.locfileid: "80881882"
---
# <a name="a-web-app-that-calls-web-apis-app-registration"></a>Веб-приложение, которое вызывает веб-AIS: Регистрация приложений

Веб-приложение, которое вызывает веб-AIS, имеет ту же регистрацию, что и веб-приложение, которое подписывает пользователей. Таким образом, следуйте инструкциям в [веб-приложение, которое подписывает в пользователей: Регистрация приложений](scenario-web-app-sign-user-app-registration.md).

Однако, поскольку веб-приложение теперь также вызывает веб-AI, оно становится конфиденциальным клиентским приложением. Поэтому требуется дополнительная регистрация. Приложение должно обмениваться учетными данными клиентов или *секретами*с платформой идентификации Майкрософт.

[!INCLUDE [Registration of client secrets](../../../includes/active-directory-develop-scenarios-registration-client-secrets.md)]

## <a name="api-permissions"></a>Разрешения API

Веб-приложения называют AI от имени пользователя, вписаваемого в нее. Для этого они должны запросить *делегированные разрешения.* Для получения подробной информации [см.](quickstart-configure-app-access-web-apis.md#add-permissions-to-access-web-apis)

## <a name="next-steps"></a>Дальнейшие действия

> [!div class="nextstepaction"]
> [Веб-приложение, которое вызывает web-aIS: конфигурация кода](scenario-web-app-call-api-app-configuration.md)
