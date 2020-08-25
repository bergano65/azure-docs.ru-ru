---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 08/11/2020
ms.author: trbye
ms.openlocfilehash: bee28c946242ecf227287fb36b5b03aa6defb1c2
ms.sourcegitcommit: c28fc1ec7d90f7e8b2e8775f5a250dd14a1622a6
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/13/2020
ms.locfileid: "88170171"
---
[!INCLUDE [SPX Setup](../../spx-setup.md)]

## <a name="synthesize-speech-to-a-speaker"></a>Синтезирование речи в виде речи докладчика

Итак, все готово для запуска интерфейса командной строки службы "Речь" для синтезирования речи из текста. В командной строке перейдите в каталог, в котором содержится двоичный файл интерфейса командной строки службы "Речь". Затем выполните следующую команду.

```bash
spx synthesize --text "The speech synthesizer greets you!"
```

Интерфейс командной строки службы "Речь" воспроизведет фразу на естественном английском языке через динамик компьютера.

## <a name="synthesize-speech-to-a-file"></a>Синтезирование речи в файл

Выполните следующую команду, чтобы изменить выходные данные со своего динамика, сохраняемые в файл `.wav`.

```bash
spx synthesize --text "The speech synthesizer greets you!" --audio output greetings.wav
```

Интерфейс командной строки службы "Речь" воспроизведет фразу на естественном английском языке в аудиофайл `greetings.wav`.
В среде Windows вы можно воспроизвести этот файл, введя команду `start greetings.wav`.