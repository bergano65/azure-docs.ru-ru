---
title: Веб-API, что вызовы, веб-API (перемещение в рабочую среду) - платформой Microsoft identity
description: Сведения о создании веб-API, на более низком уровне вызовы веб-API-интерфейсы (перемещение в рабочую среду).
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
ms.openlocfilehash: 7de892a0851e556c5046fe4b3691f39b42a9d237
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/06/2019
ms.locfileid: "65074760"
---
# <a name="web-api-that-calls-web-apis---move-to-production"></a>Веб-API, который вызывает веб-API - переносу в рабочую среду

Получив токен для вызова веб-API, вы можете переместить приложение в рабочей среде.

[!INCLUDE [Move to production common steps](../../../includes/active-directory-develop-scenarios-production.md)]

## <a name="learn-more"></a>Подробнее

Теперь, когда вы знаете основы для вызова веб-API из собственных веб-API, будет интересно узнать это руководство, в котором описывается код, который используется для создания защищенного веб-API вызывает веб-API.

| Образец | платформа | ОПИСАНИЕ |
|--------|----------|-------------|
| [active-directory-aspnetcore-webapi-tutorial-v2](https://github.com/Azure-Samples/active-directory-dotnet-native-aspnetcore-v2/tree/master/2.%20Web%20API%20now%20calls%20Microsoft%20Graph) | ASP.NET Core 2.2 веб-API, рабочий стол (WPF) | Веб-API ASP.NET Core 2.2 вызова Microsoft Graph, сам вызвать из приложения WPF с помощью платформы удостоверений Microsoft (версии 2.0) |
