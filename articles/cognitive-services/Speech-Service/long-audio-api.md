---
title: Длинный аудио API (Предварительная версия) — служба речи
titleSuffix: Azure Cognitive Services
description: ''
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 10/26/2019
ms.author: erhopf
ms.openlocfilehash: ed00a9df46660cc6bfb4ec5fd9a93c80f5d6653e
ms.sourcegitcommit: 6c01e4f82e19f9e423c3aaeaf801a29a517e97a0
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/04/2019
ms.locfileid: "74815332"
---
# <a name="long-audio-api-preview"></a>Длинный аудио API (Предварительная версия)

Длинный аудио API предназначен для асинхронного синтеза текста длинной формы в речь (например, для звуковых книг). Этот API не возвращает синтезированный звук в режиме реального времени, а предполагает, что вы запрашиваете ответ и используете выходные данные, так как они становятся доступными из службы. В отличие от API преобразования текста в речь, используемого речевым пакетом SDK, длинный аудио API может создавать синтезированный звук дольше 10 минут, что делает его идеальным для издателей и платформ звуковых данных.

Дополнительные преимущества для длинного аудио API:

* Синтезированный речевой ввод, возвращенный службой, использует нейронные голоса, что обеспечивает высокую точность вывода звука.
* Так как ответы в реальном времени не поддерживаются, нет необходимости развертывать конечную точку голоса.

> [!NOTE]
> Длинный аудио API теперь поддерживает только [Пользовательский нейронный голос](https://docs.microsoft.com/azure/cognitive-services/speech-service/how-to-custom-voice#custom-neural-voices).

## <a name="workflow"></a>Рабочий процесс

Как правило, при использовании длинного аудио API вы отправляете текстовый файл или файлы для синтезирования, опрашиваете состояние, а затем, если состояние прошло успешно, можно загрузить выходные данные звука.

На этой схеме представлен общий обзор рабочего процесса.

![Схема рабочего потока для длинного звука API](media/long-audio-api/long-audio-api-workflow.png)

## <a name="prepare-content-for-synthesis"></a>Подготовка содержимого для синтеза

При подготовке текстового файла убедитесь, что он:

* Является обычным текстом (txt) или SSML Text (. txt)
  * Для обычного текста каждый абзац отделяется нажатием клавиши **Ввод/возврат** -просмотр. [пример входного текста с открытым текстом](https://github.com/Azure-Samples/Cognitive-Speech-TTS/blob/master/CustomVoice-API-Samples/Java/en-US.txt)
  * Для текста SSML каждая часть SSML считается абзацом. SSML части должны быть разделены с помощью различных абзацев. [Пример текстового ввода](https://github.com/Azure-Samples/Cognitive-Speech-TTS/blob/master/CustomVoice-API-Samples/Java/SSMLTextInputSample.txt)для просмотра SSML. Сведения о коде языка см. в разделе [язык разметки речи (SSML)](speech-synthesis-markup.md) .
* Кодируется как [UTF-8 с меткой порядка байтов (BOM)](https://www.w3.org/International/questions/qa-utf8-bom.en#bom)
* Содержит более 10 000 символов или более 50 абзацев
* Является отдельным файлом, а не ZIP

## <a name="audio-output-formats"></a>Форматы вывода звука

API длинного звука поддерживает следующие форматы вывода звука:

> [!NOTE]
> Формат аудио по умолчанию — Metallica-16khz-16-разрядный-Mono-PCM.

* Metallica-8khz-16-разрядный-Mono-PCM
* Metallica-16khz-16-разрядный-Mono-PCM
* Metallica-24khz-16-разрядный-Mono-PCM
* Metallica-48khz-16-разрядный-Mono-PCM
* Audio-16khz-32kbitrate-Mono-MP3
* Audio-16khz-64kbitrate-Mono-MP3
* Audio-16khz-128kbitrate-Mono-MP3
* Audio-24khz-48kbitrate-Mono-MP3
* Audio-24khz-96kbitrate-Mono-MP3
* Audio-24khz-160kbitrate-Mono-MP3

## <a name="quickstarts"></a>Краткие руководства

Мы предлагаем самые краткие руководства, которые помогут вам успешно запустить длинный аудио API. В этой таблице содержится список длинных кратких руководств по API аудио, упорядоченных по языку.

* [Краткое руководство. Python](https://aka.ms/long-audio-python)

## <a name="sample-code"></a>Пример кода
Пример кода для длинного аудио API доступен на сайте GitHub.

* [Пример кода: Python](https://github.com/Azure-Samples/Cognitive-Speech-TTS/tree/master/CustomVoice-API-Samples/Python)
* [Пример кода:C#](https://github.com/Azure-Samples/Cognitive-Speech-TTS/tree/master/CustomVoice-API-Samples/CSharp)
* [Пример кода: Java](https://github.com/Azure-Samples/Cognitive-Speech-TTS/blob/master/CustomVoice-API-Samples/Java/)
