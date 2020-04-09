---
author: IEvangelist
ms.service: cognitive-services
ms.subservice: speech-service
ms.date: 04/04/2020
ms.topic: include
ms.author: dapine
zone_pivot_groups: programming-languages-set-two
ms.openlocfilehash: 247b2a75af675c95745153e20b923d004a2f2f79
ms.sourcegitcommit: 67addb783644bafce5713e3ed10b7599a1d5c151
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/05/2020
ms.locfileid: "80671330"
---
## <a name="prerequisites"></a>Предварительные требования

Необходимые условия:

* <a href="~/articles/cognitive-services/Speech-Service/quickstarts/setup-platform.md?tabs=jre&pivots=programming-language-java" target="_blank">Установите пакет SDK службы "Речь" для среды разработки и создайте пустой пример проекта<span class="docon docon-navigate-external x-hidden-focus"></span></a>.

## <a name="create-a-luis-app-for-intent-recognition"></a>Создание приложения LUIS для распознавания намерений

[!INCLUDE [Create a LUIS app for intent recognition](../luis-sign-up.md)]

## <a name="open-your-project"></a>Открытие проекта

1. Откройте предпочтительную интегрированную среду разработки.
2. Загрузите проект и откройте `Main.java`.

## <a name="start-with-some-boilerplate-code"></a>Добавление стандартного кода

Добавим код, который выступает в качестве основы для нашего проекта.

[!code-java[](~/samples-cognitive-services-speech-sdk/quickstart/java/jre/intent-recognition/src/speechsdk/quickstart/Main.java?range=6-20,68-75)]

## <a name="create-a-speech-configuration"></a>Создание конфигурации службы "Речь"

Прежде чем инициализировать объект `IntentRecognizer`, необходимо создать конфигурацию, использующую ключ и расположение ресурса прогнозирования LUIS.  

Вставьте этот код в блок try / catch в `main()`. Обязательно обновите следующие значения:

* Замените `"YourLanguageUnderstandingSubscriptionKey"` ключом прогнозирования LUIS.
* Замените `"YourLanguageUnderstandingServiceRegion"` расположением LUIS. Используйте **идентификатор региона** из [региона](https://aka.ms/speech/sdkregion)

>[!TIP]
> Если вам нужна помощь с поиском этих значений, перейдите к разделу [Создание приложения LUIS для распознавания намерений](#create-a-luis-app-for-intent-recognition).

[!code-java[](~/samples-cognitive-services-speech-sdk/quickstart/java/jre/intent-recognition/src/speechsdk/quickstart/Main.java?range=27)]

В этом примере для создания `SpeechConfig` используется метод `FromSubscription()`. Полный список доступных методов см. в статье [SpeechConfig Class](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechconfig?view=azure-dotnet) (Класс SpeechConfig).

Пакет SDK для распознавания речи по умолчанию распознает использование языкового стандарта en-us. Дополнительные сведения о выборе исходного языка см. в разделе [Specify source language for speech to text](../../../../how-to-specify-source-language.md) (Указание исходного языка для преобразования речи в текст).

## <a name="initialize-an-intentrecognizer"></a>Инициализация объекта IntentRecognizer

Теперь создадим объект `IntentRecognizer`. Вставьте этот код непосредственно под конфигурацией службы "Речь".

[!code-java[](~/samples-cognitive-services-speech-sdk/quickstart/java/jre/intent-recognition/src/speechsdk/quickstart/Main.java?range=30)]

## <a name="add-a-languageunderstandingmodel-and-intents"></a>Добавление модели LanguageUnderstandingModel и намерений

Необходимо сопоставить объект `LanguageUnderstandingModel` с распознавателем намерений и добавить намерения, которые необходимо распознать. Мы будем использовать намерения из предварительно созданной предметной области для системы домашней автоматики.

Вставьте приведенный ниже код под строкой вашего `IntentRecognizer`. Обязательно замените `"YourLanguageUnderstandingAppId"` идентификатором приложения LUIS.

>[!TIP]
> Если вам нужна помощь с поиском этих значений, перейдите к разделу [Создание приложения LUIS для распознавания намерений](#create-a-luis-app-for-intent-recognition).

[!code-java[](~/samples-cognitive-services-speech-sdk/quickstart/java/jre/intent-recognition/src/speechsdk/quickstart/Main.java?range=33-35)]

## <a name="recognize-an-intent"></a>Распознавание намерения

В объекте `IntentRecognizer` необходимо вызвать метод `recognizeOnceAsync()`. С помощью этого метода служба "Речь" узнает, что для распознавания отправляется одна фраза и что после идентификации фразы необходимо остановить распознавание речи.

Вставьте приведенный ниже код под строкой модели:

[!code-java[](~/samples-cognitive-services-speech-sdk/quickstart/java/jre/intent-recognition/src/speechsdk/quickstart/Main.java?range=40)]

## <a name="display-the-recognition-results-or-errors"></a>Отображение результатов распознавания (или ошибок)

Когда служба "Речь" возвращает результат распознавания, необходимо с ним что-то сделать. Мы оставим его как есть и выведем результат в консоли.

Вставьте приведенный ниже код под вызовом `recognizeOnceAsync()`.

[!code-java[](~/samples-cognitive-services-speech-sdk/quickstart/java/jre/intent-recognition/src/speechsdk/quickstart/Main.java?range=43-64)]

## <a name="release-resources"></a>Высвобождение ресурсов

После завершения работы с речевыми ресурсами необходимо высвободить их. Вставьте следующий код в конце блока try/catch:

[!code-java[](~/samples-cognitive-services-speech-sdk/quickstart/java/jre/intent-recognition/src/speechsdk/quickstart/Main.java?range=66-67)]

## <a name="check-your-code"></a>Проверка кода

На этом этапе код должен выглядеть так:

> [!NOTE]
> Мы добавили несколько комментариев к этой версии.

[!code-java[](~/samples-cognitive-services-speech-sdk/quickstart/java/jre/intent-recognition/src/speechsdk/quickstart/Main.java?range=6-75)]

## <a name="build-and-run-your-app"></a>Создание и запуск приложения

Нажмите клавишу <kbd>F11</kbd> или выберите **Запустить** > **Отладка**.
Слова, произносимые в микрофон, в течение следующих 15 секунд будут распознаны и записаны в окне консоли.

## <a name="next-steps"></a>Дальнейшие действия

[!INCLUDE [footer](./footer.md)]
