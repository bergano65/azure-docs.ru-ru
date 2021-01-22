---
title: Пакетные операции с использованием CLI службы "Речь"
titleSuffix: Azure Cognitive Services
description: Узнайте, как выполнять пакетные операции преобразования речи в текст (распознавание речи) и текста в речь (синтез речи) с помощью CLI службы "Речь".
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 01/13/2021
ms.author: erhopf
ms.openlocfilehash: 60cacafc89f2335b87885e4ea11dcf8992d68c3f
ms.sourcegitcommit: fc23b4c625f0b26d14a5a6433e8b7b6fb42d868b
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/17/2021
ms.locfileid: "98540245"
---
# <a name="speech-cli-batch-operations"></a>Пакетные операции CLI службы Речи

При использовании службы Azure "Речь" часто выполняются пакетные операции. Из этой статьи вы узнаете, как выполнять пакетные операции преобразования речи в текст (распознавание речи) и текста в речь (синтез речи) с помощью CLI службы "Речь". В частности, вы узнаете, как выполнять следующие задачи:

* выполнить пакетное распознавание речи в каталоге звуковых файлов;
* пакетный синтез речи путем итерации по файлу `.tsv`;

## <a name="batch-speech-to-text-speech-recognition"></a>пакетное преобразование речи в текст (распознавание речи).

Служба "Речь" часто используется для распознавания речи из аудиофайлов. В этом примере выполняется итерация по каталогу с помощью CLI службы "Речь" для записи результатов распознавания для каждого файла `.wav`. Флаг `--files` определяет каталог, в котором хранятся звуковые файлы. Подстановочный знак `*.wav` указывает CLI службы "Речь" выполнять распознавание для каждого файла с расширением `.wav`. Выходные данные для всех файлов распознавания записываются в `speech_output.tsv` в виде значений, разделенных знаками табуляции.

> [!NOTE]
> Аргумент `--threads` можно также использовать в следующем разделе для команд `spx synthesize`. Доступные потоки будут зависеть от ЦП и его текущей загрузки в процентах.

```console
spx recognize --files C:\your_wav_file_dir\*.wav --output file C:\output_dir\speech_output.tsv --threads 10
```

Ниже приведен пример файла структуры выходного файла.

```output
audio.input.id  recognizer.session.started.sessionid    recognizer.recognized.result.text
sample_1    07baa2f8d9fd4fbcb9faea451ce05475    A sample wave file.
sample_2    8f9b378f6d0b42f99522f1173492f013    Sample text synthesized.
```

## <a name="batch-text-to-speech-speech-synthesis"></a>Пакетное преобразование текста в речь (синтез речи)

Самый простой способ выполнить пакетное преобразование текста в речь — создать новый файл `.tsv` (со значениями, разделенными знаками табуляции) и использовать команду `--foreach` в CLI службы "Речь". Вы можете создать файл `.tsv` с помощью предпочитаемого текстового редактора. В этом примере назовем файл `text_synthesis.tsv`:

>[!IMPORTANT]
> При копировании содержимого этого текстового файла убедитесь, что в нем между сведениями о расположении файла и текстом используется **знак табуляции**, а не пробелы. Иногда при копировании содержимого из этого примера знаки табуляции преобразуются в пробелы. Это приводит к сбою команды `spx` при запуске.

```Input
audio.output    text
C:\batch_wav_output\wav_1.wav   Sample text to synthesize.
C:\batch_wav_output\wav_2.wav   Using the Speech CLI to run batch-synthesis.
C:\batch_wav_output\wav_3.wav   Some more text to test capabilities.
```

Затем выполните команду, чтобы указать `text_synthesis.tsv`, выполните синтез для каждого поля `text` и запишите результат по соответствующему пути `audio.output` в виде файла `.wav`.

```console
spx synthesize --foreach in @C:\your\path\to\text_synthesis.tsv
```

Эта команда эквивалентна запуску **`spx synthesize --text "Sample text to synthesize" --audio output C:\batch_wav_output\wav_1.wav`для каждой** записи в файле `.tsv`.

Обратите внимание на несколько моментов.

* Заголовки столбцов `audio.output` и `text` соответствуют аргументам командной строки `--audio output` и `--text`. Аргументы командной строки, содержащие несколько частей, например `--audio output`, должны быть отформатированы в файле без пробелов, начальных дефисов и точек, разделяющих строки (например, `audio.output`). Все остальные существующие аргументы командной строки можно добавить в файл в виде дополнительных столбцов с соблюдением этой структуры.
* Если файл отформатирован таким образом, для `--foreach` не требуется передавать дополнительные аргументы.
* Обязательно отделите каждое значение в `.tsv` **табуляцией**.

Но заголовки столбцов вашего файла `.tsv` могут **не соответствовать** аргументам командной строки, как в следующем примере:

```Input
wav_path    str_text
C:\batch_wav_output\wav_1.wav   Sample text to synthesize.
C:\batch_wav_output\wav_2.wav   Using the Speech CLI to run batch-synthesis.
C:\batch_wav_output\wav_3.wav   Some more text to test capabilities.
```

Эти имена полей можно переопределить надлежащим образом, используя следующий синтаксис в вызове `--foreach`. Это тот же вызов, что и выше.

```console
spx synthesize --foreach audio.output;text in @C:\your\path\to\text_synthesis.tsv
```

## <a name="next-steps"></a>Дальнейшие действия

* [Обзор CLI службы "Речь"](./spx-overview.md)
* [Краткое руководство по CLI службы Речи](./spx-basics.md)
