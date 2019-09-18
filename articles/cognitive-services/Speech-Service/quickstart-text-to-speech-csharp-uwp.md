---
title: Краткое руководство. Синтез речи с помощью C# (UWP) в службе "Речь"
titleSuffix: Azure Cognitive Services
description: Из этой статьи вы узнаете, как создать приложение C# для универсальной платформы Windows с помощью пакета SDK службы "Речь" в Cognitive Services. Вы синтезируете речь из текста в режиме реального времени через микрофон устройства. Приложение создается с использованием пакета SDK службы "Речь" для NuGet и Microsoft Visual Studio 2019.
services: cognitive-services
author: yinhew
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 08/19/2019
ms.author: yinhew
ms.openlocfilehash: 65b65c9af377b6a9951f9f328e0732850d3b9c1d
ms.sourcegitcommit: 49c4b9c797c09c92632d7cedfec0ac1cf783631b
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/05/2019
ms.locfileid: "70382222"
---
# <a name="quickstart-synthesize-speech-in-a-uwp-app-by-using-the-speech-sdk"></a>Краткое руководство. Синтез речи в приложении для универсальной платформы Windows с помощью пакета SDK службы "Речь"

Кроме того, доступны краткие руководства по [распознаванию речи](quickstart-csharp-uwp.md), [переводу речи](quickstart-translate-speech-uwp.md) и [виртуальному помощнику для обработки голоса](quickstart-virtual-assistant-csharp-uwp.md).

Из этой статьи вы узнаете, как разработать приложение C# для универсальной платформы Windows с помощью [пакета SDK службы "Речь"](speech-sdk.md) в Cognitive Services. Эта программа синтезирует речь из текста в режиме реального времени и выводит ее на динамик устройства. Вы создадите приложение с помощью [пакета SDK службы "Речь" для NuGet](https://aka.ms/csspeech/nuget) и Microsoft Visual Studio 2019 (любого выпуска).

> [!NOTE]
> Универсальная платформа Windows позволяет разрабатывать приложения, работающие на любом устройстве, которое поддерживает Windows 10, включая компьютеры, Xbox, Surface Hub и другие устройств.

## <a name="prerequisites"></a>Предварительные требования

Для работы с этим кратким руководством вам понадобится:

* [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/).
* Ключ подписки Azure для службы "Речь". [Получить бесплатно](get-started.md).

## <a name="create-a-visual-studio-project"></a>Создание проекта Visual Studio

[!INCLUDE [](../../../includes/cognitive-services-speech-service-quickstart-uwp-create-proj.md)]

## <a name="add-sample-code"></a>Добавление примеров кода

Теперь добавьте XAML-код, определяющий пользовательский интерфейс приложения, и добавьте реализацию C# кода программной части.

1. Откройте `MainPage.xaml` в **обозревателе решений**.

1. В представлении XAML конструктора вставьте следующий фрагмент кода XAML в тег **Grid** (между `<Grid>` и `</Grid>`):

   [!code-xml[UI elements](~/samples-cognitive-services-speech-sdk/quickstart/text-to-speech/csharp-uwp/helloworld/MainPage.xaml#StackPanel)]

1. В **Обозревателе решений**откройте исходный файл кода программной части`MainPage.xaml.cs`. (Он сгруппирован в `MainPage.xaml`.)

1. Замените все содержимое следующим кодом:

   [!code-csharp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/text-to-speech/csharp-uwp/helloworld/MainPage.xaml.cs#code)]

1. В обработчике `Speak_ButtonClicked` исходного файла найдите строку `YourSubscriptionKey` и замените ее своим ключом подписки.

1. В обработчике `Speak_ButtonClicked` найдите строку `YourServiceRegion` и замените ее названием [региона](regions.md), связанного с вашей подпиской. (Например, используйте `westus` для подписки с бесплатной пробной версией.)

1. В строке меню выберите **Файл** > **Сохранить все**, чтобы сохранить изменения.

## <a name="build-and-run-the-application"></a>Создание и запуск приложения

Теперь все готово для сборки и тестирования приложения.

1. В строке меню выберите **Сборка** > **Построить решение**,чтобы создать приложение. Теперь код должен компилироваться без ошибок.

1. Выберите **Отладка** > **Начать отладку**(или нажмите клавишу **F5**), чтобы запустить приложение. Откроется окно **helloworld**.

   ![Пример приложения для синтеза речи в UWP на языке C# — краткое руководство](media/sdk/qs-text-to-speech-uwp-helloworld-window.png)

1. Введите любой текст в текстовое поле и нажмите кнопку **Speak** (Сказать). Введенный текст передается в службы "Речь" и синтезируется в речь, которая воспроизводится через динамик.

    ![Пользовательский интерфейс для синтеза речи](media/sdk/qs-tts-csharp-uwp-ui-result.png)

## <a name="next-steps"></a>Дополнительная информация

> [!div class="nextstepaction"]
> [Примеры для C# на сайте GitHub](https://aka.ms/csspeech/samples)

## <a name="see-also"></a>См. также

- [Создание и использование настраиваемых голосовых моделей](how-to-custom-voice-create-voice.md)
- [Запись образцов голосов](record-custom-voice-samples.md)
