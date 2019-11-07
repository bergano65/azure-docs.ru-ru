---
title: Краткое руководство. Распознавание речи с микрофона в службе "Речь" с помощью C++ (Windows)
titleSuffix: Azure Cognitive Services
description: Узнайте, как распознавать речь в Windows, используя C++ и пакет SDK службы "Речь"
services: cognitive-services
author: wolfma61
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 08/19/2019
ms.author: wolfma
ms.openlocfilehash: 1a08a5413ae41ef8c17e817a01be9e9efcb7ff99
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/04/2019
ms.locfileid: "73505718"
---
## <a name="prerequisites"></a>Предварительные требования

Перед началом работы нужно:

> [!div class="checklist"]
> * [Создать ресурс службы "Речь" Azure](../../../../get-started.md).
> * [Настроить среду разработки](../../../../quickstarts/setup-platform.md?tabs=windows).
> * [Создать пустой пример проекта](../../../../quickstarts/create-project.md?tabs=windows).

## <a name="add-sample-code"></a>Добавление примеров кода

1. Откройте исходный файл **helloworld.cpp**.

1. Замените все содержимое следующим фрагментом кода:

   [!code-cpp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/cpp/windows/from-microphone/helloworld/helloworld.cpp#code)]

1. В том же файле замените строку `YourSubscriptionKey` своим ключом подписки.

1. Замените строку `YourServiceRegion` значением [региона](~/articles/cognitive-services/Speech-Service/regions.md), связанного с подпиской (например, `westus` для бесплатной пробной подписки).

1. В строке меню выберите **Файл** > **Сохранить все**.

## <a name="build-and-run-the-application"></a>Создание и запуск приложения

1. В строке меню выберите **Сборка** > **Построить решение**, чтобы создать приложение. Теперь код должен компилироваться без ошибок.

1. Выберите **Отладка** > **Начать отладку** (или нажмите клавишу **F5**), чтобы запустить приложение **helloworld**.

1. Произнесите фразу или предложение на английском языке. Приложение передает речь в речевую службу, которая расшифровывает речь в текст и отправляет его обратно в приложение для отображения.

   ![Вывод на консоль после успешного распознавания](~/articles/cognitive-services/Speech-Service/media/sdk/qs-cpp-windows-08-console-output-release.png)

## <a name="next-steps"></a>Дополнительная информация

[!INCLUDE [footer](./footer.md)]