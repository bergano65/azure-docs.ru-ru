---
title: Краткое руководство. Распознавание речи из звукового файла в службе "Речь"
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
ms.openlocfilehash: bbd3880ea679dc5fc86c0fc1ece101ca3fca74d9
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/04/2019
ms.locfileid: "73506286"
---
В этом кратком руководстве для распознавания речи из звукового файла используется [пакет SDK для службы "Речь"](~/articles/cognitive-services/speech-service/speech-sdk.md). После выполнения нескольких предварительных требований для распознавания речи из файла необходимо выполнить всего пять шагов:
> [!div class="checklist"]
> * Создайте объект ````SpeechConfig````, содержащий ключ и регион подписки.
> * Создайте объект ````AudioConfig````, указывающий имя WAV-файла.
> * Создайте объект ````SpeechRecognizer````, используя приведенные выше объекты ````SpeechConfig```` и ````AudioConfig````.
> * С помощью объекта ````SpeechRecognizer```` запустите процесс распознавания для одного речевого фрагмента.
> * Проверьте возвращенный результат ````SpeechRecognitionResult````.
