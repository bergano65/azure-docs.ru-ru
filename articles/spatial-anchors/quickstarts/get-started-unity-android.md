---
title: Краткое руководство. Создание приложения Android Unity с помощью Пространственных привязок Azure | Документация Майкрософт
description: В этом кратком руководстве вы узнаете, как создать приложение Android с Unity с помощью Пространственных привязок.
author: craigktreasure
manager: aliemami
services: azure-spatial-anchors
ms.author: crtreasu
ms.date: 02/24/2019
ms.topic: quickstart
ms.service: azure-spatial-anchors
ms.openlocfilehash: bce71db594d2bbd869dcc5a1ff5cb494a7a6f1c2
ms.sourcegitcommit: bf509e05e4b1dc5553b4483dfcc2221055fa80f2
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/22/2019
ms.locfileid: "59994953"
---
# <a name="quickstart-create-an-android-unity-app-with-azure-spatial-anchors"></a>Краткое руководство. Создание приложения Android Unity с помощью Пространственных привязок Azure

Краткое руководство по созданию приложения Android Unity с помощью [Пространственных привязок Azure](../overview.md). "Пространственные привязки Azure" — это кроссплатформенная служба разработчика, которая позволяет создавать среды смешанной реальности с применением объектов, не меняющих своего расположения на устройствах с течением времени. После завершения вы получите приложение Android ARCore, разработанное с использованием Unity, которое может сохранять и отзывать пространственные привязки.

Вы узнаете, как:

> [!div class="checklist"]
> * создать учетную запись в службе "Пространственные привязки";
> * настроить параметры сборки Unity;
> * скачать и импортировать пакеты SDK с поддержкой ARCore для Unity;
> * настроить идентификатор и ключ учетной записи в службе "Пространственные привязки";
> * экспортировать проект Android Studio;
> * развертывать и запускать на устройстве Android.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Предварительные требования

В рамках этого краткого руководства вам потребуются:

- Компьютер под управлением macOS или Windows с <a href="https://unity3d.com/get-unity/download" target="_blank">Unity 2018.3+</a> и <a href="https://developer.android.com/studio/" target="_blank">Android Studio 3.3+</a>.
  - Если вы используете ОС Windows, вам также потребуется <a href="https://git-scm.com/download/win" target="_blank">Git для Windows</a>.
  - Если вы используете macOS, установите Git с помощью Homebrew. Введите в одну строку терминала такую команду: `/usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"`. Затем выполните `brew install git`.
- Устройство Android с включенным <a href="https://developer.android.com/studio/debug/dev-options" target="_blank">режимом разработчика</a> и поддержкой <a href="https://developers.google.com/ar/discover/supported-devices" target="_blank">ARCore</a>.
- Приложение должно использовать версию **1.7** пакета SDK ARCore для Unity.

[!INCLUDE [Create Spatial Anchors resource](../../../includes/spatial-anchors-get-started-create-resource.md)]

## <a name="open-the-sample-project-in-unity"></a>Открытие примера проекта в Unity

[!INCLUDE [Clone Sample Repo](../../../includes/spatial-anchors-clone-sample-repository.md)]

[!INCLUDE [Android Unity Build Settings](../../../includes/spatial-anchors-unity-android-build-settings.md)]

## <a name="configure-account-identifier-and-key"></a>Настройка идентификатора и ключа учетной записи

В области **Project** (Проект) перейдите к `Assets/AzureSpatialAnchorsPlugin/Examples` и откройте файл сцены `AzureSpatialAnchorsBasicDemo.unity`.

[!INCLUDE [Configure Unity Scene](../../../includes/spatial-anchors-unity-configure-scene.md)]

Сохраните сцену, выбрав **File** -> **Save** (Файл > Сохранить).

## <a name="export-the-android-studio-project"></a>Экспорт проекта Android Studio

[!INCLUDE [Export Unity Project](../../../includes/spatial-anchors-unity-export-project-snip.md)]

Убедитесь, что флажок **Export Project** (Экспорт проекта) не установлен. Щелкните **Build And Run** (Сборка и запуск). Вам будет предложено сохранить файл `.apk`, для которого можно выбрать любое имя.

Следуйте инструкциям в программе для размещения и отзыва привязки.

> [!NOTE]
> Если во время запуска приложения вы не видите камеру как фон (к примеру, вы видите вместо этого пустой, синий фон или фон с другой текстурой), то вам нужно повторно импортировать файлы в Unity. Остановите приложение. В верхнем меню в Unity выберите **Assets -> Reimport all** (Ресурсы -> Повторно импортировать все). Затем снова запустите приложение.

[!INCLUDE [Clean-up section](../../../includes/clean-up-section-portal.md)]

[!INCLUDE [Next steps](../../../includes/spatial-anchors-quickstarts-nextsteps.md)]

> [!div class="nextstepaction"]
> [Руководство по совместному использованию службы "Пространственные привязки" на разных устройствах](../tutorials/tutorial-share-anchors-across-devices.md)
