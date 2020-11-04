---
author: trrwilson
ms.service: cognitive-services
ms.topic: include
ms.date: 05/25/2020
ms.author: trrwilson
ms.openlocfilehash: b05e63dc549fdf1fec4485e27086bb32251b41d8
ms.sourcegitcommit: 857859267e0820d0c555f5438dc415fc861d9a6b
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/30/2020
ms.locfileid: "93136004"
---
## <a name="prerequisites"></a>Предварительные требования

Необходимые условия:

> [!div class="checklist"]
> * [Создать ресурс службы "Речь" Azure.](../../../../overview.md#try-the-speech-service-for-free)
> * [Настройте среду разработки и создайте пустой проект](../../../../quickstarts/setup-platform.md).
> * Создание программы-робота, подключенной к [Каналу Direct Line Speech](https://docs.microsoft.com/azure/bot-service/bot-service-channel-connect-directlinespeech)
> * Убедитесь, что у вас есть доступ к микрофону для аудиозахвата.
>
  > [!NOTE]
  > Ознакомьтесь со [списком поддерживаемых регионов для голосовых помощников](~/articles/cognitive-services/speech-service/regions.md#voice-assistants) и убедитесь, что ваши ресурсы развернуты в одном из этих регионов.

## <a name="setup-your-environment"></a>Настройка среды

Обновите файл go.mod, используя последнюю версию пакета SDK. Для этого добавьте следующую строку:
```sh
require (
    github.com/Microsoft/cognitive-services-speech-sdk-go v1.14.0
)
```

## <a name="start-with-some-boilerplate-code"></a>Добавление стандартного кода
Замените содержимое этого исходного файла (например, `quickstart.go`) следующим содержимым:

- определение пакета main;
- импорт необходимых модулей из пакета SDK службы "Речь";
- переменные для хранения сведений о боте, которые будут заменены далее в этом кратком руководстве;
- простая реализация с использованием микрофона для получения входных аудиоданных;
- обработчики событий для различных событий, происходящих во время взаимодействия.

```sh
package main

import (
    "fmt"
    "time"

    "github.com/Microsoft/cognitive-services-speech-sdk-go/audio"
    "github.com/Microsoft/cognitive-services-speech-sdk-go/dialog"
    "github.com/Microsoft/cognitive-services-speech-sdk-go/speech"
)

func main() {
    subscription :=  "YOUR_SUBSCRIPTION_KEY"
    region := "YOUR_BOT_REGION"

    audioConfig, err := audio.NewAudioConfigFromDefaultMicrophoneInput()
    if err != nil {
        fmt.Println("Got an error: ", err)
        return
    }
    defer audioConfig.Close()
    config, err := dialog.NewBotFrameworkConfigFromSubscription(subscription, region)
    if err != nil {
        fmt.Println("Got an error: ", err)
        return
    }
    defer config.Close()
    connector, err := dialog.NewDialogServiceConnectorFromConfig(config, audioConfig)
    if err != nil {
        fmt.Println("Got an error: ", err)
        return
    }
    defer connector.Close()
    activityReceivedHandler := func(event dialog.ActivityReceivedEventArgs) {
        defer event.Close()
        fmt.Println("Received an activity.")
    }
    connector.ActivityReceived(activityReceivedHandler)
    recognizedHandle := func(event speech.SpeechRecognitionEventArgs) {
        defer event.Close()
        fmt.Println("Recognized ", event.Result.Text)
    }
    connector.Recognized(recognizedHandle)
    recognizingHandler := func(event speech.SpeechRecognitionEventArgs) {
        defer event.Close()
        fmt.Println("Recognizing ", event.Result.Text)
    }
    connector.Recognizing(recognizingHandler)
    connector.ListenOnceAsync()
    <-time.After(10 * time.Second)
}
```

Замените значения `YOUR_SUBSCRIPTION_KEY` и `YOUR_BOT_REGION` фактическими значениями из ресурса службы "Речь".

- Перейдите на портал Azure и откройте раздел своего ресурса службы "Речь".
- Выберите слева раздел **Keys and Endpoint** (Ключи и конечная точка). В нем вы увидите два ключа подписки.
    - Используйте один из них в качестве замены значения `YOUR_SUBSCRIPTION_KEY`.
- Откройте слева раздел **Обзор** , запишите имя региона и сопоставьте его с идентификатором региона.
    - Используйте идентификатор региона для замены значения `YOUR_BOT_REGION`, например: `"westus"` для **Западная часть США**.

   > [!NOTE]
   > Ознакомьтесь со [списком поддерживаемых регионов для голосовых помощников](~/articles/cognitive-services/speech-service/regions.md#voice-assistants) и убедитесь, что ваши ресурсы развернуты в одном из этих регионов.

   > [!NOTE]
   > Для получения сведений о настройке бота см. документацию по Bot Framework для [канала Direct Line Speech](https://docs.microsoft.com/azure/bot-service/bot-service-channel-connect-directlinespeech).

## <a name="code-explanation"></a>Пояснение к коду
Для создания объекта конфигурации службы "Речь" нужно указать регион и ключ подписки службы "Речь". Объект конфигурации нужен для создания экземпляра объекта распознавателя речи.

Экземпляр распознавателя предоставляет несколько способов распознавания речи. В этом примере речь распознается непрерывно. Так служба "Речь" узнает, что для распознавания отправляется несколько фраз и что для остановки распознавания программа завершает работу. По мере получения результатов код будет записывать их в консоль.

## <a name="build-and-run"></a>Сборка и запуск
Теперь настройка для создания проекта и проверки пользовательского голосового помощника с помощью службы "Речь" выполнена.
1. Выполнение сборки проекта, например **go build**
2. Запустите модуль и произнесите фразу или предложение в микрофон устройства. Ваша речь передастся в канал "Речь Direct Line" и преобразуется в текст, который появится в качестве выходных данных.


> [!NOTE]
> Пакет SDK для распознавания речи по умолчанию распознает использование языкового стандарта en-us. Дополнительные сведения о выборе исходного языка см. в разделе [Specify source language for speech to text](../../../../how-to-specify-source-language.md) (Указание исходного языка для преобразования речи в текст).

## <a name="next-steps"></a>Дальнейшие действия

[!INCLUDE [footer](./footer.md)]
