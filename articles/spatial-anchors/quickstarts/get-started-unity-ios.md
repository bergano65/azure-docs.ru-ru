---
title: Краткое руководство по созданию приложения Unity в iOS с использованием Пространственных привязок Azure | Документация Майкрософт
description: В этом кратком руководстве вы узнаете, как создать приложение iOS с Unity с помощью Пространственных привязок.
author: craigktreasure
manager: aliemami
services: azure-spatial-anchors
ms.author: crtreasu
ms.date: 02/24/2019
ms.topic: quickstart
ms.service: azure-spatial-anchors
ms.openlocfilehash: fa9f613bfbb3028715629f1d27b90531bd1dac3a
ms.sourcegitcommit: fdd6a2927976f99137bb0fcd571975ff42b2cac0
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/27/2019
ms.locfileid: "56959686"
---
# <a name="quickstart-create-an-ios-unity-app-with-azure-spatial-anchors"></a>Краткое руководство. Создание приложения Unity в iOS с помощью Пространственных привязок Azure

Краткое руководство по созданию приложения Unity в iOS с помощью [Пространственных привязок Azure](../overview.md). "Пространственные привязки Azure" — это кроссплатформенная служба разработчика, которая позволяет создавать среды смешанной реальности с применением объектов, не меняющих своего расположения на устройствах с течением времени. После завершения вы получите приложение iOS ARKit, разработанное с использованием Unity, которое может сохранять и повторно вызывать пространственные привязки.

Вы узнаете, как:

> [!div class="checklist"]
> * создать учетную запись в службе "Пространственные привязки";
> * настроить параметры сборки Unity;
> * скачать и импортировать модуль Unity ARKit;
> * настроить идентификатор и ключ учетной записи в службе "Пространственные привязки";
> * экспортировать проект Xcode;
> * выполнить развертывание и запуск на устройстве iOS.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Предварительные требования

В рамках этого краткого руководства вам потребуются:

- Компьютер macOS с установленными компонентами <a href="https://unity3d.com/get-unity/download" target="_blank">Unity 2018.3 или более поздней версии</a>, <a href="https://geo.itunes.apple.com/us/app/xcode/id497799835?mt=12" target="_blank">Xcode 10</a> и <a href="https://cocoapods.org" target="_blank">CocoaPods</a>.
- Устройство для разработки на iOS, <a href="https://developer.apple.com/documentation/arkit/verifying_device_support_and_user_permission" target="_blank">совместимое с ARKit</a>.

[!INCLUDE [Create Spatial Anchors resource](../../../includes/spatial-anchors-get-started-create-resource.md)]

## <a name="open-the-sample-project-in-unity"></a>Открытие примера проекта в Unity

[!INCLUDE [Clone Sample Repo](../../../includes/spatial-anchors-clone-sample-repository.md)]

[!INCLUDE [iOS Unity Build Settings](../../../includes/spatial-anchors-unity-ios-build-settings.md)]

## <a name="configure-account-identifier-and-key"></a>Настройка идентификатора и ключа учетной записи

В области **Project** (Проект) перейдите к `Assets/AzureSpatialAnchorsPlugin/Examples` и откройте файл сцены `AzureSpatialAnchorsBasicDemo.unity`.

[!INCLUDE [Configure Unity Scene](../../../includes/spatial-anchors-unity-configure-scene.md)]

Сохраните сцену, выбрав **File** -> **Save** (Файл > Сохранить).

## <a name="export-the-xcode-project"></a>Экспорт проекта Xcode

[!INCLUDE [Export Unity Project](../../../includes/spatial-anchors-unity-export-project-snip.md)]

[!INCLUDE [Configure Xcode](../../../includes/spatial-anchors-unity-ios-xcode.md)]

Следуйте инструкциям в программе для размещения и отзыва привязки.

> [!NOTE]
> Если во время запуска приложения вы не видите камеру как фон (к примеру, вы видите вместо этого пустой, синий фон или фон с другой текстурой), то вам нужно повторно импортировать файлы в Unity. Остановите приложение. В верхнем меню в Unity выберите **Assets -> Re-import all** (Ресурсы -> Повторно импортировать все). Затем снова запустите приложение.

В Xcode остановите приложение, нажав кнопку **Stop** (Остановить).

[!INCLUDE [Clean-up section](../../../includes/clean-up-section-portal.md)]

[!INCLUDE [Next steps](../../../includes/spatial-anchors-quickstarts-nextsteps.md)]

> [!div class="nextstepaction"]
> [Руководство. Совместное использование службы "Пространственные привязки" на разных устройствах](../tutorials/tutorial-share-anchors-across-devices.md)
