---
title: Получение конечной точки прогнозирования V3
titleSuffix: Azure Cognitive Services
services: cognitive-services
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: include
ms.date: 05/05/2020
ms.openlocfilehash: 85fcd0308083350a02afd4eadac2b361337f3b33
ms.sourcegitcommit: 857859267e0820d0c555f5438dc415fc861d9a6b
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/30/2020
ms.locfileid: "93128224"
---
1. На портале LUIS в разделе **Управление** (в верхнем правом меню) на странице **Ресурсы Azure** (меню слева) на вкладке **Ресурсы прогнозирования** скопируйте **пример запроса** в нижней части страницы. URL-адрес содержит идентификатор приложения, ключ и имя слота. URL-адрес конечной точки прогнозирования V3 выглядит следующим образом: `https://YOUR-RESOURCE-NAME.api.cognitive.microsoft.com/luis/prediction/v3.0/apps/APP-ID/slots/SLOT-NAME/predict?subscription-key=YOUR-PREDICTION-KEY&<optional-name-value-pairs>&query=YOUR_QUERY_HERE`

    :::image type="content" source="../media/prediction-resources-example-query.png" alt-text="Пример запроса в разделе ресурсов для прогнозирования" lightbox="../media/prediction-resources-example-query.png":::
    
    Вставьте URL-адрес в новую вкладку браузера. Если вы не видите URL-адрес, у вас нет ресурса для прогнозирования, и его нужно будет создать. 

    

    

