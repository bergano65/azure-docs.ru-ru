---
title: Перемещение веб-API вызов веб-API в рабочую среду — платформа Microsoft Identity | Службы
description: Узнайте, как переместить веб-API, который вызывает веб-API, в рабочую среду.
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
ms.openlocfilehash: aea3a173fd07b66021d35142a84499ae9c66c014
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/20/2020
ms.locfileid: "86518204"
---
# <a name="a-web-api-that-calls-web-apis-move-to-production"></a>Веб-API, который вызывает веб-API: переместить в рабочую среду

После получения маркера для вызова веб-API можно переместить приложение в рабочую среду.

[!INCLUDE [Move to production common steps](../../../includes/active-directory-develop-scenarios-production.md)]

## <a name="learn-more"></a>Дополнительные сведения

Теперь, когда вы знакомы с основами вызова веб-API из собственного веб-API, вы можете заинтересовать следующий учебник, в котором описывается код, используемый для создания защищенного веб-API, который вызывает веб-API.

| Пример | Платформа | Описание |
|--------|----------|-------------|
| [Active-Directory-aspnetcore-webapi-Tutorial-v2](https://github.com/Azure-Samples/active-directory-dotnet-native-aspnetcore-v2/tree/master/2.%20Web%20API%20now%20calls%20Microsoft%20Graph) глава 1 | ASP.NET Core веб-API, Desktop (WPF) | ASP.NET Core вызовы веб-API Microsoft Graph, которые вызываются из приложения WPF с помощью платформы Microsoft Identity (v 2.0). |
