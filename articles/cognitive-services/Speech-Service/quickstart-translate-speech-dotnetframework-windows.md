---
title: Краткое руководство. Перевод речи с помощью C# (.NET Framework в Windows) — служба "Речь"
titleSuffix: Azure Cognitive Services
description: В этом кратком руководстве вы создадите приложение .NET Framework для записи речи пользователя, ее перевода на другой язык и вывода текста в командную строку. Это руководство предназначено для пользователей Windows.
services: cognitive-services
author: wolfma61
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 08/28/2019
ms.author: erhopf
ms.openlocfilehash: bb710a3e3adb13aa3999c13043c8bb93f6b885f1
ms.sourcegitcommit: e9936171586b8d04b67457789ae7d530ec8deebe
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/27/2019
ms.locfileid: "71327347"
---
# <a name="quickstart-translate-speech-with-the-speech-sdk-for-net-framework-windows"></a>Краткое руководство. Перевод речи с помощью пакета SDK службы "Речь" для .NET Framework (Windows)

Кроме того, доступны краткие руководства по [распознаванию речи](quickstart-csharp-dotnet-windows.md) и [синтезу речи](quickstart-text-to-speech-dotnet-windows.md).

В этом кратком руководстве вы создадите приложение .NET Framework, которое захватывает речь пользователя с микрофона компьютера, переводит речь и расшифровывает переведенный текст в командную строку в режиме реального времени. Приложение предназначено для работы в 32- или 64-разрядной версии Windows с использованием [пакета NuGet SDK службы "Речь"](https://aka.ms/csspeech/nuget) и Microsoft Visual Studio 2019.

Полный список языков для перевода речи см. в статье, посвященной [поддержке языков](language-support.md).

## <a name="prerequisites"></a>Предварительные требования

Для работы с этим кратким руководством вам понадобится:

* [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/).
* Ключ подписки Azure для службы "Речь". [Получить бесплатно](get-started.md).

## <a name="create-a-visual-studio-project"></a>Создание проекта Visual Studio

[!INCLUDE [Create project](../../../includes/cognitive-services-speech-service-create-speech-project-vs-csharp.md)]

## <a name="add-sample-code"></a>Добавление примеров кода

1. Откройте файл **Program.cs** и замените имеющийся код приведенным ниже кодом.

   [!code-csharp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/speech-translation/csharp-dotnet-windows/helloworld/Program.cs#code)]

1. Найдите строку `YourSubscriptionKey` и замените ее своим ключом подписки.

1. Найдите строку `YourServiceRegion` и замените ее названием [региона](regions.md), связанного с вашей подпиской. Например, если вы используете бесплатную пробную подписку, ваш регион — `westus`.

1. В строке меню выберите **Файл** > **Сохранить все**.

## <a name="build-and-run-the-application"></a>Создание и запуск приложения

1. В строке меню выберите **Сборка** > **Построить решение**, чтобы создать приложение. Теперь код должен компилироваться без ошибок.

1. Выберите **Отладка** > **Начать отладку** (или нажмите клавишу **F5**), чтобы запустить приложение **helloworld**.

1. Произнесите фразу или предложение на английском языке в микрофон устройства. Приложение передает речь в речевую службу, которая переводит речь в текст на другом языке (в нашем примере это немецкий). Служба распознавания речи отправляет переведенный текст обратно в приложение, которое отображает перевод в окне.

   ![Пользовательский интерфейс перевода речи](media/sdk/qs-translate-csharp-dotnetcore-windows-output.png)

## <a name="next-steps"></a>Дополнительная информация

Дополнительные примеры, например чтение речи из звукового файла и вывод переведенного текста в виде синтезированной речи, доступны на сайте GitHub.

> [!div class="nextstepaction"]
> [Примеры для C# на сайте GitHub](https://aka.ms/csspeech/samples)

## <a name="see-also"></a>См. также

- [Train a model for Custom Speech](how-to-custom-speech-train-model.md) (Обучение модели для Пользовательского распознавания речи)
