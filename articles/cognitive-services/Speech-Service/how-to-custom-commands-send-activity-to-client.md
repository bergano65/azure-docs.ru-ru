---
title: Отправка действия настраиваемых команд клиентскому приложению
titleSuffix: Azure Cognitive Services
description: Из этой статьи вы узнаете, как отправить действие из приложения настраиваемых команд в клиентское приложение, запускающее пакет SDK для распознавания речи.
services: cognitive-services
author: xiaojul
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 06/18/2020
ms.author: xiaojul
ms.openlocfilehash: 0a3e3455615006c0e93cf32eebcdaedac9960a79
ms.sourcegitcommit: 4042aa8c67afd72823fc412f19c356f2ba0ab554
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/24/2020
ms.locfileid: "85307734"
---
# <a name="send-custom-commands-activity-to-client-application"></a>Отправка действия настраиваемых команд клиентскому приложению

Из этой статьи вы узнаете, как отправить действие из приложения настраиваемых команд в клиентское приложение, запускающее пакет SDK для распознавания речи.

Вы выполняете следующие задачи:

- Определение и Отправка пользовательских полезных данных JSON из приложения пользовательских команд
- Получение и визуализация содержимого настраиваемой полезной нагрузки JSON из клиентского приложения SDK для языка C# UWP

## <a name="prerequisites"></a>Предварительные требования
> [!div class = "checklist"]
> * [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/)
> * Ключ подписки Azure для службы речи: [получите бесплатную](get-started.md) или создайте ее на [портал Azure](https://portal.azure.com)
> * Ранее [созданное приложение пользовательских команд](quickstart-custom-commands-application.md)
> * Клиентское приложение с поддержкой Speech SDK: [практические руководства. Интеграция с клиентским приложением с помощью пакета SDK для речи](./how-to-custom-commands-setup-speech-sdk.md)

## <a name="setup-send-activity-to-client"></a>Настройка отправки действия клиенту 
1. Откройте созданное ранее приложение пользовательских команд.
1. Выберите команду **турнонофф** , выберите **конфирматионреспонсе** в разделе правило завершения, а затем выберите **Добавить действие** .
1. В разделе **новый тип действия**выберите **Отправить действие клиенту** .
1. Скопируйте приведенный ниже код JSON в **содержимое действия** .
   ```json
   {
     "type": "event",
     "name": "UpdateDeviceState",
     "state": "{OnOff}",
     "device": "{SubjectDevice}"
   }
   ```
1. Нажмите кнопку **сохранить** , чтобы создать новое правило с действием Отправить действие.

   > [!div class="mx-imgBorder"]
   > ![Правило завершения отправки действия](media/custom-commands/send-activity-to-client-completion-rules.png)

## <a name="integrate-with-client-application"></a>Интеграция с клиентским приложением

В [пошаговом окне Настройка клиентского приложения с помощью речевого пакета SDK (Предварительная версия)](./how-to-custom-commands-setup-speech-sdk.md)вы создали клиентское приложение UWP с пакетом SDK для распознавания речи, которое обрабатывает такие команды `turn on the tv` , как, `turn off the fan` . После добавления некоторых визуальных элементов можно увидеть результат этих команд.

Добавьте помеченные поля с текстом, **указывающим** или **отключив** использование следующего XML-кода, добавленного в`MainPage.xaml`

```xml
<StackPanel Orientation="Horizontal" HorizontalAlignment="Center" Margin="20">
    <Grid x:Name="Grid_TV" Margin="50, 0" Width="100" Height="100" Background="LightBlue">
        <StackPanel>
            <TextBlock Text="TV" Margin="0, 10" TextAlignment="Center"/>
            <TextBlock x:Name="State_TV" Text="off" TextAlignment="Center"/>
        </StackPanel>
    </Grid>
    <Grid x:Name="Grid_Fan" Margin="50, 0" Width="100" Height="100" Background="LightBlue">
        <StackPanel>
            <TextBlock Text="Fan" Margin="0, 10" TextAlignment="Center"/>
            <TextBlock x:Name="State_Fan" Text="off" TextAlignment="Center"/>
        </StackPanel>
    </Grid>
</StackPanel>
```

### <a name="add-reference-libraries"></a>Добавление библиотек ссылок

Так как вы создали полезные данные JSON, необходимо добавить ссылку на библиотеку [JSON.NET](https://www.newtonsoft.com/json) для управления десериализацией.

1. Щелкните правой кнопкой мыши клиентское решение.
1. Выберите **Управление пакетами NuGet для решения**, нажмите кнопку **установить** . 
1. Поиск **Newtonsoft.json** в списке обновлений обновите **Microsoft. NETCore. UniversalWindowsPlatform** до последней версии

> [!div class="mx-imgBorder"]
> ![Отправка полезных данных действия](media/custom-commands/send-activity-to-client-json-nuget.png)

В "MainPage. XAML. cs" добавьте
- `using Newtonsoft.Json;` 
- `using Windows.ApplicationModel.Core;`

### <a name="handle-the-received-payload"></a>Обрабатывает полученные полезные данные

В `InitializeDialogServiceConnector` замените `ActivityReceived` обработчик событий следующим кодом. Измененный `ActivityReceived` обработчик событий извлечет полезные данные из действия и изменит визуальное состояние телевизора или вентилятора соответственно.

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
   > ![Отправка полезных данных действия](media/custom-commands/send-activity-to-client-turn-on-tv.png)

## <a name="next-steps"></a>Следующие шаги

> [!div class="nextstepaction"]
> [Как настроить конечные веб-точки (Предварительная версия)](./how-to-custom-commands-setup-web-endpoints.md)