---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 04/02/2020
ms.author: trbye
ms.openlocfilehash: 36243fd5db76a4706318f41b3e2cb3f557c17189
ms.sourcegitcommit: 34a6fa5fc66b1cfdfbf8178ef5cdb151c97c721c
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2020
ms.locfileid: "81400794"
---
В этом кратком руководстве показано, как с помощью [пакета SDK для службы "Речь"](~/articles/cognitive-services/speech-service/speech-sdk.md) интерактивно распознать речь с микрофона и получить текстовую расшифровку из полученной аудиозаписи. Эту функцию легко интегрировать в приложения или устройства для выполнения общих задач распознавания, таких как распознавание бесед. Она также подходит для более сложных интеграций, таких как использование Bot Framework с пакетом SDK для службы "Речь" для создания голосовых помощников.

После выполнения нескольких предварительных требований для распознавания речи с микрофона необходимо выполнить всего четыре шага:

> [!div class="checklist"]
> * Создайте объект `SpeechConfig`, содержащий ключ и регион подписки.
> * Создайте объект `SpeechRecognizer`, используя приведенный выше объект `SpeechConfig`.
> * С помощью объекта `SpeechRecognizer` запустите процесс распознавания для одного речевого фрагмента.
> * Проверьте возвращенный результат `SpeechRecognitionResult`.
