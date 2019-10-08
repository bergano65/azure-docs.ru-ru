---
title: Краткое руководство. Распознавание речи с помощью .NET Framework (Windows) — служба "Речь"
titleSuffix: Azure Cognitive Services
description: Сведения в этом руководстве помогут создать консольное приложение для преобразования речи в текст с помощью .NET Framework для Windows и пакета SDK службы "Речь". После завершения вы сможете преобразовывать речь в текст в режиме реального времени с использованием микрофона компьютера.
services: cognitive-services
author: wolfma61
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 08/28/2019
ms.author: wolfma
ms.openlocfilehash: cb140647394858fbc0a9a00ea125365d5b7a08d5
ms.sourcegitcommit: e9936171586b8d04b67457789ae7d530ec8deebe
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/27/2019
ms.locfileid: "71327048"
---
# <a name="quickstart-recognize-speech-with-the-speech-sdk-for-net-framework-windows"></a>Краткое руководство. Распознавание речи с помощью пакета SDK службы "Речь" для .NET Framework (Windows)

Также доступны краткие руководства по [синтезу речи](quickstart-text-to-speech-dotnet-windows.md), [переводу речи](quickstart-translate-speech-dotnetframework-windows.md).

При необходимости переключитесь на другой язык программирования и (или) среду:<br/>
[!INCLUDE [Selector](../../../includes/cognitive-services-speech-service-quickstart-selector.md)]

Сведения в этом руководстве помогут создать консольное приложение для преобразования речи в текст с помощью .NET Framework для Windows и пакета SDK службы "Речь". После завершения вы сможете преобразовывать речь в текст в режиме реального времени с использованием микрофона компьютера.

Для быстрой демонстрации (без самостоятельного создания проекта Visual Studio, как описано в этой статье) загрузите новейшие [примеры пакета SDK службы "Речь" в Cognitive Services](https://github.com/Azure-Samples/cognitive-services-speech-sdk) с GitHub.

## <a name="prerequisites"></a>Предварительные требования

Чтобы выполнить этот проект, вам потребуется:

* [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/).
* Ключ подписки для службы "Речь". [Получить бесплатно](get-started.md).
* Доступ к микрофону компьютера.

## <a name="create-a-visual-studio-project"></a>Создание проекта Visual Studio

[!INCLUDE [Create project](../../../includes/cognitive-services-speech-service-create-speech-project-vs-csharp.md)]

## <a name="add-sample-code"></a>Добавление примеров кода

1. Откройте файл **Program.cs** и замените автоматически созданный код этим примером:

   [!code-csharp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/csharp-dotnet-windows/helloworld/Program.cs#code)]

1. Найдите строку `YourSubscriptionKey` и замените ее ключом подписки службы "Речь".

1. Найдите строку `YourServiceRegion` и замените ее названием [региона](regions.md), связанного с вашей подпиской. Например, если вы используете бесплатную пробную подписку, ваш регион — `westus`.

1. В строке меню выберите **Файл** > **Сохранить все**.

## <a name="build-and-run-the-app"></a>Создание и запуск приложения

1. В строке меню выберите **Сборка** > **Построить решение**, чтобы создать приложение. Теперь код должен компилироваться без ошибок.

1. Выберите **Отладка** > **Начать отладку** (или нажмите клавишу **F5**), чтобы запустить приложение **helloworld**.

1. Произнесите фразу или предложение на английском языке в микрофон устройства. Приложение передает речь в службы распознавания речи, которые расшифровывают речь в текст и отправляют его обратно в приложение для отображения.

   ![Пользовательский интерфейс распознавания речи](media/sdk/qs-csharp-dotnet-windows-10-console-output.png)

## <a name="next-steps"></a>Дополнительная информация

> [!div class="nextstepaction"]
> [Примеры для C# на сайте GitHub](https://aka.ms/csspeech/samples)

## <a name="see-also"></a>См. также

- [Train a model for Custom Speech](how-to-custom-speech-train-model.md) (Обучение модели для Пользовательского распознавания речи)
