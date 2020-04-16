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
ms.date: 04/04/2020
ms.author: yinhew
ms.openlocfilehash: 89f491aae91787ec6b67b9aa2daee262f3bab62e
ms.sourcegitcommit: 530e2d56fc3b91c520d3714a7fe4e8e0b75480c8
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/14/2020
ms.locfileid: "81275339"
---
## <a name="prerequisites"></a>Предварительные требования

Перед началом работы нужно сделать следующее:

> [!div class="checklist"]
> * [Создать ресурс службы "Речь" Azure.](../../../../get-started.md)
> * [Настройте среду разработки и создайте пустой проект](../../../../quickstarts/setup-platform.md?tabs=dotnet&pivots=programming-language-csharp).

## <a name="add-sample-code"></a>Добавление примеров кода

1. Откройте файл **Program.cs** и замените автоматически созданный код этим примером:

   [!code-csharp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/csharp/dotnet/text-to-speech/helloworld/Program.cs#code)]

1. Найдите строку `YourSubscriptionKey` и замените ее ключом подписки службы "Речь".

1. Найдите строку `YourServiceRegion` и замените ее названием [региона](~/articles/cognitive-services/Speech-Service/regions.md), связанного с вашей подпиской. Например, если вы используете бесплатную пробную подписку, ваш регион — `westus`.

1. В строке меню выберите **Файл** > **Сохранить все**.

## <a name="build-and-run-the-application"></a>Создание и запуск приложения

1. В строке меню выберите **Сборка** > **Построить решение**, чтобы создать приложение. Теперь код должен компилироваться без ошибок.

1. Выберите **Отладка** > **Начать отладку** (или нажмите клавишу **F5**), чтобы запустить приложение **helloworld**.

1. Введите фразу или предложение на английском языке. Приложение передает текст в службу "Речь", которая отправляет синтезированную речь в приложение для воспроизведения динамиком.

   ![Пользовательский интерфейс для синтеза речи](~/articles/cognitive-services/Speech-Service/media/sdk/qs-tts-csharp-dotnet-windows-console-output.png)

## <a name="next-steps"></a>Дальнейшие действия

[!INCLUDE [Speech synthesis basics](../../text-to-speech-next-steps.md)]

## <a name="see-also"></a>См. также раздел

- [Создание пользовательского голоса](~/articles/cognitive-services/Speech-Service/how-to-custom-voice-create-voice.md)
- [Запись пользовательских образцов голоса](~/articles/cognitive-services/Speech-Service/record-custom-voice-samples.md)
