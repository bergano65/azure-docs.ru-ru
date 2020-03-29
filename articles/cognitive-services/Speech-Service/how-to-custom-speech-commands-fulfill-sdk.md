---
title: Как выполнять команды клиента с помощью Speech SDK
titleSuffix: Azure Cognitive Services
description: В этой статье мы объясняем, как обрабатывать действия пользовательских команд на клиенте с помощью Speech SDK.
services: cognitive-services
author: don-d-kim
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/12/2020
ms.author: donkim
ms.openlocfilehash: e109955774722da7f55defe1417de35ff202cce8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79367755"
---
# <a name="fulfill-commands-from-a-client-with-the-speech-sdk-preview"></a>Выполняй команды клиента с помощью Speech SDK (Предварительный просмотр)

Для выполнения задач с помощью приложения Custom Commands можно отправлять пользовательские полезные нагрузки на подключенное клиентское устройство.

В этой статье вы будете:

- Определите и отправьте пользовательскую полезную нагрузку JSON из приложения Custom Commands
- Получайте и визуализировать пользовательское содержимое полезной нагрузки JSON из клиентского приложения SDK SDK

## <a name="prerequisites"></a>Предварительные требования

- [Визуальная студия 2019](https://visualstudio.microsoft.com/downloads/)
- Ключ подписки Azure для службы speech
  - [Получить его бесплатно](get-started.md) или создать на [портале Azure](https://portal.azure.com)
- Ранее созданное приложение пользовательских команд
  - [Быстрый запуск: Создайте пользовательскую команду с параметрами (Предварительный просмотр)](./quickstart-custom-speech-commands-create-parameters.md)
- Приложение с включенным речевыми клиентами
  - [Быстрый запуск: Подключитесь к приложению Custom Command с помощью речевого SDK (Предварительный просмотр)](./quickstart-custom-speech-commands-speech-sdk.md)

## <a name="optional-get-started-fast"></a>Дополнительно: Начать быстро

В этой статье описывается, шаг за шагом, как сделать приложение клиента, чтобы поговорить с вашим приложением пользовательских команд. Если вы предпочитаете нырять прямо в, полный, готовый к компиляции исходный код, используемый в этой статье, доступен в [образцах речи SDK.](https://aka.ms/csspeech/samples)

## <a name="fulfill-with-json-payload"></a>Выполнить с полезной нагрузкой JSON

1. Откройте ранее созданное приложение Custom Commands от [студии speech Studio](https://speech.microsoft.com/)
1. Проверьте раздел **«Правила завершения»,** чтобы убедиться, что у вас есть ранее созданное правило, которое отвечает пользователю
1. Чтобы отправить полезную нагрузку непосредственно клиенту, создайте новое правило с действием «Отправить активность»

   > [!div class="mx-imgBorder"]
   > ![Правило завершения действия](media/custom-speech-commands/fulfill-sdk-completion-rule.png)

   | Параметр | Рекомендуемое значение | Описание |
   | ------- | --------------- | ----------- |
   | Имя правила | ОбновлениеDeviceState | Имя, описывающее цель правила |
   | Условия | Требуемый `OnOff` параметр - и`SubjectDevice` | Условия, определяющие, когда правило может быть запущено |
   | Действия | `SendActivity`(см. ниже) | Действие, которое необходимо предпринять, когда условие правила истинно |

   > [!div class="mx-imgBorder"]
   > ![Отправка полезной нагрузки активности](media/custom-speech-commands/fulfill-sdk-send-activity-action.png)

   ```json
   {
     "type": "event",
     "name": "UpdateDeviceState",
     "state": "{OnOff}",
     "device": "{SubjectDevice}"
   }
   ```

## <a name="create-visuals-for-device-on-or-off-state"></a>Создание визуальных элементов для устройства в состоянии или вне его

В [приложении «Быстрый старт: Подключитесь к пользовательскому командному приложению» с помощью приложения Speech SDK (Preview)](./quickstart-custom-speech-commands-speech-sdk.md) вы создали клиентское приложение Speech SDK, которое обрабатывало команды, такие как `turn on the tv`: `turn off the fan` Теперь добавьте некоторые визуальные эффекты, чтобы вы могли видеть результат этих команд.

Добавьте помеченные коробки с текстом, указывающим **на выключение** или **выключение,** используя следующий XML, добавленный в`MainPage.xaml.cs`

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

## <a name="handle-customizable-payload"></a>Обработка настраиваемой полезной нагрузки

Теперь, когда вы создали полезную нагрузку JSON, можно добавить ссылку на [JSON.NET](https://www.newtonsoft.com/json) библиотеку для обработки десериализации.

> [!div class="mx-imgBorder"]
> ![Отправка полезной нагрузки активности](media/custom-speech-commands/fulfill-sdk-json-nuget.png)

Добавляйте `InitializeDialogServiceConnector` следующее `ActivityReceived` в обработчик событий. Дополнительный код будет извлекать полезную нагрузку из деятельности и изменить визуальное состояние телевизора или вентилятора соответственно.

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

## <a name="try-it-out"></a>Попробуйте продукт

1. Запуск приложения
1. Выберите микрофон Включить
1. Выберите кнопку Talk
1. Сказать`turn on the tv`
1. Визуальное состояние телевизора должно измениться на "On"

## <a name="next-steps"></a>Дальнейшие действия

> [!div class="nextstepaction"]
> [Как: Добавление валидаций к параметрам Custom Command (предварительный просмотр)](./how-to-custom-speech-commands-validations.md)
