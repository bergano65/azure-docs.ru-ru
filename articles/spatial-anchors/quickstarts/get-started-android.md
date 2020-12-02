---
title: Краткое руководство. Создание приложения Android
description: В этом кратком руководстве вы узнаете, как создать приложение Android с помощью Пространственных привязок.
author: msftradford
manager: MehranAzimi-msft
services: azure-spatial-anchors
ms.author: parkerra
ms.date: 11/20/2020
ms.topic: quickstart
ms.service: azure-spatial-anchors
ms.openlocfilehash: b7be4e257fc884c655fe380f69b08797a907fbbb
ms.sourcegitcommit: b8eba4e733ace4eb6d33cc2c59456f550218b234
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/23/2020
ms.locfileid: "96022655"
---
# <a name="quickstart-create-an-android-app-with-azure-spatial-anchors"></a>Краткое руководство. Создание приложения Android с помощью Пространственных привязок Azure

Краткое руководство по созданию приложения Android с помощью [Пространственных привязок Azure](../overview.md) в Java или C++/NDK. "Пространственные привязки Azure" — это кроссплатформенная служба разработчика, которая позволяет создавать среды смешанной реальности с применением объектов, не меняющих своего расположения на устройствах с течением времени. После завершения вы получите приложение Android ARCore, которое может сохранять и отзывать пространственные привязки.

Вы узнаете, как:

> [!div class="checklist"]
> * создать учетную запись в службе "Пространственные привязки";
> * настроить идентификатор и ключ учетной записи в службе "Пространственные привязки";
> * развертывать и запускать на устройстве Android.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Предварительные требования

В рамках этого краткого руководства вам потребуются:

- Компьютер под управлением macOS или Windows с <a href="https://developer.android.com/studio/" target="_blank">Android Studio 3.4 или более поздних версий</a>.
  - Если вы используете ОС Windows, вам также потребуется <a href="https://git-scm.com/download/win" target="_blank">Git для Windows</a> и <a href="https://git-lfs.github.com/">Git LFS</a>.
  - Если вы используете macOS, установите Git с помощью Homebrew. Введите в одну строку терминала такую команду: `/usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"`. Затем выполните `brew install git` и `brew install git-lfs`.
  - Чтобы создать пример NDK, вам также необходимо установить пакет NDK и SDK Tools CMake 3.6 (или более поздней версии) в Android Studio.
- Устройство Android с включенным <a href="https://developer.android.com/studio/debug/dev-options" target="_blank">режимом разработчика</a> и поддержкой <a href="https://developers.google.com/ar/discover/supported-devices" target="_blank">ARCore</a>.
  - Для взаимодействия компьютера с устройством Android могут потребоваться дополнительные драйверы устройств. Дополнительные сведения и инструкции см. [здесь](https://developer.android.com/studio/run/device.html).
- Целевой ОС для вашего приложения должна быть ARCore **1.11.0**.

[!INCLUDE [Create Spatial Anchors resource](../../../includes/spatial-anchors-get-started-create-resource.md)]

## <a name="open-the-sample-project"></a>Открытие примера проекта

# <a name="java"></a>[Java](#tab/openproject-java)

[!INCLUDE [Clone Sample Repo](../../../includes/spatial-anchors-clone-sample-repository.md)]

# <a name="ndk"></a>[NDK](#tab/openproject-ndk)

[!INCLUDE [Clone Sample Repo](../../../includes/spatial-anchors-clone-sample-repository.md)]

Загрузите файл `arcore_c_api.h`[отсюда](https://raw.githubusercontent.com/google-ar/arcore-android-sdk/v1.11.0/libraries/include/arcore_c_api.h) и поместите его в папку `Android\NDK\libraries\include`.

Из нового клонированного репозитория инициализируйте подмодули, выполнив следующую команду:

```console
git submodule update --init --recursive
```

---

Откройте Android Studio.

# <a name="java"></a>[Java](#tab/openproject-java)

Выберите **Open an existing Android Studio project** (Открыть существующий проект Android Studio), а затем выберите проект, расположенный в `Android/Java/`.

# <a name="ndk"></a>[NDK](#tab/openproject-ndk)

Выберите **Open an existing Android Studio project** (Открыть существующий проект Android Studio), а затем выберите проект, расположенный в `Android/NDK/`.

---

## <a name="configure-account-identifier-and-key"></a>Настройка идентификатора и ключа учетной записи

Следующим шагом является настройка приложения для использования идентификатора вашей учетной записи и ключа учетной записи. Вы скопировали их в текстовый редактор, когда выполняли [настройку ресурсов пространственных привязок](#create-a-spatial-anchors-resource).

# <a name="java"></a>[Java](#tab/openproject-java)

Откройте среду `Android/Java/app/src/main/java/com/microsoft/sampleandroid/AzureSpatialAnchorsManager.java`.

Найдите поле `SpatialAnchorsAccountKey` и замените `Set me` ключом учетной записи.

Найдите поле `SpatialAnchorsAccountId` и замените `Set me` идентификатором учетной записи.

Найдите поле `SpatialAnchorsAccountDomain` и замените `Set me` доменом учетной записи.

# <a name="ndk"></a>[NDK](#tab/openproject-ndk)

Откройте среду `Android/NDK/app/src/main/cpp/AzureSpatialAnchorsApplication.cpp`.

Найдите поле `SpatialAnchorsAccountKey` и замените `Set me` ключом учетной записи.

Найдите поле `SpatialAnchorsAccountId` и замените `Set me` идентификатором учетной записи.

Найдите поле `SpatialAnchorsAccountDomain` и замените `Set me` доменом учетной записи.

---

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
