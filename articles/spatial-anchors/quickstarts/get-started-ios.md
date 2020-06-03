---
title: Краткое руководство. Создание приложения iOS
description: В этом кратком руководстве вы узнаете, как создать приложение iOS с помощью службы "Пространственные привязки".
author: craigktreasure
manager: vriveras
services: azure-spatial-anchors
ms.author: crtreasu
ms.date: 02/24/2019
ms.topic: quickstart
ms.service: azure-spatial-anchors
ms.custom: has-adal-ref
ms.openlocfilehash: 542b5168bb70d876d91f8a747a230f2b754568e9
ms.sourcegitcommit: 64fc70f6c145e14d605db0c2a0f407b72401f5eb
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/27/2020
ms.locfileid: "83873106"
---
# <a name="quickstart-create-an-ios-app-with-azure-spatial-anchors-in-either-swift-or-objective-c"></a>Краткое руководство. Создание приложения iOS на языке Swift или Objective-C с помощью службы "Пространственные привязки Azure"

В этом кратком руководстве объясняется, как создать приложение iOS на языке Swift или Objective-C с помощью службы [Пространственные привязки Azure](../overview.md). "Пространственные привязки Azure" — это кроссплатформенная служба разработчика, которая позволяет создавать среды смешанной реальности с применением объектов, не меняющих своего расположения на устройствах с течением времени. По завершении работы с руководством у вас будет приложение ARKit iOS, которое может сохранять и отзывать пространственные привязки.

Вы узнаете, как:

> [!div class="checklist"]
> * создать учетную запись в службе "Пространственные привязки";
> * настроить идентификатор и ключ учетной записи в службе "Пространственные привязки";
> * выполнить развертывание и запуск на устройстве iOS.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Предварительные требования

В рамках этого краткого руководства вам потребуются:

- Компьютер для разработки под управлением macOS с установленной последней версией <a href="https://geo.itunes.apple.com/us/app/xcode/id497799835?mt=12" target="_blank">Xcode</a> и <a href="https://cocoapods.org" target="_blank">CocoaPods</a>.
- Система Git, установленная с помощью HomeBrew:
  1. Введите следующую команду в виде одной строки в окне терминала: `/usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"`. 
  1. Выполните команды `brew install git` и `brew install git-lfs`.
  1. Обновите конфигурацию git с помощью `git lfs install` (для текущего пользователя) или `git lfs install --system` (для всей системы).
- Устройство для разработки на iOS, <a href="https://developer.apple.com/documentation/arkit/verifying_device_support_and_user_permission" target="_blank">совместимое с ARKit</a>.

[!INCLUDE [Create Spatial Anchors resource](../../../includes/spatial-anchors-get-started-create-resource.md)]

## <a name="open-the-sample-project"></a>Открытие примера проекта

Выполните в терминале следующие действия.

[!INCLUDE [Clone Sample Repo](../../../includes/spatial-anchors-clone-sample-repository.md)]

Установите необходимые pod с помощью CocoaPods.

# <a name="swift"></a>[Swift](#tab/openproject-swift)

Перейдите на страницу `iOS/Swift/`.

```bash
cd ./iOS/Swift/
```

# <a name="objective-c"></a>[Objective-C](#tab/openproject-objc)

Перейдите на страницу `iOS/Objective-C/`.

```bash
cd ./iOS/Objective-C/
```

---

Выполните команду `pod install --repo-update`, чтобы установить CocoaPods для проекта.

Теперь откройте файл с расширением `.xcworkspace` в Xcode.

> [!NOTE]
> См. [руководство по устранению неполадок с CocoaPod после обновления до macOS Catalina (10.15)](#cocoapods-issues-on-macos-catalina-1015).

# <a name="swift"></a>[Swift](#tab/openproject-swift)

```bash
open ./SampleSwift.xcworkspace
```

# <a name="objective-c"></a>[Objective-C](#tab/openproject-objc)

```bash
open ./SampleObjC.xcworkspace
```

---

## <a name="configure-account-identifier-and-key"></a>Настройка идентификатора и ключа учетной записи

Следующим шагом является настройка приложения для использования идентификатора вашей учетной записи и ключа учетной записи. Вы скопировали их в текстовый редактор, когда выполняли [настройку ресурсов пространственных привязок](#create-a-spatial-anchors-resource).

# <a name="swift"></a>[Swift](#tab/openproject-swift)

Откройте среду `iOS/Swift/SampleSwift/ViewControllers/BaseViewController.swift`.

Найдите поле `spatialAnchorsAccountKey` и замените `Set me` ключом учетной записи.

Найдите поле `spatialAnchorsAccountId` и замените `Set me` идентификатором учетной записи.

Найдите `startSession()` и добавьте следующую строку, подставив в нее полученный ранее домен учетной записи: `cloudSession!.configuration.accountDomain = "MyAccountDomain";`.

# <a name="objective-c"></a>[Objective-C](#tab/openproject-objc)

Откройте среду `iOS/Objective-C/SampleObjC/BaseViewController.m`.

Найдите поле `SpatialAnchorsAccountKey` и замените `Set me` ключом учетной записи.

Найдите поле `SpatialAnchorsAccountId` и замените `Set me` идентификатором учетной записи.

Найдите `startSession()` и добавьте следующую строку, подставив в нее полученный ранее домен учетной записи: `_cloudSession.configuration.accountDomain = @"MyAccountDomain";`.

---

## <a name="deploy-the-app-to-your-ios-device"></a>Развертывание приложения на устройстве iOS

Подключите устройство iOS к Mac и задайте **активную схему** для устройства iOS.

![Выбор устройства](./media/get-started-ios/select-device.png)

Выберите параметр **Build and then run the current scheme** (Сборка, а затем запуск текущей схемы).

![Развертывание и запуск](./media/get-started-ios/deploy-run.png)

> [!NOTE]
> Если у вас возникнет ошибка `library not found for -lPods-SampleObjC`, скорее всего, вы открыли файл `.xcodeproj` вместо `.xcworkspace`. Откройте файл `.xcworkspace` и повторите попытку.

В Xcode остановите приложение, нажав кнопку **Stop** (Остановить).

## <a name="troubleshooting"></a>Устранение неполадок

### <a name="cocoapods-issues-on-macos-catalina-1015"></a>Проблемы с CocoaPods в macOS Catalina (10.15)

Если вы недавно установили обновление macOS Catalina (10.15), а до этого — CocoaPods, то CocoaPods может перестать работать и не сможет правильно настроить модули pod и файлы проекта `.xcworkspace`. Чтобы устранить эту проблему, необходимо переустановить CocoaPods, выполнив следующие команды:

```shell
brew update
brew install cocoapods --build-from-source
brew link --overwrite cocoapods
```

### <a name="app-crashes-when-deploying-to-ios-1031-from-a-personal-provisioning-profiledeveloper-account"></a>Сбой приложения при развертывании в iOS 10.3.1 из личной учетной записи профиля подготовки или разработчика 

При развертывании приложения iOS в iOS 10.3.1 из личной учетной записи профиля обеспечения или разработчика может появиться следующее сообщение об ошибке: `Library not loaded: @rpath/ADAL...`. 

Чтобы устранить проблему:

- Используйте профиль обеспечения, который не является профилем личной группы (платная учетная запись разработчика).
- Разверните приложение на устройстве iOS под управлением iOS 13.3 или более ранней версии или на компьютере с версией выпуска iOS 13.4 или Beta.
- Дополнительные сведения об этой проблеме см. на сайте [Stack Overflow](https://stackoverflow.com/questions/60015309/running-ios-apps-causes-runtime-error-for-frameworks-code-signature-invalid).


[!INCLUDE [Clean-up section](../../../includes/clean-up-section-portal.md)]

[!INCLUDE [Next steps](../../../includes/spatial-anchors-quickstarts-nextsteps.md)]

> [!div class="nextstepaction"]
> [Руководство. по совместному использованию службы "Пространственные привязки" на разных устройствах](../tutorials/tutorial-share-anchors-across-devices.md)
