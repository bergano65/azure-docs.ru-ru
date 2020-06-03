---
author: v-demjoh
ms.service: cognitive-services
ms.topic: include
ms.date: 05/13/2020
ms.author: v-demjoh
ms.openlocfilehash: 398dfe36314f86614515168986023146d695e69b
ms.sourcegitcommit: cf7caaf1e42f1420e1491e3616cc989d504f0902
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/22/2020
ms.locfileid: "83806426"
---
## <a name="find-a-file-that-contains-speech"></a>Выбор файла, содержащего речь

Речевой интерфейс командной строки может распознать речь во многих форматах файлов и на естественных языках. В этом кратком руководстве вы можете использовать WAV-файл (16 кГц или 8 кГц, 16 бит, моно, PCM), содержащий английский язык.

1. Загрузите <a href="https://github.com/Azure-Samples/cognitive-services-speech-sdk/blob/master/samples/csharp/sharedcontent/console/whatstheweatherlike.wav" download="whatstheweatherlike" target="_blank">whatstheweatherlike.wav <span class="docon docon-download x-hidden-focus"></span></a>.
2. Скопируйте файл `whatstheweatherlike.wav` в тот же каталог, что и двоичный файл интерфейса командной строки службы "Речь".

## <a name="run-the-speech-cli"></a>Запуск интерфейса командной строки службы "Речь"

Итак, все готово для запуска интерфейса командной строки службы "Речь" для распознавания речи из аудиофайла.

В командной строке перейдите в каталог, в котором содержится двоичный файл интерфейса командной строки службы "Речь", и введите следующее:

```bash
spx recognize --file whatstheweatherlike.wav
```

> [!NOTE]
> По умолчанию интерфейс командной строки службы "Речь" использует английский язык. Вы можете выбрать другой язык [из таблицы преобразования речи в текст](../../../../language-support.md).
> Например, для распознавания речи на немецком добавьте `--source de-DE`.

В интерфейсе командной строки службы "Речь" транскрипция текста отображается на экране. Затем интерфейс командной строки службы "Речь" будет закрыт.
