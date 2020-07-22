---
title: Краткое руководство. Настройка отслеживания тел с помощью Azure Kinect
description: В этом кратком руководстве описан процесс настройки пакета SDK для отслеживания тел с помощью Azure Kinect.
author: qm13
ms.author: quentinm
ms.prod: kinect-dk
ms.date: 06/26/2019
ms.topic: quickstart
keywords: kinect, azure, датчик, доступ, глубина, пакет sdk, тело, отслеживание, совместный, настройка, cuda, nvidia
ms.openlocfilehash: 2cf4c1097730f88fc4bd66c28e1bdddd7fea8640
ms.sourcegitcommit: fe6c9a35e75da8a0ec8cea979f9dec81ce308c0e
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/26/2020
ms.locfileid: "85277848"
---
# <a name="quickstart-set-up-azure-kinect-body-tracking"></a>Краткое руководство. Настройка пакета SDK для отслеживания тел с помощью Azure Kinect

В этом кратком руководстве приведены пошаговые инструкции по запуску отслеживания тел в Azure Kinect DK.

## <a name="system-requirements"></a>Требования к системе

Чтобы использовать пакет SDK для отслеживания тел, на главном ПК должен быть установлен процессор GPU от NVIDIA. Рекомендуемые характеристики основного ПК для отслеживания тел см. на странице [требований к системе](system-requirements.md).

## <a name="install-software"></a>Установка ПО

### <a name="install-the-latest-nvidia-driver"></a>[Установка последней версии драйвера NVIDIA](https://www.nvidia.com/Download/index.aspx?lang=en-us)

Скачайте и установите последнюю версию драйвера NVIDIA для видеоадаптера. Старые драйверы могут быть несовместимы с двоичными файлами CUDA, распространяемыми вместе с пакетом SDK для отслеживания тел.

### <a name="visual-c-redistributable-for-visual-studio-2015"></a>[Распространяемый пакет Visual C++ для Visual Studio 2015](https://www.microsoft.com/en-us/download/details.aspx?id=48145)

Скачайте и установите Распространяемый компонент Visual C++ для Visual Studio 2015. 

## <a name="set-up-hardware"></a>Настройка оборудования

### <a name="set-up-azure-kinect-dk"></a>[Настройка Azure Kinect DK](set-up-azure-kinect-dk.md)

Запустите [средство просмотра Azure Kinect](azure-kinect-viewer.md), чтобы проверить правильность настройки Azure Kinect DK.

## <a name="download-the-body-tracking-sdk"></a>Скачивание пакета SDK для отслеживания тел
 
1. Щелкните ссылку, чтобы [скачать пакет SDK для отслеживания тел](body-sdk-download.md).
2. Установите пакет SDK для отслеживания тел на ПК.

## <a name="verify-body-tracking"></a>Проверка отслеживания тел

Запустите **средство просмотра Azure Kinect для отслеживания тел** и убедитесь в правильности настройки пакета SDK для отслеживания тел. Средство просмотра устанавливается с помощью установщика MSI пакета SDK. Его можно найти в меню "Пуск" или по этому пути: `<SDK Installation Path>\tools\k4abt_simple_3d_viewer.exe`.

Если у вас нет мощного процессора GPU и вы просто хотите проверить результат, можно запустить **средство просмотра Azure Kinect для отслеживания тел** в командной строке с помощью следующей команды: `<SDK Installation Path>\tools\k4abt_simple_3d_viewer.exe CPU`

Если все настроено правильно, должны отобразиться окно с облаком точек в трехмерном пространстве и отслеживаемыми телами.


![Средство просмотра объемного изображения для отслеживания тел](./media/quickstarts/samples-simple3dviewer.png)

## <a name="examples"></a>Примеры

Примеры использования пакета SDK для отслеживания тел см. [здесь](https://github.com/microsoft/Azure-Kinect-Samples/tree/master/body-tracking-samples).

## <a name="next-steps"></a>Дальнейшие действия

> [!div class="nextstepaction"]
>[Создание первого приложения для отслеживания тел](build-first-body-app.md)

