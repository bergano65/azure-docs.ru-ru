---
title: Зарегистрируйте веб-aPI, который вызывает веб-API - идентификационная платформа Microsoft Azure
description: Узнайте, как создать веб-aPI, который вызывает ниже по течению веб-API (регистрация приложений).
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
ms.openlocfilehash: 048f7d41bd9d106121859e6b1fc013258067af9c
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/08/2020
ms.locfileid: "80885129"
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
