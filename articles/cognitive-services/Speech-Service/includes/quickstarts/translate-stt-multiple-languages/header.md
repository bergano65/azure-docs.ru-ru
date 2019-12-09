---
title: Краткое руководство. Перевод речи на несколько языков в службе "Речь"
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
ms.openlocfilehash: bc35c1efcad2ca9ebb5eaf23dd84f7189858a159
ms.sourcegitcommit: 6c01e4f82e19f9e423c3aaeaf801a29a517e97a0
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/04/2019
ms.locfileid: "74817317"
---
В этом кратком руководстве вы будете использовать [пакет SDK для службы "Речь"](~/articles/cognitive-services/speech-service/speech-sdk.md) для интерактивного перевода речи с одного языка в речь на другом языке. После выполнения нескольких предварительных требований преобразование речи в текст на нескольких языках займет всего шесть шагов:
> [!div class="checklist"]
> * Создайте объект ````SpeechTranslationConfig````, содержащий ключ и регион подписки.
> * Обновите объект ````SpeechTranslationConfig````, чтобы указать исходный язык распознавания речи.
> * Обновите объект ````SpeechTranslationConfig````, чтобы указать несколько целевых языков перевода.
> * Создайте объект ````TranslationRecognizer````, используя приведенный выше объект ````SpeechTranslationConfig````.
> * С помощью объекта ````TranslationRecognizer```` запустите процесс распознавания для одного речевого фрагмента.
> * Проверьте возвращенный результат ````TranslationRecognitionResult````.
