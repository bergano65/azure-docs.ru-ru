---
title: Краткое руководство. Перевод речи с помощью приложения C# для универсальной платформы Windows в службе "Речь"
titleSuffix: Azure Cognitive Services
description: В этом кратком руководстве вы создадите простое приложение универсальной платформы Windows (UWP) для записи речи пользователя, перевода ее на другой язык и вывода текста в командную строку. Это руководство предназначено для пользователей Windows.
services: cognitive-services
author: wolfma61
manager: cgronlun
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 12/13/2018
ms.author: erhopf
ms.openlocfilehash: fa65eabecba062a7b5d875483d821dfdfb2d64b3
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/29/2019
ms.locfileid: "55207740"
---
# <a name="quickstart-translate-speech-with-the-speech-sdk-for-c-uwp"></a>Краткое руководство. Перевод речи с помощью пакета SDK службы "Речь" для C# (UWP)

В этом кратком руководстве вы создадите простое приложение универсальной платформы Windows (UWP), которое записывает речь пользователя с микрофона компьютера, переводит речь и расшифровывает переведенный текст в командную строку в режиме реального времени. Приложение предназначено для работы в 64-разрядной версии Windows с использованием [пакета NuGet SDK службы "Речь"](https://aka.ms/csspeech/nuget) и Microsoft Visual Studio 2017.

Полный список языков для перевода речи см. в статье, посвященной [поддержке языков](language-support.md).

> [!NOTE]
> UWP позволяет разрабатывать приложения, работающие на любом устройстве, которое поддерживает Windows 10, включая компьютеры, Xbox, Surface Hub и другие устройства.

## <a name="prerequisites"></a>Предварительные требования

Для работы с этим кратким руководством вам понадобится:

* [Visual Studio 2017](https://visualstudio.microsoft.com/downloads/)
* Ключ подписки Azure для службы "Речь". [Его можно получить бесплатно](get-started.md).

## <a name="create-a-visual-studio-project"></a>Создание проекта Visual Studio

[!INCLUDE [](../../../includes/cognitive-services-speech-service-quickstart-uwp-create-proj.md)]

## <a name="add-sample-code"></a>Добавление примеров кода

1. Пользовательский интерфейс приложения определяется с помощью XAML. Откройте `MainPage.xaml` в обозревателе решений. В представлении XAML конструктора вставьте следующий фрагмент кода XAML между `<Grid>` и `</Grid>`.

    ```xaml
    <StackPanel Orientation="Vertical" HorizontalAlignment="Center"  Margin="20,50,0,0" VerticalAlignment="Center" Width="800">
        <Button x:Name="EnableMicrophoneButton" Content="Enable Microphone"  Margin="0,0,10,0" Click="EnableMicrophone_ButtonClicked" Height="35"/>
        <Button x:Name="SpeechRecognitionButton" Content="Translate speech from the microphone input" Margin="0,10,10,0" Click="SpeechTranslationFromMicrophone_ButtonClicked" Height="35"/>
        <StackPanel x:Name="StatusPanel" Orientation="Vertical" RelativePanel.AlignBottomWithPanel="True" RelativePanel.AlignRightWithPanel="True" RelativePanel.AlignLeftWithPanel="True">
            <TextBlock x:Name="StatusLabel" Margin="0,10,10,0" TextWrapping="Wrap" Text="Status:" FontSize="20"/>
            <Border x:Name="StatusBorder" Margin="0,0,0,0">
                <ScrollViewer VerticalScrollMode="Auto"  VerticalScrollBarVisibility="Auto" MaxHeight="200">
                    <!-- Use LiveSetting to enable screen readers to announce the status update. -->
                    <TextBlock x:Name="StatusBlock" FontWeight="Bold" AutomationProperties.LiveSetting="Assertive"
            MaxWidth="{Binding ElementName=Splitter, Path=ActualWidth}" Margin="10,10,10,20" TextWrapping="Wrap"  />
                </ScrollViewer>
            </Border>
        </StackPanel>
    </StackPanel>
    ```

1. Откройте исходный файл с кодом `MainPage.xaml.cs` (он находится в группе `MainPage.xaml`). Замените все содержимое приведенным ниже кодом.

    ```csharp
    using System;
    using System.Threading.Tasks;
    using Windows.UI.Xaml;
    using Windows.UI.Xaml.Controls;
    using Windows.UI.Xaml.Media;
    using Microsoft.CognitiveServices.Speech;
    using Microsoft.CognitiveServices.Speech.Translation;

    namespace helloworld
    {
        /// <summary>
        /// An empty page that can be used on its own or navigated to within a Frame.
        /// </summary>
        public sealed partial class MainPage : Page
        {
            public MainPage()
            {
                this.InitializeComponent();
            }

            private async void EnableMicrophone_ButtonClicked(object sender, RoutedEventArgs e)
            {
                bool isMicAvailable = true;
                try
                {
                    var mediaCapture = new Windows.Media.Capture.MediaCapture();
                    var settings = new Windows.Media.Capture.MediaCaptureInitializationSettings();
                    settings.StreamingCaptureMode = Windows.Media.Capture.StreamingCaptureMode.Audio;
                    await mediaCapture.InitializeAsync(settings);
                }
                catch (Exception)
                {
                    isMicAvailable = false;
                }
                if (!isMicAvailable)
                {
                    await Windows.System.Launcher.LaunchUriAsync(new Uri("ms-settings:privacy-microphone"));
                }
                else
                {
                    NotifyUser("Microphone was enabled", NotifyType.StatusMessage);
                }
            }

            private async void SpeechTranslationFromMicrophone_ButtonClicked(object sender, RoutedEventArgs e)
            {
                // Creates an instance of a speech config with specified subscription key and service region.
                // Replace with your own subscription key and service region (e.g., "westus").
                var config = SpeechTranslationConfig.FromSubscription("YourSubscriptionKey", "YourServiceRegion");

                // Sets source and target languages.
                string fromLanguage = "en-US";
                config.SpeechRecognitionLanguage = fromLanguage;
                config.AddTargetLanguage("de");

                try
                {
                    // Creates a speech recognizer using microphone as audio input.
                    using (var recognizer = new TranslationRecognizer(config))
                    {
                        // The TaskCompletionSource to stop recognition.
                        var stopRecognition = new TaskCompletionSource<int>();

                        // Subscribes to events.
                        recognizer.Recognizing += (s, ee) =>
                        {
                            NotifyUser($"RECOGNIZING in '{fromLanguage}': Text={ee.Result.Text}", NotifyType.StatusMessage);
                            foreach (var element in ee.Result.Translations)
                            {
                                NotifyUser($"    TRANSLATING into '{element.Key}': {element.Value}", NotifyType.StatusMessage);
                            }
                        };

                        recognizer.Recognized += (s, ee) =>
                        {
                            if (ee.Result.Reason == ResultReason.TranslatedSpeech)
                            {
                                NotifyUser($"\nFinal result: Reason: {ee.Result.Reason.ToString()}, recognized text in {fromLanguage}: {ee.Result.Text}.", NotifyType.StatusMessage);
                                foreach (var element in ee.Result.Translations)
                                {
                                    NotifyUser($"    TRANSLATING into '{element.Key}': {element.Value}", NotifyType.StatusMessage);
                                }
                            }
                        };

                        recognizer.Canceled += (s, ee) =>
                        {
                            NotifyUser($"\nRecognition canceled. Reason: {ee.Reason}; ErrorDetails: {ee.ErrorDetails}", NotifyType.StatusMessage);
                        };

                        recognizer.SessionStarted += (s, ee) =>
                        {
                            NotifyUser("\nSession started event.", NotifyType.StatusMessage);
                        };

                        recognizer.SessionStopped += (s, ee) =>
                        {
                            NotifyUser("\nSession stopped event.", NotifyType.StatusMessage);
                            stopRecognition.TrySetResult(0);
                        };

                        // Starts continuous recognition. Uses StopContinuousRecognitionAsync() to stop recognition.
                        await recognizer.StartContinuousRecognitionAsync().ConfigureAwait(false);

                        // Waits for completion.
                        // Use Task.WaitAny to keep the task rooted.
                        Task.WaitAny(new[] { stopRecognition.Task });

                        // Stops continuous recognition.
                        await recognizer.StopContinuousRecognitionAsync().ConfigureAwait(false);
                    }
                }
                catch (Exception ex)
                {
                    NotifyUser($"{ex.ToString()}", NotifyType.ErrorMessage);
                }
            }

            private enum NotifyType
            {
                StatusMessage,
                ErrorMessage
            };

            private void NotifyUser(string strMessage, NotifyType type)
            {
                // If called from the UI thread, then update immediately.
                // Otherwise, schedule a task on the UI thread to perform the update.
                if (Dispatcher.HasThreadAccess)
                {
                    UpdateStatus(strMessage, type);
                }
                else
                {
                    var task = Dispatcher.RunAsync(Windows.UI.Core.CoreDispatcherPriority.Normal, () => UpdateStatus(strMessage, type));
                }
            }

            private void UpdateStatus(string strMessage, NotifyType type)
            {
                switch (type)
                {
                    case NotifyType.StatusMessage:
                        StatusBorder.Background = new SolidColorBrush(Windows.UI.Colors.Green);
                        break;
                    case NotifyType.ErrorMessage:
                        StatusBorder.Background = new SolidColorBrush(Windows.UI.Colors.Red);
                        break;
                }
                StatusBlock.Text += string.IsNullOrEmpty(StatusBlock.Text) ? strMessage : "\n" + strMessage;

                // Collapse the StatusBlock if it has no text to conserve real estate.
                StatusBorder.Visibility = !string.IsNullOrEmpty(StatusBlock.Text) ? Visibility.Visible : Visibility.Collapsed;
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
                // Raise an event if necessary to enable a screen reader to announce the status update.
                var peer = Windows.UI.Xaml.Automation.Peers.FrameworkElementAutomationPeer.FromElement(StatusBlock);
                if (peer != null)
                {
                    peer.RaiseAutomationEvent(Windows.UI.Xaml.Automation.Peers.AutomationEvents.LiveRegionChanged);
                }
            }
        }
    }
    ```

1. В обработчике `SpeechTranslationFromMicrophone_ButtonClicked` этого файла замените строку `YourSubscriptionKey` своим ключом подписки.

1. В обработчике `SpeechTranslationFromMicrophone_ButtonClicked` замените строку `YourServiceRegion` значением [региона](regions.md), связанного с подпиской (например, `westus` для бесплатной пробной подписки).

1. Сохраните все внесенные в проект изменения.

## <a name="build-and-run-the-app"></a>Создание и запуск приложения

1. Создайте приложение. В строке меню последовательно выберите **Сборка** > **Собрать решение**. Теперь код должен компилироваться без ошибок.

    ![Снимок экрана приложения Visual Studio с выделенным параметром "Собрать решение"](media/sdk/qs-csharp-uwp-08-build.png "Успешная сборка")

1. Запустите приложение. В строке меню последовательно выберите **Отладка** > **Начать отладку** или нажмите клавишу **F5**.

    ![Снимок экрана приложения Visual Studio с выделенным параметром "Начать отладку"](media/sdk/qs-csharp-uwp-09-start-debugging.png "Start the app into debugging")

1. Откроется окно. Выберите**Включить микрофон** и подтвердите разрешение в появившемся окне запроса.

    ![Снимок экрана запроса разрешения](media/sdk/qs-csharp-uwp-10-access-prompt.png "Запуск отладки приложения")

1. Щелкните **Распознавание речи с помощью микрофона** и произнесите фразу или предложение на английском языке в микрофон вашего устройства. Ваша речь передается в службу "Речь" и преобразовывается в текст, который появляется в том же окне.

    ![Снимок экрана пользовательского интерфейса распознавания речи](media/sdk/qs-translate-csharp-uwp-ui-result.png)

## <a name="next-steps"></a>Дополнительная информация

> [!div class="nextstepaction"]
> [Примеры для C# на сайте GitHub](https://aka.ms/csspeech/samples)

## <a name="see-also"></a>См. также

- [Настройка акустических моделей](how-to-customize-acoustic-models.md)
- [Настройка языковых моделей](how-to-customize-language-model.md)
