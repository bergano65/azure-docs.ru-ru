---
title: Веб-API, вызывающее подчиненными веб-API-интерфейсы (регистрации приложения) — платформе Microsoft identity
description: Сведения о создании веб-API, вызывающее подчиненными веб-API-интерфейсы (регистрации приложения)
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
ms.openlocfilehash: fb03869cdea2150b6e922e2d6d81e577c3be02da
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/06/2019
ms.locfileid: "65075390"
---
# <a name="web-api-that-calls-web-apis---app-registration"></a>Веб-API, что вызовы веб-интерфейсы API — Регистрация приложения

Веб-API, который вызывает веб-интерфейсы API нижнего уровня имеет тот же способ регистрации как защищенный веб-API. Таким образом, вам потребуется выполнить инструкции в [защищенные веб-API — Регистрация приложения](scenario-protected-web-api-app-registration.md).

Тем не менее так как веб-приложение теперь вызовы веб-API, становится конфиденциального клиентского приложения. Вот почему нет дополнительных регистрация сведения, необходимые: приложение необходимо, чтобы общие секретные данные (учетные данные клиента) с платформой Microsoft identity.

[!INCLUDE [Pre-requisites](../../../includes/active-directory-develop-scenarios-registration-client-secrets.md)]

## <a name="api-permissions"></a>Разрешения API

Интерфейсы API вызвать веб-приложений от имени пользователя, для которого был получен токен носителя. Они должны запрашивать делегированные разрешения. Дополнительные сведения см. в разделе [добавить разрешения для доступа к веб-API](quickstart-configure-app-access-web-apis.md#add-permissions-to-access-web-apis).

## <a name="next-steps"></a>Дальнейшие действия

> [!div class="nextstepaction"]
> [Конфигурации кода приложения](scenario-web-api-call-api-app-configuration.md)
