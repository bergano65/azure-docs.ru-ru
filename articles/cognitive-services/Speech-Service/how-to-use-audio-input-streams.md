---
title: Принцип работы входного аудиопотока в пакете SDK для службы "Речь"
titleSuffix: Azure Cognitive Services
description: Обзор возможностей API входного аудиопотока в пакете SDK для службы "Речь".
services: cognitive-services
author: fmegen
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: fmegen
ms.openlocfilehash: 3039276a49e7bb41660d114e78ca047a3f77f279
ms.sourcegitcommit: 598c5a280a002036b1a76aa6712f79d30110b98d
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/15/2019
ms.locfileid: "74109934"
---
# <a name="about-the-speech-sdk-audio-input-stream-api"></a>Об API входного аудиопотока в пакете SDK для службы "Речь"

API **потока входных данных** РЕЧЕВОГО пакета SDK предоставляет способ потоковой передачи в распознаватели вместо использования микрофона или API-интерфейсов входного файла.

При использовании входных аудиопотоков выполняется следующая процедура.

- Определите формат аудиопотока. Формат должен поддерживаться пакетом SDK для службы "Речь" и службой "Речь". Сейчас поддерживается только приведенная ниже конфигурация.

  Образцы звука в формате PCM с одним каналом, 16 000 выборок в секунду, 32 000 байтов в секунду; выравнивание по двум блокам (16 битов, включая заполнение для выборки), на образец используется 16 битов.

  Соответствующий код в пакете SDK для создания звукового формата выглядит следующим образом.

  ```csharp
  byte channels = 1;
  byte bitsPerSample = 16;
  int samplesPerSecond = 16000;
  var audioFormat = AudioStreamFormat.GetWaveFormatPCM(samplesPerSecond, bitsPerSample, channels);
  ```

- Убедитесь, что ваш код может предоставлять необработанные аудиоданные, соответствующие указанным выше характеристикам. Если источник аудиоданных не соответствует поддерживаемым форматам, звук необходимо перекодировать в требуемый формат.

- Создайте собственный класс входного аудиопотока, производный от `PullAudioInputStreamCallback`. Реализуйте члены `Read()` и `Close()`. Точная сигнатура функции зависит от языка, однако код будет выглядеть аналогично этому примеру кода.

  ```csharp
   public class ContosoAudioStream : PullAudioInputStreamCallback {
      ContosoConfig config;

      public ContosoAudioStream(const ContosoConfig& config) {
          this.config = config;
      }

      public int Read(byte[] buffer, uint size) {
          // returns audio data to the caller.
          // e.g. return read(config.YYY, buffer, size);
      }

      public void Close() {
          // close and cleanup resources.
      }
   };
  ```

- Создайте конфигурацию звука, основанную на формате звука и входном аудиопотоке. Передайте конфигурацию для обычной речи и конфигурацию ввода звука при создании распознавателя. Например,

  ```csharp
  var audioConfig = AudioConfig.FromStreamInput(new ContosoAudioStream(config), audioFormat);

  var speechConfig = SpeechConfig.FromSubscription(...);
  var recognizer = new SpeechRecognizer(speechConfig, audioConfig);

  // run stream through recognizer
  var result = await recognizer.RecognizeOnceAsync();

  var text = result.GetText();
  ```

## <a name="next-steps"></a>Дополнительная информация

- [Получение пробной версии службы "Речь"](https://azure.microsoft.com/try/cognitive-services/)
- [Распознавание речи в C#](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-csharp&tabs=dotnet)
