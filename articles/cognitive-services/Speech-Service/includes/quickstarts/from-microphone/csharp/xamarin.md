---
title: Краткое руководство. Распознавание речи с микрофона в службе "Речь" с помощью C# (Xamarin)
titleSuffix: Azure Cognitive Services
description: В этой статье вы создадите кросс-платформенное приложение C# Xamarin для универсальной платформы Windows, Android и iOS с помощью пакета SDK службы "Речь"для Cognitive Services. Вы преобразуете речь с микрофона вашего устройства или симулятора в текст в режиме реального времени. Приложение создается с использованием пакета SDK службы "Речь" для NuGet и Microsoft Visual Studio 2019.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 10/28/2019
ms.author: erhopf
ms.openlocfilehash: 1f98e4ac6bdc0a9b7a5982b58677732ede1911f7
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/04/2019
ms.locfileid: "73505678"
---
## <a name="prerequisites"></a>Предварительные требования

Перед началом работы нужно:

> [!div class="checklist"]
> * [Создать ресурс службы "Речь" Azure](../../../../get-started.md).
> * [Настроить среду разработки](../../../../quickstarts/setup-platform.md?tabs=xamarin).
> * [Создать пустой пример проекта](../../../../quickstarts/create-project.md?tabs=xamarin).

Если вы уже сделали это, отлично. Давайте продолжим.

## <a name="add-sample-code-for-the-common-helloworld-project"></a>Добавление примера кода для общего проекта helloworld

Общий проект helloworld содержит не зависящие от платформы реализации для вашего кросс-платформенного приложения. Теперь добавьте XAML-код, определяющий пользовательский интерфейс приложения, и реализацию кода C# программной части.

1. В **обозревателе решений** в рамках общего проекта helloworld откройте `MainPage.xaml`.

1. В представлении XAML конструктора вставьте следующий фрагмент кода XAML в тег **Grid** (между `<StackLayout>` и `</StackLayout>`):

   [!code-xml[UI elements](~/samples-cognitive-services-speech-sdk/quickstart/csharp/xamarin/helloworld/helloworld/MainPage.xaml)]

1. В **Обозревателе решений** откройте исходный файл кода программной части `MainPage.xaml.cs`. Он сгруппирован в `MainPage.xaml`.

1. Замените все содержимое следующим фрагментом кода:

   [!code-csharp[Quickstart code](~/samples-cognitive-services-speech-sdk/quickstart/csharp/xamarin/helloworld/helloworld/MainPage.xaml.cs)]

1. В обработчике `OnRecognitionButtonClicked` исходного файла найдите строку `YourSubscriptionKey` и замените ее своим ключом подписки.


1. В обработчике `OnRecognitionButtonClicked` найдите строку `YourServiceRegion` и замените ее названием [региона](~/articles/cognitive-services/Speech-Service/regions.md), связанного с вашей подпиской. (Например, используйте `westus` для подписки с бесплатной пробной версией.)

1. Далее необходимо создать сервис [Xamarin Service](https://docs.microsoft.com/xamarin/android/app-fundamentals/services/creating-a-service/), который используется для запроса разрешений на доступ к микрофону с различных проектов платформы (UWP, Android и iOS). Для этого добавьте в проект helloworld новую папку с именем *Службы* и создайте в ней новый исходный файл C#. Вы можете щелкнуть правой кнопкой мыши папку *Службы* и выбрать пункт **Добавить** > **Новый элемент** > **Файл кода**. Переименуйте файл `IMicrophoneService.cs` и поместите в него весь код из следующего фрагмента:

   [!code-csharp[Quickstart code](~/samples-cognitive-services-speech-sdk/quickstart/csharp/xamarin/helloworld/helloworld/Services/IMicrophoneService.cs)]

#### <a name="androidtabandroid"></a>[Android](#tab/android)
## <a name="add-sample-code-for-the-helloworldandroid-project"></a>Добавление примера кода для проекта `helloworld.Android`

Теперь добавьте код C#, определяющий специфическую часть приложения для Android.

1. В **обозревателе решений** в рамках общего проекта helloworld.Android откройте `MainActivity.cs`.

1. Замените все содержимое следующим фрагментом кода:

   [!code-csharp[Quickstart code](~/samples-cognitive-services-speech-sdk/quickstart/csharp/xamarin/helloworld/helloworld.Android/MainActivity.cs)]

1. Затем добавьте реализацию Android для `MicrophoneService`, создав папку *Службы* в рамках проекта helloworld.Android. После этого создайте в нем новый исходный файл C#. Переименуйте файл на `MicrophoneService.cs`. Скопируйте следующий фрагмент кода и вставьте его в файл:

   [!code-csharp[Quickstart code](~/samples-cognitive-services-speech-sdk/quickstart/csharp/xamarin/helloworld/helloworld.Android/Services/MicrophoneService.cs)]

1. После этого откройте `AndroidManifest.xml` в папке *Свойства*. Добавьте следующие настройки разрешения на использование микрофона между `<manifest>` и `</manifest>`:

   ```xml
   <uses-permission android:name="android.permission.RECORD_AUDIO" />
   ```
   
#### <a name="iostabios"></a>[iOS](#tab/ios)
## <a name="add-sample-code-for-the-helloworldios-project"></a>Добавление примера кода для проекта `helloworld.iOS`

Теперь добавьте код C#, определяющий специфическую часть приложения для iOS. Кроме того, создайте специфические конфигурации для устройств Apple в проекте helloworld.iOS.

1. В **обозревателе решений** в рамках проекта helloworld.iOS откройте `AppDelegate.cs`.

1. Замените все содержимое следующим фрагментом кода:

   [!code-csharp[Quickstart code](~/samples-cognitive-services-speech-sdk/quickstart/csharp/xamarin/helloworld/helloworld.iOS/AppDelegate.cs)]

1. Затем добавьте реализацию iOS для `MicrophoneService`, создав папку *Службы* в рамках проекта helloworld.iOS. После этого создайте в нем новый исходный файл C#. Переименуйте файл на `MicrophoneService.cs`. Скопируйте следующий фрагмент кода и вставьте его в файл:

   [!code-csharp[Quickstart code](~/samples-cognitive-services-speech-sdk/quickstart/csharp/xamarin/helloworld/helloworld.iOS/Services/MicrophoneService.cs)]

1. Откройте `Info.plist` в проекте helloworld.iOS в текстовом редакторе. Добавьте следующую пару "ключ — значение" в раздел dict:

   <key>NSMicrophoneUsageDescription</key>
   <string>Этот пример приложения требует доступа к микрофону</string>

   > [!NOTE]
   > Если вы выполняете сборку для устройства iPhone, убедитесь, что `Bundle Identifier` соответствует идентификатору приложения для профиля подготовки устройства. В противном случае произойдет сбой сборки. С iPhoneSimulator вы можете оставить его как есть.

1. Если вы работаете на компьютере с Windows, вам необходимо установить соединение с устройством Mac для сборки, выбрав элементы **Инструменты** > **iOS** > **Связать с Mac**. Следуйте инструкциям мастера Visual Studio, чтобы включить подключение к устройству Mac.

#### <a name="uwptabhelloworlduwp"></a>[UWP](#tab/helloworlduwp)
## <a name="add-sample-code-for-the-helloworlduwp-project"></a>Добавление примера кода для проекта `helloworld.UWP`

## <a name="add-sample-code-for-the-helloworlduwp-project"></a>Добавление примера кода для проекта helloworld.UWP

Теперь добавьте код C#, определяющий специфическую часть приложения для UWP.

1. В **обозревателе решений** в рамках проекта helloworld.UWP откройте `MainPage.xaml.cs`.

1. Замените все содержимое следующим фрагментом кода:

   [!code-csharp[Quickstart code](~/samples-cognitive-services-speech-sdk/quickstart/csharp/xamarin/helloworld/helloworld.UWP/MainPage.xaml.cs)]

1. Затем добавьте реализацию UWP для `MicrophoneService`, создав папку *Службы* в рамках проекта helloworld.UWP. После этого создайте в нем новый исходный файл C#. Переименуйте файл на `MicrophoneService.cs`. Скопируйте следующий фрагмент кода и вставьте его в файл:

   [!code-csharp[Quickstart code](~/samples-cognitive-services-speech-sdk/quickstart/csharp/xamarin/helloworld/helloworld.UWP/Services/MicrophoneService.cs)]

1. Затем дважды щелкните файл `Package.appxmanifest` в проекте helloworld.UWP в Visual Studio. В разделе **Возможности** убедитесь, что выбран параметр **Микрофон**, и сохраните файл.

1. Далее дважды щелкните файл `Package.appxmanifest` под проектом `helloworld.UWP` внутри Visual Studio и под **Возможности** > **Микрофон** и сохраните файл.
   > Примечание. На случай, если вы увидите предупреждение: "Файла сертификата не существует: helloworld.UWP_TemporaryKey.pfx", пожалуйста, проверьте [преобразование речи в текст](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-csharp&tabs=uwp) для получения дополнительной информации.

1. В строке меню выберите **Файл** > **Сохранить все**, чтобы сохранить изменения.

## <a name="build-and-run-the-uwp-application"></a>Создание и запуск приложения UWP

1. Задайте helloworld.UWP в качестве запускаемого проекта. Щелкните проект helloworld.UWP правой кнопкой мыши и выберите пункт **Сборка**, чтобы выполнить сборку приложения.

1. Выберите **Отладка** > **Начать отладку** (или нажмите клавишу **F5**), чтобы запустить приложение. Откроется окно **helloworld**.

   ![Пример приложения для распознавания речи в UWP на языке C# — краткое руководство](../../../../media/sdk/qs-csharp-xamarin-helloworld-uwp-window.png)

1. Выберите **Включить микрофон**. Когда появится запрос на доступ, выберите **Да**.

   ![Запрос на разрешение доступа к микрофону](../../../../media/sdk/qs-csharp-xamarin-uwp-access-prompt.png)

1. Щелкните **Начать распознавание речи** и произнесите фразу или предложение на английском языке в микрофон вашего устройства. Ваша речь передается в службу "Речь" и преобразуется в текст, который появляется в том же окне.

   ![Пользовательский интерфейс распознавания речи](../../../../media/sdk/qs-csharp-xamarin-uwp-ui-result.png)
* * *

## <a name="build-and-run-the-android-and-ios-applications"></a>Создание и запуск приложений для Android и iOS

Создание и запуск приложений для Android и iOS на устройстве или симуляторе происходит так же, как на UWP. Важно убедиться, что все пакеты SDK установлены правильно, как указано в разделе "Необходимые условия" этой статьи.

## <a name="next-steps"></a>Дополнительная информация

[!INCLUDE [footer](./footer.md)]
