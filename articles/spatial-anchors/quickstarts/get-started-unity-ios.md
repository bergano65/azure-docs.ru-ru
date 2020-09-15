---
title: Краткое руководство. Создание приложения Unity iOS
description: В этом кратком руководстве вы узнаете, как создать приложение iOS с Unity с помощью Пространственных привязок.
author: craigktreasure
manager: vriveras
services: azure-spatial-anchors
ms.author: crtreasu
ms.date: 08/14/2020
ms.topic: quickstart
ms.service: azure-spatial-anchors
ms.openlocfilehash: 4d5ec2411b7b1a65da9a1f4262822cb85a3b386e
ms.sourcegitcommit: c52e50ea04dfb8d4da0e18735477b80cafccc2cf
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/08/2020
ms.locfileid: "89536278"
---
# <a name="quickstart-create-a-unity-ios-app-with-azure-spatial-anchors"></a>Краткое руководство. Создание приложения iOS в Unity с помощью Пространственных привязок Azure

В этом кратком руководстве показано, как создать приложения iOS в Unity с помощью [Пространственных привязок Azure](../overview.md). "Пространственные привязки Azure" — это кроссплатформенная служба разработчика, которая позволяет создавать среды смешанной реальности с применением объектов, не меняющих своего расположения на устройствах с течением времени. После завершения вы получите приложение iOS ARKit, разработанное с использованием Unity, которое может сохранять и повторно вызывать пространственные привязки.

Вы узнаете, как:

> [!div class="checklist"]
> * создать учетную запись в службе "Пространственные привязки";
> * настроить параметры сборки Unity;
> * настроить идентификатор и ключ учетной записи в службе "Пространственные привязки";
> * Экспорт проекта Xcode
> * выполнить развертывание и запуск на устройстве iOS.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Предварительные требования

В рамках этого краткого руководства вам потребуются:

- Компьютер под управлением macOS с установленными компонентами <a href="https://unity3d.com/get-unity/download" target="_blank">Unity 2019.4 (LTS)</a>, последней версией <a href="https://geo.itunes.apple.com/us/app/xcode/id497799835?mt=12" target="_blank">Xcode</a>.
- Система Git, установленная с помощью HomeBrew. Введите в одну строку терминала такую команду: `/usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"`. Затем выполните `brew install git` и `brew install git-lfs`.
- Устройство для разработки на iOS, <a href="https://developer.apple.com/documentation/arkit/verifying_device_support_and_user_permission" target="_blank">совместимое с ARKit</a>.

[!INCLUDE [Create Spatial Anchors resource](../../../includes/spatial-anchors-get-started-create-resource.md)]

## <a name="download-and-open-the-unity-sample-project"></a>Скачивание и открытие примера проекта Unity

[!INCLUDE [Clone Sample Repo](../../../includes/spatial-anchors-clone-sample-repository.md)]

[!INCLUDE [Open Unity Project](../../../includes/spatial-anchors-open-unity-project.md)]

[!INCLUDE [iOS Unity Build Settings](../../../includes/spatial-anchors-unity-ios-build-settings.md)]

[!INCLUDE [Configure Unity Scene](../../../includes/spatial-anchors-unity-configure-scene.md)]

## <a name="export-the-xcode-project"></a>Экспорт проекта Xcode

[!INCLUDE [Export Unity Project](../../../includes/spatial-anchors-unity-export-project-snip.md)]

[!INCLUDE [Configure Xcode](../../../includes/spatial-anchors-unity-ios-xcode.md)]

Следуйте инструкциям в программе для размещения и отзыва привязки.

По завершении остановите приложение, нажав в Xcode кнопку **Stop** (Остановить).

## <a name="troubleshooting"></a>Устранение неполадок

### <a name="rendering-issues"></a>Проблемы отрисовки

Если во время запуска приложения вы не видите камеру как фон (к примеру, вы видите вместо этого пустой, синий фон или фон с другой текстурой), то вам нужно повторно импортировать файлы в Unity. Остановите приложение. В верхнем меню в Unity выберите **Assets -> Re-import all** (Ресурсы -> Повторно импортировать все). Затем снова запустите приложение.

[!INCLUDE [Clean-up section](../../../includes/clean-up-section-portal.md)]

[!INCLUDE [Next steps](../../../includes/spatial-anchors-quickstarts-nextsteps.md)]

> [!div class="nextstepaction"]
> [Руководство. по совместному использованию службы "Пространственные привязки" на разных устройствах](../tutorials/tutorial-share-anchors-across-devices.md)

> [!div class="nextstepaction"]
> [Руководство. Настройка Пространственных привязок Azure в проекте Unity](../how-tos/setup-unity-project.md)
