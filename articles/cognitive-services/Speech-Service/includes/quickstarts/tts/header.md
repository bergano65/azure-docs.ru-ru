---
title: Краткое руководство. Синтез речи в службе "Речь"
titleSuffix: Azure Cognitive Services
description: Узнайте, как синтезировать речь, используя пакет SDK службы "Речь"
services: cognitive-services
author: yulin-li
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 09/20/2019
ms.author: yulili
ms.openlocfilehash: 263a8e98c4c029728272c020efe00e82be20f5e2
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/04/2019
ms.locfileid: "73505078"
---
В этом кратком руководстве будет использоваться [пакет SDK службы "Речь"](~/articles/cognitive-services/speech-service/speech-sdk.md) для преобразования текста в синтезированную речь. После выполнения нескольких предварительных требований преобразование синтезированной речи с помощью говорящих по умолчанию укладывается всего в четыре шага:
> [!div class="checklist"]
> * Создайте объект ````SpeechConfig````, содержащий ключ и регион подписки.
> * Создайте объект ````SpeechSynthesizer````, используя приведенный выше объект ````SpeechConfig````.
> * Используйте объект ````SpeechSynthesizer```` для диктовки текста.
> * Проверьте возвращенный результат ````SpeechSynthesisResult```` на наличие ошибок.
