---
title: Как выполнять пользовательские команды на клиенте с помощью пакета SDK для распознавания речи (Предварительная версия)
titleSuffix: Azure Cognitive Services
description: В этой статье вы обрабатываете действия пользовательских команд на клиенте с помощью пакета SDK для распознавания речи
services: cognitive-services
author: donkim
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 10/09/2019
ms.author: donkim
ms.openlocfilehash: a986da74a668075457e28a9a37b6a11fd04a84e4
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/04/2019
ms.locfileid: "73507025"
---
# <a name="how-to-fulfill-commands-on-the-client-with-the-speech-sdk-preview"></a>Как выполнять команды на клиенте с помощью пакета SDK для распознавания речи (Предварительная версия)

Для выполнения задач с помощью пользовательского приложения команды можно отправить пользовательские полезные данные на подключенное клиентское устройство.

В этой статье вы выполните следующие действия:

- Определение и Отправка пользовательских полезных данных JSON из приложения пользовательских команд
- Получение и визуализация содержимого настраиваемой полезной нагрузки JSON из C# клиентского приложения SDK для обработки речи UWP

## <a name="prerequisites"></a>Технические условия

- [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/)
- Ключ подписки Azure для речевых служб
   - [Получите его бесплатно](get-started.md) или создайте его на [портал Azure](https://portal.azure.com)
- Ранее созданное приложение пользовательских команд
   - [Краткое руководство. Создание настраиваемой команды с параметрами (Предварительная версия)](./quickstart-custom-speech-commands-create-parameters.md)
- Клиентское приложение с поддержкой Speech SDK
   - [Краткое руководство. подключение к пользовательскому командному приложению с помощью речевого пакета SDK (Предварительная версия)](./quickstart-custom-speech-commands-speech-sdk.md)

## <a name="optional-get-started-fast"></a>Необязательно: быстрое начало работы

В этой статье подробно описывается, как создать клиентское приложение для взаимодействия с приложением пользовательских команд. Если вы предпочитаете, то готовый к компиляции исходный код, используемый в этой статье, доступен в [образцах пакета SDK для распознавания речи](https://aka.ms/csspeech/samples).

## <a name="fulfill-with-json-payload"></a>Условия выполнения с использованием полезных данных JSON

1. Откройте созданное ранее приложение пользовательских команд из [речевого студии](https://speech.microsoft.com/) .
1. Проверьте раздел **правила завершения** , чтобы убедиться, что создано ранее созданное правило, отвечающее пользователю.
1. Чтобы отправить полезные данные непосредственно клиенту, создайте новое правило с действием Отправить действие.
   > [!div class="mx-imgBorder"]
   > ](media/custom-speech-commands/fulfill-sdk-completion-rule.png) правило завершения действия ![отправки

   | Настройка    | Рекомендуемое значение                                  | Description (Описание)                                        |
   | ---------- | ------------------------------------------------ | -------------------------------------------------- |
   | Имя правила  | упдатедевицестате                                | Имя, описывающее назначение правила.          |
   | Условия | Обязательный параметр — `OnOff` и `SubjectDevice` | Условия, определяющие, когда может выполняться правило    |
   | Действия    | `SendActivity` (см. ниже)                        | Действие, выполняемое, если условие правила имеет значение true |

   > [!div class="mx-imgBorder"]
   > ![](media/custom-speech-commands/fulfill-sdk-send-activity-action.png) полезных данных действия отправки

   ```json
   {
     "name": "UpdateDeviceState",
     "state": "{OnOff}",
     "device": "{SubjectDevice}"
   }
   ```

## <a name="create-visuals-for-device-on-or-off-state"></a>Создание визуальных элементов для состояния или отключения устройства

В [кратком руководстве. подключение к пользовательскому командному приложению с помощью пакета SDK для распознавания речи (Предварительная версия)](./quickstart-custom-speech-commands-speech-sdk.md) вы создали клиентское приложение SDK для распознавания речи, которое обрабатывает такие команды, как `turn on the tv`, `turn off the fan`. Теперь добавьте несколько визуальных элементов, чтобы можно было увидеть результат этих команд.

Добавьте помеченные поля с текстом, **указывающим** или **отключив** использование следующего XML-кода, добавленного в `MainPage.xaml.cs`

```xml
<StackPanel Orientation="Horizontal" HorizontalAlignment="Center" Margin="20">
    <Grid x:Name="Grid_TV" Margin="50, 0" Width="100" Height="100" Background="LightBlue">
        <StackPanel>
            <TextBlock Text="TV" Margin="0, 10" TextAlignment="Center"/>
            <TextBlock x:Name="State_TV" Text="Off" TextAlignment="Center"/>
        </StackPanel>
    </Grid>
    <Grid x:Name="Grid_Fan" Margin="50, 0" Width="100" Height="100" Background="LightBlue">
        <StackPanel>
            <TextBlock Text="Fan" Margin="0, 10" TextAlignment="Center"/>
            <TextBlock x:Name="State_Fan" Text="Off" TextAlignment="Center"/>
        </StackPanel>
    </Grid>
</StackPanel>
```

## <a name="handle-customizable-payload"></a>Обработку настраиваемых полезных данных

Теперь, когда вы создали полезные данные JSON, можно добавить ссылку на библиотеку [JSON.NET](https://www.newtonsoft.com/json) , чтобы обрабатывалась десериализация.

> [!div class="mx-imgBorder"]
> ![](media/custom-speech-commands/fulfill-sdk-json-nuget.png) полезных данных действия отправки

В `InitializeDialogServiceConnector` добавьте следующий элемент в обработчик событий `ActivityReceived`. Дополнительный код извлекает полезные данные из действия и соответствующим образом изменяет визуальное состояние телевизора или вентилятора.

```C#
connector.ActivityReceived += async (sender, activityReceivedEventArgs) =>
{
    NotifyUser($"Activity received, hasAudio={activityReceivedEventArgs.HasAudio} activity={activityReceivedEventArgs.Activity}");

    dynamic activity = JsonConvert.DeserializeObject(activityReceivedEventArgs.Activity);
    var payload = activity?.Value;

    if(payload?.name == "SetDeviceState")
    {
        var state = payload?.state;
        var device = payload?.device;
        switch(device)
        {
            case "tv":
                State_TV.Text = state;
                break;
            case "fan":
                State_Fan.Text = state;
                break;
            default:
                NotifyUser($"Received request to set unsupported device {device} to {state}");
                break;
        }
    }

    if (activityReceivedEventArgs.HasAudio)
    {
        SynchronouslyPlayActivityAudio(activityReceivedEventArgs.Audio);
    }
};
```

## <a name="try-it-out"></a>Попробовать

1. Запуск приложения
1. Выберите включить микрофон
1. Нажмите кнопку "разговор"
1. Скажем `turn on the tv`
1. Визуальное состояние телевизора должно измениться на "вкл."

## <a name="next-steps"></a>Дальнейшие действия
> [!div class="nextstepaction"]
> [Как добавить проверки в пользовательские параметры команды (Предварительная версия)](./how-to-custom-speech-commands-validations.md)

