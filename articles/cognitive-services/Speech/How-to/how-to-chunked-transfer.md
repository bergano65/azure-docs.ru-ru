---
title: Как использовать блоки при передаче аудиопотока | Документация Майкрософт
titlesuffix: Azure Cognitive Services
description: Использование блочной передачи аудиопотока в службу "Распознавание речи Bing".
services: cognitive-services
author: zhouwangzw
manager: wolfma
ms.service: cognitive-services
ms.subservice: bing-speech
ms.topic: article
ms.date: 09/18/2018
ms.author: zhouwang
ms.openlocfilehash: 82c07332af7d4a5df4a6af15ef65abcd8a00f603
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/29/2019
ms.locfileid: "55216886"
---
# <a name="chunked-transfer-encoding"></a>Кодирование блочной передачи

[!INCLUDE [Deprecation note](../../../../includes/cognitive-services-bing-speech-api-deprecation-note.md)]

Для преобразования речи в текст API распознавания речи Microsoft позволяет отправить аудио как один сплошной блок или разделить его на небольшие фрагменты. Для эффективной потоковой передачи аудио и сокращения задержки транскрибирования рекомендуется использовать [кодирование блочной передачи](https://en.wikipedia.org/wiki/Chunked_transfer_encoding) для передачи аудиозаписи в службу. Другие реализации могут привести к более высокой задержке, которую заметит пользователь. Дополнительные сведения о требованиях см. на странице [Аудиопотоки](../concepts.md#audio-streams).

> [!NOTE]
> Вы не можете загружать аудио длительностью более чем 10 секунд в одном запросе, а общая длительность запроса не может превышать 14 секунд.
> [!NOTE]
> Необходимо указать кодирование блочной передачи только в том случае, если для вызова службы распознавания речи вы используете [REST API](../GetStarted/GetStartedREST.md). В приложениях, которые используют [клиентские библиотеки](../GetStarted/GetStartedClientLibraries.md), не нужно настраивать кодировку блочной передачи.

Ниже показано, как можно задать кодировку блочной передачи и передать звуковой файл, разделенный на 1024-байтовых фрагмента.

```cs

HttpWebRequest request = null;
request = (HttpWebRequest)HttpWebRequest.Create(requestUri);
request.SendChunked = true;
request.Accept = @"application/json;text/xml";
request.Method = "POST";
request.ProtocolVersion = HttpVersion.Version11;
request.Host = @"speech.platform.bing.com";
request.ContentType = @"audio/wav; codec=audio/pcm; samplerate=16000";
request.Headers["Ocp-Apim-Subscription-Key"] = "YOUR_SUBSCRIPTION_KEY";

using (fs = new FileStream(audioFile, FileMode.Open, FileAccess.Read))
{

    /*
    * Open a request stream and write 1024 byte chunks in the stream one at a time.
    */
    byte[] buffer = null;
    int bytesRead = 0;
    using (Stream requestStream = request.GetRequestStream())
    {
        /*
        * Read 1024 raw bytes from the input audio file.
        */
        buffer = new Byte[checked((uint)Math.Min(1024, (int)fs.Length))];
        while ((bytesRead = fs.Read(buffer, 0, buffer.Length)) != 0)
        {
            requestStream.Write(buffer, 0, bytesRead);
        }

        // Flush
        requestStream.Flush();
    }
}
```
