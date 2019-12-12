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
ms.date: 12/09/2019
ms.author: yulili
ms.openlocfilehash: d4781808ce8e80f62e86ce1d0c6db9c38b2636d0
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/10/2019
ms.locfileid: "74980610"
---
В этом кратком руководстве показано, как использовать [пакет SDK для службы "Речь"](~/articles/cognitive-services/speech-service/speech-sdk.md) для интерактивного перевода речи с одного языка в текст на другом языке. После выполнения нескольких предварительных требований преобразование речи в текст займет всего пять шагов:
> [!div class="checklist"]
> * Создайте объект ````SpeechConfig````, содержащий ключ и регион подписки.
> * Обновите объект ````SpeechConfig````, чтобы указать исходный и целевой языки.
> * Создайте объект ````TranslationRecognizer````, используя приведенный выше объект ````SpeechConfig````.
> * С помощью объекта ````TranslationRecognizer```` запустите процесс распознавания для одного речевого фрагмента.
> * Проверьте возвращенный результат ````TranslationRecognitionResult````.
