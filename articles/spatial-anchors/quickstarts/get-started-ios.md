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
ms.openlocfilehash: bb64e2b633957ca92636a03333be40aa275dfd25
ms.sourcegitcommit: 50ea09d19e4ae95049e27209bd74c1393ed8327e
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/26/2019
ms.locfileid: "56870250"
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
- Устройство для разработки на iOS, <a href="https://developer.apple.com/documentation/arkit/verifying_device_support_and_user_permission" target="_blank">совместимое с ARKit</a>.

[!INCLUDE [Create Spatial Anchors resource](../../../includes/spatial-anchors-get-started-create-resource.md)]

## <a name="open-the-sample-project"></a>Открытие примера проекта

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

На следующем шаге для настройки приложения вам потребуется идентификатор и ключ учетной записи, зарегистрированные при настройке ресурса службы "Пространственные привязки".

# <a name="swifttabopenproject-swift"></a>[Swift](#tab/openproject-swift)

Откройте `iOS/Swift/SampleSwift/ViewController.swift`.

Найдите поле `SpatialAnchorsAccountKey` и замените `Set me` ключом учетной записи.

Найдите поле `SpatialAnchorsAccountId` и замените `Set me` идентификатором учетной записи.

# <a name="objective-ctabopenproject-objc"></a>[Objective-C](#tab/openproject-objc)

Откройте `iOS/Objective-C/SampleObjC/ViewController.m`.

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
> [Руководство. Совместное использование службы "Пространственные привязки" на разных устройствах](../tutorials/tutorial-share-anchors-across-devices.md)
