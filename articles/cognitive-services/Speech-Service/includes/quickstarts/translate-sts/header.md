---
title: Краткое руководство. Перевод речи в речь в службе "Речь"
titleSuffix: Azure Cognitive Services
description: ПОДЛЕЖИТ УТОЧНЕНИЮ
services: cognitive-services
author: yulin-li
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 09/20/2019
ms.author: yulili
ms.openlocfilehash: 77b060a5caf9865a1296cd6644cf0c51e3253a0e
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/04/2019
ms.locfileid: "73504846"
---
В этом кратком руководстве вы будете использовать [пакет SDK для службы "Речь"](~/articles/cognitive-services/speech-service/speech-sdk.md) для интерактивного перевода речи с одного языка в речь на другом языке. После выполнения нескольких предварительных требований преобразование речи в речь займет всего шесть шагов:
> [!div class="checklist"]
> * Создайте объект ````SpeechTranslationConfig````, содержащий ключ и регион подписки.
> * Обновите объект ````SpeechTranslationConfig````, чтобы указать исходный и целевой языки.
> * Обновите объект ````SpeechTranslationConfig````, чтобы указать название голоса речевого вывода.
> * Создайте объект ````TranslationRecognizer````, используя приведенный выше объект ````SpeechTranslationConfig````.
> * С помощью объекта ````TranslationRecognizer```` запустите процесс распознавания для одного речевого фрагмента.
> * Проверьте возвращенный результат ````TranslationRecognitionResult````.
