---
title: Краткое руководство. Перевод речи с помощью C++ (Windows) в службе "Речь"
titleSuffix: Azure Cognitive Services
description: При работе с этим кратким руководством вы создадите простое приложение C++ для записи речи пользователя, ее перевода на другой язык и вывода текста в командную строку. Это руководство предназначено для пользователей Windows.
services: cognitive-services
author: wolfma61
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 03/13/2019
ms.author: erhopf
ms.openlocfilehash: f7120e80d079723ed8265320ba4b38d76a825a00
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/18/2019
ms.locfileid: "59499824"
---
# <a name="quickstart-translate-speech-with-the-speech-sdk-for-c"></a>Краткое руководство. Перевод речи с помощью пакета SDK службы "Речь" для C++

При работе с этим кратким руководством вы создадите простое приложение C++, которое записывает речь пользователя с микрофона компьютера, переводит речь и транскрибирует переведенный текст в командную строку в реальном времени. Приложение предназначено для работы в 64-разрядной версии Windows с использованием [пакета NuGet SDK службы "Речь"](https://aka.ms/csspeech/nuget) и Microsoft Visual Studio 2017.

Полный список языков для перевода речи см. в статье, посвященной [поддержке языков](language-support.md).

## <a name="prerequisites"></a>Предварительные требования

Для работы с этим кратким руководством вам понадобится:

* [Visual Studio 2017](https://visualstudio.microsoft.com/downloads/)
* Ключ подписки Azure для службы "Речь". [Его можно получить бесплатно](get-started.md).

## <a name="create-a-visual-studio-project"></a>Создание проекта Visual Studio

[!INCLUDE[](../../../includes/cognitive-services-speech-service-quickstart-cpp-create-proj.md)]

## <a name="add-sample-code"></a>Добавление примеров кода

1. Откройте исходный файл *helloworld.cpp*. Замените весь код ниже первого оператора включения (`#include "stdafx.h"` или `#include "pch.h"`) следующим кодом.

    [!code-cpp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/speech-translation/cpp-windows/helloworld/helloworld.cpp#code)]

1. В том же файле замените строку `YourSubscriptionKey` своим ключом подписки.

1. Замените строку `YourServiceRegion` значением [региона](regions.md), связанного с подпиской (например, `westus` для бесплатной пробной подписки).

1. Сохраните внесенные в проект изменения.

## <a name="build-and-run-the-app"></a>Создание и запуск приложения

1. Создайте приложение. В строке меню последовательно выберите **Сборка** > **Собрать решение**. Теперь код должен компилироваться без ошибок.

   ![Снимок экрана приложения Visual Studio с выделенным параметром "Собрать решение"](media/sdk/qs-cpp-windows-06-build.png)

1. Запустите приложение. В строке меню последовательно выберите **Отладка** > **Начать отладку** или нажмите клавишу **F5**.

   ![Снимок экрана приложения Visual Studio с выделенным параметром "Начать отладку"](media/sdk/qs-cpp-windows-07-start-debugging.png)

1. Появится всплывающее окно консоли с запросом произнести что-либо. Произнесите фразу или предложение на английском языке. Ваша речь передается в службу "Речь", переводится и транскрибируется в текст, который появляется в том же окне.

   ![Снимок экрана: выходные данные в консоли после успешного перевода](media/sdk/qs-translate-cpp-windows-output.png)

## <a name="next-steps"></a>Дополнительная информация

Дополнительные примеры, например чтение речи из звукового файла и вывод переведенного текста в виде синтезированной речи, доступны на сайте GitHub.

> [!div class="nextstepaction"]
> [Примеры для C++ на сайте GitHub](https://aka.ms/csspeech/samples)

## <a name="see-also"></a>См. также

- [Настройка акустических моделей](how-to-customize-acoustic-models.md)
- [Настройка языковых моделей](how-to-customize-language-model.md)
