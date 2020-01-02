---
title: Краткое руководство. Синтез речи с помощью C# (.NET Core) в службе "Речь"
titleSuffix: Azure Cognitive Services
description: Узнайте, как синтезировать речь в Windows, используя C# в .NET Core и пакет SDK службы "Речь"
services: cognitive-services
author: yinhew
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 6/24/2019
ms.author: yinhew
ms.openlocfilehash: 6e732237b22bec6e2a66d44f12ac25e5f9adca3f
ms.sourcegitcommit: 6c01e4f82e19f9e423c3aaeaf801a29a517e97a0
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/04/2019
ms.locfileid: "74818282"
---
> [!NOTE]
> .NET Core — это кроссплатформенная платформа .NET с открытым кодом, которая реализует спецификацию [.NET Standard](https://docs.microsoft.com/dotnet/standard/net-standard).

## <a name="prerequisites"></a>Предварительные требования

Перед началом работы нужно сделать следующее:

> [!div class="checklist"]
> * [Создать ресурс службы "Речь" Azure.](../../../../get-started.md)
> * [Настроить среду разработки.](../../../../quickstarts/setup-platform.md?tabs=dotnetcore)
> * [Создать пустой пример проекта.](../../../../quickstarts/create-project.md?tabs=dotnetcore)
## <a name="add-sample-code"></a>Добавление примеров кода

1. Откройте файл `Program.cs` и замените все содержимое приведенным ниже кодом.

    [!code-csharp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/csharp/dotnetcore/text-to-speech/helloworld/Program.cs#code)]

1. В том же файле замените строку `YourSubscriptionKey` своим ключом подписки.

1. Замените строку `YourServiceRegion` значением [региона](~/articles/cognitive-services/Speech-Service/regions.md), связанного с подпиской (например, `westus` для бесплатной пробной подписки).

1. Сохраните внесенные в проект изменения.

## <a name="build-and-run-the-app"></a>Создание и запуск приложения

1. Создайте приложение. В строке меню выберите**Сборка** > **Собрать решение**. Теперь код должен компилироваться без ошибок.

    ![Снимок экрана приложения Visual Studio с выделенным параметром "Собрать решение"](~/articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-dotnetcore-windows-05-build.png "Удачная сборка")

1. Запустите приложение. В строке меню выберите **Отладка** > **Начать отладку** или нажмите клавишу **F5**.

    ![Снимок экрана приложения Visual Studio с выделенным параметром "Начать отладку"](~/articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-dotnetcore-windows-06-start-debugging.png "Запуск приложения для отладки")

1. Появится всплывающее окно консоли с запросом ввести текст. Введите несколько слов или предложение. Введенный текст передается в службу "Речь" и синтезируется в речь, которая воспроизводится через динамик.

    ![Снимок экрана с выходными данными в консоли после успешного синтезирования](~/articles/cognitive-services/Speech-Service/media/sdk/qs-tts-csharp-dotnet-windows-console-output.png "Выходные данные в консоли после успешного синтеза")

## <a name="next-steps"></a>Дополнительная информация

[!INCLUDE [footer](./footer.md)]

## <a name="see-also"></a>См. также

- [Создание пользовательского голоса](~/articles/cognitive-services/Speech-Service/how-to-custom-voice-create-voice.md)
- [Запись пользовательских образцов голоса](~/articles/cognitive-services/Speech-Service/record-custom-voice-samples.md)
