---
title: Управляющая программа приложение вызова веб-API (вызова веб-API) — платформе Microsoft identity
description: Узнайте, как создать управляющую программу, вызовы веб-API-интерфейсы (вызова веб-API)
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
editor: ''
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
ms.openlocfilehash: aff375f996126d9e8b64361fc0e5673c25d30c19
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/13/2019
ms.locfileid: "65076275"
---
# <a name="daemon-app-that-calls-web-apis---call-a-web-api-from-the-app"></a>Приложение управляющей программы, что вызовы веб-API - вызов веб-API из приложения

Управляющую программу можно вызвать веб-API из приложения .NET управляющей программы или вызывать несколько предварительно утвержденные веб-API.

## <a name="calling-a-web-api-from-a-net-daemon-application"></a>Вызов веб-API из приложения .NET управляющей программы

[!INCLUDE [Call web API in .NET](../../../includes/active-directory-develop-scenarios-call-apis-dotnet.md)]

<!--
More includes will come later for Python and Java
-->

## <a name="calling-several-apis"></a>Вызов несколько интерфейсов API

Для приложений управляющей программы веб-API, вы вызываете должны быть предварительно утверждены. Не будет любой добавочное согласие с помощью приложений управляющей программы (нет никакого взаимодействия с пользователем). Администратор клиента должен установить предварительно согласие, приложение и все разрешения API. Если вы хотите вызвать несколько интерфейсов API, необходимо получить маркер для каждого ресурса, каждый вызов время `AcquireTokenForClient`. MSAL будет использовать кэш маркеров приложения, чтобы избежать излишних вызовов служб.

## <a name="next-steps"></a>Дальнейшие действия

> [!div class="nextstepaction"]
> [Управляющая программа приложение — перемещения в рабочую среду](./scenario-daemon-production.md)