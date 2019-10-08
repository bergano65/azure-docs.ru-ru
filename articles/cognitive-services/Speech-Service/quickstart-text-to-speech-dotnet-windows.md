---
title: Краткое руководство. Синтезирование речи с помощью .NET Framework (Windows) — служба "Речь"
titleSuffix: Azure Cognitive Services
description: Сведения в этом руководстве помогут создать консольное приложение для преобразования текста в речь с помощью .NET Framework для Windows и пакета SDK службы "Речь". Завершив работу, вы сможете синтезировать речь из текста и прослушивать речь из динамика в реальном времени.
services: cognitive-services
author: yinhew
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 08/27/2019
ms.author: yinhew
ms.openlocfilehash: 1a411455e4a6dea22e092cdfc8e70ee23b656435
ms.sourcegitcommit: e9936171586b8d04b67457789ae7d530ec8deebe
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/27/2019
ms.locfileid: "71327447"
---
# <a name="quickstart-synthesize-speech-with-the-speech-sdk-for-net-framework-windows"></a>Краткое руководство. Синтезирование речи с помощью пакета SDK службы "Речь" для .NET Framework (Windows)

Кроме того, доступны краткие руководства по [распознаванию речи](quickstart-csharp-dotnet-windows.md) и [переводу речи](quickstart-translate-speech-dotnetframework-windows.md).

Сведения в этом руководстве помогут создать консольное приложение для преобразования текста в речь с помощью .NET Framework для Windows и пакета SDK службы "Речь". Завершив работу, вы сможете синтезировать речь из текста и прослушивать речь из динамика в реальном времени.

Для быстрой демонстрации (без самостоятельного создания проекта Visual Studio, как описано в этой статье) загрузите новейшие [примеры пакета SDK службы "Речь" в Cognitive Services](https://github.com/Azure-Samples/cognitive-services-speech-sdk) с GitHub.

## <a name="prerequisites"></a>Предварительные требования

Чтобы выполнить этот проект, вам потребуется:

* [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/).
* Ключ подписки для службы "Речь". [Получить бесплатно](get-started.md).
* Динамик (или гарнитура).

## <a name="create-a-visual-studio-project"></a>Создание проекта Visual Studio

[!INCLUDE [Create project](../../../includes/cognitive-services-speech-service-create-speech-project-vs-csharp.md)]

## <a name="add-sample-code"></a>Добавление примеров кода

1. Откройте файл **Program.cs** и замените автоматически созданный код этим примером:

   [!code-csharp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/text-to-speech/csharp-dotnet-windows/helloworld/Program.cs#code)]

1. Найдите строку `YourSubscriptionKey` и замените ее ключом подписки службы "Речь".

1. Найдите строку `YourServiceRegion` и замените ее названием [региона](regions.md), связанного с вашей подпиской. Например, если вы используете бесплатную пробную подписку, ваш регион — `westus`.

1. В строке меню выберите **Файл** > **Сохранить все**.

## <a name="build-and-run-the-application"></a>Создание и запуск приложения

1. В строке меню выберите **Сборка** > **Построить решение**, чтобы создать приложение. Теперь код должен компилироваться без ошибок.

1. Выберите **Отладка** > **Начать отладку** (или нажмите клавишу **F5**), чтобы запустить приложение **helloworld**.

1. Введите фразу или предложение на английском языке. Приложение передает текст в службы "Речь", которые отправляют синтезированную речь в приложение для воспроизведения динамиком.

   ![Пользовательский интерфейс для синтеза речи](media/sdk/qs-tts-csharp-dotnet-windows-console-output.png)

## <a name="next-steps"></a>Дополнительная информация

> [!div class="nextstepaction"]
> [Примеры для C# на сайте GitHub](https://aka.ms/csspeech/samples)

## <a name="see-also"></a>См. также

- [Создание пользовательского голоса](how-to-custom-voice-create-voice.md)
- [Запись пользовательских образцов голоса](record-custom-voice-samples.md)
