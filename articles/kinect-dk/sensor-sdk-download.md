---
title: Скачивание пакета SDK для датчика Azure Kinect
description: Узнайте, как скачать и установить пакет SDK для датчика Azure Kinect в Windows и Linux.
author: Brent-A
ms.author: brenta
ms.prod: kinect-dk
ms.date: 06/26/2019
ms.topic: conceptual
keywords: azure, kinect, пакет sdk, скачать обновление, последняя версия, доступно, установка
ms.openlocfilehash: 2fd14781c42192c713d826729f8fab6c698d6321
ms.sourcegitcommit: 2ba6303e1ac24287762caea9cd1603848331dd7a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/15/2020
ms.locfileid: "97505483"
---
# <a name="azure-kinect-sensor-sdk-download"></a>Скачивание пакета SDK для датчика Azure Kinect

На этой странице содержатся ссылки на скачивание каждой версии пакета SDK для датчика Azure Kinect. Установщик предоставляет все необходимые файлы для разработки для Azure Kinect.

## <a name="azure-kinect-sensor-sdk-contents"></a>Содержимое пакета SDK для датчика Azure Kinect

- Заголовки и библиотеки для создания приложения с помощью Azure Kinect DK.
- Распространяемые библиотеки DLL, которые требуются для приложений, использующих Azure Kinect DK.
- [Средство просмотра Azure Kinect.](azure-kinect-viewer.md)
- [Средство записи Azure Kinect.](azure-kinect-recorder.md)
- [Средство обновления встроенного ПО Azure Kinect.](azure-kinect-firmware-tool.md)

## <a name="windows-installation-instructions"></a>Инструкции по установке Windows

Сведения об установке для последних и предыдущих версий пакета SDK и встроенного по для Azure Kinect можно найти [здесь](https://github.com/microsoft/Azure-Kinect-Sensor-SDK/blob/develop/docs/usage.md).

Исходный код можно найти [здесь](https://github.com/microsoft/Azure-Kinect-Sensor-SDK).

> [!NOTE]
> При установке пакета SDK запомните путь, по которому выполнена установка. Например, C:\Program Files\Azure Kinect SDK 1.2. Средства, упоминаемые в статьях, находятся в этом каталоге.

## <a name="linux-installation-instructions"></a>Инструкции по установке (Linux)

Сейчас единственным поддерживаемым дистрибутивом является Ubuntu 18.04. Чтобы запросить поддержку других дистрибутивов, перейдите на [эту страницу](https://aka.ms/azurekinectfeedback).

Сначала необходимо настроить [репозиторий пакетов Майкрософт](https://packages.microsoft.com/), следуя инструкциям, приведенным [здесь](/windows-server/administration/linux-package-repository-for-microsoft-software).

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