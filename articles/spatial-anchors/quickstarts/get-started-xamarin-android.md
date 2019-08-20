---
title: Краткое руководство по созданию приложения Android в Xamarin с использованием службы "Пространственные привязки Azure" |Документация Майкрософт
description: В этом кратком руководстве вы узнаете, как создать приложение Android с Xamarin с помощью Пространственных привязок.
author: craigktreasure
manager: virivera
services: azure-spatial-anchors
ms.author: crtreasu
ms.date: 07/22/2019
ms.topic: quickstart
ms.service: azure-spatial-anchors
ms.openlocfilehash: 0958961f1e66be130dd2be816f6002dd34465dc6
ms.sourcegitcommit: 13a289ba57cfae728831e6d38b7f82dae165e59d
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/09/2019
ms.locfileid: "68931425"
---
# <a name="quickstart-create-a-xamarin-android-app-with-azure-spatial-anchors"></a>Краткое руководство. Создание приложения Android в Xamarin с помощью Пространственных привязок Azure

Краткое руководство по созданию приложения Android в Xamarin с помощью [Пространственных привязок Azure](../overview.md). "Пространственные привязки Azure" — это кроссплатформенная служба разработчика, которая позволяет создавать среды смешанной реальности с применением объектов, не меняющих своего расположения на устройствах с течением времени. После завершения вы получите приложение Android, которое может сохранять и отзывать пространственные привязки.

Вы узнаете, как:

> [!div class="checklist"]
> * создавать учетную запись в службе "Пространственные привязки";
> * настроить идентификатор и ключ учетной записи в службе "Пространственные привязки";
> * развертывать и запускать на устройстве Android.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Предварительные требования

В рамках этого краткого руководства вам потребуются:
- Компьютер с ОС Windows или MacOS:
  - При использовании Windows:
    - Актуальная версия <a href="https://www.visualstudio.com/downloads/" target="_blank">Visual Studio 2019 16.2+</a>.
    - <a href="https://git-scm.com/download/win" target="_blank">Git для Windows</a>
  - При использовании macOS:
    - Актуальная версия <a href="https://docs.microsoft.com/visualstudio/mac/installation?view=vsmac-2019" target="_blank">Visual Studio для Mac 8.1+</a>.
    - <a href="https://git-scm.com/download/mac" target="_blank">Git для macOS</a>
- На выбранной вами платформе установлена и запущена последняя версия Xamarin.Android. Руководство по установке Xamarin.Android см. в руководствах по [ установке Xamarin.Android](https://docs.microsoft.com/xamarin/android/get-started/installation/index).
- Устройство Android с включенным <a href="https://developer.android.com/studio/debug/dev-options" target="_blank">режимом разработчика</a> и поддержкой <a href="https://developers.google.com/ar/discover/supported-devices" target="_blank">ARCore</a>.
  - Для взаимодействия компьютера с устройством Android могут потребоваться дополнительные драйверы устройств. Дополнительные сведения см. [здесь](https://developer.android.com/studio/run/device.html).
- Целевой ОС для вашего приложения должна быть ARCore **1.8**.

[!INCLUDE [Create Spatial Anchors resource](../../../includes/spatial-anchors-get-started-create-resource.md)]

## <a name="open-the-sample-project"></a>Открытие примера проекта

[!INCLUDE [Clone Sample Repo](../../../includes/spatial-anchors-clone-sample-repository.md)]

Откройте `Xamarin/SampleXamarin.sln` в Visual Studio.

## <a name="configure-account-identifier-and-key"></a>Настройка идентификатора и ключа учетной записи

Следующим шагом является настройка приложения для использования идентификатора вашей учетной записи и ключа учетной записи. Вы скопировали их в текстовый редактор, когда выполняли [настройку ресурсов Пространственных привязок](#create-a-spatial-anchors-resource).

Откройте `Xamarin/SampleXamarin.Common/AccountDetails.cs`.

Найдите поле `SpatialAnchorsAccountKey` и замените `Set me` ключом учетной записи.

Найдите поле `SpatialAnchorsAccountId` и замените `Set me` идентификатором учетной записи.

## <a name="deploy-the-app-to-your-android-device"></a>Развертывание приложения на устройстве Android

Включите устройство с Android, войдите и подключите его к ПК с помощью USB-кабеля.

Задайте для запускаемого проекта значение **SampleXamarin.Android**, измените **Конфигурацию решения** на **Запустить**и выберите устройство, на котором вы хотите выполнить развертывание из раскрывающегося списка селектора устройства.

# <a name="windowstabdeploy-windows"></a>[Windows](#tab/deploy-windows)

![Настройка Visual Studio](./media/get-started-xamarin-android/visual-studio-windows-configuration.png)

Выберите **Отладка** > **Начать отладку**, чтобы развернуть и запустить приложение.

# <a name="macostabdeploy-macos"></a>[macOS](#tab/deploy-macos)

![Настройка Visual Studio](./media/get-started-xamarin-android/visual-studio-macos-configuration.jpg)

Выберите **Запустить** > **Запуск без отладки**, чтобы развернуть и запустить приложение.

---

В приложении выберите **Базовый**, чтобы запустить демонстрацию, и следуйте инструкциям по размещению и отзыву привязки.

> ![Снимок экрана 1](./media/get-started-xamarin-android/screenshot-1.jpg)
> ![Снимок экрана 2](./media/get-started-xamarin-android/screenshot-2.jpg)
> ![Снимок экрана 3](./media/get-started-xamarin-android/screenshot-3.jpg)

[!INCLUDE [Clean-up section](../../../includes/clean-up-section-portal.md)]

[!INCLUDE [Next steps](../../../includes/spatial-anchors-quickstarts-nextsteps.md)]

> [!div class="nextstepaction"]
> [Руководство. по совместному использованию службы "Пространственные привязки" на разных устройствах](../tutorials/tutorial-share-anchors-across-devices.md)
