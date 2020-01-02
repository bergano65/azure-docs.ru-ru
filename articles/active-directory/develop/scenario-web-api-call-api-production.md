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
ms.openlocfilehash: 0d59a5b2a74c10e36103713725113cbe8c9cc412
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/10/2019
ms.locfileid: "74965175"
---
# <a name="web-api-that-calls-web-apis---move-to-production"></a>Веб-API, вызывающий веб-API — перемещение в рабочую среду

После получения маркера для вызова веб-API можно переместить приложение в рабочую среду.

[!INCLUDE [Move to production common steps](../../../includes/active-directory-develop-scenarios-production.md)]

## <a name="learn-more"></a>Дополнительные сведения

Теперь, когда вы знакомы с основами вызова веб-API из собственного веб-API, вы, возможно, заинтересованы в этом руководстве, описывающем код, используемый для создания защищенного веб-API, вызывающего веб-API.

| Пример | платформа | Описание |
|--------|----------|-------------|
| [active-directory-aspnetcore-webapi-tutorial-v2](https://github.com/Azure-Samples/active-directory-dotnet-native-aspnetcore-v2/tree/master/2.%20Web%20API%20now%20calls%20Microsoft%20Graph) | Веб-API ASP.NET Core 2,2, Desktop (WPF) | ASP.NET Core 2,2 вызов веб-API Microsoft Graph, который вызывается из приложения WPF с помощью платформы Microsoft Identity (v 2.0) |
