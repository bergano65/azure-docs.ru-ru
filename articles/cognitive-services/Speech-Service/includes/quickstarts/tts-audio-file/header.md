---
title: Краткое руководство. Синтезирование речи с записью в звуковой файл в службе "Речь"
titleSuffix: Azure Cognitive Services
description: TBD
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 10/28/2019
ms.author: erhopf
ms.openlocfilehash: 9b13d8fc3b77426a59dea5399223b79c4bb4b1a1
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/25/2019
ms.locfileid: "75469763"
---
В этом кратком руководстве будет использоваться [пакет SDK службы "Речь"](~/articles/cognitive-services/speech-service/speech-sdk.md) для преобразования текста в синтезированную речь в звуковом файле. После выполнения нескольких предварительных требований для синтезирования речи в файл необходимо выполнить всего пять шагов:
> [!div class="checklist"]
> * Создайте объект ````SpeechConfig````, содержащий ключ и регион подписки.
> * Создайте объект конфигурации звукового файла, указывающий имя WAV-файла.
> * Создайте объект ````SpeechSynthesizer```` с помощью объектов конфигурации, приведенных выше.
> * С помощью объекта ````SpeechSynthesizer```` преобразуйте текст в синтезированную речь, сохранив ее в указанном звуковом файле.
> * Проверьте возвращенный результат ````SpeechSynthesizer```` на наличие ошибок.
