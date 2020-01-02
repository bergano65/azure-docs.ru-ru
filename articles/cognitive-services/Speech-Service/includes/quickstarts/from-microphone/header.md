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
ms.date: 11/20/2019
ms.author: erhopf
ms.openlocfilehash: a72597163a8c11cd8b515d052dc69992784e61e0
ms.sourcegitcommit: 5aefc96fd34c141275af31874700edbb829436bb
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/04/2019
ms.locfileid: "74818824"
---
В этом кратком руководстве показано, как с помощью [пакета SDK для службы "Речь"](~/articles/cognitive-services/speech-service/speech-sdk.md) интерактивно распознать речь с микрофона и получить текстовую расшифровку из полученной аудиозаписи. Эту функцию легко интегрировать в приложения или устройства для выполнения общих задач распознавания, таких как распознавание бесед. Она также подходит для более сложных интеграций, таких как использование Bot Framework с пакетом SDK для службы "Речь" для создания голосовых помощников.

После выполнения нескольких предварительных требований для распознавания речи с микрофона необходимо выполнить всего четыре шага:

> [!div class="checklist"]
> * Создайте объект `SpeechConfig`, содержащий ключ и регион подписки.
> * Создайте объект `SpeechRecognizer`, используя приведенный выше объект `SpeechConfig`.
> * С помощью объекта `SpeechRecognizer` запустите процесс распознавания для одного речевого фрагмента.
> * Проверьте возвращенный результат `SpeechRecognitionResult`.
