---
title: Краткое руководство. Распознавание речи, намерений и сущностей в службе "Речь"
titleSuffix: Azure Cognitive Services
description: TBD
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 1/02/2019
ms.author: erhopf
zone_pivot_groups: programming-languages-set-two
ms.openlocfilehash: a833d39ab91cd803f066d707306a6ff648d37e8f
ms.sourcegitcommit: 51ed913864f11e78a4a98599b55bbb036550d8a5
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/04/2020
ms.locfileid: "75660557"
---
В этом кратком руководстве показано, как распознать намерения из звуковых данных, полученных с микрофона, с помощью [пакета SDK для службы "Речь"](~/articles/cognitive-services/speech-service/speech-sdk.md) и службы "Распознавание речи". В частности, вы будете использовать пакет SDK для службы "Речь" и предварительно созданную предметную область в службе "Распознавание речи", чтобы определить намерения для системы домашней автоматики, например включение или выключение света. 

После выполнения нескольких предварительных требований распознавание речи и определение намерений из звуковых данных, полученных с микрофона, займут всего несколько шагов:

> [!div class="checklist"]
>
> * Создайте объект ````SpeechConfig````, содержащий ключ и регион подписки.
> * Создайте объект ````IntentRecognizer````, используя приведенный выше объект ````SpeechConfig````.
> * С помощью объекта ````IntentRecognizer```` запустите процесс распознавания для одного речевого фрагмента.
> * Проверьте возвращенный результат ````IntentRecognitionResult````.
