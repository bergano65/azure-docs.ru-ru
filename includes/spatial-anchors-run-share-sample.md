---
author: ramonarguelles
ms.service: spatial-anchors
ms.topic: include
ms.date: 1/30/2019
ms.author: rgarcia
ms.openlocfilehash: 397a8a9b07b4d7a88d0345399ac4abcc3e738a82
ms.sourcegitcommit: 956749f17569a55bcafba95aef9abcbb345eb929
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/29/2019
ms.locfileid: "58631145"
---
## <a name="set-up-your-device"></a>Настройка устройства

В Unity откройте проект в папке `Unity`.

![Окно Unity](./media/spatial-anchors-unity/unity-window.png)

### <a name="set-up-an-android-device"></a>Настройка устройства Android

[!INCLUDE [Android Unity Build Settings](spatial-anchors-unity-android-build-settings.md)]

### <a name="set-up-an-ios-device"></a>Настройка устройства iOS

[!INCLUDE [iOS Unity Build Settings](spatial-anchors-unity-ios-build-settings.md)]

## <a name="configure-the-account-identifier-and-key"></a>Настройка идентификатора и ключа учетной записи

В области **Project** (Проект) перейдите к `Assets/AzureSpatialAnchorsPlugin/Examples` и откройте файл сцены `AzureSpatialAnchorsLocalSharedDemo.unity`.

[!INCLUDE [Configure Unity Scene](spatial-anchors-unity-configure-scene.md)]

В области **Inspector** (Инспектор) введите `Sharing Anchors Service url` (из развертывания веб-приложения ASP.NET в Azure) в качестве значения для `Base Sharing Url`, заменив `index.html` на `api/anchors`. Результат будет выглядеть так: `https://<app_name>.azurewebsites.net/api/anchors`.

Сохраните сцену, выбрав **File** > **Save** (Файл > Сохранить).

## <a name="deploy-to-your-device"></a>Развертывание на устройстве

### <a name="deploy-to-android-device"></a>Развертывание на устройстве с Android

Войдите в систему на устройстве Android и подключите его к компьютеру с помощью USB-кабеля.

Откройте **Build Settings** (Параметры сборки), выбрав пункты **File** > **Build Settings** (Файл > Параметры сборки).

В разделе **Scenes In Build** (Сцены в сборке) установите флажок рядом со сценой `AzureSpatialAnchorsPlugin/Examples/AzureSpatialAnchorsLocalSharedDemo` и снимите флажки со всех остальных сцен.

Убедитесь, что флажок **Export Project** (Экспорт проекта) не установлен. Выберите **Build And Run** (Сборка и запуск). Появится запрос на сохранение файла `.apk`. Вы можете выбрать для него любое имя.

Следуйте указаниям в приложении. Вы можете выбрать **Create & Share Anchor** (Создать привязку и предоставить к ней общий доступ) или **Locate Shared Anchor** (Найти общую привязку). Первый сценарий позволяет создать привязку, которая впоследствии может быть обнаружена на том же или на другом устройстве. Второй сценарий позволяет найти привязки, к которым уже был предоставлен общий доступ, если вы ранее запускали приложение на том же устройстве либо на другом. Когда вы выберете сценарий, в приложении отобразятся дальнейшие пошаговые инструкции. Например, вам будет предложено переместить устройство вокруг, чтобы собрать информацию об окружающей среде. Затем вам нужно будет разместить привязку к окружающей среде, подождать, пока она загрузится, и так далее.

### <a name="deploy-to-an-ios-device"></a>Развертывание на устройстве с iOS

Откройте **Build Settings** (Параметры сборки), выбрав пункты **File** > **Build Settings** (Файл > Параметры сборки).

В разделе **Scenes In Build** (Сцены в сборке) установите флажок рядом со сценой `AzureSpatialAnchorsPlugin/Examples/AzureSpatialAnchorsLocalSharedDemo` и снимите флажки со всех остальных сцен.

[!INCLUDE [Configure Xcode](spatial-anchors-unity-ios-xcode.md)]

Следуйте указаниям в приложении. Вы можете выбрать **Create & Share Anchor** (Создать привязку и предоставить к ней общий доступ) или **Locate Shared Anchor** (Найти общую привязку). Первый сценарий позволяет создать привязку, которая впоследствии может быть обнаружена на том же или на другом устройстве. Второй сценарий позволяет найти привязки, к которым уже был предоставлен общий доступ, если вы ранее запускали приложение на том же устройстве либо на другом. Когда вы выберете сценарий, в приложении отобразятся дальнейшие пошаговые инструкции. Например, вам будет предложено переместить устройство вокруг, чтобы собрать информацию об окружающей среде. Затем вам нужно будет разместить привязку к окружающей среде, подождать, пока она загрузится, и так далее.

В Xcode остановите приложение, нажав кнопку **Stop** (Остановить).
