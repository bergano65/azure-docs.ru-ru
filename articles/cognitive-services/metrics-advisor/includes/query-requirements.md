---
title: Требования к запросам
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: metrics-advisor
ms.topic: include
ms.date: 09/10/2020
ms.author: aahi
ms.openlocfilehash: 68b5946cb21bef1c8979a94c8780682cfe9defa6
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/25/2020
ms.locfileid: "91377516"
---
В запросе используйте параметр `@StartTime`, чтобы получить данные метрик для определенной метки времени. Вместо этого будет использоваться строка в формате `yyyy-MM-ddTHH:mm:ss`. 

> [!IMPORTANT]
> Убедитесь, что запрос возвращает данные метрик только из **одной метки времени**. Помощник по метрикам выполнит запрос к каждой метке времени, чтобы получить соответствующие данные метрики. Например, запрос метрики со степенью детализации *ежедневно* должен содержать только одну метку времени (`2020-06-21T00:00:00Z`) при однократном выполнении запроса. 
