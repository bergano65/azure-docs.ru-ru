---
title: Краткое руководство. Синтез речи с помощью C# (.NET Core) в службе "Речь"
titleSuffix: Azure Cognitive Services
description: Узнайте, как синтезировать речь в Windows, используя C# в .NET Core и пакет SDK службы "Речь"
services: cognitive-services
author: yinhew
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 6/24/2019
ms.author: yinhew
ms.openlocfilehash: 7b4a018e38ca625e38dc1658a95d3ce0e677f711
ms.sourcegitcommit: c63e5031aed4992d5adf45639addcef07c166224
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/28/2019
ms.locfileid: "67467196"
---
# <a name="quickstart-synthesize-speech-with-the-speech-sdk-for-net-core"></a>Краткое руководство. Синтез речи с помощью пакета SDK службы "Речь" для .NET Core

Также доступны краткие руководства по [распознаванию речи](quickstart-csharp-dotnetcore-windows.md) и [переводу речи](quickstart-translate-speech-dotnetcore-windows.md).

Из этой статьи вы узнаете, как с помощью [пакета SDK службы "Речь"](speech-sdk.md) в Cognitive Services создать консольное приложение C# для .NET Core в Windows. Вы синтезируете речь из текста в режиме реального времени через микрофон компьютера. Приложение создается с использованием [пакета SDK NuGet для службы "Речь"](https://aka.ms/csspeech/nuget) и Microsoft Visual Studio 2017 (любого выпуска).

> [!NOTE]
> .NET Core — это кроссплатформенная платформа .NET с открытым кодом, которая реализует спецификацию [.NET Standard](https://docs.microsoft.com/dotnet/standard/net-standard).

Для работы с этим кратким руководством вам потребуется ключ подписки службы "Речь". Его можно получить бесплатно. Дополнительные сведения см. в статье [Бесплатная пробная подписка на службу "Речь"](get-started.md).

## <a name="prerequisites"></a>Предварительные требования

Для работы с этим кратким руководством вам понадобится:

* [Базовый пакет SDK для .NET](https://dotnet.microsoft.com/download)
* [Visual Studio 2017](https://visualstudio.microsoft.com/downloads/)
* Ключ подписки Azure для службы "Речь". [Его можно получить бесплатно](get-started.md).

## <a name="create-a-visual-studio-project"></a>Создание проекта Visual Studio

[!INCLUDE [](../../../includes/cognitive-services-speech-service-quickstart-dotnetcore-create-proj.md)]

## <a name="add-sample-code"></a>Добавление примеров кода

1. Откройте файл `Program.cs` и замените все содержимое приведенным ниже кодом.

    [!code-csharp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/text-to-speech/csharp-dotnetcore/helloworld/Program.cs#code)]

1. В том же файле замените строку `YourSubscriptionKey` своим ключом подписки.

1. Замените строку `YourServiceRegion` значением [региона](regions.md), связанного с подпиской (например, `westus` для бесплатной пробной подписки).

1. Сохраните внесенные в проект изменения.

## <a name="build-and-run-the-app"></a>Создание и запуск приложения

1. Создайте приложение. В строке меню последовательно выберите **Сборка** > **Собрать решение**. Теперь код должен компилироваться без ошибок.

    ![Снимок экрана приложения Visual Studio с выделенным параметром "Собрать решение"](media/sdk/qs-csharp-dotnetcore-windows-05-build.png "Успешная сборка")

1. Запустите приложение. В строке меню последовательно выберите **Отладка** > **Начать отладку** или нажмите клавишу **F5**.

    ![Снимок экрана приложения Visual Studio с выделенным параметром "Начать отладку"](media/sdk/qs-csharp-dotnetcore-windows-06-start-debugging.png "Start the app into debugging")

1. Появится всплывающее окно консоли с запросом ввести текст. Введите несколько слов или предложение. Введенный текст передается в службы "Речь" и синтезируется в речь, которая воспроизводится через динамик.

    ![Снимок экрана с выходными данными консоли после успешного синтеза](media/sdk/qs-tts-csharp-dotnet-windows-console-output.png "Выходные данные консоли после успешного синтеза")

## <a name="next-steps"></a>Дополнительная информация

Дополнительные примеры, например по синтезу речи в аудиофайл, доступны на сайте GitHub.

> [!div class="nextstepaction"]
> [Примеры для C# на сайте GitHub](https://aka.ms/csspeech/samples)

## <a name="see-also"></a>См. также

- [Настройка голосов](how-to-customize-voice-font.md)
- [Запись образцов голосов](record-custom-voice-samples.md)
