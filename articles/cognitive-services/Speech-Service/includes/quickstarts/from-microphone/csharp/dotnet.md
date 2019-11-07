---
title: Краткое руководство. Распознавание речи с микрофона в службе "Речь" с помощью C# (.NET)
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
ms.openlocfilehash: 88ac0f05bf937af19a0bd6bf3cf2253fd3052f4c
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/04/2019
ms.locfileid: "73505542"
---
## <a name="prerequisites"></a>Предварительные требования

Перед началом работы нужно:

> [!div class="checklist"]
> * [Создать ресурс службы "Речь" Azure](../../../../get-started.md).
> * [Настроить среду разработки](../../../../quickstarts/setup-platform.md?tabs=dotnet).
> * [Создать пустой пример проекта](../../../../quickstarts/create-project.md?tabs=dotnet).

## <a name="open-your-project-in-visual-studio"></a>Откройте проект в Visual Studio.

Сначала необходимо убедиться, что проект открыт в Visual Studio.

1. Запустите Visual Studio 2019.
2. Загрузите проект и откройте `Program.cs`.

## <a name="start-with-some-boilerplate-code"></a>Добавление стандартного кода

Добавим код, который выступает в качестве основы для нашего проекта. Имейте в виду, что вы создали асинхронный метод `RecognizeSpeechAsync()`.
[!code-csharp[](~/samples-cognitive-services-speech-sdk/quickstart/csharp/dotnet/from-microphone/helloworld/Program.cs?range=5-15,43-52)]

## <a name="create-a-speech-configuration"></a>Создание конфигурации службы "Речь"

Перед инициализацией объекта `SpeechRecognizer` необходимо создать конфигурацию, использующую ключ и регион подписки. Вставьте код в метод `RecognizeSpeechAsync()`.

> [!NOTE]
> В этом примере для создания `SpeechConfig` используется метод `FromSubscription()`. Полный список доступных методов см. в статье [SpeechConfig Class](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechconfig?view=azure-dotnet) (Класс SpeechConfig).
[!code-csharp[](~/samples-cognitive-services-speech-sdk/quickstart/csharp/dotnet/from-microphone/helloworld/Program.cs?range=16)]

## <a name="initialize-a-speechrecognizer"></a>Инициализация SpeechRecognizer

Теперь создадим объект `SpeechRecognizer`. Этот объект создается внутри оператора using для обеспечения надлежащего выпуска неуправляемых ресурсов. Вставьте этот код в метод `RecognizeSpeechAsync()` непосредственно под конфигурацией службы "Речь".
[!code-csharp[](~/samples-cognitive-services-speech-sdk/quickstart/csharp/dotnet/from-microphone/helloworld/Program.cs?range=17-19,42)]

## <a name="recognize-a-phrase"></a>Распознавание фразы

В объекте `SpeechRecognizer` необходимо вызвать метод `RecognizeOnceAsync()`. С помощью этого метода служба "Речь" узнает, что для распознавания отправляется одна фраза и что после идентификации фразы необходимо остановить распознавание речи.

В операторе using добавьте этот код: [!code-csharp[](~/samples-cognitive-services-speech-sdk/quickstart/csharp/dotnet/from-microphone/helloworld/Program.cs?range=20)]

## <a name="display-the-recognition-results-or-errors"></a>Отображение результатов распознавания (или ошибок)

Когда служба "Речь" возвращает результат распознавания, необходимо с ним что-то сделать. Мы оставим его как есть и выведем результат в консоли.

В операторе using под `RecognizeOnceAsync()` добавьте этот код: [!code-csharp[](~/samples-cognitive-services-speech-sdk/quickstart/csharp/dotnet/from-microphone/helloworld/Program.cs?range=22-41)]

## <a name="check-your-code"></a>Проверка кода

На этом этапе код должен выглядеть так: [!code-csharp[](~/samples-cognitive-services-speech-sdk/quickstart/csharp/dotnet/from-microphone/helloworld/Program.cs)]

## <a name="build-and-run-your-app"></a>Создание и запуск приложения

Теперь можно приступать к созданию приложения и проверке распознавания речи, используя службу "Речь".

1. **Скомпилируйте код**. В строке меню Visual Studio последовательно выберите **Сборка** > **Собрать решение**.
2. **Запустите приложение**. В строке меню выберите **Отладка** > **Начать отладку** или нажмите клавишу **F5**.
3. **Начните распознавание**. Вам будет предложено произнести фразу на английском языке. Речь, записанная в виде текста, отправляется в службу "Речь" и выводится в консоли.

## <a name="next-steps"></a>Дополнительная информация

[!INCLUDE [footer](./footer.md)]
