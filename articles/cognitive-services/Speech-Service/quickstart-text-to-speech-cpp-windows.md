---
title: Краткое руководство. Синтезирование речи с помощью C++ (Windows) в службах "Речь"
titleSuffix: Azure Cognitive Services
description: Из этой статьи вы узнаете, как синтезировать речь в Windows, используя C++ и пакет SDK для служб "Речь"
services: cognitive-services
author: yinhew
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 4/03/2019
ms.author: yinhew
ms.openlocfilehash: a9613f0839735457cf172fdacb0ef6a943ca27de
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/02/2019
ms.locfileid: "65020801"
---
# <a name="quickstart-synthesize-speech-in-c-on-windows-by-using-the-speech-sdk"></a>Краткое руководство. Синтезирование речи в приложении C++ для Windows с помощью пакета SDK для служб "Речь"

В этой статье вы создадите консольное приложение C++ для Windows. Вы можете использовать [пакет SDK для служб "Речь"](speech-sdk.md) в Cognitive Services для синтезирования речи из текста в реальном времени и ее воспроизведения через динамики компьютера. Приложение создается с использованием [пакета SDK NuGet для службы "Речь"](https://aka.ms/csspeech/nuget) и Microsoft Visual Studio 2017 (любого выпуска).

Компонент, описанный в этой статье, доступен в [пакете SDK 1.5.0 для службы "Речь"](https://www.nuget.org/packages/Microsoft.CognitiveServices.Speech/1.5.0).

Полный список языков и голосов для синтезирования речи см. в статье, посвященной [поддержке языков](language-support.md#text-to-speech).

## <a name="prerequisites"></a>Предварительные требования

Для работы с этим кратким руководством вам потребуется ключ подписки службы "Речь". Его можно получить бесплатно. Дополнительные сведения см. в статье [Бесплатная пробная подписка на службу "Речь"](get-started.md).

## <a name="create-a-visual-studio-project"></a>Создание проекта Visual Studio

[!INCLUDE [Quickstart C++ project](../../../includes/cognitive-services-speech-service-quickstart-cpp-create-proj.md)]

## <a name="add-sample-code"></a>Добавление примеров кода

1. Откройте исходный файл *helloworld.cpp*. Замените весь код ниже первого оператора включения (`#include "stdafx.h"` или `#include "pch.h"`) следующим кодом.

   [!code-cpp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/text-to-speech/cpp-windows/helloworld/helloworld.cpp#code)]

1. В том же файле замените строку `YourSubscriptionKey` своим ключом подписки.

1. Замените строку `YourServiceRegion` значением [региона](regions.md), связанного с подпиской (например, `westus` для бесплатной пробной подписки).

1. Сохраните внесенные в проект изменения.

## <a name="build-and-run-the-app"></a>Создание и запуск приложения

1. Создайте приложение. В строке меню последовательно выберите **Сборка** > **Собрать решение**. Теперь код должен компилироваться без ошибок.

   ![Снимок экрана приложения Visual Studio с выделенным параметром "Собрать решение"](media/sdk/qs-cpp-windows-06-build.png)

1. Запустите приложение. В строке меню последовательно выберите **Отладка** > **Начать отладку** или нажмите клавишу **F5**.

   ![Снимок экрана приложения Visual Studio с выделенным параметром "Начать отладку"](media/sdk/qs-cpp-windows-07-start-debugging.png)

1. Появится всплывающее окно консоли с запросом ввести текст. Введите несколько слов или предложение. Введенный текст передается в службы "Речь" и синтезируется в речь, которая воспроизводится через динамик.

   ![Снимок экрана с выходными данными в консоли после успешного синтезирования](media/sdk/qs-tts-cpp-windows-console-output.png)

## <a name="next-steps"></a>Дополнительная информация

Дополнительные примеры, например по сохранению речи в аудиофайл, доступны на сайте GitHub.

> [!div class="nextstepaction"]
> [Примеры для C++ на сайте GitHub](https://aka.ms/csspeech/samples)

## <a name="see-also"></a>См. также

- [Настройка голосов](how-to-customize-voice-font.md)
- [Запись образцов голосов](record-custom-voice-samples.md)
