---
title: Краткое руководство. Синтезирование речи с записью в звуковой файл в службе "Речь"
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
ms.openlocfilehash: f69c00f9cf787a192c62f12a707927c5c51a1d31
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/04/2019
ms.locfileid: "73504926"
---
В этом кратком руководстве будет использоваться [пакет SDK службы "Речь"](~/articles/cognitive-services/speech-service/speech-sdk.md) для преобразования текста в синтезированную речь в звуковом файле. После выполнения нескольких предварительных требований для синтезирования речи в файл необходимо выполнить всего пять шагов:
> [!div class="checklist"]
> * Создайте объект ````SpeechConfig````, содержащий ключ и регион подписки.
> * Создайте объект конфигурации звукового файла, указывающий имя WAV-файла.
> * Создайте объект ````SpeechSynthesizer```` с помощью объектов конфигурации, приведенных выше.
> * С помощью объекта ````SpeechSynthesizer```` преобразуйте текст в синтезированную речь, сохранив ее в указанном звуковом файле.
> * Проверьте возвращенный результат ````SpeechSynthesizer```` на наличие ошибок.
