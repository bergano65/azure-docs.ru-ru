---
title: Краткое руководство. Распознавание речи, намерений и сущностей в службе "Речь"
titleSuffix: Azure Cognitive Services
description: ПОДЛЕЖИТ УТОЧНЕНИЮ
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 10/28/2019
ms.author: erhopf
zone_pivot_groups: programming-languages-set-two
ms.openlocfilehash: a87ed9355a5939393fd5e20f395cc96f35e7f150
ms.sourcegitcommit: 5aefc96fd34c141275af31874700edbb829436bb
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/04/2019
ms.locfileid: "74816077"
---
В этом кратком руководстве для интерактивного распознавания речи из звуковых данных, полученных с микрофона, используется [пакет SDK службы "Речь"](~/articles/cognitive-services/speech-service/speech-sdk.md). После выполнения нескольких предварительных требований для распознавания речи с микрофона необходимо выполнить всего четыре шага:
> [!div class="checklist"]
>
> * Создайте объект ````SpeechConfig````, содержащий ключ и регион подписки.
> * Создайте объект ````IntentRecognizer````, используя приведенный выше объект ````SpeechConfig````.
> * С помощью объекта ````IntentRecognizer```` запустите процесс распознавания для одного речевого фрагмента.
> * Проверьте возвращенный результат ````IntentRecognitionResult````.
