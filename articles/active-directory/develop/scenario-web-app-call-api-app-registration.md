---
title: Веб-приложения, что вызовы веб-API (регистрации приложения) — платформе Microsoft identity
description: Узнайте, как создать веб-приложение, вызовы веб-API-интерфейсы (регистрации приложения)
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
ms.openlocfilehash: 5becdc287f7cad28aa6c7c07ebc107586e9a2b2a
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/13/2019
ms.locfileid: "65075195"
---
# <a name="web-app-that-calls-web-apis---app-registration"></a>Веб-приложения, что вызовы веб-интерфейсы API — Регистрация приложения

Web app вызова веб-API имеет тот же способ регистрации как пользователям вход в веб-приложения. Таким образом, потребуется следуйте инструкциям в [веб-приложение, входа в систему пользователи — Регистрация приложения](scenario-web-app-sign-user-app-registration.md)

Тем не менее с момента веб-приложение теперь вызовы веб-API, он становится конфиденциального клиентского приложения. Вот почему имеется много дополнительных необходима регистрация: требуется общие секретные данные (учетные данные клиента) с платформой Microsoft identity.

[!INCLUDE [Pre-requisites](../../../includes/active-directory-develop-scenarios-registration-client-secrets.md)]

## <a name="api-permissions"></a>Разрешения API

Интерфейсы API вызвать веб-приложений от имени выполнившего вход пользователя. Они должны запрашивать делегированные разрешения. Дополнительные сведения см. [добавить разрешения для доступа к веб-API](quickstart-configure-app-access-web-apis.md#add-permissions-to-access-web-apis)

## <a name="next-steps"></a>Дальнейшие действия

> [!div class="nextstepaction"]
> [Конфигурации кода приложения](scenario-web-app-call-api-app-configuration.md)
