---
title: Краткое руководство. Распознавание речи из звукового файла в службе "Речь"
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 01/14/2020
ms.author: dapine
ms.openlocfilehash: 2b6270535c0cf69549a7412bd38d9207454e5500
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76037809"
---
В этом кратком руководстве для распознавания речи из звукового файла используется [пакет SDK для службы "Речь"](~/articles/cognitive-services/speech-service/speech-sdk.md). После выполнения нескольких предварительных требований для распознавания речи из файла необходимо выполнить несколько шагов:
> [!div class="checklist"]
> * Создайте объект `SpeechConfig`, содержащий ключ и регион подписки.
> * Создайте объект `AudioConfig`, указывающий имя WAV-файла.
> * Создайте объект `SpeechRecognizer`, используя приведенные выше объекты `SpeechConfig` и `AudioConfig`.
> * С помощью объекта `SpeechRecognizer` запустите процесс распознавания для одного речевого фрагмента.
> * Проверьте возвращенный результат `SpeechRecognitionResult`.
