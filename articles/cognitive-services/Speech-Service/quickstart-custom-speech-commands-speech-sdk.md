---
title: 'Быстрый запуск: Подключитесь к приложению пользовательских команд с помощью речевого SDK - Служба речи'
titleSuffix: Azure Cognitive Services
description: В этой статье вы создадите клиентское приложение Speech SDK с пользовательскими командами.
services: cognitive-services
author: don-d-kim
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 12/09/2019
ms.author: donkim
ms.openlocfilehash: 9e324af0b90f595b5b7af2a417a562efb193d854
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "76156783"
---
# <a name="quickstart-connect-to-a-custom-commands-application-with-the-speech-sdk-preview"></a>Быстрый запуск: Подключитесь к приложению пользовательских команд с помощью речевого SDK (Предварительный просмотр)

После создания размещенного приложения Custom Commands можно начать говорить с ним с клиентского устройства.

В этой статье вы будете:

- Опубликовать приложение Custom Commands и получить идентификатор приложения (App ID)
- Создайте клиентское приложение с помощью Speech SDK, чтобы вы могли общаться с приложением Custom Commands

## <a name="prerequisites"></a>Предварительные требования

Для завершения этой статьи требуется приложение Custom Commands. Если вы еще не создали приложение Custom Commands, вы можете сделать это в предыдущих запусках:

- [Быстрый запуск: Создайте пользовательский командный заказ (Предварительный просмотр)](./quickstart-custom-speech-commands-create-new.md)
- [Быстрый запуск: Создайте пользовательскую команду с параметрами (Предварительный просмотр)](./quickstart-custom-speech-commands-create-parameters.md)

Вам также нужно:

- [Визуальная студия 2019](https://visualstudio.microsoft.com/downloads/)
- Ключ подписки Azure для служб "Речь". [Получить его бесплатно](get-started.md) или создать на [портале Azure](https://portal.azure.com)

## <a name="optional-get-started-fast"></a>Дополнительно: Начать быстро

Этот быстрый запуск описывает, шаг за шагом, как сделать клиентское приложение для подключения к вашему приложению пользовательских команд. Если вы хотите сразу приступить к работе, полноценный, готовый к компиляции исходный код, используемый в этом кратком руководстве, доступен в [Примерах пакета SDK для распознавания речи](https://aka.ms/csspeech/samples) в папке `quickstart`.

## <a name="step-1-publish-custom-commands-application"></a>Шаг 1: Опубликовать приложение пользовательских команд

1. Откройте [ранее созданное приложение Custom Commands](./quickstart-custom-speech-commands-create-new.md) и выберите **Publish**

   > [!div class="mx-imgBorder"]
   > ![публикации приложения](media/custom-speech-commands/fulfill-sdk-publish-application.png)

1. Копирование идентификатора приложения из уведомления о публикации для последующего использования

## <a name="step-2-create-a-visual-studio-project"></a>Шаг 2: Создайте проект Визуальной студии

[!INCLUDE [](../../../includes/cognitive-services-speech-service-quickstart-uwp-create-proj.md)]

## <a name="step-3-add-sample-code"></a>Шаг 3: Добавить образец кода

На этом этапе мы добавляем код XAML, который определяет пользовательский интерфейс приложения, и добавляем реализацию кода СЗ.

### <a name="xaml-code"></a>Код XAML

Создайте пользовательский интерфейс приложения, добавив код XAML.

1. В **Solution Explorer**, открыть`MainPage.xaml`

1. В представлении XAML конструктора замените все содержимое следующим фрагментом кода:

   ```xml
   <Page
       x:Class="helloworld.MainPage"
       xmlns="http://schemas.microsoft.com/winfx/2006/xaml/presentation"
       xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
       xmlns:local="using:helloworld"
       xmlns:d="http://schemas.microsoft.com/expression/blend/2008"
       xmlns:mc="http://schemas.openxmlformats.org/markup-compatibility/2006"
       mc:Ignorable="d"
       Background="{ThemeResource ApplicationPageBackgroundThemeBrush}">

       <Grid>
           <StackPanel Orientation="Vertical" HorizontalAlignment="Center"
                       Margin="20,50,0,0" VerticalAlignment="Center" Width="800">
               <Button x:Name="EnableMicrophoneButton" Content="Enable Microphone"
                       Margin="0,0,10,0" Click="EnableMicrophone_ButtonClicked"
                       Height="35"/>
               <Button x:Name="ListenButton" Content="Talk"
                       Margin="0,10,10,0" Click="ListenButton_ButtonClicked"
                       Height="35"/>
               <StackPanel x:Name="StatusPanel" Orientation="Vertical"
                           RelativePanel.AlignBottomWithPanel="True"
                           RelativePanel.AlignRightWithPanel="True"
                           RelativePanel.AlignLeftWithPanel="True">
                   <TextBlock x:Name="StatusLabel" Margin="0,10,10,0"
                              TextWrapping="Wrap" Text="Status:" FontSize="20"/>
                   <Border x:Name="StatusBorder" Margin="0,0,0,0">
                       <ScrollViewer VerticalScrollMode="Auto"
                                     VerticalScrollBarVisibility="Auto" MaxHeight="200">
                           <!-- Use LiveSetting to enable screen readers to announce
                                the status update. -->
                           <TextBlock
                               x:Name="StatusBlock" FontWeight="Bold"
                               AutomationProperties.LiveSetting="Assertive"
                               MaxWidth="{Binding ElementName=Splitter, Path=ActualWidth}"
                               Margin="10,10,10,20" TextWrapping="Wrap"  />
                       </ScrollViewer>
                   </Border>
               </StackPanel>
           </StackPanel>
           <MediaElement x:Name="mediaElement"/>
       </Grid>
   </Page>
   ```

Конструктор обновляется для отображения пользовательского интерфейса приложения.

### <a name="c-code-behind-source"></a>Источник кода C# программной части

Добавьте код за исходным кодом, чтобы приложение работало в штатном порядке. Источник кода программной части включает в себя:

- Необходимые `using` операторы для `Speech` пространства `Speech.Dialog` имен
- простая реализация доступа к микрофону с привязкой к обработчику кнопки;
- базовые вспомогательные функции пользовательского интерфейса для представления сообщений и ошибок в приложении;
- целевая точка для пути кода инициализации, который будет добавлен позже;
- вспомогательное приложение для воспроизведения текста, преобразованного в речь (без поддержки потоковой передачи данных);
- пустой обработчик кнопки воспроизведения, который будет заполнен позже.

Добавьте код за исходным кодом следующим образом:

1. В **Solution Explorer**откройте исходный `MainPage.xaml.cs` файл с `MainPage.xaml`кодом (сгруппированный под)

1. Замените содержимое файла следующим кодом:

   ```csharp
   using Microsoft.CognitiveServices.Speech;
   using Microsoft.CognitiveServices.Speech.Audio;
   using Microsoft.CognitiveServices.Speech.Dialog;
   using System;
   using System.Diagnostics;
   using System.IO;
   using System.Text;
   using Windows.Foundation;
   using Windows.Storage.Streams;
   using Windows.UI.Xaml;
   using Windows.UI.Xaml.Controls;
   using Windows.UI.Xaml.Media;

   namespace helloworld
   {
       public sealed partial class MainPage : Page
       {
           private DialogServiceConnector connector;

           private enum NotifyType
           {
               StatusMessage,
               ErrorMessage
           };

           public MainPage()
           {
               this.InitializeComponent();
           }

           private async void EnableMicrophone_ButtonClicked(
               object sender, RoutedEventArgs e)
           {
               bool isMicAvailable = true;
               try
               {
                   var mediaCapture = new Windows.Media.Capture.MediaCapture();
                   var settings =
                       new Windows.Media.Capture.MediaCaptureInitializationSettings();
                   settings.StreamingCaptureMode =
                       Windows.Media.Capture.StreamingCaptureMode.Audio;
                   await mediaCapture.InitializeAsync(settings);
               }
               catch (Exception)
               {
                   isMicAvailable = false;
               }
               if (!isMicAvailable)
               {
                   await Windows.System.Launcher.LaunchUriAsync(
                       new Uri("ms-settings:privacy-microphone"));
               }
               else
               {
                   NotifyUser("Microphone was enabled", NotifyType.StatusMessage);
               }
           }

           private void NotifyUser(
               string strMessage, NotifyType type = NotifyType.StatusMessage)
           {
               // If called from the UI thread, then update immediately.
               // Otherwise, schedule a task on the UI thread to perform the update.
               if (Dispatcher.HasThreadAccess)
               {
                   UpdateStatus(strMessage, type);
               }
               else
               {
                   var task = Dispatcher.RunAsync(
                       Windows.UI.Core.CoreDispatcherPriority.Normal,
                       () => UpdateStatus(strMessage, type));
               }
           }

           private void UpdateStatus(string strMessage, NotifyType type)
           {
               switch (type)
               {
                   case NotifyType.StatusMessage:
                       StatusBorder.Background = new SolidColorBrush(
                           Windows.UI.Colors.Green);
                       break;
                   case NotifyType.ErrorMessage:
                       StatusBorder.Background = new SolidColorBrush(
                           Windows.UI.Colors.Red);
                       break;
               }
               StatusBlock.Text += string.IsNullOrEmpty(StatusBlock.Text)
                   ? strMessage : "\n" + strMessage;

               if (!string.IsNullOrEmpty(StatusBlock.Text))
               {
                   StatusBorder.Visibility = Visibility.Visible;
                   StatusPanel.Visibility = Visibility.Visible;
               }
               else
               {
                   StatusBorder.Visibility = Visibility.Collapsed;
                   StatusPanel.Visibility = Visibility.Collapsed;
               }
               // Raise an event if necessary to enable a screen reader
               // to announce the status update.
               var peer = Windows.UI.Xaml.Automation.Peers.FrameworkElementAutomationPeer.FromElement(StatusBlock);
               if (peer != null)
               {
                   peer.RaiseAutomationEvent(
                       Windows.UI.Xaml.Automation.Peers.AutomationEvents.LiveRegionChanged);
               }
           }

           // Waits for and accumulates all audio associated with a given
           // PullAudioOutputStream and then plays it to the MediaElement. Long spoken
           // audio will create extra latency and a streaming playback solution
           // (that plays audio while it continues to be received) should be used --
           // see the samples for examples of this.
           private void SynchronouslyPlayActivityAudio(
               PullAudioOutputStream activityAudio)
           {
               var playbackStreamWithHeader = new MemoryStream();
               playbackStreamWithHeader.Write(Encoding.ASCII.GetBytes("RIFF"), 0, 4); // ChunkID
               playbackStreamWithHeader.Write(BitConverter.GetBytes(UInt32.MaxValue), 0, 4); // ChunkSize: max
               playbackStreamWithHeader.Write(Encoding.ASCII.GetBytes("WAVE"), 0, 4); // Format
               playbackStreamWithHeader.Write(Encoding.ASCII.GetBytes("fmt "), 0, 4); // Subchunk1ID
               playbackStreamWithHeader.Write(BitConverter.GetBytes(16), 0, 4); // Subchunk1Size: PCM
               playbackStreamWithHeader.Write(BitConverter.GetBytes(1), 0, 2); // AudioFormat: PCM
               playbackStreamWithHeader.Write(BitConverter.GetBytes(1), 0, 2); // NumChannels: mono
               playbackStreamWithHeader.Write(BitConverter.GetBytes(16000), 0, 4); // SampleRate: 16kHz
               playbackStreamWithHeader.Write(BitConverter.GetBytes(32000), 0, 4); // ByteRate
               playbackStreamWithHeader.Write(BitConverter.GetBytes(2), 0, 2); // BlockAlign
               playbackStreamWithHeader.Write(BitConverter.GetBytes(16), 0, 2); // BitsPerSample: 16-bit
               playbackStreamWithHeader.Write(Encoding.ASCII.GetBytes("data"), 0, 4); // Subchunk2ID
               playbackStreamWithHeader.Write(BitConverter.GetBytes(UInt32.MaxValue), 0, 4); // Subchunk2Size

               byte[] pullBuffer = new byte[2056];

               uint lastRead = 0;
               do
               {
                   lastRead = activityAudio.Read(pullBuffer);
                   playbackStreamWithHeader.Write(pullBuffer, 0, (int)lastRead);
               }
               while (lastRead == pullBuffer.Length);

               var task = Dispatcher.RunAsync(
                   Windows.UI.Core.CoreDispatcherPriority.Normal, () =>
               {
                   mediaElement.SetSource(
                       playbackStreamWithHeader.AsRandomAccessStream(), "audio/wav");
                   mediaElement.Play();
               });
           }

           private void InitializeDialogServiceConnector()
           {
               // New code will go here
           }

           private async void ListenButton_ButtonClicked(
               object sender, RoutedEventArgs e)
           {
               // New code will go here
           }
       }
   }
   ```

1. Добавьте следующий код в корпус метода`InitializeDialogServiceConnector`

   ```csharp
   // This code creates the `DialogServiceConnector` with your subscription information.
   // create a DialogServiceConfig by providing a Custom Commands application id and Cognitive Services subscription key
   // the RecoLanguage property is optional (default en-US); note that only en-US is supported in Preview
   const string speechCommandsApplicationId = "YourApplicationId"; // Your application id
   const string speechSubscriptionKey = "YourSpeechSubscriptionKey"; // Your subscription key
   const string region = "YourServiceRegion"; // The subscription service region. Note: only 'westus2' is currently supported

   var speechCommandsConfig = CustomCommandsConfig.FromSubscription(speechCommandsApplicationId, speechSubscriptionKey, region);
   speechCommandsConfig.SetProperty(PropertyId.SpeechServiceConnection_RecoLanguage, "en-us");
   connector = new DialogServiceConnector(speechCommandsConfig);
   ```

1. Замените `YourApplicationId`строки `YourSpeechSubscriptionKey`и `YourServiceRegion` собственные значения для приложения, подписки на речевые речи и [региона](regions.md)

1. Придатите следующий фрагмент кода к концу тела метода`InitializeDialogServiceConnector`

   ```csharp
   //
   // This code sets up handlers for events relied on by `DialogServiceConnector` to communicate its activities,
   // speech recognition results, and other information.
   //
   // ActivityReceived is the main way your client will receive messages, audio, and events
   connector.ActivityReceived += async (sender, activityReceivedEventArgs) =>
   {
       NotifyUser(
           $"Activity received, hasAudio={activityReceivedEventArgs.HasAudio} activity={activityReceivedEventArgs.Activity}");

       if (activityReceivedEventArgs.HasAudio)
       {
           SynchronouslyPlayActivityAudio(activityReceivedEventArgs.Audio);
       }
   };

   // Canceled will be signaled when a turn is aborted or experiences an error condition
   connector.Canceled += (sender, canceledEventArgs) =>
   {
       NotifyUser($"Canceled, reason={canceledEventArgs.Reason}");
       if (canceledEventArgs.Reason == CancellationReason.Error)
       {
           NotifyUser(
               $"Error: code={canceledEventArgs.ErrorCode}, details={canceledEventArgs.ErrorDetails}");
       }
   };

   // Recognizing (not 'Recognized') will provide the intermediate recognized text
   // while an audio stream is being processed
   connector.Recognizing += (sender, recognitionEventArgs) =>
   {
       NotifyUser($"Recognizing! in-progress text={recognitionEventArgs.Result.Text}");
   };

   // Recognized (not 'Recognizing') will provide the final recognized text
   // once audio capture is completed
   connector.Recognized += (sender, recognitionEventArgs) =>
   {
       NotifyUser($"Final speech-to-text result: '{recognitionEventArgs.Result.Text}'");
   };

   // SessionStarted will notify when audio begins flowing to the service for a turn
   connector.SessionStarted += (sender, sessionEventArgs) =>
   {
       NotifyUser($"Now Listening! Session started, id={sessionEventArgs.SessionId}");
   };

   // SessionStopped will notify when a turn is complete and
   // it's safe to begin listening again
   connector.SessionStopped += (sender, sessionEventArgs) =>
   {
       NotifyUser($"Listening complete. Session ended, id={sessionEventArgs.SessionId}");
   };
   ```

1. Добавьте следующий фрагмент кода к `ListenButton_ButtonClicked` телу `MainPage` метода в классе

   ```csharp
   // This code sets up `DialogServiceConnector` to listen, since you already established the configuration and
   // registered the event handlers.
   if (connector == null)
   {
       InitializeDialogServiceConnector();
       // Optional step to speed up first interaction: if not called,
       // connection happens automatically on first use
       var connectTask = connector.ConnectAsync();
   }

   try
   {
       // Start sending audio
       await connector.ListenOnceAsync();
   }
   catch (Exception ex)
   {
       NotifyUser($"Exception: {ex.ToString()}", NotifyType.ErrorMessage);
   }
   ```

1. Из панели меню выберите **Файл** > **Сохранить все,** чтобы сохранить изменения

## <a name="build-and-run-the-application"></a>Создание и запуск приложения

1. Из панели меню выберите решение **Build** > **Build для** создания приложения. Теперь код должен компилироваться без ошибок.

1. Для запуска приложения выберите **Debug** > **Start Debugging** (или **нажмите F5).** Откроется окно **helloworld**.

   ![Пример приложения виртуального помощника UWP в C# — краткое руководство](media/sdk/qs-voice-assistant-uwp-helloworld-window.png)

1. Выберите **Включить микрофон**. Если всплывает запрос на разрешение доступа, выберите **«Да».**

   ![Запрос на разрешение доступа к микрофону](media/sdk/qs-csharp-uwp-10-access-prompt.png)

1. Выберите **Talk**, и говорить по-английски фразу или предложение в микрофон вашего устройства. Ваша речь передастся в канал "Речь Direct Line" и преобразуется в текст, который появится в том же окне.

## <a name="next-steps"></a>Дальнейшие действия

> [!div class="nextstepaction"]
> [Как: Выполнить команды на клиента с речью SDK (предварительный просмотр)](./how-to-custom-speech-commands-fulfill-sdk.md)
> [Как: Добавить проверки параметров пользовательского командования (Предварительный просмотр)](./how-to-custom-speech-commands-validations.md)
