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
ms.openlocfilehash: 078ed3e5f3a19bfa4350f9edea858b717c69e3f8
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: ru-RU
ms.lasthandoff: 07/02/2020
ms.locfileid: "81537157"
---
# <a name="a-web-api-that-calls-web-apis-move-to-production"></a>Веб-API, который вызывает веб-API: переместить в рабочую среду

После получения маркера для вызова веб-API можно переместить приложение в рабочую среду.

[!INCLUDE [Move to production common steps](../../../includes/active-directory-develop-scenarios-production.md)]

## <a name="learn-more"></a>Подробнее

Теперь, когда вы знакомы с основами вызова веб-API из собственного веб-API, вы можете заинтересовать следующий учебник, в котором описывается код, используемый для создания защищенного веб-API, который вызывает веб-API.

| Пример | Платформа | Описание |
|--------|----------|-------------|
| [Active-Directory-aspnetcore-webapi-Tutorial-v2](https://github.com/Azure-Samples/active-directory-dotnet-native-aspnetcore-v2/tree/master/2.%20Web%20API%20now%20calls%20Microsoft%20Graph) | Веб-API ASP.NET Core 2,2, Desktop (WPF) | ASP.NET Core 2,2 вызовы веб-API Microsoft Graph, которые вызываются из приложения WPF с помощью платформы Microsoft Identity (v 2.0). |
