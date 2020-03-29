---
title: Зарегистрируйте веб-aPI, который вызывает веб-API - идентификационная платформа Microsoft Azure
description: Узнайте, как создать веб-aPI, который вызывает ниже по течению веб-API (регистрация приложений).
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
ms.openlocfilehash: bafd71f34602535bb6193a8d8114a1182e4e8f40
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76701796"
---
# <a name="a-web-api-that-calls-web-apis-app-registration"></a>Веб-aPI, который вызывает веб-API: Регистрация приложений

Веб-API, который вызывает ниже по течению веб-API, имеет ту же регистрацию, что и защищенный веб-API. Таким образом, вы должны следовать инструкциям в [Защищенном веб-API: Регистрация приложений](scenario-protected-web-api-app-registration.md).

Поскольку веб-приложение теперь вызывает веб-aI, оно становится конфиденциальным клиентским приложением. Вот почему требуется дополнительная регистрационная информация: приложение должно делиться секретами (учетными данными клиентов) с платформой идентификации Майкрософт.

[!INCLUDE [Pre-requisites](../../../includes/active-directory-develop-scenarios-registration-client-secrets.md)]

## <a name="api-permissions"></a>Разрешения API

Веб-приложения называют AI от имени пользователей, для которых был получен токен носителя. Веб-приложениям необходимо запрашивать делегированные разрешения. Для получения дополнительной [информации см.](quickstart-configure-app-access-web-apis.md#add-permissions-to-access-web-apis)

## <a name="next-steps"></a>Дальнейшие действия

> [!div class="nextstepaction"]
> [Веб-aPI, который вызывает веб-API: конфигурация кода](scenario-web-api-call-api-app-configuration.md)
