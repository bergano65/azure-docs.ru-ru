---
title: Скачивание пакета SDK для датчика Azure Kinect
description: Узнайте, как скачать и установить пакет SDK для датчика Azure Kinect в Windows и Linux.
author: Brent-A
ms.author: brenta
ms.prod: kinect-dk
ms.date: 06/26/2019
ms.topic: conceptual
keywords: azure, kinect, пакет sdk, скачать обновление, последняя версия, доступно, установка
ms.openlocfilehash: 2c23977c7e01a9137b72b44d1bdc0e1373bafa0a
ms.sourcegitcommit: d597800237783fc384875123ba47aab5671ceb88
ms.contentlocale: ru-RU
ms.lasthandoff: 04/03/2020
ms.locfileid: "84334272"
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

[Установщик Microsoft](https://download.microsoft.com/download/4/5/a/45aa3917-45bf-4f24-b934-5cff74df73e1/Azure%20Kinect%20SDK%201.4.0.exe) | [Исходный код GitHub](https://github.com/microsoft/Azure-Kinect-Sensor-SDK/issues/1093)

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
