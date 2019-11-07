---
title: Краткое руководство. Распознавание речи, намерений и сущностей в службе "Речь" с помощью Python
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
zone_pivot_groups: programming-languages-set-two
ms.openlocfilehash: 8916cb0438f0005d3ff9f720cf1b5a5653eeac77
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/04/2019
ms.locfileid: "73506166"
---
## <a name="prerequisites"></a>Предварительные требования

Перед началом работы нужно:

> [!div class="checklist"]
> * [Создать ресурс службы "Речь" Azure](../../../../get-started.md).
> * [Создать приложение LUIS и получить ключ конечной точки](../../../../quickstarts/create-luis.md).
> * [Настроить среду разработки](../../../../quickstarts/setup-platform.md).
> * [Создать пустой пример проекта](../../../../quickstarts/create-project.md).

## <a name="open-your-project"></a>Открытие проекта

Откройте Quickstart.py в редакторе Python.

## <a name="start-with-some-boilerplate-code"></a>Добавление стандартного кода

Добавим код, который выступает в качестве основы для нашего проекта.
[!code-python[](~/samples-cognitive-services-speech-sdk/quickstart/python/intent-recognition/quickstart.py?range=5-7)]

## <a name="create-a-speech-configuration"></a>Создание конфигурации службы "Речь"

Перед инициализацией объекта `IntentRecognizer` необходимо создать конфигурацию, использующую ключ и регион конечной точки LUIS. Вставьте этот код.

В этом примере создается объект `SpeechConfig` с использованием ключа и региона LUIS. Полный список доступных методов см. в статье [SpeechConfig Class](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechconfig) (Класс SpeechConfig).

> [!NOTE]
> Очень важно использовать ключ конечной точки LUIS, а не ключи для начала работы или разработки, так как только ключ конечной точки допустим для распознавания с преобразованием речи в намерения. Инструкции по получению правильного ключа см. в статье [Создание приложения LUIS и получение ключа конечной точки](~/articles/cognitive-services/Speech-Service/quickstarts/create-luis.md) (Создание приложения LUIS и получение ключа конечной точки).

[!code-python[](~/samples-cognitive-services-speech-sdk/quickstart/python/intent-recognition/quickstart.py?range=12)]

## <a name="initialize-a-intentrecognizer"></a>Инициализация объекта IntentRecognizer

Теперь создадим объект `IntentRecognizer`. Вставьте этот код непосредственно под конфигурацией службы "Речь".
[!code-python[](~/samples-cognitive-services-speech-sdk/quickstart/python/intent-recognition/quickstart.py?range=15)]

## <a name="add-a-languageunderstandingmodel-and-intents"></a>Добавление модели LanguageUnderstandingModel и намерений

Теперь необходимо сопоставить объект `LanguageUnderstandingModel` с распознавателем намерений и добавить намерения, которые необходимо распознать.
[!code-python[](~/samples-cognitive-services-speech-sdk/quickstart/python/intent-recognition/quickstart.py?range=19-27)]

## <a name="recognize-an-intent"></a>Распознавание намерения

В объекте `IntentRecognizer` необходимо вызвать метод `recognize_once()`. С помощью этого метода служба "Речь" узнает, что для распознавания отправляется одна фраза и что после идентификации фразы необходимо остановить распознавание речи.
[!code-python[](~/samples-cognitive-services-speech-sdk/quickstart/python/intent-recognition/quickstart.py?range=35)]

## <a name="display-the-recognition-results-or-errors"></a>Отображение результатов распознавания (или ошибок)

Когда служба "Речь" возвращает результат распознавания, необходимо с ним что-то сделать. Мы оставим его как есть и выведем результат в консоли.

В операторе using под вызовом `recognize_once()` добавьте следующий код: [!code-python[](~/samples-cognitive-services-speech-sdk/quickstart/python/intent-recognition/quickstart.py?range=38-47)]

## <a name="check-your-code"></a>Проверка кода

На этом этапе код должен выглядеть так: (Мы добавили несколько комментариев к этой версии.) [!code-python[](~/samples-cognitive-services-speech-sdk/quickstart/python/intent-recognition/quickstart.py?range=5-47)]

## <a name="build-and-run-your-app"></a>Создание и запуск приложения

Запустите пример из консоли или в интегрированной среде разработки:

    ````
    python quickstart.py
    ````

Слова, произносимые в микрофон, в течение следующих 15 секунд будут распознаны и записаны в окне консоли.

## <a name="next-steps"></a>Дополнительная информация

[!INCLUDE [footer](./footer.md)]
