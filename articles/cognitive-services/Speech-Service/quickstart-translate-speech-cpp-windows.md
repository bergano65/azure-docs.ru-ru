---
title: Краткое руководство. Перевод речи с помощью C++ (Windows) в службе "Речь"
titleSuffix: Azure Cognitive Services
description: С помощью этого краткого руководства вы создадите приложение C++ для записи речи пользователя, ее перевода на другой язык и вывода текста в командную строку. Это руководство предназначено для пользователей Windows.
services: cognitive-services
author: wolfma61
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 08/24/2019
ms.author: erhopf
ms.openlocfilehash: 6ba4e44efc7ff24aa48f9f16840b2248423f7241
ms.sourcegitcommit: 49c4b9c797c09c92632d7cedfec0ac1cf783631b
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/05/2019
ms.locfileid: "70382681"
---
# <a name="quickstart-translate-speech-in-c-on-windows-by-using-the-speech-sdk"></a>Краткое руководство. Перевод речи в классическом приложении C++ для Windows с помощью пакета SDK службы "Речь"

Кроме того, доступны краткие руководства по [распознаванию речи](quickstart-cpp-windows.md) и [синтезу речи](quickstart-text-to-speech-cpp-windows.md).

С помощью этого краткого руководства вы создадите простое приложение C++, которое записывает речь пользователя с микрофона компьютера, переводит ее и транскрибирует переведенный текст в командную строку в реальном времени. Приложение создается с помощью [пакета SDK службы "Речь" для NuGet](https://aka.ms/csspeech/nuget) и Microsoft Visual Studio 2019 (любого выпуска).

Полный список языков для перевода речи см. в статье, посвященной [поддержке языков](language-support.md).

## <a name="prerequisites"></a>Предварительные требования

Для работы с этим кратким руководством вам потребуется ключ подписки службы "Речь". Получить бесплатно Дополнительные сведения см. в статье [Бесплатная пробная подписка на службу "Речь"](get-started.md).

## <a name="create-a-visual-studio-project"></a>Создание проекта Visual Studio

[!INCLUDE [Quickstart C++ project](../../../includes/cognitive-services-speech-service-quickstart-cpp-create-proj.md)]

## <a name="add-sample-code"></a>Добавление примеров кода

1. Откройте исходный файл **helloworld.cpp**.

1. Замените все содержимое следующим фрагментом кода:

   [!code-cpp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/speech-translation/cpp-windows/helloworld/helloworld.cpp?range=2-#code)]

1. В том же файле замените строку `YourSubscriptionKey` своим ключом подписки.

1. Замените строку `YourServiceRegion` значением [региона](regions.md), связанного с подпиской (например, `westus` для бесплатной пробной подписки).

1. В строке меню выберите **Файл** > **Сохранить все**.

## <a name="build-and-run-the-application"></a>Создание и запуск приложения

1. В строке меню выберите **Сборка** > **Построить решение**, чтобы создать приложение. Теперь код должен компилироваться без ошибок.

1. Выберите **Отладка** > **Начать отладку** (или нажмите клавишу **F5**), чтобы запустить приложение **helloworld**.

1. Произнесите фразу или предложение на английском языке. Приложение передает речь в службу "Речь", которая выполняет перевод и транскрибирование в текст (в нашем примере на французский и немецкий языки). Затем служба "Речь" отправляет текст обратно в приложение для отображения.

   ![Вывод консоли после успешного перевода речи](media/sdk/qs-translate-cpp-windows-output.png)

## <a name="next-steps"></a>Дополнительная информация

Дополнительные примеры, например способы чтения речи из звукового файла и преобразования переведенного текста в синтезированную речь, доступны на сайте GitHub.

> [!div class="nextstepaction"]
> [Примеры для C++ на сайте GitHub](https://aka.ms/csspeech/samples)

## <a name="see-also"></a>См. также

- [Train a model for Custom Speech](how-to-custom-speech-train-model.md) (Обучение модели для Пользовательского распознавания речи)
