---
title: Краткое руководство. Распознавание речи с микрофона в службе "Речь" с помощью C# (UWP)
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
ms.openlocfilehash: 5f83f249228c96021979edc14c092eded03b96e4
ms.sourcegitcommit: 5aefc96fd34c141275af31874700edbb829436bb
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/04/2019
ms.locfileid: "74818870"
---
## <a name="prerequisites"></a>Предварительные требования

Необходимые условия:

> [!div class="checklist"]
> * [Создать ресурс службы "Речь" Azure.](../../../../get-started.md)
> * [Настроить среду разработки.](../../../../quickstarts/setup-platform.md?tabs=uwp)
> * [Создать пустой пример проекта.](../../../../quickstarts/create-project.md?tabs=uwp)
> * Убедитесь, что у вас есть доступ к микрофону для аудиозахвата.

Если вы уже сделали это, отлично. Давайте продолжим.

## <a name="open-your-project-in-visual-studio"></a>Откройте проект в Visual Studio.

Сначала необходимо убедиться, что проект открыт в Visual Studio.

## <a name="start-with-some-boilerplate-code"></a>Использование стандартного кода

Добавим код, который выступает в качестве основы для нашего проекта.

1. Откройте `MainPage.xaml` в **обозревателе решений**.

2. В представлении XAML конструктора вставьте следующий фрагмент кода XAML в тег **Grid** (между `<Grid>` и `</Grid>`):

   [!code-xml[UI elements](~/samples-cognitive-services-speech-sdk/quickstart/csharp/uwp/from-microphone/helloworld/MainPage.xaml#StackPanel)]

3. В **Обозревателе решений** откройте исходный файл кода программной части `MainPage.xaml.cs`. (Он сгруппирован в `MainPage.xaml`.)

4. Замените код приведенным ниже базовым кодом:

   [!code-csharp[UI elements](~/samples-cognitive-services-speech-sdk/quickstart/csharp/uwp/from-microphone/helloworld/MainPage.xaml.cs?range=6-50,55-56,94-154)]

## <a name="create-a-speech-configuration"></a>Создание конфигурации службы "Речь"

Перед инициализацией объекта `SpeechRecognizer` необходимо создать конфигурацию, использующую ключ и регион подписки. Вставьте код в метод `RecognizeSpeechAsync()`.

> [!NOTE]
> В этом примере для создания `SpeechConfig` используется метод `FromSubscription()`. Полный список доступных методов см. в статье [SpeechConfig Class](https://docs.microsoft.com/dotnet/api/) (Класс SpeechConfig) [!code-csharp[](~/samples-cognitive-services-speech-sdk/quickstart/csharp/uwp/from-microphone/helloworld/MainPage.xaml.cs?range=51-53)].

## <a name="initialize-a-speechrecognizer"></a>Инициализация SpeechRecognizer

Теперь создадим объект `SpeechRecognizer`. Этот объект создается внутри оператора using для обеспечения надлежащего выпуска неуправляемых ресурсов. Вставьте этот код в метод `RecognizeSpeechAsync()` непосредственно под конфигурацией службы "Речь".
[!code-csharp[](~/samples-cognitive-services-speech-sdk/quickstart/csharp/uwp/from-microphone/helloworld/MainPage.xaml.cs?range=58,59,93)]

## <a name="recognize-a-phrase"></a>Распознавание фразы

В объекте `SpeechRecognizer` необходимо вызвать метод `RecognizeOnceAsync()`. С помощью этого метода служба "Речь" узнает, что для распознавания отправляется одна фраза и что после идентификации фразы необходимо остановить распознавание речи.

В операторе using добавьте этот код: [!code-csharp[](~/samples-cognitive-services-speech-sdk/quickstart/csharp/uwp/from-microphone/helloworld/MainPage.xaml.cs?range=66)]

## <a name="display-the-recognition-results-or-errors"></a>Отображение результатов распознавания (или ошибок)

Когда служба "Речь" возвращает результат распознавания, необходимо с ним что-то сделать. Мы оставим его как есть и выведем результат на панели состояния.

[!code-csharp[](~/samples-cognitive-services-speech-sdk/quickstart/csharp/uwp/from-microphone/helloworld/MainPage.xaml.cs?range=68-93)]

## <a name="build-and-run-the-application"></a>Создание и запуск приложения

Теперь все готово для сборки и тестирования приложения.

1. В строке меню выберите **Сборка** > **Построить решение**, чтобы создать приложение. Теперь код должен компилироваться без ошибок.

1. Выберите **Отладка** > **Начать отладку**(или нажмите клавишу **F5**), чтобы запустить приложение. Откроется окно **helloworld**.

   ![Пример приложения для распознавания речи в UWP на языке C# — краткое руководство](~/articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-uwp-helloworld-window.png)

1. Выберите **Включить микрофон**, а когда появится запрос на разрешение доступа, выберите **Да**.

   ![Запрос на разрешение доступа к микрофону](~/articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-uwp-10-access-prompt.png)

1. Щелкните **Распознавание речи с помощью микрофона** и произнесите фразу или предложение на английском языке в микрофон вашего устройства. Ваша речь передается в службу "Речь" и преобразовывается в текст, который появляется в том же окне.

   ![Пользовательский интерфейс распознавания речи](~/articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-uwp-11-ui-result.png)

## <a name="next-steps"></a>Дополнительная информация

[!INCLUDE [footer](./footer.md)]
