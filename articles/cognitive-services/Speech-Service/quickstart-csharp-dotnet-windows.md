---
title: Краткое руководство. Распознавание речи в C# под .NET Framework на Windows с помощью пакета SDK для службы "Речь"
titleSuffix: Azure Cognitive Services
description: Узнайте, как распознавать речь в C# под .NET Framework на Windows, используя пакет SDK для службы "Речь"
services: cognitive-services
author: wolfma61
manager: cgronlun
ms.service: cognitive-services
ms.component: speech-service
ms.topic: quickstart
ms.date: 09/24/2018
ms.author: wolfma
ms.openlocfilehash: 06d4a41a4a67d077f8d79eee68938dff65adf0e8
ms.sourcegitcommit: 62759a225d8fe1872b60ab0441d1c7ac809f9102
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/19/2018
ms.locfileid: "49468523"
---
# <a name="quickstart-recognize-speech-in-c-under-net-framework-on-windows-by-using-the-speech-sdk"></a>Краткое руководство. Распознавание речи в приложении C# для .NET Framework в Windows с помощью пакета SDK для службы "Речь"

[!INCLUDE [Selector](../../../includes/cognitive-services-speech-service-quickstart-selector.md)]

Из этой статьи вы узнаете, как в Windows с помощью [пакета SDK службы "Речь"](speech-sdk.md) создать консольное приложение C# для .NET Framework. Вы преобразуете речь с микрофона вашего компьютера в текст в реальном времени. Приложение создается с использованием [пакета SDK NuGet для службы "Речь"](https://aka.ms/csspeech/nuget) и Microsoft Visual Studio 2017 (любого выпуска).

## <a name="prerequisites"></a>Предварительные требования

Для работы с этим кратким руководством вам потребуется ключ подписки службы "Речь". Его можно получить бесплатно. Дополнительные сведения см. в статье [Бесплатная пробная подписка на службу "Речь"](get-started.md).

## <a name="create-a-visual-studio-project"></a>Создание проекта Visual Studio

[!INCLUDE [Create project ](../../../includes/cognitive-services-speech-service-create-speech-project-vs-csharp.md)]

## <a name="add-sample-code"></a>Добавление примеров кода

1. Откройте файл `Program.cs` и замените все содержимое приведенным ниже кодом.

    [!code-csharp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/csharp-dotnet-windows/helloworld/Program.cs#code)]

1. В этом же файле замените строку `YourSubscriptionKey` на ваш ключ подписки службы "Речь".

1. Замените строку `YourServiceRegion` значением [региона](regions.md), связанного с подпиской (например, `westus` для бесплатной пробной подписки).

1. Сохраните внесенные в проект изменения.

## <a name="build-and-run-the-app"></a>Создание и запуск приложения

1. Создайте приложение. В строке меню последовательно выберите **Сборка** > **Собрать решение**. Теперь код должен компилироваться без ошибок.

    ![Снимок экрана приложения Visual Studio с выделенным параметром "Собрать решение"](media/sdk/qs-csharp-dotnet-windows-08-build.png "Успешная сборка")

1. Запустите приложение. В строке меню последовательно выберите **Отладка** > **Начать отладку** или нажмите клавишу **F5**.

    ![Снимок экрана приложения Visual Studio с выделенным параметром "Начать отладку"](media/sdk/qs-csharp-dotnet-windows-09-start-debugging.png "Start the app into debugging")

1. Появится всплывающее окно консоли с запросом произнести что-либо. Произнесите фразу или предложение на английском языке. Ваша речь передается в службу "Речь" и транскрибируется в текст, который отображается в том же окне.

    ![Снимок экрана с выходными данными консоли после успешного распознавания](media/sdk/qs-csharp-dotnet-windows-10-console-output.png "Console output after successful recognition")

[!INCLUDE [Download this sample](../../../includes/cognitive-services-speech-service-speech-sdk-sample-download-h2.md)]
Этот пример можно найти в папке `quickstart/csharp-dotnet-windows`.

## <a name="next-steps"></a>Дополнительная информация

> [!div class="nextstepaction"]
> [Распознавание намерений на основе речи с помощью пакета SDK службы "Речь" для C#](how-to-recognize-intents-from-speech-csharp.md)

## <a name="see-also"></a>См. также

- [Translate speech using Speech service](how-to-translate-speech-csharp.md) (Перевод речи с помощью службы распознавания речи)
- [Настройка акустических моделей](how-to-customize-acoustic-models.md)
- [Настройка языковых моделей](how-to-customize-language-model.md)
