---
title: Краткое руководство. Синтез речи с помощью C# (UWP) в службе "Речь"
titleSuffix: Azure Cognitive Services
description: Из этой статьи вы узнаете, как создать приложение C# для универсальной платформы Windows с помощью пакета SDK службы "Речь" в Cognitive Services. Вы синтезируете речь из текста в режиме реального времени через микрофон устройства. Приложение создается с использованием пакета SDK службы "Речь" для NuGet и Microsoft Visual Studio 2019.
services: cognitive-services
author: yinhew
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 08/19/2019
ms.author: yinhew
ms.openlocfilehash: 3a88f4fc14286a60feb9d72676827b0cce91eb25
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/04/2019
ms.locfileid: "73505134"
---
> [!NOTE]
> Универсальная платформа Windows позволяет разрабатывать приложения, работающие на любом устройстве, которое поддерживает Windows 10, включая компьютеры, Xbox, Surface Hub и другие устройств.

## <a name="prerequisites"></a>Предварительные требования

Перед началом работы нужно сделать следующее:

> [!div class="checklist"]
> * [Создать ресурс службы "Речь" Azure](../../../../get-started.md).
> * [Настроить среду разработки](../../../../quickstarts/setup-platform.md?tabs=uwp).
> * [Создать пустой пример проекта](../../../../quickstarts/create-project.md?tabs=uwp).

## <a name="add-sample-code"></a>Добавление примеров кода

Теперь добавьте XAML-код, определяющий пользовательский интерфейс приложения, и добавьте реализацию C# кода программной части.

1. Откройте `MainPage.xaml` в **обозревателе решений**.

1. В представлении XAML конструктора вставьте следующий фрагмент кода XAML в тег **Grid** (между `<Grid>` и `</Grid>`):

   [!code-xml[UI elements](~/samples-cognitive-services-speech-sdk/quickstart/csharp/uwp/text-to-speech/helloworld/MainPage.xaml#StackPanel)]

1. В **Обозревателе решений** откройте исходный файл кода программной части `MainPage.xaml.cs`. (Он сгруппирован в `MainPage.xaml`.)

1. Замените все содержимое следующим фрагментом кода:

   [!code-csharp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/csharp/uwp/text-to-speech/helloworld/MainPage.xaml.cs#code)]

1. В обработчике `Speak_ButtonClicked` исходного файла найдите строку `YourSubscriptionKey` и замените ее своим ключом подписки.

1. В обработчике `Speak_ButtonClicked` найдите строку `YourServiceRegion` и замените ее названием [региона](~/articles/cognitive-services/Speech-Service/regions.md), связанного с вашей подпиской. (Например, используйте `westus` для подписки с бесплатной пробной версией.)

1. В строке меню выберите **Файл** > **Сохранить все**, чтобы сохранить изменения.

## <a name="build-and-run-the-application"></a>Создание и запуск приложения

Теперь все готово для сборки и тестирования приложения.

1. В строке меню выберите **Сборка** > **Построить решение**, чтобы создать приложение. Теперь код должен компилироваться без ошибок.

1. Выберите **Отладка** > **Начать отладку**(или нажмите клавишу **F5**), чтобы запустить приложение. Откроется окно **helloworld**.

   ![Пример приложения для синтеза речи в UWP на языке C# — краткое руководство](~/articles/cognitive-services/Speech-Service/media/sdk/qs-text-to-speech-uwp-helloworld-window.png)

1. Введите любой текст в текстовое поле и нажмите кнопку **Speak** (Сказать). Введенный текст передается в службы "Речь" и синтезируется в речь, которая воспроизводится через динамик.

    ![Пользовательский интерфейс для синтеза речи](~/articles/cognitive-services/Speech-Service/media/sdk/qs-tts-csharp-uwp-ui-result.png)

## <a name="next-steps"></a>Дополнительная информация

[!INCLUDE [footer](./footer.md)]

## <a name="see-also"></a>См. также

- [Создание пользовательского голоса](~/articles/cognitive-services/Speech-Service/how-to-custom-voice-create-voice.md)
- [Запись пользовательских образцов голоса](~/articles/cognitive-services/Speech-Service/record-custom-voice-samples.md)
