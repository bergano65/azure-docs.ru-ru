---
title: Краткое руководство. Распознавание речи с помощью C++ (Windows) в службе "Речь"
titleSuffix: Azure Cognitive Services
description: Узнайте, как распознавать речь в Windows, используя C++ и пакет SDK службы "Речь"
services: cognitive-services
author: wolfma61
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 07/05/2019
ms.author: wolfma
ms.openlocfilehash: c795f1581ae36f100065c39cd47bc4efc564b9fe
ms.sourcegitcommit: 6cff17b02b65388ac90ef3757bf04c6d8ed3db03
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/29/2019
ms.locfileid: "68607885"
---
# <a name="quickstart-recognize-speech-in-c-on-windows-by-using-the-speech-sdk"></a>Краткое руководство. Распознавание речи в классическом приложении C++ для Windows с помощью пакета SDK службы "Речь"

Также доступны краткие руководства по [преобразованию текста в речь](quickstart-text-to-speech-cpp-windows.md) и [переводу речи](quickstart-translate-speech-cpp-windows.md).

При необходимости переключитесь на другой язык программирования и (или) среду:<br/>
[!INCLUDE [Selector](../../../includes/cognitive-services-speech-service-quickstart-selector.md)]

В этой статье вы создадите консольное приложение C++ для Windows. Вы будете использовать [пакет SDK для службы "Речь"](speech-sdk.md) в Cognitive Services, чтобы преобразовать речь с микрофона вашего ПК в текст в реальном времени. Приложение создается с помощью [пакета SDK службы "Речь" для NuGet](https://aka.ms/csspeech/nuget) и Microsoft Visual Studio версии 2017 и выше (любого выпуска).

## <a name="prerequisites"></a>Предварительные требования

Для работы с этим кратким руководством вам потребуется ключ подписки службы "Речь". Его можно получить бесплатно. Дополнительные сведения см. в статье [Бесплатная пробная подписка на службу "Речь"](get-started.md).

## <a name="create-a-visual-studio-project"></a>Создание проекта Visual Studio

[!INCLUDE [Quickstart C++ project](../../../includes/cognitive-services-speech-service-quickstart-cpp-create-proj.md)]

## <a name="add-sample-code"></a>Добавление примеров кода

1. Откройте исходный файл *helloworld.cpp*. Замените весь код ниже первого оператора включения (`#include "stdafx.h"` или `#include "pch.h"`) следующим кодом.

   [!code-cpp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/cpp-windows/helloworld/helloworld.cpp#code)]

1. В том же файле замените строку `YourSubscriptionKey` своим ключом подписки.

1. Замените строку `YourServiceRegion` значением [региона](regions.md), связанного с подпиской (например, `westus` для бесплатной пробной подписки).

1. Сохраните внесенные в проект изменения.

## <a name="build-and-run-the-app"></a>Создание и запуск приложения

1. Создайте приложение. В строке меню последовательно выберите **Сборка** > **Собрать решение**. Теперь код должен компилироваться без ошибок.

   ![Снимок экрана приложения Visual Studio с выделенным параметром "Собрать решение"](media/sdk/qs-cpp-windows-06-build.png)

1. Запустите приложение. В строке меню последовательно выберите **Отладка** > **Начать отладку** или нажмите клавишу **F5**.

   ![Снимок экрана приложения Visual Studio с выделенным параметром "Начать отладку"](media/sdk/qs-cpp-windows-07-start-debugging.png)

1. Появится всплывающее окно консоли с запросом произнести что-либо. Произнесите фразу или предложение на английском языке. Ваша речь передается в службу "Речь" и преобразовывается в текст, который появляется в том же окне.

   ![Снимок экрана выходных данных консоли после успешного распознавания](media/sdk/qs-cpp-windows-08-console-output-release.png)

## <a name="next-steps"></a>Дополнительная информация

Дополнительные примеры, такие как чтение речи из аудиофайла, доступны на сайте GitHub.

> [!div class="nextstepaction"]
> [Ознакомьтесь с примерами на C++ на сайте GitHub](https://aka.ms/csspeech/samples)

## <a name="see-also"></a>См. также

- [Настройка акустических моделей](how-to-customize-acoustic-models.md)
- [Настройка языковых моделей](how-to-customize-language-model.md)
