---
title: Краткое руководство. Перевод речи с помощью C# (.NET Core для Windows)
titleSuffix: Azure Cognitive Services
description: При работе с этим кратким руководством вы создадите простое приложение .NET Core для записи речи пользователя, перевода ее на другой язык и вывода текста в командную строку. Это руководство предназначено для пользователей Windows.
services: cognitive-services
author: wolfma61
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 03/13/2019
ms.author: erhopf
ms.openlocfilehash: 8f246e5b3d96855bd40ff50e2f382d8576fee021
ms.sourcegitcommit: c63e5031aed4992d5adf45639addcef07c166224
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/28/2019
ms.locfileid: "67466110"
---
# <a name="quickstart-translate-speech-with-the-speech-sdk-for-net-core"></a>Краткое руководство. Перевод речи с помощью пакета SDK службы "Речь" для .NET Core

Также доступны краткие руководства по [преобразованию речи в текст](quickstart-csharp-dotnet-windows.md) и [преобразованию текста в речь](quickstart-text-to-speech-dotnetcore.md).

При работе с этим кратким руководством вы создадите простое приложение .NET Core, которое записывает речь пользователя с микрофона компьютера, переводит речь и транскрибирует переведенный текст в командную строку в реальном времени. Приложение предназначено для работы в 64-разрядной версии Windows с использованием [пакета NuGet SDK службы "Речь"](https://aka.ms/csspeech/nuget) и Microsoft Visual Studio 2017.

Полный список языков для перевода речи см. в статье, посвященной [поддержке языков](language-support.md).

## <a name="prerequisites"></a>Предварительные требования

Для работы с этим кратким руководством вам понадобится:

* [Базовый пакет SDK для .NET](https://dotnet.microsoft.com/download)
* [Visual Studio 2017](https://visualstudio.microsoft.com/downloads/)
* Ключ подписки Azure для службы "Речь". [Его можно получить бесплатно](get-started.md).

## <a name="create-a-visual-studio-project"></a>Создание проекта Visual Studio

[!INCLUDE [](../../../includes/cognitive-services-speech-service-quickstart-dotnetcore-create-proj.md)]

## <a name="add-sample-code"></a>Добавление примеров кода

1. Откройте файл `Program.cs` и замените все содержимое приведенным ниже кодом.

    [!code-csharp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/speech-translation/csharp-dotnetcore/helloworld/Program.cs#code)]

1. В том же файле замените строку `YourSubscriptionKey` своим ключом подписки.

1. Замените строку `YourServiceRegion` значением [региона](regions.md), связанного с подпиской (например, `westus` для бесплатной пробной подписки).

1. Сохраните внесенные в проект изменения.

## <a name="build-and-run-the-app"></a>Создание и запуск приложения

1. Создайте приложение. В строке меню последовательно выберите **Сборка** > **Собрать решение**. Теперь код должен компилироваться без ошибок.

    ![Снимок экрана приложения Visual Studio с выделенным параметром "Собрать решение"](media/sdk/qs-csharp-dotnetcore-windows-05-build.png "Успешная сборка")

1. Запустите приложение. В строке меню последовательно выберите **Отладка** > **Начать отладку** или нажмите клавишу **F5**.

    ![Снимок экрана приложения Visual Studio с выделенным параметром "Начать отладку"](media/sdk/qs-csharp-dotnetcore-windows-06-start-debugging.png "Start the app into debugging")

1. Появится всплывающее окно консоли с запросом произнести что-либо. Произнесите фразу или предложение на английском языке. Ваша речь передается в службу "Речь", переводится и транскрибируется в текст, который появляется в том же окне.

    ![Снимок экрана с выходными данными консоли после успешного преобразования](media/sdk/qs-translate-csharp-dotnetcore-windows-output.png "Выходные данные после успешного преобразования")


## <a name="next-steps"></a>Дополнительная информация

Дополнительные примеры, например чтение речи из звукового файла и вывод переведенного текста в виде синтезированной речи, доступны на сайте GitHub.

> [!div class="nextstepaction"]
> [Примеры для C# на сайте GitHub](https://aka.ms/csspeech/samples)

## <a name="see-also"></a>См. также

- [Настройка акустических моделей](how-to-customize-acoustic-models.md)
- [Настройка языковых моделей](how-to-customize-language-model.md)
