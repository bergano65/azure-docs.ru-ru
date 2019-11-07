---
title: Краткое руководство. Синтезирование речи в службе "Речь" с помощью C# (Windows)
titleSuffix: Azure Cognitive Services
description: Сведения в этом руководстве помогут создать консольное приложение для преобразования текста в речь с помощью .NET Framework для Windows и пакета SDK службы "Речь". Завершив работу, вы сможете синтезировать речь из текста и прослушивать речь из динамика в реальном времени.
services: cognitive-services
author: yinhew
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 08/27/2019
ms.author: yinhew
ms.openlocfilehash: af101862e824d6527c08c232812913565536bb4a
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/04/2019
ms.locfileid: "73505158"
---
## <a name="prerequisites"></a>Предварительные требования

Перед началом работы нужно:

> [!div class="checklist"]
> * [Создать ресурс службы "Речь" Azure](../../../../get-started.md).
> * [Настроить среду разработки](../../../../quickstarts/setup-platform.md?tabs=dotnet).
> * [Создать пустой пример проекта](../../../../quickstarts/create-project.md?tabs=dotnet).

## <a name="add-sample-code"></a>Добавление примеров кода

1. Откройте файл **Program.cs** и замените автоматически созданный код этим примером:

   [!code-csharp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/csharp/dotnet/text-to-speech/helloworld/Program.cs#code)]

1. Найдите строку `YourSubscriptionKey` и замените ее ключом подписки службы "Речь".

1. Найдите строку `YourServiceRegion` и замените ее названием [региона](~/articles/cognitive-services/Speech-Service/regions.md), связанного с вашей подпиской. Например, если вы используете бесплатную пробную подписку, ваш регион — `westus`.

1. В строке меню выберите **Файл** > **Сохранить все**.

## <a name="build-and-run-the-application"></a>Создание и запуск приложения

1. В строке меню выберите **Сборка** > **Построить решение**, чтобы создать приложение. Теперь код должен компилироваться без ошибок.

1. Выберите **Отладка** > **Начать отладку** (или нажмите клавишу **F5**), чтобы запустить приложение **helloworld**.

1. Введите фразу или предложение на английском языке. Приложение передает текст в службы "Речь", которые отправляют синтезированную речь в приложение для воспроизведения динамиком.

   ![Пользовательский интерфейс для синтеза речи](~/articles/cognitive-services/Speech-Service/media/sdk/qs-tts-csharp-dotnet-windows-console-output.png)

## <a name="next-steps"></a>Дополнительная информация

[!INCLUDE [footer](./footer.md)]

## <a name="see-also"></a>См. также

- [Создание пользовательского голоса](~/articles/cognitive-services/Speech-Service/how-to-custom-voice-create-voice.md)
- [Запись пользовательских образцов голоса](~/articles/cognitive-services/Speech-Service/record-custom-voice-samples.md)
