---
title: Краткое руководство. Распознавание речи с помощью C# (.NET Core) в службе "Речь"
titleSuffix: Azure Cognitive Services
description: Узнайте, как распознавать речь в Windows или macOS, используя C# в .NET Core и пакет SDK службы "Речь"
services: cognitive-services
author: wolfma61
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 07/05/2019
ms.author: wolfma
ms.openlocfilehash: 341f173e399196e32ab59f44db4302b1b1a195e6
ms.sourcegitcommit: 6cff17b02b65388ac90ef3757bf04c6d8ed3db03
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/29/2019
ms.locfileid: "68607738"
---
# <a name="quickstart-recognize-speech-with-the-speech-sdk-for-net-core"></a>Краткое руководство. Распознавание речи с помощью пакета SDK службы "Речь" для .NET Core

Также доступны краткие руководства по [преобразованию текста в речь](quickstart-text-to-speech-dotnetcore.md) и [переводу речи](quickstart-translate-speech-dotnetcore-windows.md).

При необходимости переключитесь на другой язык программирования и (или) среду:<br/>
[!INCLUDE [Selector](../../../includes/cognitive-services-speech-service-quickstart-selector.md)]

Из этой статьи вы узнаете, как с помощью [пакета SDK службы "Речь"](speech-sdk.md) в Cognitive Services создать консольное приложение C# для .NET Core в Windows или macOS. Вы преобразуете речь с микрофона вашего компьютера в текст в реальном времени. Приложение создается с помощью [пакета SDK службы "Речь" для NuGet](https://aka.ms/csspeech/nuget) и Microsoft Visual Studio версии 2017 и выше (любого выпуска).

> [!NOTE]
> .NET Core — это кроссплатформенная платформа .NET с открытым кодом, которая реализует спецификацию [.NET Standard](https://docs.microsoft.com/dotnet/standard/net-standard).

Для работы с этим кратким руководством вам потребуется ключ подписки службы распознавания речи "Речь". Получить бесплатно Дополнительные сведения см. в статье [Бесплатная пробная подписка на службы распознавания речи](get-started.md).

## <a name="prerequisites"></a>Предварительные требования

Для работы с этим кратким руководством вам понадобится:

* [Базовый пакет SDK для .NET](https://dotnet.microsoft.com/download)
* [Visual Studio 2017](https://visualstudio.microsoft.com/downloads/) или более поздней версии.
* Ключ подписки Azure для службы "Речь". [Получить бесплатно](get-started.md).

## <a name="create-a-visual-studio-project"></a>Создание проекта Visual Studio

[!INCLUDE [](../../../includes/cognitive-services-speech-service-quickstart-dotnetcore-create-proj.md)]

## <a name="add-sample-code"></a>Добавление примеров кода

1. Откройте файл `Program.cs` и замените все содержимое приведенным ниже кодом.

    [!code-csharp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/csharp-dotnetcore/helloworld/Program.cs#code)]

1. В том же файле замените строку `YourSubscriptionKey` своим ключом подписки.

1. Замените строку `YourServiceRegion` значением [региона](regions.md), связанного с подпиской (например, `westus` для бесплатной пробной подписки).

1. Сохраните внесенные в проект изменения.

## <a name="build-and-run-the-app"></a>Создание и запуск приложения

1. Создайте приложение. В строке меню выберите**Сборка** > **Собрать решение**. Теперь код должен компилироваться без ошибок.

    ![Снимок экрана приложения Visual Studio с выделенным параметром "Собрать решение"](media/sdk/qs-csharp-dotnetcore-windows-05-build.png "Успешная сборка")

1. Запустите приложение. В строке меню выберите **Отладка** > **Начать отладку** или нажмите клавишу **F5**.

    ![Снимок экрана приложения Visual Studio с выделенным параметром "Начать отладку"](media/sdk/qs-csharp-dotnetcore-windows-06-start-debugging.png "Start the app into debugging")

1. Появится всплывающее окно консоли с запросом произнести что-либо. Произнесите фразу или предложение на английском языке. Ваша речь передается в службу распознавания речи и преобразовывается в текст, который появляется в том же окне.

    ![Снимок экрана с выходными данными консоли после успешного распознавания](media/sdk/qs-csharp-dotnetcore-windows-07-console-output.png "Console output after successful recognition")

## <a name="next-steps"></a>Дополнительная информация

Дополнительные примеры, такие как чтение речи из аудиофайла, доступны на сайте GitHub.

> [!div class="nextstepaction"]
> [Примеры для C# на сайте GitHub](https://aka.ms/csspeech/samples)

## <a name="see-also"></a>См. также

- [Настройка акустических моделей](how-to-customize-acoustic-models.md)
- [Настройка языковых моделей](how-to-customize-language-model.md)
