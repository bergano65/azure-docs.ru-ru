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
ms.openlocfilehash: 2f7ec24667514fb131af29321a53f97210e58fc9
ms.sourcegitcommit: 5aefc96fd34c141275af31874700edbb829436bb
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/04/2019
ms.locfileid: "74819304"
---
В этом кратком руководстве для распознавания речи из звукового файла используется [пакет SDK для службы "Речь"](~/articles/cognitive-services/speech-service/speech-sdk.md). После выполнения нескольких предварительных требований для распознавания речи из файла необходимо выполнить всего пять шагов:
> [!div class="checklist"]
> * Создайте объект ````SpeechConfig````, содержащий ключ и регион подписки.
> * Создайте объект ````AudioConfig````, указывающий имя WAV-файла.
> * Создайте объект ````SpeechRecognizer````, используя приведенные выше объекты ````SpeechConfig```` и ````AudioConfig````.
> * С помощью объекта ````SpeechRecognizer```` запустите процесс распознавания для одного речевого фрагмента.
> * Проверьте возвращенный результат ````SpeechRecognitionResult````.
