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
ms.date: 12/09/2019
ms.author: yulili
ms.openlocfilehash: c5f0a0fe032d18cd4f01aebe9a5c736d6d511a74
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/10/2019
ms.locfileid: "74980423"
---
В этом кратком руководстве вы будете использовать [пакет SDK для службы "Речь"](~/articles/cognitive-services/speech-service/speech-sdk.md) для интерактивного перевода речи с одного языка в речь на другом языке. После выполнения нескольких предварительных требований преобразование речи в речь займет всего шесть шагов:
> [!div class="checklist"]
> * Создайте объект ````SpeechTranslationConfig````, содержащий ключ и регион подписки.
> * Обновите объект ````SpeechTranslationConfig````, чтобы указать исходный и целевой языки.
> * Обновите объект ````SpeechTranslationConfig````, чтобы указать название голоса речевого вывода.
> * Создайте объект ````TranslationRecognizer````, используя приведенный выше объект ````SpeechTranslationConfig````.
> * С помощью объекта ````TranslationRecognizer```` запустите процесс распознавания для одного речевого фрагмента.
> * Проверьте возвращенный результат ````TranslationRecognitionResult````.
