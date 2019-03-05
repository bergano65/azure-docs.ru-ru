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
ms.openlocfilehash: e92c812ffc8b72fe79248c602e48ff01ef9fefcb
ms.sourcegitcommit: fdd6a2927976f99137bb0fcd571975ff42b2cac0
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/27/2019
ms.locfileid: "56961020"
---
# <a name="quickstart-create-an-android-unity-app-with-azure-spatial-anchors"></a>Краткое руководство. Создание приложения Android Unity с помощью Пространственных привязок Azure

Краткое руководство по созданию приложения Android Unity с помощью [Пространственных привязок Azure](../overview.md). "Пространственные привязки Azure" — это кроссплатформенная служба разработчика, которая позволяет создавать среды смешанной реальности с применением объектов, не меняющих своего расположения на устройствах с течением времени. После завершения вы получите приложение Android ARCore, разработанное с использованием Unity, которое может сохранять и отзывать пространственные привязки.

Вы узнаете, как:

> [!div class="checklist"]
> * создавать учетную запись Пространственных привязок;
> * настроить параметры сборки Unity;
> * скачать и импортировать пакеты SDK с поддержкой ARCore для Unity;
> * настроить идентификатор и ключ учетной записи в службе "Пространственные привязки";
> * экспортировать проект Android Studio;
> * развертывать и запускать на устройстве Android.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Предварительные требования

В рамках этого краткого руководства вам потребуются:

- Компьютер под управлением macOS или Windows с установленными <a href="https://unity3d.com/get-unity/download" target="_blank">Unity 2018.3+</a> и <a href="https://developer.android.com/studio/" target="_blank">Android Studio 3.3+</a>.
- Устройство Android с включенным <a href="https://developer.android.com/studio/debug/dev-options" target="_blank">режимом разработчика</a> и поддержкой <a href="https://developers.google.com/ar/discover/supported-devices" target="_blank">ARCore</a>.

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

Выберите **Экспорт**, чтобы открыть диалоговое окно. Затем выберите папку для экспорта проекта Android Studio.

После завершения экспорта будет отображаться папка с экспортированным проектом Android Studio и вложенной папкой **HelloAR U3D**.

## <a name="deploy-the-android-application"></a>Развертывание приложения Android

Откройте Android Studio и выберите **Open an existing Android Studio project** (Открыть существующий проект Android Studio). Затем выберите вложенную папку **HelloAR U3D** в экспортированном проекте Android Studio и нажмите **OК**.

При открытии появится запрос на использование программы-оболочки Gradle. Выберите **OК**, если вы желаете использовать программу-оболочку Gradle и открыть проект.

Включите устройство с Android, войдите и подключите его к ПК с помощью USB-кабеля.

Выберите **Выполнить** на панели инструментов Android Studio.

![Развертывание и запуск Android Studio](./media/get-started-unity-android/android-studio-deploy-run.png)

В диалоговом окне **Select Deployment Target** (Выбор цели развертывания) выберите устройство с Android, а затем выберите **OК**, чтобы запустить приложение на этом устройстве.

Следуйте инструкциям в программе для размещения и отзыва привязки.

> [!NOTE]
> Если во время запуска приложения вы не видите камеру как фон (к примеру, вы видите вместо этого пустой, синий фон или фон с другой текстурой), то вам нужно повторно импортировать файлы в Unity. Остановите приложение. В верхнем меню в Unity выберите **Assets -> Reimport all** (Ресурсы -> Повторно импортировать все). Затем снова запустите приложение.

Остановите программу, выбрав **Остановить** на панели инструментов Android Studio.

![Остановка Android Studio](./media/get-started-unity-android/android-studio-stop.png)

[!INCLUDE [Clean-up section](../../../includes/clean-up-section-portal.md)]

[!INCLUDE [Next steps](../../../includes/spatial-anchors-quickstarts-nextsteps.md)]

> [!div class="nextstepaction"]
> [Руководство по совместному использованию службы "Пространственные привязки" на разных устройствах](../tutorials/tutorial-share-anchors-across-devices.md)
