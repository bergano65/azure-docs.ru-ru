---
title: Краткое руководство. Синтезирование речи в службе "Речь" с помощью C++ (Windows)
titleSuffix: Azure Cognitive Services
description: Из этой статьи вы узнаете, как синтезировать речь в Windows, используя C++ и пакет SDK для служб "Речь"
services: cognitive-services
author: yinhew
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 08/24/2019
ms.author: yinhew
ms.openlocfilehash: 35c8eb640e3c8c9e33486d5126f83dd40b5b96e0
ms.sourcegitcommit: 6c01e4f82e19f9e423c3aaeaf801a29a517e97a0
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/04/2019
ms.locfileid: "74818384"
---
## <a name="prerequisites"></a>Предварительные требования

Перед началом работы нужно сделать следующее:

> [!div class="checklist"]
> * [Создать ресурс службы "Речь" Azure.](../../../../get-started.md)
> * [Настроить среду разработки.](../../../../quickstarts/setup-platform.md?tabs=windows)
> * [Создать пустой пример проекта.](../../../../quickstarts/create-project.md?tabs=windows)

## <a name="add-sample-code"></a>Добавление примеров кода

1. Откройте исходный файл **helloworld.cpp**.

1. Замените все содержимое следующим фрагментом кода:

   [!code-cpp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/cpp/windows/text-to-speech/helloworld/helloworld.cpp#code)]

1. В том же файле замените строку `YourSubscriptionKey` своим ключом подписки.

1. Замените строку `YourServiceRegion` значением [региона](~/articles/cognitive-services/Speech-Service/regions.md), связанного с подпиской (например, `westus` для бесплатной пробной подписки).

1. В строке меню выберите **Файл** > **Сохранить все**.

## <a name="build-and-run-the-application"></a>Создание и запуск приложения

1. В строке меню выберите **Сборка** > **Построить решение**, чтобы создать приложение. Теперь код должен компилироваться без ошибок.

1. Выберите **Отладка** > **Начать отладку**(или нажмите клавишу **F5**), чтобы запустить приложение **helloworld**.

1. Введите фразу или предложение на английском языке. Приложение передает текст в службу "Речь", которая отправляет синтезированную речь в приложение для воспроизведения динамиком.

   ![Выходные данные в консоли после успешного синтеза речи](~/articles/cognitive-services/Speech-Service/media/sdk/qs-tts-cpp-windows-console-output.png)

## <a name="next-steps"></a>Дополнительная информация

[!INCLUDE [footer](./footer.md)]

## <a name="see-also"></a>См. также

- [Создание пользовательского голоса](~/articles/cognitive-services/Speech-Service/how-to-custom-voice-create-voice.md)
- [Запись пользовательских образцов голоса](~/articles/cognitive-services/Speech-Service/record-custom-voice-samples.md)
