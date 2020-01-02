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
ms.openlocfilehash: 7d9a03f5a57473ce651560b261a4cf84b4ca4824
ms.sourcegitcommit: 6c01e4f82e19f9e423c3aaeaf801a29a517e97a0
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/04/2019
ms.locfileid: "74817868"
---
В этом кратком руководстве будет использоваться [пакет SDK службы "Речь"](~/articles/cognitive-services/speech-service/speech-sdk.md) для преобразования текста в синтезированную речь в звуковом файле. После выполнения нескольких предварительных требований для синтезирования речи в файл необходимо выполнить всего пять шагов:
> [!div class="checklist"]
> * Создайте объект ````SpeechConfig````, содержащий ключ и регион подписки.
> * Создайте объект конфигурации звукового файла, указывающий имя WAV-файла.
> * Создайте объект ````SpeechSynthesizer```` с помощью объектов конфигурации, приведенных выше.
> * С помощью объекта ````SpeechSynthesizer```` преобразуйте текст в синтезированную речь, сохранив ее в указанном звуковом файле.
> * Проверьте возвращенный результат ````SpeechSynthesizer```` на наличие ошибок.
