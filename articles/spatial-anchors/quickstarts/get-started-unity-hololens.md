---
title: Краткое руководство. Создание приложения HoloLens с помощью Unity
description: В этом руководстве показано, как создать приложение HoloLens с помощью Unity и службы "Пространственные привязки".
author: craigktreasure
manager: vriveras
services: azure-spatial-anchors
ms.author: crtreasu
ms.date: 09/29/2020
ms.topic: quickstart
ms.service: azure-spatial-anchors
ms.openlocfilehash: 143bc89518ff1811ad6789f71fcf7a9267e99f73
ms.sourcegitcommit: 93329b2fcdb9b4091dbd632ee031801f74beb05b
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/15/2020
ms.locfileid: "92097445"
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

- Вам нужен компьютер Windows, на котором установлены <a href="https://unity3d.com/get-unity/download" target="_blank">Unity 2019.4 (LTS)</a> и <a href="https://www.visualstudio.com/downloads/" target="_blank">Visual Studio 2019.2</a> или более поздней версии. Установка Visual Studio должна включать рабочую нагрузку **Разработка приложений для универсальной платформы Windows** и **пакет SDK для Windows 10 (10.0.18362.0 или более поздней версии)** . Необходимо также установить <a href="https://git-scm.com/download/win" target="_blank">Git для Windows</a> и <a href="https://git-lfs.github.com/">Git LFS</a>.
- Вам нужно устройство HoloLens с включенным [режимом разработчика](/windows/mixed-reality/using-visual-studio). На устройстве должно быть установлено [обновление для Windows, выпущенное в мае 2020 г.](/windows/mixed-reality/whats-new/release-notes-may-2020) Чтобы обновить HoloLens до последнего выпуска, откройте приложение **Параметры**, перейдите в раздел **Update & Security** (Обновление и безопасность), а затем выберите **Проверить обновления**.
- В приложении необходимо включить функцию **SpatialPerception**. Этот параметр находится в разделе **Build Settings** (Параметры сборки)  > **Player Settings** (Настройка проигрывателя)  > **Publishing Settings** (Параметры публикации)  > **Capabilities** (Возможности).
- В приложении необходимо включить функцию **Virtual Reality Supported** (Поддержка виртуальной реальности) с помощью **пакета SDK Смешанной реальности Windows**. Этот параметр находится в **Build Settings** (Параметры сборки)  > **Player Settings** (Настройка проигрывателя)  > **XR Settings** (Параметры XR).

[!INCLUDE [Create Spatial Anchors resource](../../../includes/spatial-anchors-get-started-create-resource.md)]

## <a name="download-and-open-the-unity-sample-project"></a>Скачивание и открытие примера проекта Unity

[!INCLUDE [Clone Sample Repo](../../../includes/spatial-anchors-clone-sample-repository.md)]

[!INCLUDE [Open Unity Project](../../../includes/spatial-anchors-open-unity-project.md)]

Откройте **Build Settings** (Параметры сборки), выбрав пункты **File** > **Build Settings** (Файл > Параметры сборки).

В разделе **Platform** (Платформа) выберите **Universal Windows Platform** (Универсальная платформа Windows). Измените **целевое устройство** на **HoloLens**.

Выберите **Switch Platform** (Изменить платформу), чтобы изменить значение на **Universal Windows Platform** (Универсальная платформа Windows). Если отсутствуют какие-то дополнительные компоненты UWP, Unity может запросить их установку.

![Окно параметров сборки Unity](./media/get-started-unity-hololens/unity-build-settings.png)

Закройте окно **Build Settings** (Параметры сборки).

[!INCLUDE [Configure Unity Scene](../../../includes/spatial-anchors-unity-configure-scene.md)]

## <a name="export-the-hololens-visual-studio-project"></a>Экспорт проекта HoloLens в Visual Studio

[!INCLUDE [Export Unity Project](../../../includes/spatial-anchors-unity-export-project-snip.md)]

Выберите **Build** (Сборка). В диалоговом окне выберите папку для экспорта проекта HoloLens в Visual Studio.

После завершения экспорта появится папка, содержащая экспортированный проект HoloLens.

## <a name="deploy-the-hololens-application"></a>Развертывание приложения HoloLens

Чтобы открыть проект в Visual Studio, дважды щелкните **HelloAR U3D.sln** в папке.

Укажите для параметра **Solution Configuration** (Конфигурация решения) значение **Release** (Выпуск), а для параметра **Solution Platform** (Платформа решения) значение **x86** и выберите **Device** (Устройство) в списке целевых вариантов развертывания.

При использовании HoloLens 2 **платформой решения** будет **ARM64**, а не **x86**.

   ![Настройка Visual Studio](./media/get-started-unity-hololens/visual-studio-configuration.png)

Включите устройство HoloLens, войдите и подключите его к компьютеру с помощью USB-кабеля.

Выберите **Debug** > **Start debugging** (Отладка > Начать отладку), чтобы развернуть приложение и запустить отладку.

Используя стрелки, в приложении выберите **BasicDemo** и нажмите кнопку **Go!** , чтобы запустить демонстрацию. Следуйте инструкциям для размещения и отзыва привязки.

![Снимок экрана 1](./media/get-started-unity-hololens/screenshot-1.jpg)
![Снимок экрана 2](./media/get-started-unity-hololens/screenshot-2.jpg)
![Снимок экрана 3](./media/get-started-unity-hololens/screenshot-3.jpg)
![Снимок экрана 4](./media/get-started-unity-hololens/screenshot-4.jpg)

В Visual Studio остановите приложение, выбрав **Остановить отладку** или нажав клавиши SHIFT+F5.

[!INCLUDE [Clean-up section](../../../includes/clean-up-section-portal.md)]

[!INCLUDE [Next steps](../../../includes/spatial-anchors-quickstarts-nextsteps.md)]

> [!div class="nextstepaction"]
> [Руководство. по совместному использованию привязок между сеансами и устройствами с помощью Пространственных привязок Azure](../tutorials/tutorial-share-anchors-across-devices.md)

> [!div class="nextstepaction"]
> [Руководство. Настройка Пространственных привязок Azure в проекте Unity](../how-tos/setup-unity-project.md)