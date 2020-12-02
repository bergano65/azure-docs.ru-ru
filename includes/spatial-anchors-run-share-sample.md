---
author: msftradford
ms.service: spatial-anchors
ms.topic: include
ms.date: 11/20/2020
ms.author: parkerra
ms.openlocfilehash: 9554e04c82299016076f09f85f604af10a94ab4a
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/26/2020
ms.locfileid: "96185476"
---
## <a name="android"></a>[Android](#tab/Android)

Пример Java для Android поддерживает совместное использование на разных устройствах.

В Android Studio откройте файл *SharedActivity.java* в папке примеров. 

Введите URL-адрес, скопированный на предыдущем шаге (из развертывания ASP.NET веб-приложения Azure), в качестве значения для `SharingAnchorsServiceUrl` в файле *SharedActivity.java*. 

Измените `index.html` в URL-адресе на `api/anchors`. Результат будет выглядеть так: `https://<app_name>.azurewebsites.net/api/anchors`.

[!INCLUDE [Run shared sample](spatial-anchors-deploy-sample.md)]

[!INCLUDE [Run shared sample](spatial-anchors-run-sample.md)]

## <a name="ios"></a>[iOS](#tab/iOS)

Пример Objective-C для iOS поддерживает совместное использование на разных устройствах.

Откройте файл *SharedDemoViewController.m* в папке примеров. 

Введите URL-адрес, полученный на предыдущем шаге (из развертывания ASP.NET веб-приложения Azure), в качестве значения для `SharingAnchorsServiceUrl` в файле *SharedDemoViewController.m*. 

Измените `index.html` в URL-адресе на `api/anchors`. Результат будет выглядеть так: `https://<app_name>.azurewebsites.net/api/anchors`.

Развертывание приложения на устройстве 

После запуска приложения выберите параметр **Tap to start Shared Demo** (Коснитесь, чтобы начать общую демонстрацию), а затем следуйте инструкциям из приложения. Вы можете выбрать **Tap to locate Anchor by its anchor number** (Коснитесь, чтобы найти привязку по ее номеру) или **Tap to create Anchor and save it to the service** (Коснитесь, чтобы создать привязку и сохранить ее в службе).

[!INCLUDE [Run shared sample](spatial-anchors-run-sample.md)]

## <a name="xamarin"></a>[Xamarin](#tab/Xamarin)

Примеры для Xamarin Android и iOS поддерживают совместное использование на разных устройствах.

Откройте файл *AccountDetails.cs* в папке примеров. 

Введите URL-адрес, полученный на предыдущем шаге (из развертывания ASP.NET веб-приложения Azure), в качестве значения для `AnchorSharingServiceUrl` в файле *AccountDetails.cs*. 

Измените `index.html` в URL-адресе на `api/anchors`. Результат будет выглядеть так: `https://<app_name>.azurewebsites.net/api/anchors`.

[!INCLUDE [Run shared sample](spatial-anchors-deploy-sample.md)]

[!INCLUDE [Run shared sample](spatial-anchors-run-sample.md)]

## <a name="unity"></a>[Unity](#tab/Unity)

[!INCLUDE [Open Unity Project](spatial-anchors-open-unity-project.md)]

### <a name="set-up-an-android-device"></a>Настройка устройства Android

[!INCLUDE [Android Unity Build Settings](spatial-anchors-unity-android-build-settings.md)]

### <a name="set-up-an-ios-device"></a>Настройка устройства iOS

[!INCLUDE [iOS Unity Build Settings](spatial-anchors-unity-ios-build-settings.md)]

[!INCLUDE [Configure Unity Scene](spatial-anchors-unity-configure-scene.md)]

В области **Проект** перейдите к `Assets\AzureSpatialAnchors.Examples\Resources`. 

Выберите **SpatialAnchorSamplesConfig**. Затем в области **Inspector** (Инспектор) введите URL-адрес `Sharing Anchors Service` (из развертывания веб-приложения ASP.NET в Azure) в качестве значения для `Base Sharing Url`. Замените `index.html` на `api/anchors`. Результат будет выглядеть так: `https://<app_name>.azurewebsites.net/api/anchors`.

Сохраните сцену, выбрав **File** > **Save** (Файл > Сохранить).

## <a name="deploy-to-your-device"></a>Развертывание на устройстве

### <a name="deploy-to-an-android-device"></a>Развертывание на устройстве с Android

Войдите в систему на устройстве Android и подключите его к компьютеру с помощью USB-кабеля.

Откройте **Build Settings** (Параметры сборки), выбрав пункты **File** > **Build Settings** (Файл > Параметры сборки).

Убедитесь, что в разделе **Scenes In Build** (Сцены в сборке) возле каждой сцены установлен флажок.

Убедитесь, что флажок **Export Project** (Экспорт проекта) не установлен. Выберите **Build And Run** (Сборка и запуск). Появится запрос на сохранение файла *APK*. Вы можете выбрать для него любое имя.

[!INCLUDE [Run shared sample](spatial-anchors-run-sample.md)]

### <a name="deploy-to-an-ios-device"></a>Развертывание на устройстве с iOS

Откройте **Build Settings** (Параметры сборки), выбрав пункты **File** > **Build Settings** (Файл > Параметры сборки).

Убедитесь, что в разделе **Scenes In Build** (Сцены в сборке) возле каждой сцены установлен флажок.

[!INCLUDE [Configure Xcode](spatial-anchors-unity-ios-xcode.md)]

[!INCLUDE [Run shared sample](spatial-anchors-run-sample.md)]

В Xcode остановите приложение, нажав кнопку **Stop** (Остановить).
