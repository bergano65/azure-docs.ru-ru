---
title: Получение конечной точки прогнозирования V3
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 10/14/2019
ms.author: diberry
ms.openlocfilehash: 23e7f88b23ac7af7470183877eeb1be89e79779c
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "80287753"
---
1. На портале LUIS в разделе **Управление** (в верхнем правом меню) на странице **Ресурсы Azure** (меню слева) на вкладке **Prediction Resources** (Ресурсы прогнозирования) скопируйте **пример запроса** в нижней части страницы.

    Вставьте URL-адрес в новую вкладку браузера.

    URL-адрес содержит идентификатор приложения, ключ и имя слота. URL-адрес конечной точки прогнозирования V3 выглядит следующим образом:

    `https://REGION.api.cognitive.microsoft.com/luis/prediction/v3.0/apps/APP-ID/slots/SLOT-NAME/predict?subscription-key=YOUR-KEY&<optional-name-value-pairs>&query=YOUR_QUERY_HERE`

