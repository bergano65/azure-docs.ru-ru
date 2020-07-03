---
title: Получение конечной точки прогнозирования V3
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 05/05/2020
ms.author: diberry
ms.openlocfilehash: 18f5422202cf972d49349cc04b845c623cabffa3
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/19/2020
ms.locfileid: "83589166"
---
1. На портале LUIS в разделе **Управление** (в верхнем правом меню) на странице **Ресурсы Azure** (меню слева) на вкладке **Ресурсы прогнозирования** скопируйте **пример запроса** в нижней части страницы.

    Вставьте URL-адрес в новую вкладку браузера.

    URL-адрес содержит идентификатор приложения, ключ и имя слота. URL-адрес конечной точки прогнозирования V3 выглядит следующим образом:

    `https://YOUR-RESOURCE-NAME.api.cognitive.microsoft.com/luis/prediction/v3.0/apps/APP-ID/slots/SLOT-NAME/predict?subscription-key=YOUR-PREDICTION-KEY&<optional-name-value-pairs>&query=YOUR_QUERY_HERE`

