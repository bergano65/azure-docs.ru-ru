---
title: Краткое руководство. Распознавание речи с микрофона в службе "Речь"
titleSuffix: Azure Cognitive Services
description: TBD
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 11/20/2019
ms.author: erhopf
ms.openlocfilehash: ab51fe0323ea23c16cb52aa7a0570f8d51d40b2f
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/25/2019
ms.locfileid: "75468061"
---
В этом кратком руководстве показано, как с помощью [пакета SDK для службы "Речь"](~/articles/cognitive-services/speech-service/speech-sdk.md) интерактивно распознать речь с микрофона и получить текстовую расшифровку из полученной аудиозаписи. Эту функцию легко интегрировать в приложения или устройства для выполнения общих задач распознавания, таких как распознавание бесед. Она также подходит для более сложных интеграций, таких как использование Bot Framework с пакетом SDK для службы "Речь" для создания голосовых помощников.

После выполнения нескольких предварительных требований для распознавания речи с микрофона необходимо выполнить всего четыре шага:

> [!div class="checklist"]
> * Создайте объект `SpeechConfig`, содержащий ключ и регион подписки.
> * Создайте объект `SpeechRecognizer`, используя приведенный выше объект `SpeechConfig`.
> * С помощью объекта `SpeechRecognizer` запустите процесс распознавания для одного речевого фрагмента.
> * Проверьте возвращенный результат `SpeechRecognitionResult`.
