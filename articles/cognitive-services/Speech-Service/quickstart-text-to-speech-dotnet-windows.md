---
title: Краткое руководство. Синтезирование речи с помощью .NET Framework (Windows) в службах "Речь"
titleSuffix: Azure Cognitive Services
description: Сведения в этом руководстве помогут создать консольное приложение для преобразования текста в речь с помощью .NET Framework для Windows и пакета SDK службы "Речь". Завершив работу, вы сможете синтезировать речь из текста и прослушивать речь из динамика в реальном времени.
services: cognitive-services
author: yinhew
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 4/03/2019
ms.author: yinhew
ms.openlocfilehash: 3b7addd369d5e26554dd4300e65bee66b8a62768
ms.sourcegitcommit: e6d53649bfb37d01335b6bcfb9de88ac50af23bd
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/09/2019
ms.locfileid: "65465576"
---
# <a name="quickstart-synthesize-speech-with-the-speech-sdk-for-net-framework-windows"></a>Краткое руководство. Синтезирование речи с помощью пакета SDK службы "Речь" для .NET Framework (Windows)

Также доступны краткие руководства по [распознаванию речи](quickstart-csharp-dotnet-windows.md) и [переводу речи](quickstart-translate-speech-dotnetframework-windows.md).

Сведения в этом руководстве помогут создать консольное приложение для преобразования текста в речь с помощью .NET Framework для Windows и пакета SDK службы "Речь". Завершив работу, вы сможете синтезировать речь из текста и прослушивать речь из динамика в реальном времени.

Быстрая демонстрация (без самостоятельного создания проекта Visual Studio, как показано ниже).

Получите новейшие [примеры пакета SDK службы "Речь" в Cognitive Services](https://github.com/Azure-Samples/cognitive-services-speech-sdk) на GitHub.

## <a name="prerequisites"></a>Предварительные требования

Чтобы выполнить этот проект, вам потребуется:

* [Visual Studio 2017](https://visualstudio.microsoft.com/downloads/)
* Ключ подписки для службы "Речь". [Его можно получить бесплатно](get-started.md).
* Динамик (или гарнитура).

## <a name="create-a-visual-studio-project"></a>Создание проекта Visual Studio

[!INCLUDE [Create project](../../../includes/cognitive-services-speech-service-create-speech-project-vs-csharp.md)]

## <a name="add-sample-code"></a>Добавление примеров кода

1. Откройте файл `Program.cs` и замените автоматически созданный код этим примером:

    [!code-csharp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/text-to-speech/csharp-dotnet-windows/helloworld/Program.cs#code)]

1. Найдите строку `YourSubscriptionKey` и замените ее ключом подписки службы "Речь".

1. Найдите и замените строку `YourServiceRegion` на [регион](regions.md), связанный с вашей подпиской. Например, если вы используете бесплатную пробную версию, ваш регион — `westus`.

1. Сохраните изменения в проекте.

## <a name="build-and-run-the-app"></a>Создание и запуск приложения

1. В строке меню последовательно выберите **Сборка** > **Собрать решение**. Теперь код должен компилироваться без ошибок.

    ![Снимок экрана приложения Visual Studio с выделенным параметром "Собрать решение"](media/sdk/qs-csharp-dotnet-windows-08-build.png "Успешная сборка")

1. В строке меню выберите **Отладка** > **Начать отладку** или нажмите клавишу **F5**, чтобы запустить приложение.

    ![Снимок экрана приложения Visual Studio с выделенным параметром "Начать отладку"](media/sdk/qs-csharp-dotnet-windows-09-start-debugging.png "Start the app into debugging")

1. Появится окно консоли с запросом на ввод текста. Введите несколько слов или предложение. Введенный текст передается в службы "Речь" и синтезируется в речь, воспроизводящуюся через динамик.

    ![Снимок экрана с выходными данными консоли после успешного распознавания](media/sdk/qs-tts-csharp-dotnet-windows-console-output.png "Console output after successful recognition")

## <a name="next-steps"></a>Дополнительная информация

> [!div class="nextstepaction"]
> [Примеры для C# на сайте GitHub](https://aka.ms/csspeech/samples)

## <a name="see-also"></a>См. также

- [Настройка голосов](how-to-customize-voice-font.md)
- [Запись образцов голосов](record-custom-voice-samples.md)
