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
ms.openlocfilehash: df4604560e05899461b11ec0788f72b27241f1b9
ms.sourcegitcommit: 5a8c65d7420daee9667660d560be9d77fa93e9c9
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/15/2019
ms.locfileid: "74125590"
---
В этом кратком руководстве для интерактивного распознавания речи из звуковых данных, полученных с микрофона, используется [пакет SDK службы "Речь"](~/articles/cognitive-services/speech-service/speech-sdk.md). После выполнения нескольких предварительных требований для распознавания речи с микрофона необходимо выполнить всего четыре шага:
> [!div class="checklist"]
>
> * Создайте объект ````SpeechConfig````, содержащий ключ и регион подписки.
> * Создайте объект ````IntentRecognizer````, используя приведенный выше объект ````SpeechConfig````.
> * С помощью объекта ````IntentRecognizer```` запустите процесс распознавания для одного речевого фрагмента.
> * Проверьте возвращенный результат ````IntentRecognitionResult````.
