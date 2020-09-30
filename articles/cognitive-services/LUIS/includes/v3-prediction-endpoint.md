---
title: Получение конечной точки прогнозирования V3
titleSuffix: Azure Cognitive Services
services: cognitive-services
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: include
ms.date: 05/05/2020
ms.openlocfilehash: a607ad71915606d6046b4c71291a49b8641bdcc9
ms.sourcegitcommit: f5580dd1d1799de15646e195f0120b9f9255617b
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/29/2020
ms.locfileid: "91545234"
---
1. На портале LUIS в разделе **Управление** (в верхнем правом меню) на странице **Ресурсы Azure** (меню слева) на вкладке **Ресурсы прогнозирования** скопируйте **пример запроса** в нижней части страницы.

    Вставьте URL-адрес в новую вкладку браузера.

    URL-адрес содержит идентификатор приложения, ключ и имя слота. URL-адрес конечной точки прогнозирования V3 выглядит следующим образом:

    `https://YOUR-RESOURCE-NAME.api.cognitive.microsoft.com/luis/prediction/v3.0/apps/APP-ID/slots/SLOT-NAME/predict?subscription-key=YOUR-PREDICTION-KEY&<optional-name-value-pairs>&query=YOUR_QUERY_HERE`

