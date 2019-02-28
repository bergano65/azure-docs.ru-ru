---
title: Краткое руководство. Распознавание речи с помощью .NET Framework (Windows) в службе "Речь"
titleSuffix: Azure Cognitive Services
description: Сведения в этом руководстве помогут создать консольное приложение для преобразования речи в текст с помощью .NET Framework для Windows и пакета SDK службы "Речь". После завершения вы сможете преобразовывать речь в текст в режиме реального времени с использованием микрофона компьютера.
services: cognitive-services
author: wolfma61
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 12/13/2018
ms.author: wolfma
ms.openlocfilehash: cff3f526586eb52466883247d41a57dae919dd21
ms.sourcegitcommit: 50ea09d19e4ae95049e27209bd74c1393ed8327e
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/26/2019
ms.locfileid: "56867231"
---
# <a name="quickstart-recognize-speech-with-the-speech-sdk-for-net-framework-windows"></a>Краткое руководство. Распознавание речи с помощью пакета SDK службы "Речь" для .NET Framework (Windows)

[!INCLUDE [Selector](../../../includes/cognitive-services-speech-service-quickstart-selector.md)]

Сведения в этом руководстве помогут создать консольное приложение для преобразования речи в текст с помощью .NET Framework для Windows и пакета SDK службы "Речь". После завершения вы сможете преобразовывать речь в текст в режиме реального времени с использованием микрофона компьютера.

## <a name="prerequisites"></a>Предварительные требования

Чтобы выполнить этот проект, вам потребуется:

* [Visual Studio 2017](https://visualstudio.microsoft.com/downloads/)
* Ключ подписки для службы "Речь". [Получить бесплатно](get-started.md).
* Доступ к микрофону компьютера.

## <a name="create-a-visual-studio-project"></a>Создание проекта Visual Studio

[!INCLUDE [Create project](../../../includes/cognitive-services-speech-service-create-speech-project-vs-csharp.md)]

## <a name="add-sample-code"></a>Добавление примеров кода

1. Откройте файл `Program.cs` и замените автоматически созданный код этим примером:

    [!code-csharp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/csharp-dotnet-windows/helloworld/Program.cs#code)]

1. Найдите и замените строку `YourSubscriptionKey` с помощью ключа подписки службы "Речь".

1. Найдите и замените строку `YourServiceRegion` на [регион](regions.md), связанный с вашей подпиской. Например, если вы используете бесплатную пробную версию, ваш регион — `westus`.

1. Сохраните изменения в проекте.

## <a name="build-and-run-the-app"></a>Создание и запуск приложения

1. В строке меню последовательно выберите **Сборка** > **Собрать решение**. Теперь код должен компилироваться без ошибок.

    ![Снимок экрана приложения Visual Studio с выделенным параметром "Собрать решение"](media/sdk/qs-csharp-dotnet-windows-08-build.png "Успешная сборка")

1. В строке меню выберите **Отладка** > **Начать отладку** или нажмите клавишу **F5**, чтобы запустить приложение.

    ![Снимок экрана приложения Visual Studio с выделенным параметром "Начать отладку"](media/sdk/qs-csharp-dotnet-windows-09-start-debugging.png "Start the app into debugging")

1. Появится окно консоли с запросом на произнесение фразы. Теперь произнесите любую фразу на английском языке. Ваша речь передается в службу "Речь" и преобразовывается в текст в режиме реального времени. Результат выводится на консоль.

    ![Снимок экрана с выходными данными консоли после успешного распознавания](media/sdk/qs-csharp-dotnet-windows-10-console-output.png "Console output after successful recognition")

## <a name="next-steps"></a>Дополнительная информация

> [!div class="nextstepaction"]
> [Примеры для C# на сайте GitHub](https://aka.ms/csspeech/samples)

## <a name="see-also"></a>См. также

- [Настройка акустических моделей](how-to-customize-acoustic-models.md)
- [Настройка языковых моделей](how-to-customize-language-model.md)
