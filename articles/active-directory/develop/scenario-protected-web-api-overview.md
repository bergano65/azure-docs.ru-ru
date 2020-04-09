---
title: Защищенный Web API - обзор
titleSuffix: Microsoft identity platform
description: Узнайте, как создать защищенный веб-aPI (обзор).
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/07/2019
ms.author: jmprieur
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: 9017fbde1a44bcdf39fa8730bed25141da19fd56
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/08/2020
ms.locfileid: "80882409"
---
# <a name="scenario-protected-web-api"></a>Сценарий: Защищенный веб-aPI

В этом сценарии вы узнаете, как разоблачить веб-API. Вы также узнаете, как защитить веб-API, чтобы получить к нему доступ только для аутентифицированных пользователей.

Чтобы использовать web-aPI, необходимо либо включить аутентифицированных пользователей с рабочими и школьными учетными записями, либо включить личные учетные записи Майкрософт.

## <a name="prerequisites"></a>Предварительные требования

[!INCLUDE [Pre-requisites](../../../includes/active-directory-develop-scenarios-prerequisites.md)]

## <a name="specifics"></a>Специфики

Вот конкретная информация, которую вы должны знать для защиты веб-AIS:

- Регистрация приложения должна предоставить по крайней мере одну область. Версия токенов, принятая вашим веб-API, зависит от аудитории, вписавшей.
- Конфигурация кода для веб-API должна проверить маркер, используемый при вызове веб-API.

## <a name="next-steps"></a>Дальнейшие действия

> [!div class="nextstepaction"]
> [Регистрация приложения](scenario-protected-web-api-app-registration.md)
