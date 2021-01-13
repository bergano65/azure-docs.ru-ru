---
author: trevorbye
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 01/27/2020
ms.author: trbye
ms.openlocfilehash: 4725a1a9cf2cb74655a37ac27a0a86f10d7f4bb9
ms.sourcegitcommit: e46f9981626751f129926a2dae327a729228216e
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/08/2021
ms.locfileid: "98053006"
---
В этом кратком руководстве показано, как распознать намерения из звуковых данных, полученных с микрофона, с помощью [пакета SDK для службы "Речь"](~/articles/cognitive-services/speech-service/speech-sdk.md) и службы "Распознавание речи". В частности, вы будете использовать пакет SDK для службы "Речь" и предварительно созданную предметную область в службе "Распознавание речи", чтобы определить намерения для системы домашней автоматики, например включение или выключение света. 

После выполнения нескольких предварительных требований распознавание речи и определение намерений из звуковых данных, полученных с микрофона, займут всего несколько шагов:

> [!div class="checklist"]
>
> * Создайте объект `SpeechConfig`, содержащий ключ и регион подписки.
> * Создайте объект `IntentRecognizer`, используя приведенный выше объект `SpeechConfig`.
> * С помощью объекта `IntentRecognizer` запустите процесс распознавания для одного речевого фрагмента.
> * Проверьте возвращенный результат `IntentRecognitionResult`.

