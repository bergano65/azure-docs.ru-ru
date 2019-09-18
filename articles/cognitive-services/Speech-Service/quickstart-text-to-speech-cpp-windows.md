---
title: Краткое руководство. Синтезирование речи с помощью C++ (Windows) в службе "Речь"
titleSuffix: Azure Cognitive Services
description: Из этой статьи вы узнаете, как синтезировать речь в Windows, используя C++ и пакет SDK для служб "Речь"
services: cognitive-services
author: yinhew
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 08/24/2019
ms.author: yinhew
ms.openlocfilehash: 686b21d3e02266af77687778c32f0d1ca6d55154
ms.sourcegitcommit: 49c4b9c797c09c92632d7cedfec0ac1cf783631b
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/05/2019
ms.locfileid: "70383076"
---
# <a name="quickstart-synthesize-speech-in-c-on-windows-by-using-the-speech-sdk"></a>Краткое руководство. Синтезирование речи в приложении C++ для Windows с помощью пакета SDK для служб "Речь"

Кроме того, доступны краткие руководства по [распознаванию речи](quickstart-cpp-windows.md) и [переводу речи](quickstart-translate-speech-cpp-windows.md).

В этой статье вы создадите консольное приложение C++ для Windows. Вы можете использовать [пакет SDK для служб "Речь"](speech-sdk.md) в Cognitive Services для синтезирования речи из текста в реальном времени и ее воспроизведения через динамики компьютера. Приложение создается с использованием [пакета SDK службы "Речь" для NuGet](https://aka.ms/csspeech/nuget) и Microsoft Visual Studio 2019 (любого выпуска).

Полный список языков и голосов для синтезирования речи см. в статье, посвященной [поддержке языков](language-support.md#text-to-speech).

## <a name="prerequisites"></a>Предварительные требования

Для работы с этим кратким руководством вам потребуется ключ подписки службы "Речь". Получить бесплатно Дополнительные сведения см. в статье [Бесплатная пробная подписка на службу "Речь"](get-started.md).

## <a name="create-a-visual-studio-project"></a>Создание проекта Visual Studio

[!INCLUDE [Quickstart C++ project](../../../includes/cognitive-services-speech-service-quickstart-cpp-create-proj.md)]

## <a name="add-sample-code"></a>Добавление примеров кода

1. Откройте исходный файл **helloworld.cpp**.

1. Замените все содержимое следующим кодом:

   [!code-cpp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/text-to-speech/cpp-windows/helloworld/helloworld.cpp#code)]

1. В том же файле замените строку `YourSubscriptionKey` своим ключом подписки.

1. Замените строку `YourServiceRegion` значением [региона](regions.md), связанного с подпиской (например, `westus` для бесплатной пробной подписки).

1. В строке меню выберите **Файл** > **Сохранить все**.

## <a name="build-and-run-the-application"></a>Создание и запуск приложения

1. В строке меню выберите **Сборка** > **Построить решение**,чтобы создать приложение. Теперь код должен компилироваться без ошибок.

1. Выберите **Отладка** > **Начать отладку**(или нажмите клавишу **F5**), чтобы запустить приложение **helloworld**.

1. Введите фразу или предложение на английском языке. Приложение передает текст в службы "Речь", которые отправляют синтезированную речь в приложение для воспроизведения динамиком.

   ![Выходные данные в консоли после успешного синтеза речи](media/sdk/qs-tts-cpp-windows-console-output.png)

## <a name="next-steps"></a>Дополнительная информация

Дополнительные примеры, например по сохранению речи в аудиофайл, доступны на сайте GitHub.

> [!div class="nextstepaction"]
> [Примеры для C++ на сайте GitHub](https://aka.ms/csspeech/samples)

## <a name="see-also"></a>См. также

- [Создание пользовательского голоса](how-to-custom-voice-create-voice.md)
- [Запись пользовательских образцов голоса](record-custom-voice-samples.md)
