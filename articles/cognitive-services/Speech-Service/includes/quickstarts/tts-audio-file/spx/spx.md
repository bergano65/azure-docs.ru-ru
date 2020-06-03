---
author: v-demjoh
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 05/18/2020
ms.author: v-demjoh
ms.openlocfilehash: bd48618a520f547c72bfba7ff04ae6a249d0a673
ms.sourcegitcommit: cf7caaf1e42f1420e1491e3616cc989d504f0902
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/22/2020
ms.locfileid: "83806225"
---
## <a name="run-the-speech-cli"></a>Запуск интерфейса командной строки службы "Речь"

Итак, все готово для запуска интерфейса командной строки службы "Речь" для синтезирования речи из текста и сохранения ее в новый аудиофайл.

В командной строке перейдите в каталог, в котором содержится двоичный файл интерфейса командной строки службы "Речь", и введите следующее:

```bash
spx synthesize --text "The speech synthesizer greets you!" --audio output greetings.wav
```

Интерфейс командной строки службы "Речь" воспроизведет фразу на естественном английском языке в аудиофайл `greetings.wav`.
В среде Windows вы можно воспроизвести этот файл, введя команду `start greetings.wav`.
