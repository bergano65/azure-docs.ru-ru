---
title: Краткое руководство. Создание приложения Android с помощью Пространственных привязок Azure | Документация Майкрософт
description: В этом кратком руководстве вы узнаете, как создать приложение Android с помощью Пространственных привязок.
author: craigktreasure
manager: aliemami
services: azure-spatial-anchors
ms.author: crtreasu
ms.date: 02/24/2019
ms.topic: quickstart
ms.service: azure-spatial-anchors
ms.openlocfilehash: 03589745e6e9b40b937c49162e99035ce6c81423
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/16/2019
ms.locfileid: "65800053"
---
# <a name="quickstart-create-an-android-app-with-azure-spatial-anchors"></a>Краткое руководство. Создание приложения Android с помощью Пространственных привязок Azure

Краткое руководство по созданию приложения Android с помощью [Пространственных привязок Azure](../overview.md) в Java или C++/NDK. "Пространственные привязки Azure" — это кроссплатформенная служба разработчика, которая позволяет создавать среды смешанной реальности с применением объектов, не меняющих своего расположения на устройствах с течением времени. После завершения вы получите приложение Android ARCore, которое может сохранять и отзывать пространственные привязки.

Вы узнаете, как:

> [!div class="checklist"]
> * создавать учетную запись в службе "Пространственные привязки";
> * настроить идентификатор и ключ учетной записи в службе "Пространственные привязки";
> * развертывать и запускать на устройстве Android.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Предварительные требования

В рамках этого краткого руководства вам потребуются:

- Компьютер под управлением macOS или Windows с <a href="https://developer.android.com/studio/" target="_blank">Android Studio 3.3+</a>.
  - Если вы используете ОС Windows, вам также потребуется <a href="https://git-scm.com/download/win" target="_blank">Git для Windows</a>.
  - Если вы используете macOS, установите Git с помощью Homebrew. Введите в одну строку терминала такую команду: `/usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"`. Затем выполните `brew install git`.
  - Чтобы создать пример NDK, вам также необходимо установить пакет NDK и SDK Tools CMake 3.6 в Android Studio.
- Устройство Android с включенным <a href="https://developer.android.com/studio/debug/dev-options" target="_blank">режимом разработчика</a> и поддержкой <a href="https://developers.google.com/ar/discover/supported-devices" target="_blank">ARCore</a>.
- Целевой ОС для вашего приложения должна быть ARCore 1.7.

[!INCLUDE [Create Spatial Anchors resource](../../../includes/spatial-anchors-get-started-create-resource.md)]

## <a name="open-the-sample-project"></a>Открытие примера проекта

[!INCLUDE [Clone Sample Repo](../../../includes/spatial-anchors-clone-sample-repository.md)]

Если вы создаете пример пакета NDK для Android, вам нужно скачать `arcore_c_api.h` [здесь](https://raw.githubusercontent.com/google-ar/arcore-android-sdk/v1.7.0/libraries/include/arcore_c_api.h) и поместить его в `Android\NDK\libraries\include`.

Откройте Android Studio.

# <a name="javatabopenproject-java"></a>[Java](#tab/openproject-java)

Выберите **Open an existing Android Studio project** (Открыть существующий проект Android Studio), а затем выберите проект, расположенный в `Android/Java/`.

# <a name="ndktabopenproject-ndk"></a>[NDK](#tab/openproject-ndk)

Выберите **Open an existing Android Studio project** (Открыть существующий проект Android Studio), а затем выберите проект, расположенный в `Android/NDK/`.

***

## <a name="configure-account-identifier-and-key"></a>Настройка идентификатора и ключа учетной записи

Следующим шагом является настройка приложения для использования идентификатора вашей учетной записи и ключа учетной записи. Вы скопировали их в текстовый редактор, когда выполняли [настройку ресурсов пространственных привязок](#create-a-spatial-anchors-resource).

# <a name="javatabopenproject-java"></a>[Java](#tab/openproject-java)

Откройте `Android/Java/app/src/main/java/com/microsoft/sampleandroid/AzureSpatialAnchorsActivity.java`.

Найдите поле `SpatialAnchorsAccountKey` и замените `Set me` ключом учетной записи.

Найдите поле `SpatialAnchorsAccountId` и замените `Set me` идентификатором учетной записи.

# <a name="ndktabopenproject-ndk"></a>[NDK](#tab/openproject-ndk)

Откройте `Android/NDK/app/src/main/cpp/AzureSpatialAnchorsApplication.cpp`.

Найдите поле `SpatialAnchorsAccountKey` и замените `Set me` ключом учетной записи.

Найдите поле `SpatialAnchorsAccountId` и замените `Set me` идентификатором учетной записи.

***

## <a name="deploy-the-app-to-your-android-device"></a>Развертывание приложения на устройстве Android

Включите устройство с Android, войдите и подключите его к ПК с помощью USB-кабеля.

Выберите **Выполнить** на панели инструментов Android Studio.

![Развертывание и запуск Android Studio](./media/get-started-android/android-studio-deploy-run.png)

В диалоговом окне **Select Deployment Target** (Выбор цели развертывания) выберите устройство с Android, а затем выберите **OК**, чтобы запустить приложение на этом устройстве.

Следуйте инструкциям в программе для размещения и отзыва привязки.

Остановите программу, выбрав **Остановить** на панели инструментов Android Studio.

![Остановка Android Studio](./media/get-started-android/android-studio-stop.png)

[!INCLUDE [Clean-up section](../../../includes/clean-up-section-portal.md)]

[!INCLUDE [Next steps](../../../includes/spatial-anchors-quickstarts-nextsteps.md)]

> [!div class="nextstepaction"]
> [Руководство. по совместному использованию службы "Пространственные привязки" на разных устройствах](../tutorials/tutorial-share-anchors-across-devices.md)
