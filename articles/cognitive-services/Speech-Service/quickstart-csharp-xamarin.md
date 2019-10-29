---
title: Краткое руководство. Распознавание речи, C# (Xamarin) — служба "Речь"
titleSuffix: Azure Cognitive Services
description: В этой статье вы создадите кросс-платформенное приложение C# Xamarin для универсальной платформы Windows, Android и iOS с помощью пакета SDK службы "Речь"для Cognitive Services. Вы преобразуете речь с микрофона вашего устройства или симулятора в текст в режиме реального времени. Приложение создается с использованием пакета SDK службы "Речь" для NuGet и Microsoft Visual Studio 2019.
services: cognitive-services
author: jhakulin
manager: robch
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 09/16/2019
ms.author: jhakulin
ms.custom: ''
ms.openlocfilehash: ad1c6f7d17d1e04fcfa5b187a29dadd2fa61edd8
ms.sourcegitcommit: 9a4296c56beca63430fcc8f92e453b2ab068cc62
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/20/2019
ms.locfileid: "72675606"
---
# <a name="quickstart-recognize-speech-by-using-a-cross-platform-xamarin-app-that-uses-the-speech-sdk"></a>Краткое руководство. Распознавание речи с помощью кроссплатформенного приложения Xamarin, использующего пакет SDK для службы "Речь"

Кроме того, доступны краткие руководства по [преобразованию речи в текст](quickstart-csharp-uwp.md), [преобразованию текста в речь](quickstart-text-to-speech-csharp-uwp.md) и [переводу речи](quickstart-translate-speech-uwp.md).

В этой статье вы разработаете кросс-платформенное приложение C# Xamarin для универсальной платформы Windows, Android и iOS с помощью [пакета SDK службы "Речь"](speech-sdk.md) для Cognitive Services. Программа преобразовывает речь с микрофона вашего устройства в текст в реальном времени. Приложение создается с использованием [пакета SDK NuGet для службы "Речь"](https://aka.ms/csspeech/nuget) и Microsoft Visual Studio 2019 (любого выпуска).

## <a name="prerequisites"></a>Предварительные требования

Для работы с этим кратким руководством вам понадобится:

* [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/).
* Ключ подписки Azure для службы "Речь". [Получить бесплатно](get-started.md).
* На компьютере с Windows 10 Fall Creators Update; (10.0; Сборка 16299) или более поздней версии с рабочим микрофоном.
* [Установка Xamarin в Visual Studio](https://docs.microsoft.com/xamarin/get-started/installation/?pivots=windows).
* [Установка Xamarin Android в Windows](https://docs.microsoft.com/xamarin/android/get-started/installation/windows).
* [Установка Xamarin iOS в Windows](https://docs.microsoft.com/xamarin/ios/get-started/installation/windows/?pivots=windows).
* Для целевого устройства Android: 
   * устройство Android (ARM32/64, x86; API 23: устройство Android 6.0 Marshmallow или более поздняя версия), [настроенное для разработки](https://developer.android.com/studio/debug/dev-options), с рабочим микрофоном.
* Для целевых устройств iOS: 
    * iOS-устройство (ARM64) или симулятор iOS (x64), [поддерживающий разработку](https://docs.microsoft.com/xamarin/ios/get-started/installation/device-provisioning/) с рабочим микрофоном.
* Для поддержки сборки Windows ARM64 установите [дополнительные инструменты сборки и пакет SDK Windows 10 для ARM/ARM64](https://blogs.windows.com/buildingapps/2018/11/15/official-support-for-windows-10-on-arm-development/).

## <a name="create-a-visual-studio-project"></a>Создание проекта Visual Studio

[!INCLUDE [](../../../includes/cognitive-services-speech-service-quickstart-xamarin-create-proj.md)]

## <a name="add-sample-code-for-the-common-helloworld-project"></a>Добавление примера кода для общего проекта helloworld

Общий проект helloworld содержит не зависящие от платформы реализации для вашего кросс-платформенного приложения. Теперь добавьте XAML-код, определяющий пользовательский интерфейс приложения, и реализацию кода C# программной части.

1. В **обозревателе решений** в рамках общего проекта helloworld откройте `MainPage.xaml`.

1. В представлении XAML конструктора вставьте следующий фрагмент кода XAML в тег **Grid** (между `<StackLayout>` и `</StackLayout>`):

   [!code-xml[UI elements](~/samples-cognitive-services-speech-sdk/quickstart/csharp-xamarin/helloworld/helloworld/MainPage.xaml)]

1. В **Обозревателе решений** откройте исходный файл кода программной части `MainPage.xaml.cs`. Он сгруппирован в `MainPage.xaml`.

1. Замените все содержимое следующим фрагментом кода:

   [!code-csharp[Quickstart code](~/samples-cognitive-services-speech-sdk/quickstart/csharp-xamarin/helloworld/helloworld/MainPage.xaml.cs)]

1. В обработчике `OnRecognitionButtonClicked` исходного файла найдите строку `YourSubscriptionKey` и замените ее своим ключом подписки.

1. В обработчике `OnRecognitionButtonClicked` найдите строку `YourServiceRegion` и замените ее названием [региона](regions.md), связанного с вашей подпиской. Например, используйте `westus` для подписки с бесплатной пробной версией.

1. Далее необходимо создать сервис [Xamarin Service](https://docs.microsoft.com/xamarin/android/app-fundamentals/services/creating-a-service/), который используется для запроса разрешений на доступ к микрофону с различных проектов платформы (UWP, Android и iOS). Для этого добавьте в проект helloworld новую папку с именем *Службы* и создайте в ней новый исходный файл C#. Вы можете щелкнуть правой кнопкой мыши папку *Службы* и выбрать пункт **Добавить** > **Новый элемент** > **Файл кода**. Переименуйте файл `IMicrophoneService.cs` и поместите в него весь код из следующего фрагмента:

   [!code-csharp[Quickstart code](~/samples-cognitive-services-speech-sdk/quickstart/csharp-xamarin/helloworld/helloworld/Services/IMicrophoneService.cs)]

## <a name="add-sample-code-for-the-helloworldandroid-project"></a>Добавление примера кода для общего проекта helloworld.Android

Теперь добавьте код C#, определяющий специфическую часть приложения для Android.

1. В **обозревателе решений** в рамках общего проекта helloworld.Android откройте `MainActivity.cs`.

1. Замените все содержимое следующим фрагментом кода:

   [!code-csharp[Quickstart code](~/samples-cognitive-services-speech-sdk/quickstart/csharp-xamarin/helloworld/helloworld.Android/MainActivity.cs)]

1. Затем добавьте реализацию Android для `MicrophoneService`, создав папку *Службы* в рамках проекта helloworld.Android. После этого создайте в нем новый исходный файл C#. Переименуйте файл на `MicrophoneService.cs`. Скопируйте следующий фрагмент кода и вставьте его в файл:

   [!code-csharp[Quickstart code](~/samples-cognitive-services-speech-sdk/quickstart/csharp-xamarin/helloworld/helloworld.Android/Services/MicrophoneService.cs)]

1. После этого откройте `AndroidManifest.xml` в папке *Свойства *. Добавьте следующие настройки разрешения на использование микрофона между `<manifest>` и `</manifest>`:

   ```xml
   <uses-permission android:name="android.permission.RECORD_AUDIO" />
   ```

## <a name="add-sample-code-for-the-helloworldios-project"></a>Добавление примера кода для проекта helloworld.iOS

Теперь добавьте код C#, определяющий специфическую часть приложения для iOS. Кроме того, создайте специфические конфигурации для устройств Apple в проекте helloworld.iOS.

1. В **обозревателе решений** в рамках проекта helloworld.iOS откройте `AppDelegate.cs`.

1. Замените все содержимое следующим фрагментом кода:

   [!code-csharp[Quickstart code](~/samples-cognitive-services-speech-sdk/quickstart/csharp-xamarin/helloworld/helloworld.iOS/AppDelegate.cs)]

1. Затем добавьте реализацию iOS для `MicrophoneService`, создав папку *Службы* в рамках проекта helloworld.iOS. После этого создайте в нем новый исходный файл C#. Переименуйте файл на `MicrophoneService.cs`. Скопируйте следующий фрагмент кода и вставьте его в файл:

   [!code-csharp[Quickstart code](~/samples-cognitive-services-speech-sdk/quickstart/csharp-xamarin/helloworld/helloworld.iOS/Services/MicrophoneService.cs)]

1. Откройте `Info.plist` в проекте helloworld.iOS в текстовом редакторе. Добавьте следующую пару "ключ — значение" в раздел dict:

   <key>NSMicrophoneUsageDescription</key>
   <string>Этот пример приложения требует доступа к микрофону</string>

   > [!NOTE] 
   > Если вы выполняете сборку для устройства iPhone, убедитесь, что `Bundle Identifier` соответствует идентификатору приложения для профиля подготовки устройства. В противном случае произойдет сбой сборки. С iPhoneSimulator вы можете оставить его как есть.

1. Если вы работаете на компьютере с Windows, вам необходимо установить соединение с устройством Mac для сборки, выбрав элементы **Инструменты** > **iOS** > **Связать с Mac**. Следуйте инструкциям мастера Visual Studio, чтобы включить подключение к устройству Mac.

## <a name="add-sample-code-for-the-helloworlduwp-project"></a>Добавление примера кода для проекта helloworld.UWP

Теперь добавьте код C#, определяющий специфическую часть приложения для UWP.

1. В **обозревателе решений** в рамках проекта helloworld.UWP откройте `MainPage.xaml.cs`.

1. Замените все содержимое следующим фрагментом кода:

   [!code-csharp[Quickstart code](~/samples-cognitive-services-speech-sdk/quickstart/csharp-xamarin/helloworld/helloworld.UWP/MainPage.xaml.cs)]

1. Затем добавьте реализацию UWP для `MicrophoneService`, создав папку *Службы* в рамках проекта helloworld.UWP. После этого создайте в нем новый исходный файл C#. Переименуйте файл на `MicrophoneService.cs`. Скопируйте следующий фрагмент кода и вставьте его в файл:

   [!code-csharp[Quickstart code](~/samples-cognitive-services-speech-sdk/quickstart/csharp-xamarin/helloworld/helloworld.UWP/Services/MicrophoneService.cs)]

1. Затем дважды щелкните файл `Package.appxmanifest` в проекте helloworld.UWP в Visual Studio. В разделе **Возможности** убедитесь, что выбран параметр **Микрофон**, и сохраните файл.

   > [!NOTE] 
   > Если отображается предупреждение "Файла сертификата не существует: helloworld.UWP_TemporaryKey.pfx", ознакомьтесь с примером [преобразования речи в текст](quickstart-csharp-uwp.md) для получения дополнительной информации.

1. В строке меню выберите **Файл** > **Сохранить все**, чтобы сохранить изменения.

## <a name="build-and-run-the-uwp-application"></a>Создание и запуск приложения UWP

1. Задайте helloworld.UWP в качестве запускаемого проекта. Щелкните проект helloworld.UWP правой кнопкой мыши и выберите пункт **Сборка**, чтобы выполнить сборку приложения. 

1. Выберите **Отладка** > **Начать отладку** (или нажмите клавишу **F5**), чтобы запустить приложение. Откроется окно **helloworld**.

   ![Пример приложения для распознавания речи в UWP на языке C# — краткое руководство](media/sdk/qs-csharp-xamarin-helloworld-uwp-window.png)

1. Выберите **Включить микрофон**. Когда появится запрос на доступ, выберите **Да**.

   ![Запрос на разрешение доступа к микрофону](media/sdk/qs-csharp-xamarin-uwp-access-prompt.png)

1. Щелкните **Начать распознавание речи** и произнесите фразу или предложение на английском языке в микрофон вашего устройства. Ваша речь передается в службу "Речь" и преобразуется в текст, который появляется в том же окне.

   ![Пользовательский интерфейс распознавания речи](media/sdk/qs-csharp-xamarin-uwp-ui-result.png)

## <a name="build-and-run-the-android-and-ios-applications"></a>Создание и запуск приложений для Android и iOS

Создание и запуск приложений для Android и iOS на устройстве или симуляторе происходит так же, как на UWP. Важно убедиться, что все пакеты SDK установлены правильно, как указано в разделе "Необходимые условия" этой статьи.

## <a name="next-steps"></a>Дополнительная информация

> [!div class="nextstepaction"]
> [Примеры для C# на сайте GitHub](https://aka.ms/csspeech/samples)
