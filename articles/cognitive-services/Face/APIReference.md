---
title: Справочник по API распознавания лиц
titleSuffix: Azure Cognitive Services
description: Справочник по API содержит сведения о Person, LargePersonGroup/каждой группе людей, LargeFaceList/FaceList и API-интерфейсы для распознавания лиц алгоритмов.
services: cognitive-services
author: SteveMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: reference
ms.date: 03/01/2018
ms.author: sbowles
ms.openlocfilehash: f4258f34bb7d353ee4e76f4675f4ef672a4a8c78
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/23/2019
ms.locfileid: "60816394"
---
# <a name="api-reference"></a>Справочник по API

API распознавания лиц Azure — это облачный API, который предоставляет алгоритмы определения и распознавания лиц. API распознавания лиц включают следующее:

- API распознавания лиц алгоритма: Охватывает основные функции, такие как [обнаружения](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236), [поиск похожих](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395237), [проверки](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523a), [идентификации](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239), и [группы](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395238).
- [API-интерфейсы FaceList](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039524b): Используются для управления списком лиц при [поиске похожих лиц](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395237).
- [API-интерфейсы пользователя LargePersonGroup](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599adcba3a7b9412a4d53f40): Используются для управления лицами людей в большой группе для [идентификации](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239).
- [API-интерфейсы LargePersonGroup](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599acdee6ac60f11b48b5a9d): Используются для управления наборами данных большой группы людей для [идентификации](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239).
- [API-интерфейсы LargeFaceList](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a157b68d2de3616c086f2cc): Используются для управления большим списком лиц для [поиска похожих лиц](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395237).
- [API-интерфейсы пользователя в каждой группе людей](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523c): Используются для управления лицами людей в группе для [идентификации](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239).
- [API-интерфейсы каждой группе людей](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395244): Используются для управления наборами данных группы людей для [идентификации](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239).
- [Моментальный снимок API-интерфейсы](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/snapshot-take): Используется для управления моментальными снимками для переноса данных между подписками.
