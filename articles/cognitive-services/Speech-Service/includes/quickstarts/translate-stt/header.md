---
title: Краткое руководство. Преобразование речи в текст в службе "Речь"
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
ms.openlocfilehash: c45e75038d1731c933ccf8bf26f9de573e409292
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/04/2019
ms.locfileid: "73504742"
---
В этом кратком руководстве показано, как использовать [пакет SDK для службы "Речь"](~/articles/cognitive-services/speech-service/speech-sdk.md) для интерактивного перевода речи с одного языка в текст на другом языке. После выполнения нескольких предварительных требований преобразование речи в текст займет всего пять шагов:
> [!div class="checklist"]
> * Создайте объект ````SpeechConfig````, содержащий ключ и регион подписки.
> * Обновите объект ````SpeechConfig````, чтобы указать исходный и целевой языки.
> * Создайте объект ````TranslationRecognizer````, используя приведенный выше объект ````SpeechConfig````.
> * С помощью объекта ````TranslationRecognizer```` запустите процесс распознавания для одного речевого фрагмента.
> * Проверьте возвращенный результат ````TranslationRecognitionResult````.
