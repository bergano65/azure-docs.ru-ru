---
title: Краткое руководство. Синтезирование речи с записью в звуковой файл с помощью C# (.NET) в службе "Речь"
titleSuffix: Azure Cognitive Services
description: ПОДЛЕЖИТ УТОЧНЕНИЮ
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 10/28/2019
ms.author: erhopf
ms.openlocfilehash: 24f44d02170866645711cb302453a009d2935fbf
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/04/2019
ms.locfileid: "73505102"
---
## <a name="prerequisites"></a>Предварительные требования

Перед началом работы нужно сделать следующее:

> [!div class="checklist"]
> * [Создать ресурс службы "Речь" Azure](../../../../get-started.md).
> * [Настроить среду разработки](../../../../quickstarts/setup-platform.md?tabs=dotnet).
> * [Создать пустой пример проекта](../../../../quickstarts/create-project.md?tabs=dotnet).

## <a name="open-your-project-in-visual-studio"></a>Откройте проект в Visual Studio.

Сначала необходимо убедиться, что проект открыт в Visual Studio.

1. Запустите Visual Studio 2019.
2. Загрузите проект и откройте `Program.cs`.

## <a name="start-with-some-boilerplate-code"></a>Использование стандартного кода

Добавим код, который выступает в качестве основы для нашего проекта. Имейте в виду, что вы создали асинхронный метод `SynthesisToAudioFileAsync()`.

````C#

using System;
using System.Threading.Tasks;
using Microsoft.CognitiveServices.Speech;

namespace helloworld
{
    class Program
    {
        public static async Task SynthesisToAudioFileAsync()
        {
        }

        static void Main()
        {
            SynthesisToAudioFileAsync().Wait();
        }
    }
}

````

## <a name="create-a-speech-configuration"></a>Создание конфигурации службы "Речь"

Перед инициализацией объекта `SpeechSynthesizer` необходимо создать конфигурацию, использующую ключ и регион подписки. Вставьте код в метод `SynthesisToAudioFileAsync()`.

````C#
var config = SpeechConfig.FromSubscription("YourSubscriptionKey", "YourServiceRegion");
````

## <a name="create-an-audio-configuration"></a>Создание конфигурации звука

Теперь необходимо создать объект ````AudioConfig````, указывающий на звуковой файл. Этот объект создается внутри оператора using для обеспечения надлежащего выпуска неуправляемых ресурсов. Вставьте этот код в метод `SynthesisToAudioFileAsync()` непосредственно под конфигурацией службы "Речь".

````C#
var fileName = "helloworld.wav";
using (var fileOutput = AudioConfig.FromWavFileOutput(fileName))
{
}
````

## <a name="initialize-a-speechsynthesizer"></a>Инициализация SpeechSynthesizer

Теперь создадим объект `SpeechSynthesizer` с помощью созданных ранее объектов `SpeechConfig` и `AudioConfig`. Этот объект также создается внутри оператора using для обеспечения надлежащего выпуска неуправляемых ресурсов. Вставьте этот код в метод `SynthesisToAudioFileAsync()` внутри оператора using, который создает оболочку для объекта ````AudioConfig````.

````C#
using (var synthesizer = new SpeechSynthesizer(config, fileOutput))
{
}
````

## <a name="synthesize-text-using-speaktextasync"></a>Синтезирование текста с помощью SpeakTextAsync

В объекте `SpeechSynthesizer` необходимо вызвать метод `SpeakTextAsync()`. Этот метод отправляет текст в службу "Речь", которая преобразует его в звук. `SpeechSynthesizer` будет использовать голос по умолчанию, если параметр `config.VoiceName` не указан явно.

В операторе using добавьте этот код:
````C#
var text = "Hello world!";
var result = await synthesizer.SpeakTextAsync(text);
````

## <a name="check-for-errors"></a>Проверка на наличие ошибок

Когда служба "Речь" возвращает результат синтеза, следует убедиться, что текст успешно синтезирован.

В операторе using под `SpeakTextAsync()` добавьте этот код:
````C#
if (result.Reason == ResultReason.SynthesizingAudioCompleted)
{
    Console.WriteLine($"Speech synthesized to [{fileName}] for text [{text}]");
}
else if (result.Reason == ResultReason.Canceled)
{
    var cancellation = SpeechSynthesisCancellationDetails.FromResult(result);
    Console.WriteLine($"CANCELED: Reason={cancellation.Reason}");

    if (cancellation.Reason == CancellationReason.Error)
    {
        Console.WriteLine($"CANCELED: ErrorCode={cancellation.ErrorCode}");
        Console.WriteLine($"CANCELED: ErrorDetails=[{cancellation.ErrorDetails}]");
        Console.WriteLine($"CANCELED: Did you update the subscription info?");
    }
}
````

## <a name="check-your-code"></a>Проверка кода

На этом этапе код должен выглядеть так:

````C#
//
// Copyright (c) Microsoft. All rights reserved.
// Licensed under the MIT license. See LICENSE.md file in the project root for full license information.
//

using System;
using System.Threading.Tasks;
using Microsoft.CognitiveServices.Speech;

namespace helloworld
{
    class Program
    {
        public static async Task SynthesisToAudioFileAsync()
        {
            var config = SpeechConfig.FromSubscription("YourSubscriptionKey", "YourServiceRegion");

            var fileName = "helloworld.wav";
            using (var fileOutput = AudioConfig.FromWavFileOutput(fileName))
            {
                using (var synthesizer = new SpeechSynthesizer(config, fileOutput))
                {
                    var text = "Hello world!";
                    var result = await synthesizer.SpeakTextAsync(text);

                    if (result.Reason == ResultReason.SynthesizingAudioCompleted)
                    {
                        Console.WriteLine($"Speech synthesized to [{fileName}] for text [{text}]");
                    }
                    else if (result.Reason == ResultReason.Canceled)
                    {
                        var cancellation = SpeechSynthesisCancellationDetails.FromResult(result);
                        Console.WriteLine($"CANCELED: Reason={cancellation.Reason}");

                        if (cancellation.Reason == CancellationReason.Error)
                        {
                            Console.WriteLine($"CANCELED: ErrorCode={cancellation.ErrorCode}");
                            Console.WriteLine($"CANCELED: ErrorDetails=[{cancellation.ErrorDetails}]");
                            Console.WriteLine($"CANCELED: Did you update the subscription info?");
                        }
                    }
                }
            }
        }

        static void Main()
        {
            SynthesisToAudioFileAsync().Wait();
        }
    }
}
````

## <a name="build-and-run-your-app"></a>Создание и запуск приложения

Теперь можно приступать к созданию приложения и проверке синтеза речи, используя службу "Речь".

1. **Скомпилируйте код**. В строке меню Visual Studio последовательно выберите **Сборка** > **Собрать решение**.
2. **Запустите приложение**. В строке меню выберите **Отладка** > **Начать отладку** или нажмите клавишу **F5**.
3. **Начните синтез**. Текст преобразуется в речь и сохраняется в указанном звуковом файле.

   ```text
   Speech synthesized to [helloworld.wav] for text [Hello world!]
   ```

## <a name="next-steps"></a>Дополнительная информация

[!INCLUDE [footer](./footer.md)]

## <a name="see-also"></a>См. также

- [Создание пользовательского голоса](~/articles/cognitive-services/Speech-Service/how-to-custom-voice-create-voice.md)
- [Запись пользовательских образцов голоса](~/articles/cognitive-services/Speech-Service/record-custom-voice-samples.md)
