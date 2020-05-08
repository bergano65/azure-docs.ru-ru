---
title: Как выполнять команды от клиента с помощью пакета SDK для распознавания речи
titleSuffix: Azure Cognitive Services
description: В этой статье объясняется, как управлять действиями настраиваемых команд на клиенте с помощью пакета SDK для распознавания речи.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 05/04/2020
ms.author: trbye
ms.openlocfilehash: f11f5f3c2ad4c9f0241d34edeb664f739f88d15c
ms.sourcegitcommit: f57297af0ea729ab76081c98da2243d6b1f6fa63
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/06/2020
ms.locfileid: "82871738"
---
# <a name="fulfill-commands-from-a-client-with-the-speech-sdk-preview"></a>Выполнение команд из клиента с помощью пакета SDK для распознавания речи (Предварительная версия)

Для выполнения задач с помощью пользовательского приложения команды можно отправить пользовательские полезные данные на подключенное клиентское устройство.

В этой статье вы выполните следующие действия:

- Определение и Отправка пользовательских полезных данных JSON из приложения пользовательских команд
- Получение и визуализация содержимого настраиваемой полезной нагрузки JSON из клиентского приложения SDK для языка C# UWP

## <a name="prerequisites"></a>Предварительные требования
> [!div class = "checklist"]
> * [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/)
> * Ключ подписки Azure для службы речи: [получите бесплатную](get-started.md) или создайте ее на [портал Azure](https://portal.azure.com)
> * Ранее созданное приложение пользовательских команд: [Краткое руководство. Создание настраиваемой команды с параметрами (Предварительная версия)](./quickstart-custom-speech-commands-create-parameters.md)
> * Клиентское приложение с поддержкой Speech SDK: [Краткое руководство. подключение к пользовательскому командному приложению с помощью пакета SDK для распознавания речи (Предварительная версия)](./quickstart-custom-speech-commands-speech-sdk.md)

## <a name="optional-get-started-fast"></a>Необязательно: быстрое начало работы

В этой статье подробно описывается, как создать клиентское приложение для взаимодействия с приложением пользовательских команд. Если вы предпочитаете, то готовый к компиляции исходный код, используемый в этой статье, доступен в [образцах пакета SDK для распознавания речи](https://aka.ms/csspeech/samples).

## <a name="fulfill-with-json-payload"></a>Условия выполнения с использованием полезных данных JSON

1. Откройте созданное ранее приложение "пользовательские команды" из [руководства по созданию настраиваемой команды с параметрами.](./quickstart-custom-speech-commands-create-parameters.md)
1. Проверьте раздел **правила завершения** , чтобы убедиться, что создано ранее созданное правило, отвечающее пользователю.
1. Чтобы отправить полезные данные непосредственно клиенту, создайте новое правило с действием Отправить действие.

   > [!div class="mx-imgBorder"]
   > ![Правило завершения отправки действия](media/custom-speech-commands/fulfill-sdk-completion-rule.png)

   | Параметр | Рекомендуемое значение | Описание |
   | ------- | --------------- | ----------- |
   | Имя правила | упдатедевицестате | Имя, описывающее назначение правила. |
   | Условия | Обязательный параметр `OnOff` -и`SubjectDevice` | Условия, определяющие, когда может выполняться правило |
   | Действия | `SendActivity`(см. ниже) | Действие, выполняемое, если условие правила имеет значение true |

1. Скопируйте приведенный ниже код JSON в **содержимое действия** .
   ```json
   {
     "type": "event",
     "name": "UpdateDeviceState",
     "state": "{OnOff}",
     "device": "{SubjectDevice}"
   }
   ```
   > [!div class="mx-imgBorder"]
   > ![Отправка полезных данных действия](media/custom-speech-commands/fulfill-sdk-send-activity-action.png)

## <a name="create-visuals-for-device-on-or-off-state"></a>Создание визуальных элементов для состояния или отключения устройства

В [кратком руководстве. подключение к пользовательскому командному приложению с помощью пакета SDK](./quickstart-custom-speech-commands-speech-sdk.md)для распознавания речи вы создали клиентское приложение SDK для `turn on the tv`распознавания `turn off the fan`речи, которое обрабатывает такие команды, как,. После добавления некоторых визуальных элементов можно увидеть результат этих команд.

Добавьте помеченные поля с текстом, **указывающим** или **отключив** использование следующего XML-кода, добавленного в`MainPage.xaml`

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
### <a name="add-reference-libraries"></a>Добавление библиотек ссылок

Так как вы создали полезные данные JSON, необходимо добавить ссылку на библиотеку [JSON.NET](https://www.newtonsoft.com/json) для управления десериализацией.
- Щелкните правой кнопкой мыши клиентское решение.
- Выберите **Управление пакетами NuGet для решения**, нажмите кнопку **установить** . 
- Выполните поиск **Newtonsoft. JSON** в списке обновлений, обновите **Microsoft. NETCore. UniversalWindowsPlatform** до последней версии.

> [!div class="mx-imgBorder"]
> ![Отправка полезных данных действия](media/custom-speech-commands/fulfill-sdk-json-nuget.png)

В "MainPage. XAML. cs" добавьте
- `using Newtonsoft.Json;` 
- `using Windows.ApplicationModel.Core;`

### <a name="handle-received-payload"></a>Обрабатывает полученные полезные данные

В `InitializeDialogServiceConnector`Замените обработчик `ActivityReceived` событий следующим кодом. Измененный `ActivityReceived` обработчик событий извлечет полезные данные из действия и изменит визуальное состояние телевизора или вентилятора соответствующим образом.

```C#
connector.ActivityReceived += async (sender, activityReceivedEventArgs) =>
{
    NotifyUser($"Activity received, hasAudio={activityReceivedEventArgs.HasAudio} activity={activityReceivedEventArgs.Activity}");

    dynamic activity = JsonConvert.DeserializeObject(activityReceivedEventArgs.Activity);
    var name = activity?.name != null ? activity.name.ToString() : string.Empty;

    if (name.Equals("UpdateDeviceState"))
    {
        Debug.WriteLine("Here");
        var state = activity?.device != null ? activity.state.ToString() : string.Empty;
        var device = activity?.device != null ? activity.device.ToString() : string.Empty;

        if (state.Equals("on") || state.Equals("off"))
        {
            switch (device)
            {
                case "tv":
                    await CoreApplication.MainView.CoreWindow.Dispatcher.RunAsync(
                        CoreDispatcherPriority.Normal, () => { State_TV.Text = state; });
                    break;
                case "fan":
                    await CoreApplication.MainView.CoreWindow.Dispatcher.RunAsync(
                        CoreDispatcherPriority.Normal, () => { State_Fan.Text = state; });
                    break;
                default:
                    NotifyUser($"Received request to set unsupported device {device} to {state}");
                    break;
            }
        }
        else { 
            NotifyUser($"Received request to set unsupported state {state}");
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
   > [!div class="mx-imgBorder"]
   > ![Отправка полезных данных действия](media/custom-speech-commands/fulfill-sdk-turn-on-tv.png)

## <a name="next-steps"></a>Дальнейшие действия

> [!div class="nextstepaction"]
> [Как добавить проверки в пользовательские параметры команды (Предварительная версия)](./how-to-custom-speech-commands-validations.md)
