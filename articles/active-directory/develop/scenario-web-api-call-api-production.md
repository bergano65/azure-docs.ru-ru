---
title: Перемещение веб-API вызов веб-API в рабочую среду — платформа Microsoft Identity | Службы
description: Узнайте, как переместить веб-API, который вызывает веб-API, в рабочую среду.
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
ms.openlocfilehash: 1426ad250e18d0132e116162a374120dda2e1200
ms.sourcegitcommit: 05cdbb71b621c4dcc2ae2d92ca8c20f216ec9bc4
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/16/2020
ms.locfileid: "76044136"
---
# <a name="a-web-api-that-calls-web-apis-move-to-production"></a>Веб-API, который вызывает веб-API: переместить в рабочую среду

После получения маркера для вызова веб-API можно переместить приложение в рабочую среду.

[!INCLUDE [Move to production common steps](../../../includes/active-directory-develop-scenarios-production.md)]

## <a name="learn-more"></a>Подробнее…

Теперь, когда вы знакомы с основами вызова веб-API из собственного веб-API, вы можете заинтересовать следующий учебник, в котором описывается код, используемый для создания защищенного веб-API, который вызывает веб-API.

| Пример | Платформа | Description |
|--------|----------|-------------|
| [active-directory-aspnetcore-webapi-tutorial-v2](https://github.com/Azure-Samples/active-directory-dotnet-native-aspnetcore-v2/tree/master/2.%20Web%20API%20now%20calls%20Microsoft%20Graph) | Веб-API ASP.NET Core 2,2, Desktop (WPF) | ASP.NET Core 2,2 вызовы веб-API Microsoft Graph, которые вызываются из приложения WPF с помощью платформы Microsoft Identity (v 2.0). |
