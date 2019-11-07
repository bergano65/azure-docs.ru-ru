---
title: Краткое руководство. Распознавание речи с микрофона в службе "Речь"
titleSuffix: Azure Cognitive Services
description: ПОДЛЕЖИТ УТОЧНЕНИЮ
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 10/28/2019
ms.author: erhopf
ms.openlocfilehash: 64f084f58ae4b12d92d6dee343a59dd56b1c351e
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/04/2019
ms.locfileid: "73505566"
---
В этом кратком руководстве для интерактивного распознавания речи из звуковых данных, полученных с микрофона, используется [пакет SDK службы "Речь"](~/articles/cognitive-services/speech-service/speech-sdk.md). После выполнения нескольких предварительных требований для распознавания речи с микрофона необходимо выполнить всего четыре шага:
> [!div class="checklist"]
> * Создайте объект ````SpeechConfig````, содержащий ключ и регион подписки.
> * Создайте объект ````SpeechRecognizer````, используя приведенный выше объект ````SpeechConfig````.
> * С помощью объекта ````SpeechRecognizer```` запустите процесс распознавания для одного речевого фрагмента.
> * Проверьте возвращенный результат ````SpeechRecognitionResult````.
