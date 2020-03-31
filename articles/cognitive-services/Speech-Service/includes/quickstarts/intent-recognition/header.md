---
author: IEvangelist
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 01/27/2020
ms.author: dapine
ms.openlocfilehash: d59a55a9b6d57d90ac6ae893b05e1064c11eb58b
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76900357"
---
В этом кратком руководстве показано, как распознать намерения из звуковых данных, полученных с микрофона, с помощью [пакета SDK для службы "Речь"](~/articles/cognitive-services/speech-service/speech-sdk.md) и службы "Распознавание речи". В частности, вы будете использовать пакет SDK для службы "Речь" и предварительно созданную предметную область в службе "Распознавание речи", чтобы определить намерения для системы домашней автоматики, например включение или выключение света. 

После выполнения нескольких предварительных требований распознавание речи и определение намерений из звуковых данных, полученных с микрофона, займут всего несколько шагов:

> [!div class="checklist"]
>
> * Создайте объект `SpeechConfig`, содержащий ключ и регион подписки.
> * Создайте объект `IntentRecognizer`, используя приведенный выше объект `SpeechConfig`.
> * С помощью объекта `IntentRecognizer` запустите процесс распознавания для одного речевого фрагмента.
> * Проверьте возвращенный результат `IntentRecognitionResult`.
