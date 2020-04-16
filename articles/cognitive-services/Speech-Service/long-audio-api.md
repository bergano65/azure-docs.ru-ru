---
title: Длинный аудио API (Предварительный просмотр) - Служба речи
titleSuffix: Azure Cognitive Services
description: Узнайте, как Long Audio API предназначен для асинхронного синтеза длинноформатного текста в речи.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 01/30/2020
ms.author: trbye
ms.openlocfilehash: b7cca314ec59e46cf17751b1aec28b5c3ea029ed
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81401061"
---
# <a name="long-audio-api-preview"></a>Длинный аудио API (Предварительный просмотр)

Длинный аудио API предназначен для асинхронного синтеза длинноформатного текста в речи (например, аудиокниги). Этот API не возвращает синтезированный звук в режиме реального времени, вместо этого ожидается, что вы будете опрос для ответа (ы) и потреблять выход (ы), как они доступны из службы. В отличие от текста к речевому API, используемому Speech SDK, Long Audio API может создавать синтезированный аудио дольше 10 минут, что делает его идеальным для издателей и платформ аудиоконтента.

Дополнительные преимущества API long Audio:

* Синтезированная речь, возвращенная службой, использует нейронные голоса, что обеспечивает высокую точность аудиовыходов.
* Поскольку ответы в режиме реального времени не поддерживаются, нет необходимости развертывать конечную точку голоса.

> [!NOTE]
> Длинный аудио API теперь поддерживает только [пользовательский нейронный голос.](https://docs.microsoft.com/azure/cognitive-services/speech-service/how-to-custom-voice#custom-neural-voices)

## <a name="workflow"></a>Рабочий процесс

Как правило, при использовании Long Audio API вы отправляете текстовый файл или файлы для синтеза, опрашивайте состояние, затем, если статус успешен, можно загрузить аудиовывод.

Эта диаграмма обеспечивает обзор рабочего процесса на высоком уровне.

![Диаграмма рабочего процесса длинного аудио-API](media/long-audio-api/long-audio-api-workflow.png)

## <a name="prepare-content-for-synthesis"></a>Подготовка контента для синтеза

При подготовке текстового файла убедитесь, что он:

* Является либо простой текст (.txt) или SSML текст (.txt)
* Закодирован как [UTF-8 с отметкой заказа Байта (BOM)](https://www.w3.org/International/questions/qa-utf8-bom.en#bom)
* Является ли один файл, а не молния
* Содержит более 400 символов для простого текста или 400 [оплачиваемых символов](https://docs.microsoft.com/azure/cognitive-services/speech-service/text-to-speech#pricing-note) для текста SSML и менее 10 000 абзацев
  * Для простого текста, каждый абзац отделяется, нажав **Enter/Return** - Просмотр [простого примера ввода текста](https://github.com/Azure-Samples/Cognitive-Speech-TTS/blob/master/CustomVoice-API-Samples/Java/en-US.txt)
  * Для текста SSML каждая часть SSML считается абзацем. Части SSML должны быть разделены различными абзацами - Посмотреть [пример ввода текста SSML](https://github.com/Azure-Samples/Cognitive-Speech-TTS/blob/master/CustomVoice-API-Samples/Java/SSMLTextInputSample.txt)
> [!NOTE]
> Для китайцев (материка), китайского (Гонконг), китайский (Тайвань), японский и корейский, одно слово будет засчитано как два символа. 

## <a name="submit-synthesis-requests"></a>Отправка запросов на синтез

После подготовки входной контента, следуйте [длинной форме аудио синтеза quickstart](https://aka.ms/long-audio-python) представить запрос. Если у вас есть несколько файлов ввода, вам нужно будет отправить несколько запросов. Есть некоторые ограничения, чтобы быть в курсе: 
* Клиенту разрешается отправлять до 5 запросов на сервер в секунду для каждой учетной записи подписки Azure. Если он превышает ограничение, клиент получит код ошибки 429 (слишком много запросов). Пожалуйста, уменьшите сумму запроса в секунду
* Серверу разрешено запускать и стоять в очереди до 120 запросов на каждую учетную запись подписки Azure. Если он превышает ограничение, сервер вернет код ошибки 429 (слишком много запросов). Пожалуйста, подождите и избегайте отправки нового запроса до тех пор, пока не будут выполнены некоторые запросы
* Сервер будет хранить до 20 000 запросов на каждую учетную запись подписки Azure. Если он превышает ограничение, пожалуйста, удалите некоторые запросы перед отправкой новых

## <a name="audio-output-formats"></a>Форматы аудиовыхода

Мы поддерживаем гибкие форматы аудиовывода. Можно создавать аудиовыводы на абзаце или совмещеть аудио в один выход, установив параметр 'concatenateResult'. Следующие форматы аудиовывода поддерживаются Длинным аудиоAPI:

> [!NOTE]
> Аудиоформат по умолчанию - riff-16khz-16bit-mono-pcm.

* рифф-8khz-16bit-моно-пкм
* рифф-16хз-16бит-моно-пкм
* рифф-24khz-16bit-моно-пкм
* рифф-48khz-16bit-моно-пкм
* аудио-16khz-32kbitrate-моно-mp3
* аудио-16khz-64kbitrate-моно-mp3
* аудио-16khz-128kbitrate-моно-mp3
* аудио-24khz-48kbitrate-моно-mp3
* аудио-24khz-96kbitrate-моно-mp3
* аудио-24khz-160kbitrate-моно-mp3

## <a name="quickstarts"></a>Краткие руководства

Мы предлагаем quickstarts разработан, чтобы помочь вам запустить Длинный аудио API успешно. Эта таблица включает в себя список long Audio API quickstarts, организованных языком.

* [Быстрый запуск: Python](https://aka.ms/long-audio-python)

## <a name="sample-code"></a>Образец кода
Пример кода для Long Audio API доступен на GitHub.

* [Пример кода: Python](https://github.com/Azure-Samples/Cognitive-Speech-TTS/tree/master/CustomVoice-API-Samples/Python)
* [Пример кода: C #](https://github.com/Azure-Samples/Cognitive-Speech-TTS/tree/master/CustomVoice-API-Samples/CSharp)
* [Пример кода: Java](https://github.com/Azure-Samples/Cognitive-Speech-TTS/blob/master/CustomVoice-API-Samples/Java/)
