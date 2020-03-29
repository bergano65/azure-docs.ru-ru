---
title: Зарегистрируйте веб-приложение, которое вызывает веб-AI - идентификационная платформа Microsoft Azure
description: Узнайте, как зарегистрировать веб-приложение, которое вызывает веб-AIS
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
ms.openlocfilehash: 5a57fcef3569734964bf6e8a41faa49800798f9b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76759063"
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
