---
title: Краткое руководство. Распознавание речи, намерений и сущностей в службе "Речь" для Java
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
ms.openlocfilehash: 441c938e9ab74eafecaf0f9188c0884696eccfae
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/04/2019
ms.locfileid: "73506118"
---
## <a name="prerequisites"></a>Предварительные требования

Перед началом работы нужно сделать следующее.

> [!div class="checklist"]
> * [Создать ресурс службы "Речь" Azure.](../../../../get-started.md)
> * [Настроить приложение LUIS и получить ключ конечной точки.](../../../../quickstarts/create-luis.md)
> * [Настроить среду разработки.](../../../../quickstarts/setup-platform.md?tabs=jre)
> * [Создать пустой пример проекта.](../../../../quickstarts/create-project.md?tabs=jre)

## <a name="open-your-project"></a>Открытие проекта

Загрузите проект и откройте `Main.java`.

## <a name="start-with-some-boilerplate-code"></a>Добавление стандартного кода

Добавим код, который выступает в качестве основы для нашего проекта.
[!code-java[](~/samples-cognitive-services-speech-sdk/quickstart/java/jre/intent-recognition/src/speechsdk/quickstart/Main.java?range=6-20,69-76)]

## <a name="create-a-speech-configuration"></a>Создание конфигурации службы "Речь"

Перед инициализацией объекта `IntentRecognizer` необходимо создать конфигурацию, использующую ключ и регион конечной точки LUIS. Вставьте этот код в блок try/catch в методе main

В этом примере для создания `SpeechConfig` используется метод `FromSubscription()`. Полный список доступных методов см. в статье [Класс SpeechConfig](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechconfig?view=azure-dotnet).

> [!NOTE]
> Очень важно использовать ключ конечной точки LUIS, а не ключи для начала или разработки, так как только ключ конечной точки допустим для распознавания преобразования речи в намерения. Инструкции по получению правильного ключа см. в статье [Создание приложения LUIS и получение ключа конечной точки](~/articles/cognitive-services/Speech-Service/quickstarts/create-luis.md).

[!code-java[](~/samples-cognitive-services-speech-sdk/quickstart/java/jre/intent-recognition/src/speechsdk/quickstart/Main.java?range=27)]

## <a name="initialize-a-intentrecognizer"></a>Инициализация объекта IntentRecognizer

Теперь создадим объект `IntentRecognizer`. Вставьте этот код непосредственно под конфигурацией службы "Речь".
[!code-java[](~/samples-cognitive-services-speech-sdk/quickstart/java/jre/intent-recognition/src/speechsdk/quickstart/Main.java?range=30)]

## <a name="add-a-languageunderstandingmodel-and-intents"></a>Добавление модели LanguageUnderstandingModel и намерений

Теперь необходимо сопоставить объект `LanguageUnderstandingModel` с распознавателем намерений и добавить намерения, которые необходимо распознать.
[!code-java[](~/samples-cognitive-services-speech-sdk/quickstart/java/jre/intent-recognition/src/speechsdk/quickstart/Main.java?range=33-36)]

## <a name="recognize-an-intent"></a>Распознавание намерения

В объекте `IntentRecognizer` необходимо вызвать метод `recognizeOnceAsync()`. С помощью этого метода служба "Речь" узнает, что для распознавания отправляется одна фраза и что после идентификации фразы необходимо остановить распознавание речи.

[!code-java[](~/samples-cognitive-services-speech-sdk/quickstart/java/jre/intent-recognition/src/speechsdk/quickstart/Main.java?range=41)]

## <a name="display-the-recognition-results-or-errors"></a>Отображение результатов распознавания (или ошибок)

Когда служба "Речь" возвращает результат распознавания, необходимо с ним что-то сделать. Мы оставим его как есть и выведем результат в консоли.

Под вызовом `recognizeOnceAsync()` добавьте следующий код: [!code-java[](~/samples-cognitive-services-speech-sdk/quickstart/java/jre/intent-recognition/src/speechsdk/quickstart/Main.java?range=44-65)]

## <a name="release-resources"></a>Высвобождение ресурсов

После завершения работы с речевыми ресурсами необходимо высвободить их. Вставьте следующий код в конце блока try/catch: [!code-java[](~/samples-cognitive-services-speech-sdk/quickstart/java/jre/intent-recognition/src/speechsdk/quickstart/Main.java?range=67-68)]

## <a name="check-your-code"></a>Проверка кода

На этом этапе код должен выглядеть так: (Мы добавили несколько комментариев к этой версии.) [!code-java[](~/samples-cognitive-services-speech-sdk/quickstart/java/jre/intent-recognition/src/speechsdk/quickstart/Main.java?range=6-76)]

## <a name="build-and-run-your-app"></a>Сборка и запуск приложения

Нажмите клавишу F11 или выберите **Запустить** > **Отладка**.
Слова, произносимые в микрофон, в течение следующих 15 секунд будут распознаны и записаны в окне консоли.

## <a name="next-steps"></a>Дополнительная информация

[!INCLUDE [footer](./footer.md)]