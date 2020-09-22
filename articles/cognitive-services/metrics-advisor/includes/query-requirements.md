---
title: Требования к запросам
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 09/10/2020
ms.author: aahi
ms.openlocfilehash: 23da214f4ffc9e589523f7ada00a77494183eb15
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/22/2020
ms.locfileid: "90940139"
---
В запросе используйте параметр `@StartTime`, чтобы получить данные метрик для определенной метки времени. Вместо этого будет использоваться строка в формате `yyyy-MM-ddTHH:mm:ss`. 

> [!IMPORTANT]
> Убедитесь, что запрос возвращает данные метрик только из **одной метки времени**. Помощник по метрикам выполнит запрос к каждой метке времени, чтобы получить соответствующие данные метрики. Например, запрос метрики со степенью детализации *ежедневно* должен содержать только одну метку времени (`2020-06-21T00:00:00Z`) при однократном выполнении запроса. 
