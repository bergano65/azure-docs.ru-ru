---
author: IEvangelist
ms.service: cognitive-services
ms.topic: include
ms.date: 04/02/2020
ms.author: dapine
ms.openlocfilehash: 3775690802c89805ccf9df1ee6d6717a8818213f
ms.sourcegitcommit: 0450ed87a7e01bbe38b3a3aea2a21881f34f34dd
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/03/2020
ms.locfileid: "80658484"
---
В этом кратком руководстве показано, как с помощью [пакета SDK для службы "Речь"](~/articles/cognitive-services/speech-service/speech-sdk.md) интерактивно распознать речь с микрофона и получить текстовую расшифровку из полученной аудиозаписи. Эту функцию легко интегрировать в приложения или устройства для выполнения общих задач распознавания, таких как распознавание бесед. Она также подходит для более сложных интеграций, таких как использование Bot Framework с пакетом SDK для службы "Речь" для создания голосовых помощников.

После выполнения нескольких предварительных требований для распознавания речи с микрофона необходимо выполнить всего четыре шага:

> [!div class="checklist"]
> * Создайте объект `SpeechConfig`, содержащий ключ и регион подписки.
> * Создайте объект `SpeechRecognizer`, используя приведенный выше объект `SpeechConfig`.
> * С помощью объекта `SpeechRecognizer` запустите процесс распознавания для одного речевого фрагмента.
> * Проверьте возвращенный результат `SpeechRecognitionResult`.
