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
ms.openlocfilehash: bc6e43faca47e360daa8214e6b9f6e9df4a2f130
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/04/2019
ms.locfileid: "73495269"
---
1. На портале LUIS в разделе "Управление" (меню справа вверху) на странице Keys and endpoint (Ключи и конечные точки) (меню слева) выберите URL-адрес конечной точки в нижней части страницы.

    В результате откроется вкладка браузера с URL-адресом конечной точки в адресной строке.

    URL-адрес содержит идентификатор приложения, ключ и имя слота. URL-адрес конечной точки прогнозирования V3 выглядит следующим образом:

    `https://REGION.api.cognitive.microsoft.com/luis/prediction/v3.0/apps/APP-ID/slots/SLOT-NAME/predict?subscription-key=YOUR-KEY&<optional-name-value-pairs>&query=UTTERANCE-TEXT.`

