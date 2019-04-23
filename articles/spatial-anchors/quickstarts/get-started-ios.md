---
title: Краткое руководство по созданию приложения iOS с использованием службы "Пространственные привязки Azure" |Документация Майкрософт
description: В этом кратком руководстве вы узнаете, как создать приложение iOS с помощью службы "Пространственные привязки".
author: craigktreasure
manager: aliemami
services: azure-spatial-anchors
ms.author: crtreasu
ms.date: 02/24/2019
ms.topic: quickstart
ms.service: azure-spatial-anchors
ms.openlocfilehash: b8ee1bdfece35d774cdc79595a495b0b3719a836
ms.sourcegitcommit: bf509e05e4b1dc5553b4483dfcc2221055fa80f2
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/22/2019
ms.locfileid: "59996651"
---
# <a name="quickstart-create-an-ios-app-with-azure-spatial-anchors-in-either-swift-or-objective-c"></a>Краткое руководство. Создание приложения iOS на языке Swift или Objective-C с помощью службы "Пространственные привязки Azure"

В этом кратком руководстве объясняется, как создать приложение iOS на языке Swift или Objective-C с помощью службы [Пространственные привязки Azure](../overview.md). "Пространственные привязки Azure" — это кроссплатформенная служба разработчика, которая позволяет создавать среды смешанной реальности с применением объектов, не меняющих своего расположения на устройствах с течением времени. По завершении работы с руководством у вас будет приложение ARKit iOS, которое может сохранять и отзывать пространственные привязки.

Вы узнаете, как:

> [!div class="checklist"]
> * создавать учетную запись в службе "Пространственные привязки";
> * настроить идентификатор и ключ учетной записи в службе "Пространственные привязки";
> * выполнять развертывание и запуск на устройстве iOS.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Предварительные требования

В рамках этого краткого руководства вам потребуются:

- Компьютер для разработки под управлением macOS и установленными <a href="https://geo.itunes.apple.com/us/app/xcode/id497799835?mt=12" target="_blank">Xcode 10+</a> и <a href="https://cocoapods.org" target="_blank">CocoaPods</a>.
- Система Git, установленная с помощью HomeBrew. В терминале ведите такую команду в одну строку: `/usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"`. Затем выполните `brew install git`.
- Устройство для разработки на iOS, <a href="https://developer.apple.com/documentation/arkit/verifying_device_support_and_user_permission" target="_blank">совместимое с ARKit</a>.

[!INCLUDE [Create Spatial Anchors resource](../../../includes/spatial-anchors-get-started-create-resource.md)]

## <a name="open-the-sample-project"></a>Открытие примера проекта

Выполните в терминале следующие действия.

[!INCLUDE [Clone Sample Repo](../../../includes/spatial-anchors-clone-sample-repository.md)]

Установите необходимые pod с помощью CocoaPods.

# <a name="swifttabopenproject-swift"></a>[Swift](#tab/openproject-swift)

Перейдите на страницу `iOS/Swift/`.

```bash
cd ./iOS/Swift/
```

# <a name="objective-ctabopenproject-objc"></a>[Objective-C](#tab/openproject-objc)

Перейдите на страницу `iOS/Objective-C/`.

```bash
cd ./iOS/Objective-C/
```

***

Выполните команду `pod install --repo-update`, чтобы установить CocoaPods для проекта.

Теперь откройте файл с расширением `.xcworkspace` в Xcode.

# <a name="swifttabopenproject-swift"></a>[Swift](#tab/openproject-swift)

```bash
open ./SampleSwift.xcworkspace
```

# <a name="objective-ctabopenproject-objc"></a>[Objective-C](#tab/openproject-objc)

```bash
open ./SampleObjC.xcworkspace
```

***

## <a name="configure-account-identifier-and-key"></a>Настройка идентификатора и ключа учетной записи

Следующим шагом является настройка приложения для использования идентификатора вашей учетной записи и ключа учетной записи. Вы скопировали их в текстовый редактор, когда выполняли [настройку ресурсов пространственных привязок](#create-a-spatial-anchors-resource).

# <a name="swifttabopenproject-swift"></a>[Swift](#tab/openproject-swift)

Откройте `iOS/Swift/SampleSwift/ViewControllers/BaseViewController.swift`.

Найдите поле `spatialAnchorsAccountKey` и замените `Set me` ключом учетной записи.

Найдите поле `spatialAnchorsAccountId` и замените `Set me` идентификатором учетной записи.

# <a name="objective-ctabopenproject-objc"></a>[Objective-C](#tab/openproject-objc)

Откройте `iOS/Objective-C/SampleObjC/BaseViewController.m`.

Найдите поле `SpatialAnchorsAccountKey` и замените `Set me` ключом учетной записи.

Найдите поле `SpatialAnchorsAccountId` и замените `Set me` идентификатором учетной записи.

***

## <a name="deploy-the-app-to-your-ios-device"></a>Развертывание приложения на устройстве iOS

Подключите устройство iOS к Mac и задайте **активную схему** для устройства iOS.

![Выбор устройства](./media/get-started-ios/select-device.png)

Выберите параметр **Build and then run the current scheme** (Сборка, а затем запуск текущей схемы).

![Развертывание и запуск](./media/get-started-ios/deploy-run.png)

> [!NOTE]
> Если у вас возникнет ошибка `library not found for -lPods-SampleObjC`, скорее всего, вы открыли файл `.xcodeproj` вместо `.xcworkspace`. Откройте файл `.xcworkspace` и повторите попытку.

В Xcode остановите приложение, нажав кнопку **Stop** (Остановить).

[!INCLUDE [Clean-up section](../../../includes/clean-up-section-portal.md)]

[!INCLUDE [Next steps](../../../includes/spatial-anchors-quickstarts-nextsteps.md)]

> [!div class="nextstepaction"]
> [Руководство по совместному использованию службы "Пространственные привязки" на разных устройствах](../tutorials/tutorial-share-anchors-across-devices.md)
