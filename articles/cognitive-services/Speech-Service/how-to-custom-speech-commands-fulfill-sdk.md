---
title: Как выполнять команды от клиента с помощью пакета SDK для распознавания речи
titleSuffix: Azure Cognitive Services
description: В этой статье объясняется, как управлять действиями настраиваемых команд на клиенте с помощью пакета SDK для распознавания речи.
services: cognitive-services
author: don-d-kim
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/12/2020
ms.author: donkim
ms.openlocfilehash: e109955774722da7f55defe1417de35ff202cce8
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/29/2020
ms.locfileid: "79367755"
---
# <a name="fulfill-commands-from-a-client-with-the-speech-sdk-preview"></a>Выполнение команд из клиента с помощью пакета SDK для распознавания речи (Предварительная версия)

Для выполнения задач с помощью пользовательского приложения команды можно отправить пользовательские полезные данные на подключенное клиентское устройство.

В этой статье вы выполните следующие действия:

- Определение и Отправка пользовательских полезных данных JSON из приложения пользовательских команд
- Получение и визуализация содержимого настраиваемой полезной нагрузки JSON из клиентского приложения SDK для языка C# UWP

## <a name="prerequisites"></a>Предварительные требования

- [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/)
- Ключ подписки Azure для службы речи
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
   > ![Правило завершения отправки действия](media/custom-speech-commands/fulfill-sdk-completion-rule.png)

   | Параметр | Рекомендуемое значение | Описание |
   | ------- | --------------- | ----------- |
   | Имя правила | упдатедевицестате | Имя, описывающее назначение правила. |
   | Условия | Обязательный параметр `OnOff` -и`SubjectDevice` | Условия, определяющие, когда может выполняться правило |
   | Действия | `SendActivity`(см. ниже) | Действие, выполняемое, если условие правила имеет значение true |

   > [!div class="mx-imgBorder"]
   > ![Отправка полезных данных действия](media/custom-speech-commands/fulfill-sdk-send-activity-action.png)

   ```json
   {
     "type": "event",
     "name": "UpdateDeviceState",
     "state": "{OnOff}",
     "device": "{SubjectDevice}"
   }
   ```

## <a name="create-visuals-for-device-on-or-off-state"></a>Создание визуальных элементов для состояния или отключения устройства

В [кратком руководстве. подключение к пользовательскому командному приложению с помощью пакета SDK для распознавания речи (Предварительная версия)](./quickstart-custom-speech-commands-speech-sdk.md) вы создали клиентское `turn on the tv`приложение `turn off the fan`SDK для распознавания речи, которое обрабатывает такие команды, как,. Теперь добавьте несколько визуальных элементов, чтобы можно было увидеть результат этих команд.

Добавьте помеченные поля с текстом, **указывающим** или **отключив** использование следующего XML-кода, добавленного в`MainPage.xaml.cs`

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
> ![Отправка полезных данных действия](media/custom-speech-commands/fulfill-sdk-json-nuget.png)

В `InitializeDialogServiceConnector` обработчик `ActivityReceived` событий добавьте следующий элемент. Дополнительный код извлекает полезные данные из действия и соответствующим образом изменяет визуальное состояние телевизора или вентилятора.

```C#
connector.ActivityReceived += async (sender, activityReceivedEventArgs) =>
{
    NotifyUser($"Activity received, hasAudio={activityReceivedEventArgs.HasAudio} activity={activityReceivedEventArgs.Activity}");

    dynamic activity = JsonConvert.DeserializeObject(activityReceivedEventArgs.Activity);

    if(activity?.name == "SetDeviceState")
    {
        var state = activity?.state;
        var device = activity?.device;
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

## <a name="try-it-out"></a>Попробуйте!

1. Запуск приложения
1. Выберите включить микрофон
1. Нажмите кнопку "разговор"
1. Пишут`turn on the tv`
1. Визуальное состояние телевизора должно измениться на "вкл."

## <a name="next-steps"></a>Следующие шаги

> [!div class="nextstepaction"]
> [Как добавить проверки в пользовательские параметры команды (Предварительная версия)](./how-to-custom-speech-commands-validations.md)
