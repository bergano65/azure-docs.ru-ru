---
author: trrwilson
ms.service: cognitive-services
ms.topic: include
ms.date: 05/25/2020
ms.author: trrwilson
ms.openlocfilehash: a18ce7b7c28b99967668bc33c5a94cbb58bfbc34
ms.sourcegitcommit: d95cab0514dd0956c13b9d64d98fdae2bc3569a0
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/25/2020
ms.locfileid: "91377585"
---
## <a name="prerequisites"></a>Предварительные требования

Необходимые условия:

> [!div class="checklist"]
> * [Создать ресурс службы "Речь" Azure.](../../../../overview.md#try-the-speech-service-for-free)
> * [Настройка среды разработки и создание пустого проекта](../../../../quickstarts/setup-platform.md)
> * Убедитесь, что у вас есть доступ к микрофону для аудиозахвата.

## <a name="setup-your-environment"></a>Настройте среду.

Обновите файл go.mod, используя последнюю версию пакета SDK. Для этого добавьте следующую строку:
```sh
require (
    github.com/Microsoft/cognitive-services-speech-sdk-go v1.13.0
)
```

## <a name="start-with-some-boilerplate-code"></a>Добавление стандартного кода
1. Замените содержимое этого исходного файла (например, `sr-quickstart.go`) следующим содержимым:

- определение пакета main;
- импорт необходимых модулей из пакета SDK службы "Речь";
- переменные для хранения сведений о подписке, которые будут заменены далее в этом кратком руководстве;
- простая реализация с использованием микрофона для получения входных аудиоданных;
- обработчики событий для различных событий, происходящих во время распознавания речи.

```sh
package recognizer

import (
    "bufio"
    "fmt"
    "os"

    "github.com/Microsoft/cognitive-services-speech-sdk-go/audio"
    "github.com/Microsoft/cognitive-services-speech-sdk-go/speech"
)

func recognizingHandler(event speech.SpeechRecognitionEventArgs) {
    defer event.Close()
    fmt.Println("Recognizing:", event.Result.Text)
}

func recognizedHandler(event speech.SpeechRecognitionEventArgs) {
    defer event.Close()
    fmt.Println("Recognized:", event.Result.Text)
}

func cancelledHandler(event speech.SpeechRecognitionCanceledEventArgs) {
    defer event.Close()
    fmt.Println("Received a cancellation: ", event.ErrorDetails)
}

func main() {
    subscription :=  "YOUR_SUBSCRIPTION_KEY"
    region := "YOUR_SUBSCRIPTIONKEY_REGION"

    audioConfig, err := audio.NewAudioConfigFromDefaultMicrophoneInput()
    if err != nil {
        fmt.Println("Got an error: ", err)
        return
    }
    defer audioConfig.Close()
    config, err := speech.NewSpeechConfigFromSubscription(subscription, region)
    if err != nil {
        fmt.Println("Got an error: ", err)
        return
    }
    defer config.Close()
    speechRecognizer, err := speech.NewSpeechRecognizerFromConfig(config, audioConfig)
    if err != nil {
        fmt.Println("Got an error: ", err)
        return
    }
    defer speechRecognizer.Close()
    speechRecognizer.Recognizing(recognizingHandler)
    speechRecognizer.Recognized(recognizedHandler)
    speechRecognizer.Canceled(cancelledHandler)
    speechRecognizer.StartContinuousRecognitionAsync()
    defer speechRecognizer.StopContinuousRecognitionAsync()
    bufio.NewReader(os.Stdin).ReadBytes('\n')
}
```

Замените значения `YOUR_SUBSCRIPTION_KEY` и `YOUR_SUBSCRIPTIONKEY_REGION` фактическими значениями из ресурса службы "Речь".

- Перейдите на портал Azure и откройте раздел ресурса службы "Речь".
- Выберите слева раздел **Ключи**, в нем вы увидите два ключа подписки.
    - Используйте один из них в качестве замены значения `YOUR_SUBSCRIPTION_KEY`.
- Откройте слева раздел **Обзор**, запишите имя региона и сопоставьте его с идентификатором региона.
- Используйте идентификатор региона как замену значения `YOUR_SUBSCRIPTIONKEY_REGION`, например: `"westus"` для **Западная часть США**.

## <a name="code-explanation"></a>Пояснение к коду
Для создания объекта конфигурации службы "Речь" нужно указать регион и ключ подписки службы "Речь". Объект конфигурации нужен для создания экземпляра объекта распознавателя речи.

Экземпляр распознавателя предоставляет несколько способов распознавания речи. В этом примере речь распознается непрерывно. Так служба "Речь" узнает, что для распознавания отправляется несколько фраз и что для остановки распознавания программа завершает работу. По мере получения результатов код будет записывать их в консоль.

## <a name="build-and-run"></a>Сборка и запуск
Вы готовы создать проект и протестировать распознавание речи с помощью службы "Речь".
1. Выполнение сборки проекта, например **go build**
2. Запустите модуль и произнесите фразу или предложение в микрофон устройства. Ваша речь будет передана в службу "Речь" и преобразована в текст, который появится в окне вывода.


> [!NOTE]
> Пакет SDK для распознавания речи по умолчанию распознает использование языкового стандарта en-us. Дополнительные сведения о выборе исходного языка см. в разделе [Specify source language for speech to text](../../../../how-to-specify-source-language.md) (Указание исходного языка для преобразования речи в текст).


## <a name="next-steps"></a>Дальнейшие действия

[!INCLUDE [footer](./footer.md)]
