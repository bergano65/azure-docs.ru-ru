---
author: ramonarguelles
ms.service: spatial-anchors
ms.topic: include
ms.date: 1/30/2019
ms.author: rgarcia
ms.openlocfilehash: 563c2bd561328561d30acee6910b70d53ef64c6b
ms.sourcegitcommit: 8a59b051b283a72765e7d9ac9dd0586f37018d30
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/20/2019
ms.locfileid: "58305261"
---
## <a name="set-up-your-device"></a>Настройка устройства

### <a name="set-up-an-android-device"></a>Настройка устройства Android

[!INCLUDE [Android Unity Build Settings](spatial-anchors-unity-android-build-settings.md)]

### <a name="set-up-an-ios-device"></a>Настройка устройства iOS

[!INCLUDE [iOS Unity Build Settings](spatial-anchors-unity-ios-build-settings.md)]

## <a name="configure-the-account-identifier-and-key"></a>Настройка идентификатора и ключа учетной записи

В области **Project** (Проект) перейдите к `Assets/AzureSpatialAnchorsPlugin/Examples` и откройте файл сцены `AzureSpatialAnchorsLocalSharedDemo.unity`.

[!INCLUDE [Configure Unity Scene](spatial-anchors-unity-configure-scene.md)]

В области **Inspector** (Инспектор) введите `Sharing Anchors Service url` (из развертывания веб-приложения ASP.NET в Azure) в качестве значения для `Base Sharing Url`, заменив `index.html` на `api/anchors`. Результат будет выглядеть так: `https://<app_name>.azurewebsites.net/api/anchors`.

Сохраните сцену, выбрав **File** > **Save** (Файл > Сохранить).

## <a name="to-deploy-the-app-to-an-android-device"></a>Развертывание приложения на устройстве Android

Войдите в систему на устройстве Android и подключите его к компьютеру с помощью USB-кабеля.

Откройте **Build Settings** (Параметры сборки), выбрав пункты **File** > **Build Settings** (Файл > Параметры сборки).

В разделе **Scenes In Build** (Сцены в сборке) установите флажок рядом со сценой `AzureSpatialAnchorsPlugin/Examples/AzureSpatialAnchorsLocalSharedDemo` и снимите флажки со всех остальных сцен.

Убедитесь, что флажок **Export Project** (Экспорт проекта) не установлен. Выберите **Build And Run** (Сборка и запуск). Появится запрос на сохранение файла `.apk`. Вы можете выбрать для него любое имя.

Следуйте указаниям в приложении. Вы можете выбрать **Create & Share Anchor** (Создать привязку и предоставить к ней общий доступ) или **Locate Shared Anchor** (Найти общую привязку). Первый вариант позволяет создать привязку, которая впоследствии может быть расположена на том же или на другом устройстве. Второй вариант позволяет найти привязки, к которым уже был предоставлен общий доступ, если вы ранее запускали приложение на том же устройстве либо на другом.

## <a name="to-deploy-the-app-to-an-ios-device"></a>Развертывание приложения на устройстве iOS

Откройте **Build Settings** (Параметры сборки), выбрав пункты **File** > **Build Settings** (Файл > Параметры сборки).

В разделе **Scenes In Build** (Сцены в сборке) установите флажок рядом со сценой `AzureSpatialAnchorsPlugin/Examples/AzureSpatialAnchorsLocalSharedDemo` и снимите флажки со всех остальных сцен.

[!INCLUDE [Configure Xcode](spatial-anchors-unity-ios-xcode.md)]

Следуйте указаниям в приложении. Вы можете выбрать **Create & Share Anchor** (Создать привязку и предоставить к ней общий доступ) или **Locate Shared Anchor** (Найти общую привязку). Первый вариант позволяет создать привязку, которая впоследствии может быть расположена на том же или на другом устройстве. Второй вариант позволяет найти привязки, к которым уже был предоставлен общий доступ, если вы ранее запускали приложение на том же устройстве либо на другом.

В Xcode остановите приложение, нажав кнопку **Stop** (Остановить).
