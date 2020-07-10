---
title: Скачивание пакета SDK для датчика Azure Kinect
description: Узнайте, как скачать и установить пакет SDK для датчика Azure Kinect в Windows и Linux.
author: Brent-A
ms.author: brenta
ms.prod: kinect-dk
ms.date: 06/26/2019
ms.topic: conceptual
keywords: azure, kinect, пакет sdk, скачать обновление, последняя версия, доступно, установка
ms.openlocfilehash: 48cdd35a80d68a7ec0d900639c0fca3a2c66787e
ms.sourcegitcommit: 1e6c13dc1917f85983772812a3c62c265150d1e7
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/09/2020
ms.locfileid: "86171794"
---
# <a name="azure-kinect-sensor-sdk-download"></a>Скачивание пакета SDK для датчика Azure Kinect

На этой странице содержатся ссылки на скачивание каждой версии пакета SDK для датчика Azure Kinect. Установщик предоставляет все необходимые файлы для разработки для Azure Kinect.

## <a name="azure-kinect-sensor-sdk-contents"></a>Содержимое пакета SDK для датчика Azure Kinect

- Заголовки и библиотеки для создания приложения с помощью Azure Kinect DK.
- Распространяемые библиотеки DLL, которые требуются для приложений, использующих Azure Kinect DK.
- [Средство просмотра Azure Kinect.](azure-kinect-viewer.md)
- [Средство записи Azure Kinect.](azure-kinect-recorder.md)
- [Средство обновления встроенного ПО Azure Kinect.](azure-kinect-firmware-tool.md)

## <a name="windows-download-link"></a>Ссылка для скачивания (Windows)

[Установщик Microsoft](https://download.microsoft.com/download/3/d/6/3d6d9e99-a251-4cf3-8c6a-8e108e960b4b/Azure%20Kinect%20SDK%201.4.1.exe) | [Исходный код GitHub](https://github.com/microsoft/Azure-Kinect-Sensor-SDK/issues/1093)

> [!NOTE]
> При установке пакета SDK запомните путь, по которому выполнена установка. Например, C:\Program Files\Azure Kinect SDK 1.2. Средства, упоминаемые в статьях, находятся в этом каталоге.

См. предыдущие версии пакета SDK для датчика Azure Kinect и встроенного ПО на сайте [GitHub](https://github.com/microsoft/Azure-Kinect-Sensor-SDK/blob/develop/docs/usage.md).

## <a name="linux-installation-instructions"></a>Инструкции по установке (Linux)

Сейчас единственным поддерживаемым дистрибутивом является Ubuntu 18.04. Чтобы запросить поддержку других дистрибутивов, перейдите на [эту страницу](https://aka.ms/azurekinectfeedback).

Сначала необходимо настроить [репозиторий пакетов Майкрософт](https://packages.microsoft.com/), следуя инструкциям, приведенным [здесь](https://docs.microsoft.com/windows-server/administration/linux-package-repository-for-microsoft-software).

Теперь можно установить необходимые пакеты. Пакет `k4a-tools` включает [средство просмотра Azure Kinect](azure-kinect-viewer.md), [средство записи Azure Kinect](record-sensor-streams-file.md), а также [средство обновления программного обеспечения Azure Kinect](azure-kinect-firmware-tool.md). Для установки выполните следующую команду:

 `sudo apt install k4a-tools`

 Пакет `libk4a<major>.<minor>-dev` содержит заголовки и файлы CMake для сборки в `libk4a`.
Пакет `libk4a<major>.<minor>` содержит общие объекты, требуемые для запуска исполняемых файлов, зависящих от `libk4a`.

 Для работы с основными руководствами требуется пакет `libk4a<major>.<minor>-dev`. Для установки выполните следующую команду:

 `sudo apt install libk4a1.1-dev`

Если команда выполнена, это значит, что пакет SDK готов к использованию.

## <a name="change-log-and-older-versions"></a>Журнал изменений и более старые версии

Журнал изменений пакета SDK для датчика Azure Kinect см. [здесь](https://github.com/microsoft/Azure-Kinect-Sensor-SDK/blob/develop/CHANGELOG.md).

Более старую версию пакета SDK для датчика Azure Kinect см. [здесь](https://github.com/microsoft/Azure-Kinect-Sensor-SDK/blob/develop/docs/usage.md).

## <a name="next-steps"></a>Дальнейшие действия

[Настройка Azure Kinect DK](set-up-azure-kinect-dk.md)
