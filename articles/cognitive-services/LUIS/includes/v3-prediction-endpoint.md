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
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/05/2020
ms.locfileid: "91545234"
---
1. На портале LUIS в разделе **Управление** (в верхнем правом меню) на странице **Ресурсы Azure** (меню слева) на вкладке **Ресурсы прогнозирования** скопируйте **пример запроса** в нижней части страницы.

    Вставьте URL-адрес в новую вкладку браузера.

    URL-адрес содержит идентификатор приложения, ключ и имя слота. URL-адрес конечной точки прогнозирования V3 выглядит следующим образом:

    `https://YOUR-RESOURCE-NAME.api.cognitive.microsoft.com/luis/prediction/v3.0/apps/APP-ID/slots/SLOT-NAME/predict?subscription-key=YOUR-PREDICTION-KEY&<optional-name-value-pairs>&query=YOUR_QUERY_HERE`

