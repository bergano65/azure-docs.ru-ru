---
title: Краткое руководство. Распознавание речи из звукового файла в службе "Речь"
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 10/28/2019
ms.author: erhopf
ms.openlocfilehash: e333b156eaf9c4b6e09e631513ea099018f0691b
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/25/2019
ms.locfileid: "75468234"
---
В этом кратком руководстве для распознавания речи из звукового файла используется [пакет SDK для службы "Речь"](~/articles/cognitive-services/speech-service/speech-sdk.md). После выполнения нескольких предварительных требований для распознавания речи из файла необходимо выполнить всего пять шагов:
> [!div class="checklist"]
> * Создайте объект ````SpeechConfig````, содержащий ключ и регион подписки.
> * Создайте объект ````AudioConfig````, указывающий имя WAV-файла.
> * Создайте объект ````SpeechRecognizer````, используя приведенные выше объекты ````SpeechConfig```` и ````AudioConfig````.
> * С помощью объекта ````SpeechRecognizer```` запустите процесс распознавания для одного речевого фрагмента.
> * Проверьте возвращенный результат ````SpeechRecognitionResult````.
