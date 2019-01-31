---
title: Краткое руководство. Распознавание речи с помощью приложения C# для универсальной платформы Windows в службе "Речь"
titleSuffix: Azure Cognitive Services
description: Из этой статьи вы узнаете, как создать приложение C# для универсальной платформы Windows с помощью пакета SDK службы "Речь" в Cognitive Services. Вы преобразуете речь с микрофона вашего устройства в текст в реальном времени. Приложение создается с использованием пакета SDK службы "Речь" для NuGet и Microsoft Visual Studio 2017.
services: cognitive-services
author: wolfma61
manager: cgronlun
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 12/06/2018
ms.author: wolfma
ms.custom: seodec18
ms.openlocfilehash: b6b7ec6e6601c37ce66147ba635693205f2a05b5
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/29/2019
ms.locfileid: "55224366"
---
# <a name="quickstart-recognize-speech-in-a-uwp-app-by-using-the-speech-sdk"></a>Краткое руководство. Распознавание речи в приложении для универсальной платформы Windows с помощью пакета SDK службы "Речь"

[!INCLUDE [Selector](../../../includes/cognitive-services-speech-service-quickstart-selector.md)]

Из этой статьи вы узнаете, как разработать приложение C# для универсальной платформы Windows (Windows 1709 или более поздней версии) с помощью [пакета SDK службы "Речь"](speech-sdk.md) в Cognitive Services. Программа будет преобразовывать речь с микрофона вашего устройства в текст в реальном времени. Приложение создается с использованием [пакета SDK NuGet для службы "Речь"](https://aka.ms/csspeech/nuget) и Microsoft Visual Studio 2017 (любого выпуска).

> [!NOTE]
> Универсальная платформа Windows позволяет разрабатывать приложения, работающие на любом устройстве, которое поддерживает Windows 10, включая компьютеры, Xbox, Surface Hub и другие устройств.

## <a name="prerequisites"></a>Предварительные требования

Для работы с этим кратким руководством вам понадобится:

* [Visual Studio 2017](https://visualstudio.microsoft.com/downloads/)
* Ключ подписки Azure для службы "Речь". [Его можно получить бесплатно](get-started.md).

## <a name="create-a-visual-studio-project"></a>Создание проекта Visual Studio

[!INCLUDE [](../../../includes/cognitive-services-speech-service-quickstart-uwp-create-proj.md)]

## <a name="add-sample-code"></a>Добавление примеров кода

1. Пользовательский интерфейс приложения определяется с помощью XAML. Откройте `MainPage.xaml` в обозревателе решений. В представлении XAML конструктора вставьте следующий фрагмент кода XAML в тег Grid (между `<Grid>` и `</Grid>`).

   [!code-xml[UI elements](~/samples-cognitive-services-speech-sdk/quickstart/csharp-uwp/helloworld/MainPage.xaml#StackPanel)]

1. Откройте исходный файл с кодом `MainPage.xaml.cs` (он находится в группе `MainPage.xaml`). Замените все содержимое приведенным ниже кодом.

   [!code-csharp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/csharp-uwp/helloworld/MainPage.xaml.cs#code)]

1. В обработчике `SpeechRecognitionFromMicrophone_ButtonClicked` этого файла замените строку `YourSubscriptionKey` своим ключом подписки.

1. В обработчике `SpeechRecognitionFromMicrophone_ButtonClicked` замените строку `YourServiceRegion` значением [региона](regions.md), связанного с подпиской (например, `westus` для бесплатной пробной подписки).

1. Сохраните все внесенные в проект изменения.

## <a name="build-and-run-the-app"></a>Создание и запуск приложения

1. Создайте приложение. В строке меню последовательно выберите **Сборка** > **Собрать решение**. Теперь код должен компилироваться без ошибок.

    ![Снимок экрана приложения Visual Studio с выделенным параметром "Собрать решение"](media/sdk/qs-csharp-uwp-08-build.png "Успешная сборка")

1. Запустите приложение. В строке меню последовательно выберите **Отладка** > **Начать отладку** или нажмите клавишу **F5**.

    ![Снимок экрана приложения Visual Studio с выделенным параметром "Начать отладку"](media/sdk/qs-csharp-uwp-09-start-debugging.png "Start the app into debugging")

1. Откроется окно. Выберите**Включить микрофон** и подтвердите разрешение в появившемся окне запроса.

    ![Снимок экрана запроса разрешения](media/sdk/qs-csharp-uwp-10-access-prompt.png "Запуск отладки приложения")

1. Щелкните **Распознавание речи с помощью микрофона** и произнесите фразу или предложение на английском языке в микрофон вашего устройства. Ваша речь передается в службу "Речь" и преобразовывается в текст, который появляется в том же окне.

    ![Снимок экрана пользовательского интерфейса распознавания речи](media/sdk/qs-csharp-uwp-11-ui-result.png)

## <a name="next-steps"></a>Дополнительная информация

> [!div class="nextstepaction"]
> [Примеры для C# на сайте GitHub](https://aka.ms/csspeech/samples)

## <a name="see-also"></a>См. также

- [Translate speech using Speech service](how-to-translate-speech-csharp.md) (Перевод речи с помощью службы распознавания речи)
- [Настройка акустических моделей](how-to-customize-acoustic-models.md)
- [Настройка языковых моделей](how-to-customize-language-model.md)
