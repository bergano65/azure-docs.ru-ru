---
title: Краткое руководство. Распознавание речи с помощью C# (UWP) в службе "Речь"
titleSuffix: Azure Cognitive Services
description: Из этой статьи вы узнаете, как создать приложение C# для универсальной платформы Windows с помощью пакета SDK службы "Речь" в Cognitive Services. Вы преобразуете речь с микрофона вашего устройства в текст в реальном времени. Приложение создается с использованием пакета SDK службы "Речь" для NuGet и Microsoft Visual Studio 2019.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 08/19/2019
ms.author: erhopf
ms.custom: seodec18
ms.openlocfilehash: fe5ff376a7895e2ca5246c0b9eb575752b07c7a1
ms.sourcegitcommit: 49c4b9c797c09c92632d7cedfec0ac1cf783631b
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/05/2019
ms.locfileid: "70382285"
---
# <a name="quickstart-recognize-speech-in-a-uwp-app-by-using-the-speech-sdk"></a>Краткое руководство. Распознавание речи в приложении для универсальной платформы Windows с помощью пакета SDK службы "Речь"

Также доступны краткие руководства по [синтезу речи](quickstart-text-to-speech-csharp-uwp.md), [переводу речи](quickstart-translate-speech-uwp.md) и [виртуальному помощнику для обработки голоса](quickstart-virtual-assistant-csharp-uwp.md).

При необходимости переключитесь на другой язык программирования и (или) среду:<br/>
[!INCLUDE [Selector](../../../includes/cognitive-services-speech-service-quickstart-selector.md)]

Из этой статьи вы узнаете, как разработать приложение C# для универсальной платформы Windows с помощью [пакета SDK службы "Речь"](speech-sdk.md) в Cognitive Services. Программа преобразовывает речь с микрофона вашего устройства в текст в реальном времени. Приложение создается с использованием [пакета SDK NuGet для службы "Речь"](https://aka.ms/csspeech/nuget) и Microsoft Visual Studio 2019 (любого выпуска).

> [!NOTE]
> Универсальная платформа Windows позволяет разрабатывать приложения, работающие на любом устройстве, которое поддерживает Windows 10, включая компьютеры, Xbox, Surface Hub и другие устройств.

## <a name="prerequisites"></a>Предварительные требования

Для работы с этим кратким руководством вам понадобится:

* [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/).
* Ключ подписки Azure для службы "Речь". [Получить бесплатно](get-started.md).

## <a name="create-a-visual-studio-project"></a>Создание проекта Visual Studio

[!INCLUDE [](../../../includes/cognitive-services-speech-service-quickstart-uwp-create-proj.md)]

## <a name="add-sample-code"></a>Добавление примеров кода

Теперь добавьте XAML-код, определяющий пользовательский интерфейс приложения, и добавьте реализацию C# кода программной части.

1. Откройте `MainPage.xaml` в **обозревателе решений**.

1. В представлении XAML конструктора вставьте следующий фрагмент кода XAML в тег **Grid** (между `<Grid>` и `</Grid>`):

   [!code-xml[UI elements](~/samples-cognitive-services-speech-sdk/quickstart/csharp-uwp/helloworld/MainPage.xaml#StackPanel)]

1. В **Обозревателе решений** откройте исходный файл кода программной части `MainPage.xaml.cs`. (Он сгруппирован в `MainPage.xaml`.)

1. Замените все содержимое следующим фрагментом кода:

   [!code-csharp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/csharp-uwp/helloworld/MainPage.xaml.cs#code)]

1. В обработчике `SpeechRecognitionFromMicrophone_ButtonClicked` исходного файла найдите строку `YourSubscriptionKey` и замените ее своим ключом подписки.

1. В обработчике `SpeechRecognitionFromMicrophone_ButtonClicked` найдите строку `YourServiceRegion` и замените ее названием [региона](regions.md), связанного с вашей подпиской. (Например, используйте `westus` для подписки с бесплатной пробной версией.)

1. В строке меню выберите **Файл** > **Сохранить все**, чтобы сохранить изменения.

## <a name="build-and-run-the-application"></a>Создание и запуск приложения

Теперь все готово для сборки и тестирования приложения.

1. В строке меню выберите **Сборка** > **Построить решение**, чтобы создать приложение. Теперь код должен компилироваться без ошибок.

1. Выберите **Отладка** > **Начать отладку**(или нажмите клавишу **F5**), чтобы запустить приложение. Откроется окно **helloworld**.

   ![Пример приложения для распознавания речи в UWP на языке C# — краткое руководство](media/sdk/qs-csharp-uwp-helloworld-window.png)

1. Выберите **Включить микрофон**, а когда появится запрос на разрешение доступа, выберите **Да**.

   ![Запрос на разрешение доступа к микрофону](media/sdk/qs-csharp-uwp-10-access-prompt.png)

1. Щелкните **Распознавание речи с помощью микрофона** и произнесите фразу или предложение на английском языке в микрофон вашего устройства. Ваша речь передается в службу "Речь" и преобразуется в текст, который появляется в том же окне.

   ![Пользовательский интерфейс распознавания речи](media/sdk/qs-csharp-uwp-11-ui-result.png)

## <a name="next-steps"></a>Дополнительная информация

> [!div class="nextstepaction"]
> [Примеры для C# на сайте GitHub](https://aka.ms/csspeech/samples)

## <a name="see-also"></a>См. также

- [Краткое руководство Перевод речи с помощью пакета SDK службы "Речь" для C# (UWP)](quickstart-translate-speech-uwp.md)
- [Train a model for Custom Speech](how-to-custom-speech-train-model.md) (Обучение модели для Пользовательского распознавания речи)
