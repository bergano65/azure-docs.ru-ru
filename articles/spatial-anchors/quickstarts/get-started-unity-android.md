---
title: Краткое руководство по созданию приложения Android в Unity с использованием службы "Пространственные привязки Azure" |Документация Майкрософт
description: В этом кратком руководстве вы узнаете, как создать приложение Android с Unity с помощью Пространственных привязок.
author: craigktreasure
manager: aliemami
services: azure-spatial-anchors
ms.author: crtreasu
ms.date: 02/24/2019
ms.topic: quickstart
ms.service: azure-spatial-anchors
ms.openlocfilehash: 24a3424a73fb21530f3cde227aa9f05f16bd6ad0
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/26/2019
ms.locfileid: "68562457"
---
# <a name="quickstart-create-a-unity-android-app-with-azure-spatial-anchors"></a>Краткое руководство. Создание приложения Android в Unity с помощью Пространственных привязок Azure

В этом кратком руководстве показано, как создать приложения Android в Unity с помощью [Пространственных привязок Azure](../overview.md). "Пространственные привязки Azure" — это кроссплатформенная служба разработчика, которая позволяет создавать среды смешанной реальности с применением объектов, не меняющих своего расположения на устройствах с течением времени. После завершения вы получите приложение Android ARCore, разработанное с использованием Unity, которое может сохранять и отзывать пространственные привязки.

Вы узнаете, как:

> [!div class="checklist"]
> * создать учетную запись в службе "Пространственные привязки";
> * настроить параметры сборки Unity;
> * настроить идентификатор и ключ учетной записи в службе "Пространственные привязки";
> * экспортировать проект Android Studio;
> * развертывать и запускать на устройстве Android.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Предварительные требования

В рамках этого краткого руководства вам потребуются:

- Компьютер под управлением macOS или Windows с <a href="https://unity3d.com/get-unity/download" target="_blank">Unity 2019.1+</a>, с модулями платформы Android Build Support, средств NDK и пакета SDK для Android, а также с <a href="https://developer.android.com/studio/" target="_blank">Android Studio 3.4+</a>.
  - Если вы используете ОС Windows, вам также потребуется <a href="https://git-scm.com/download/win" target="_blank">Git для Windows</a>.
  - Если вы используете macOS, установите Git с помощью Homebrew. Введите в одну строку терминала такую команду: `/usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"`. Затем выполните `brew install git`.
- Устройство Android с включенным <a href="https://developer.android.com/studio/debug/dev-options" target="_blank">режимом разработчика</a> и поддержкой <a href="https://developers.google.com/ar/discover/supported-devices" target="_blank">ARCore</a>.

[!INCLUDE [Create Spatial Anchors resource](../../../includes/spatial-anchors-get-started-create-resource.md)]

## <a name="download-and-open-the-unity-sample-project"></a>Скачивание и открытие примера проекта Unity

[!INCLUDE [Clone Sample Repo](../../../includes/spatial-anchors-clone-sample-repository.md)]

[!INCLUDE [Open Unity Project](../../../includes/spatial-anchors-open-unity-project.md)]

[!INCLUDE [Android Unity Build Settings](../../../includes/spatial-anchors-unity-android-build-settings.md)]

## <a name="configure-account-identifier-and-key"></a>Настройка идентификатора и ключа учетной записи

В области **Project** (Проект) перейдите к `Assets/AzureSpatialAnchors.Examples/Scenes` и откройте файл сцены `AzureSpatialAnchorsBasicDemo.unity`.

[!INCLUDE [Configure Unity Scene](../../../includes/spatial-anchors-unity-configure-scene.md)]

Сохраните сцену, выбрав **File** -> **Save** (Файл > Сохранить).

## <a name="export-the-android-studio-project"></a>Экспорт проекта Android Studio

[!INCLUDE [Export Unity Project](../../../includes/spatial-anchors-unity-export-project-snip.md)]

В окне **Run Device** (Запуск устройства) выберите свое устройство и щелкните **Сборка и запуск**. Вам будет предложено сохранить файл `.apk`, для которого можно выбрать любое имя.

Следуйте инструкциям в программе для размещения и отзыва привязки.

## <a name="troubleshooting"></a>Устранение неполадок

Если во время запуска приложения вы не видите камеру как фон (к примеру, вы видите вместо этого пустой, синий фон или фон с другой текстурой), то вам нужно повторно импортировать файлы в Unity. Остановите приложение. В верхнем меню в Unity выберите **Assets -> Reimport all** (Ресурсы -> Повторно импортировать все). Затем снова запустите приложение.

[!INCLUDE [Clean-up section](../../../includes/clean-up-section-portal.md)]

[!INCLUDE [Next steps](../../../includes/spatial-anchors-quickstarts-nextsteps.md)]

> [!div class="nextstepaction"]
> [Руководство. по совместному использованию службы "Пространственные привязки" на разных устройствах](../tutorials/tutorial-share-anchors-across-devices.md)
