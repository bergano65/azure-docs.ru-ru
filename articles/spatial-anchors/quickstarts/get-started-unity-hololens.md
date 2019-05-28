---
title: Краткое руководство. Создание приложения HoloLens в Unity с использованием службы "Пространственные привязки Azure" | Документация Майкрософт
description: В этом руководстве показано, как создать приложение HoloLens с помощью Unity и службы "Пространственные привязки".
author: craigktreasure
manager: aliemami
services: azure-spatial-anchors
ms.author: crtreasu
ms.date: 02/24/2019
ms.topic: quickstart
ms.service: azure-spatial-anchors
ms.openlocfilehash: 31c7af3edf6364ebca021f87240e73c74e64ebc4
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2019
ms.locfileid: "64704137"
---
# <a name="quickstart-create-a-unity-hololens-app-that-uses-azure-spatial-anchors"></a>Краткое руководство. Создание приложения HoloLens в Unity с использованием службы "Пространственные привязки Azure"

В этом кратком руководстве показано, как создать приложение HoloLens в Unity с помощью службы [Пространственные привязки Azure](../overview.md). Пространственные привязки — это кроссплатформенная служба для разработки, которая позволяет создавать среды смешанной реальности с объектами, расположение которых на устройствах не меняется с течением времени. Завершив работу, вы получите приложение HoloLens, разработанное с использованием Unity, которое может сохранять и удалять пространственные привязки.

Вы узнаете, как:

- создать учетную запись в службе "Пространственные привязки";
- настроить параметры сборки Unity;
- настроить идентификатор и ключ учетной записи в службе "Пространственные привязки";
- экспортировать проект HoloLens в Visual Studio;
- развернуть приложение и запустить его на устройстве HoloLens.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Предварительные требования

Для работы с этим кратким руководством сделайте следующее:


- Вам нужен компьютер Windows, на котором установлены <a href="https://unity3d.com/get-unity/download" target="_blank">Unity 2018.3</a> или более поздней версии и <a href="https://www.visualstudio.com/downloads/" target="_blank">Visual Studio 2017</a> или более поздней версии. Установка Visual Studio должна включать рабочую нагрузку **Разработка приложений для универсальной платформы Windows**. Необходимо также установить <a href="https://git-scm.com/download/win" target="_blank">Git для Windows</a>.
- Вам нужно устройство HoloLens с включенным [режимом разработчика](https://docs.microsoft.com/windows/mixed-reality/using-visual-studio). На этом устройстве должно быть установлено [обновление Windows 10 за октябрь 2018 г.](https://docs.microsoft.com/windows/mixed-reality/release-notes-october-2018) (также известное как RS5). Чтобы обновить HoloLens до последнего выпуска, откройте приложение **Параметры**, перейдите в раздел **Update & Security** (Обновление и безопасность), а затем выберите **Проверить обновления**.
- В приложении необходимо включить функцию **SpatialPerception**. Этот параметр находится в разделе **Build Settings** (Параметры сборки)  > **Player Settings** (Настройка проигрывателя)  > **Publishing Settings** (Параметры публикации)  > **Capabilities** (Возможности).
- В приложении необходимо включить функцию **Virtual Reality Supported** (Поддержка виртуальной реальности) с помощью **пакета SDK Смешанной реальности Windows**. Этот параметр находится в **Build Settings** (Параметры сборки)  > **Player Settings** (Настройка проигрывателя)  > **XR Settings** (Параметры XR).

[!INCLUDE [Create Spatial Anchors resource](../../../includes/spatial-anchors-get-started-create-resource.md)]

## <a name="open-the-sample-project-in-unity"></a>Открытие примера проекта в Unity

[!INCLUDE [Clone Sample Repo](../../../includes/spatial-anchors-clone-sample-repository.md)]

В Unity откройте проект в папке Unity.

Откройте **Build Settings** (Параметры сборки), выбрав пункты **File** > **Build Settings** (Файл > Параметры сборки).

В разделе **Platform** (Платформа) выберите **Universal Windows Platform** (Универсальная платформа Windows). Измените **целевое устройство** на **HoloLens**.

Выберите **Switch Platform** (Изменить платформу), чтобы изменить значение на **Universal Windows Platform** (Универсальная платформа Windows). Если отсутствуют какие-то дополнительные компоненты UWP, Unity может запросить их установку.

   ![Окно параметров сборки Unity](./media/get-started-unity-hololens/unity-build-settings.png)

Закройте окно **Build Settings** (Параметры сборки).

## <a name="configure-the-account-identifier-and-key"></a>Настройка идентификатора и ключа учетной записи

В области **Project** (Проект) перейдите к `Assets/AzureSpatialAnchorsPlugin/Examples` и откройте файл сцены `AzureSpatialAnchorsBasicDemo.unity`.

[!INCLUDE [Configure Unity Scene](../../../includes/spatial-anchors-unity-configure-scene.md)]

Сохраните сцену, выбрав **File** > **Save** (Файл > Сохранить).

## <a name="export-the-hololens-visual-studio-project"></a>Экспорт проекта HoloLens в Visual Studio

[!INCLUDE [Export Unity Project](../../../includes/spatial-anchors-unity-export-project-snip.md)]

Выберите **Build** (Сборка). В диалоговом окне выберите папку для экспорта проекта HoloLens в Visual Studio.

После завершения экспорта появится папка, содержащая экспортированный проект HoloLens.

## <a name="deploy-the-hololens-application"></a>Развертывание приложения HoloLens

Чтобы открыть проект в Visual Studio, дважды щелкните **HelloAR U3D.sln** в папке.

Укажите для параметра **Solution Configuration** (Конфигурация решения) значение **Release** (Выпуск), а для параметра **Solution Platform** (Платформа решения) значение **x86** и выберите **Device** (Устройство) в списке целевых вариантов развертывания.

При использовании HoloLens 2 **платформой решения** будет **ARM**, а не **x86**.

   ![Настройка Visual Studio](./media/get-started-unity-hololens/visual-studio-configuration.png)

Включите устройство HoloLens, войдите и подключите его к компьютеру с помощью USB-кабеля.

Выберите **Debug** > **Start debugging** (Отладка > Начать отладку), чтобы развернуть приложение и запустить отладку.

Следуйте инструкциям в программе для размещения и отзыва привязки.

В Visual Studio остановите приложение, выбрав **Остановить отладку** или нажав клавиши SHIFT+F5.

[!INCLUDE [Clean-up section](../../../includes/clean-up-section-portal.md)]

[!INCLUDE [Next steps](../../../includes/spatial-anchors-quickstarts-nextsteps.md)]

> [!div class="nextstepaction"]
> [Руководство по совместному использованию привязок между сеансами и устройствами с помощью Пространственных привязок Azure](../tutorials/tutorial-share-anchors-across-devices.md)
