---
author: ramonarguelles
ms.service: spatial-anchors
ms.topic: include
ms.date: 8/27/2020
ms.author: rgarcia
ms.openlocfilehash: 8e3f58a5a88495263396c4e915a6363ad065c202
ms.sourcegitcommit: c52e50ea04dfb8d4da0e18735477b80cafccc2cf
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/08/2020
ms.locfileid: "89570190"
---
## <a name="android"></a>[Android](#tab/Android)

Пример Java для Android поддерживает совместное использование на разных устройствах.
Откройте файл `SharedActivity.java` из папки с примерами в Android Studio. Введите URL-адрес, полученный на предыдущем шаге (из развертывания ASP.NET веб-приложения Azure) в качестве значения для `SharingAnchorsServiceUrl` в файле `SharedActivity.java`. Измените `index.html` в URL-адресе на `api/anchors`. Результат будет выглядеть так: `https://<app_name>.azurewebsites.net/api/anchors`.

[!INCLUDE [Run shared sample](spatial-anchors-run-sample.md)]

## <a name="ios"></a>[iOS](#tab/iOS)

Пример Objective-C для iOS поддерживает совместное использование на разных устройствах.
Откройте файл `SharedDemoViewController.m` в папке с примерами. Введите URL-адрес, полученный на предыдущем шаге (из развертывания ASP.NET веб-приложения Azure) в качестве значения для `SharingAnchorsServiceUrl` в файле `SharedDemoViewController.m`. Измените `index.html` в URL-адресе на `api/anchors`. Результат будет выглядеть так: `https://<app_name>.azurewebsites.net/api/anchors`.

[!INCLUDE [Run shared sample](spatial-anchors-run-sample.md)]

## <a name="xamarin"></a>[Xamarin](#tab/Xamarin)

Примеры для Xamarin Android и iOS поддерживают совместное использование на разных устройствах.
Откройте файл `AccountDetails.cs` в папке с примерами. Введите URL-адрес, полученный на предыдущем шаге (из развертывания ASP.NET веб-приложения Azure) в качестве значения для `AnchorSharingServiceUrl` в файле `AccountDetails.cs`. Измените `index.html` в URL-адресе на `api/anchors`. Результат будет выглядеть так: `https://<app_name>.azurewebsites.net/api/anchors`.

[!INCLUDE [Run shared sample](spatial-anchors-run-sample.md)]

## <a name="unity"></a>[Unity](#tab/Unity)

[!INCLUDE [Open Unity Project](spatial-anchors-open-unity-project.md)]

### <a name="set-up-an-android-device"></a>Настройка устройства Android

[!INCLUDE [Android Unity Build Settings](spatial-anchors-unity-android-build-settings.md)]

### <a name="set-up-an-ios-device"></a>Настройка устройства iOS

[!INCLUDE [iOS Unity Build Settings](spatial-anchors-unity-ios-build-settings.md)]

[!INCLUDE [Configure Unity Scene](spatial-anchors-unity-configure-scene.md)]

На панели **Проект** перейдите к `Assets\AzureSpatialAnchors.Examples\Resources`. Выберите `SpatialAnchorSamplesConfig`. Затем в области **Inspector** (Инспектор) введите `Sharing Anchors Service url` (из развертывания веб-приложения ASP.NET в Azure) в качестве значения для `Base Sharing Url`, заменив `index.html` на `api/anchors`. Результат будет выглядеть так: `https://<app_name>.azurewebsites.net/api/anchors`.

Сохраните сцену, выбрав **File** > **Save** (Файл > Сохранить).

## <a name="deploy-to-your-device"></a>Развертывание на устройстве

### <a name="deploy-to-android-device"></a>Развертывание на устройстве с Android

Войдите в систему на устройстве Android и подключите его к компьютеру с помощью USB-кабеля.

Откройте **Build Settings** (Параметры сборки), выбрав пункты **File** > **Build Settings** (Файл > Параметры сборки).

В разделе **Scenes In Build** (Сцены в сборке) убедитесь, что возле всех сцен установлен флажок.

Убедитесь, что флажок **Export Project** (Экспорт проекта) не установлен. Выберите **Build And Run** (Сборка и запуск). Появится запрос на сохранение файла `.apk`. Вы можете выбрать для него любое имя.

[!INCLUDE [Run shared sample](spatial-anchors-run-sample.md)]

### <a name="deploy-to-an-ios-device"></a>Развертывание на устройстве с iOS

Откройте **Build Settings** (Параметры сборки), выбрав пункты **File** > **Build Settings** (Файл > Параметры сборки).

В разделе **Scenes In Build** (Сцены в сборке) убедитесь, что возле всех сцен установлен флажок.

[!INCLUDE [Configure Xcode](spatial-anchors-unity-ios-xcode.md)]

[!INCLUDE [Run shared sample](spatial-anchors-run-sample.md)]

В Xcode остановите приложение, нажав кнопку **Stop** (Остановить).
