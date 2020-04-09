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
ms.date: 04/04/2020
ms.author: yinhew
ms.openlocfilehash: 87fe6b49ce98b3fcb2b16c6573f81aa25fac7f5c
ms.sourcegitcommit: 67addb783644bafce5713e3ed10b7599a1d5c151
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/05/2020
ms.locfileid: "80671659"
---
> [!NOTE]
> .NET Core — это кроссплатформенная платформа .NET с открытым кодом, которая реализует спецификацию [.NET Standard](https://docs.microsoft.com/dotnet/standard/net-standard).

## <a name="prerequisites"></a>Предварительные требования

Перед началом работы нужно сделать следующее:

> [!div class="checklist"]
> * [Создать ресурс службы "Речь" Azure.](../../../../get-started.md)
> * [Настройте среду разработки и создайте пустой проект](../../../../quickstarts/setup-platform.md?tabs=dotnetcore&pivots=programming-language-csharp).

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

## <a name="next-steps"></a>Дальнейшие действия

[!INCLUDE [footer](./footer.md)]

## <a name="see-also"></a>См. также раздел

- [Создание пользовательского голоса](~/articles/cognitive-services/Speech-Service/how-to-custom-voice-create-voice.md)
- [Запись пользовательских образцов голоса](~/articles/cognitive-services/Speech-Service/record-custom-voice-samples.md)
