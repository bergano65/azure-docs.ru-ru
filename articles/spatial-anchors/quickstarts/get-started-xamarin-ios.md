---
title: Краткое руководство. Создание приложения Xamarin.iOS
description: В этом кратком руководстве вы узнаете, как создать приложение для iOS с Xamarin, используя Пространственные привязки.
author: craigktreasure
manager: vriveras
services: azure-spatial-anchors
ms.author: crtreasu
ms.date: 07/22/2019
ms.topic: quickstart
ms.service: azure-spatial-anchors
ms.openlocfilehash: 12fb644430d4bfd6444c6bbf09d37b4662ca6fd1
ms.sourcegitcommit: 85eb6e79599a78573db2082fe6f3beee497ad316
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/05/2020
ms.locfileid: "87810128"
---
# <a name="quickstart-create-a-xamarin-ios-app-with-azure-spatial-anchors"></a>Краткое руководство. Создание приложение для iOS с Xamarin с помощью Пространственных привязок Azure

В этом кратком руководстве описано, как создать приложение для iOS с Xamarin, используя службу [Пространственные привязки Azure](../overview.md). "Пространственные привязки Azure" — это кроссплатформенная служба разработчика, которая позволяет создавать среды смешанной реальности с применением объектов, не меняющих своего расположения на устройствах с течением времени. По завершению у вас будет приложение для iOS, которое может сохранять и вызывать пространственную привязку.

Вы узнаете, как:

> [!div class="checklist"]
> * создать учетную запись в службе "Пространственные привязки";
> * настроить идентификатор и ключ учетной записи в службе "Пространственные привязки";
> * выполнить развертывание и запуск на устройстве iOS.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Предварительные требования

В рамках этого краткого руководства вам потребуются:
- Mac под управлением macOS High Sierra (10.13) или более поздней версии с:
  - последней версией Xcode и iOS SDK, установленной из [App Store](https://itunes.apple.com/us/app/xcode/id497799835?mt=12);
  - последней версией <a href="https://docs.microsoft.com/visualstudio/mac/installation?view=vsmac-2019" target="_blank">Visual Studio для Mac 8.1+</a>;
  - <a href="https://git-scm.com/download/mac" target="_blank">Git для macOS</a>.
  - <a href="https://git-lfs.github.com/">Git LFS</a>.

[!INCLUDE [Create Spatial Anchors resource](../../../includes/spatial-anchors-get-started-create-resource.md)]

## <a name="open-the-sample-project"></a>Открытие примера проекта

[!INCLUDE [Clone Sample Repo](../../../includes/spatial-anchors-clone-sample-repository.md)]

Откройте `Xamarin/SampleXamarin.sln` в Visual Studio.

## <a name="configure-account-identifier-and-key"></a>Настройка идентификатора и ключа учетной записи

Следующим шагом является настройка приложения для использования идентификатора вашей учетной записи и ключа учетной записи. Вы скопировали их в текстовый редактор, когда выполняли [настройку ресурсов пространственных привязок](#create-a-spatial-anchors-resource).

Откройте среду `Xamarin/SampleXamarin.Common/AccountDetails.cs`.

Найдите поле `SpatialAnchorsAccountKey` и замените `Set me` ключом учетной записи.

Найдите поле `SpatialAnchorsAccountId` и замените `Set me` идентификатором учетной записи.

Найдите поле `SpatialAnchorsAccountDomain` и замените `Set me` доменом учетной записи.

## <a name="deploy-the-app-to-your-ios-device"></a>Развертывание приложения на устройстве iOS

Включите устройство iOS, войдите в систему и подключите его к компьютеру с помощью USB-кабеля.

Задайте для запускаемого проекта значение **SampleXamarin.iOS**, измените **Конфигурацию решения** на **Запустить**, и выберите устройство, на котором вы хотите выполнить развертывание из раскрывающегося списка селектора устройства.

![Настройка Visual Studio](./media/get-started-xamarin-iOS/visual-studio-macos-configuration.jpg)

Выберите **Выполнить** > **Запустить без отладки**, чтобы развернуть и запустить свое приложение.

В приложении выберите **Базовый**, чтобы запустить демонстрацию, и следуйте инструкциям для размещения и вызова привязки.

> ![Снимок экрана 1](./media/get-started-xamarin-ios/screenshot-1.jpg)
> ![Снимок экрана 2](./media/get-started-xamarin-ios/screenshot-2.jpg)
> ![Снимок экрана 3](./media/get-started-xamarin-ios/screenshot-3.jpg)

[!INCLUDE [Clean-up section](../../../includes/clean-up-section-portal.md)]

[!INCLUDE [Next steps](../../../includes/spatial-anchors-quickstarts-nextsteps.md)]

> [!div class="nextstepaction"]
> [Руководство. по совместному использованию службы "Пространственные привязки" на разных устройствах](../tutorials/tutorial-share-anchors-across-devices.md)
