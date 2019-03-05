---
author: ramonarguelles
ms.service: spatial-anchors
ms.topic: include
ms.date: 1/30/2019
ms.author: rgarcia
ms.openlocfilehash: 5f1e0153b1f919bc9d7e921d2a1b3ae745b2b01f
ms.sourcegitcommit: e88188bc015525d5bead239ed562067d3fae9822
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/24/2019
ms.locfileid: "56753522"
---
## <a name="open-the-sample-project-in-unity"></a>Открытие примера проекта в Unity

[!INCLUDE [Clone Sample Repo](spatial-anchors-clone-sample-repository.md)]

## <a name="to-set-up-for-an-android-device"></a>Настройка для устройства Android

[!INCLUDE [Android Unity Build Settings](spatial-anchors-unity-android-build-settings.md)]

## <a name="to-set-up-for-an-ios-device"></a>Настройка для устройства iOS

[!INCLUDE [iOS Unity Build Settings](spatial-anchors-unity-ios-build-settings.md)]

## <a name="configure-account-identifier-and-key"></a>Настройка идентификатора и ключа учетной записи

В области **Project** (Проект) перейдите в папку `Assets/AzureSpatialAnchorsPlugin/Examples` и откройте файл сцены `AzureSpatialAnchorsLocalSharedDemo.unity`.

[!INCLUDE [Configure Unity Scene](spatial-anchors-unity-configure-scene.md)]

Кроме того, в области **Inspector** (Инспектор) введите `Sharing Anchors Service url` (из развертывания веб-приложения ASP.NET в Azure) в качестве значения для `Base Sharing Url`, заменив `index.html` на `api/anchors`. Результат будет выглядеть так: `https://<app_name>.azurewebsites.net/api/anchors`.

Сохраните сцену, выбрав **File** -> **Save** (Файл > Сохранить).

## <a name="to-deploy-to-an-android-device"></a>Для развертывания на устройстве с Android

Включите устройство с Android, войдите и подключите его к ПК с помощью USB-кабеля.

Откройте **Build Settings** (Параметры сборки), выбрав пункты **File** -> **Build Settings** (Файл > Параметры сборки).

В разделе **Scenes In Build** (Сцены в сборке) установите флажок рядом со сценой `AzureSpatialAnchorsPlugin/Examples/AzureSpatialAnchorsLocalSharedDemo` и снимите флажки со всех остальных сцен.

Убедитесь, что флажок **Export Project** (Экспорт проекта) не установлен. Щелкните **Build And Run** (Сборка и запуск). Вам будет предложено сохранить файл `.apk`, для которого можно выбрать любое имя.

Следуйте указаниям в приложении. Вы можете выбрать **Create & Share Anchor** (Создать привязку и предоставить к ней общий доступ) или **Locate Shared Anchor** (Найти общую привязку). Первый вариант позволяет создать привязку, которая впоследствии может быть расположена на том же или на другом устройстве. Второй вариант позволяет найти привязки, к которым ранее был предоставлен общий доступ, если вы ранее запускали приложение на том же устройстве либо на другом.

## <a name="to-deploy-to-an-ios-device"></a>Для развертывания на устройстве с iOS

Откройте **Build Settings** (Параметры сборки), выбрав пункты **File** -> **Build Settings** (Файл > Параметры сборки).

В разделе **Scenes In Build** (Сцены в сборке) установите флажок рядом со сценой `AzureSpatialAnchorsPlugin/Examples/AzureSpatialAnchorsLocalSharedDemo` и снимите флажки со всех остальных сцен.

[!INCLUDE [Configure Xcode](spatial-anchors-unity-ios-xcode.md)]

Следуйте указаниям в приложении. Вы можете выбрать **Create & Share Anchor** (Создать привязку и предоставить к ней общий доступ) или **Locate Shared Anchor** (Найти общую привязку). Первый вариант позволяет создать привязку, которая впоследствии может быть расположена на том же или на другом устройстве. Второй вариант позволяет найти привязки, к которым ранее был предоставлен общий доступ, если вы ранее запускали приложение на том же устройстве либо на другом.

В Xcode остановите приложение, нажав кнопку **Stop** (Остановить).
