---
title: Скачивание пакета SDK для отслеживания текста Kinect для Azure
description: Узнайте, как скачать каждую версию пакета SDK для датчика Kinect для Azure в Windows или Linux.
author: qm13
ms.author: quentinm
ms.prod: kinect-dk
ms.date: 06/26/2019
ms.topic: conceptual
keywords: Azure, Kinect, пакет SDK, скачать обновление, последние, доступные, установка, основной текст, отслеживание
ms.openlocfilehash: e3f8233d208e2a45c1af9a52a76b6064b15bfe4b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/09/2020
ms.locfileid: "85277069"
---
# <a name="download-azure-kinect-body-tracking-sdk"></a>Скачать пакет SDK для отслеживания текста Kinect для Azure

Этот документ содержит ссылки для установки каждой версии пакета SDK для отслеживания текста Kinect для Azure.

## <a name="azure-kinect-body-tracking-sdk-contents"></a>Содержимое пакета SDK для отслеживания текста Kinect для Azure

- Заголовки и библиотеки для создания приложения отслеживания текста с помощью Azure Kinect DK.
- Распространяемые библиотеки DLL, необходимые для приложений отслеживания текста с помощью Azure Kinect DK.
- Примеры приложений для отслеживания текста.

## <a name="windows-download-links"></a>Ссылки для загрузки Windows

Version       | Скачать
--------------|----------
1.0.1 | [msi](https://www.microsoft.com/en-us/download/details.aspx?id=100942) [NuGet](https://www.nuget.org/packages/Microsoft.Azure.Kinect.BodyTracking/1.0.1) MSI
1.0.0 | [msi](https://www.microsoft.com/en-us/download/details.aspx?id=100848) [NuGet](https://www.nuget.org/packages/Microsoft.Azure.Kinect.BodyTracking/1.0.0) MSI
0.9.5 | [msi](https://www.microsoft.com/en-us/download/details.aspx?id=100636) [NuGet](https://www.nuget.org/packages/Microsoft.Azure.Kinect.BodyTracking/0.9.5) MSI
0.9.4 | [msi](https://www.microsoft.com/en-us/download/details.aspx?id=100415) [NuGet](https://www.nuget.org/packages/Microsoft.Azure.Kinect.BodyTracking/0.9.4) MSI
0.9.3 | [msi](https://www.microsoft.com/en-us/download/details.aspx?id=100307) [NuGet](https://www.nuget.org/packages/Microsoft.Azure.Kinect.BodyTracking/0.9.3) MSI
0.9.2 | [msi](https://www.microsoft.com/en-us/download/details.aspx?id=100128) [NuGet](https://www.nuget.org/packages/Microsoft.Azure.Kinect.BodyTracking/0.9.2) MSI
0.9.1 | [msi](https://www.microsoft.com/en-us/download/details.aspx?id=100063) [NuGet](https://www.nuget.org/packages/Microsoft.Azure.Kinect.BodyTracking/0.9.1) MSI
0.9.0 | [msi](https://www.microsoft.com/en-us/download/details.aspx?id=58402) [NuGet](https://www.nuget.org/packages/Microsoft.Azure.Kinect.BodyTracking/0.9.0) MSI

## <a name="linux-installation-instructions"></a>Инструкции по установке (Linux)

Сейчас единственным поддерживаемым дистрибутивом является Ubuntu 18.04. Чтобы запросить поддержку других дистрибутивов, перейдите на [эту страницу](https://aka.ms/azurekinectfeedback).

Сначала необходимо настроить [репозиторий пакетов Майкрософт](https://packages.microsoft.com/), следуя инструкциям, приведенным [здесь](https://docs.microsoft.com/windows-server/administration/linux-package-repository-for-microsoft-software).

Пакет `libk4abt<major>.<minor>-dev` содержит заголовки и файлы CMake для сборки в `libk4abt`.
`libk4abt<major>.<minor>`Пакет содержит общие объекты, необходимые для запуска исполняемых файлов, зависящих от, а `libk4abt` также для примера средства просмотра.

Для работы с основными руководствами требуется пакет `libk4abt<major>.<minor>-dev`. Для установки выполните следующую команду:

`sudo apt install libk4abt1.0-dev`

Если команда выполнена, это значит, что пакет SDK готов к использованию.

> [!NOTE]
> При установке пакета SDK запомните путь, по которому выполнена установка. Например, "C:\Program Files\azure Multi Kinect Body Tracking SDK 1.0.0". Примеры, упоминаемые в статьях, приведены в этом пути.
> Примеры отслеживания текста находятся в папке " [Body-Tracking-Samples](https://github.com/microsoft/Azure-Kinect-Samples/tree/master/body-tracking-samples) " в репозитории Azure-Kinect-Samples. Примеры, упоминаемые в статьях, можно найти здесь.

## <a name="change-log"></a>Журнал изменений

### <a name="v101"></a>v 1.0.1
* [Исправление ошибки] Устранена проблема, из – за которой пакет SDK аварийно завершает работу при загрузке onnxruntime.dll по пути в Windows Build 19025 или более поздней версии: [Link](https://github.com/microsoft/Azure-Kinect-Sensor-SDK/issues/932)

### <a name="v100"></a>v1.0.0
* Функциями Добавьте оболочку C# в установщик MSI.
* [Исправление ошибки] Устранена проблема, из – за которой невозможно правильно обнаружить вращение головного [канала: ссылка](https://github.com/microsoft/Azure-Kinect-Sensor-SDK/issues/997)
* [Исправление ошибки] Устранение проблемы, с которой загрузка ЦП проходит до 100% на компьютере Linux: [ссылка](https://github.com/microsoft/Azure-Kinect-Sensor-SDK/issues/1007)
* Регистрируют Добавьте два примера в репозиторий. В примере 1 показано, как преобразовать результаты отслеживания текста из пространства глубины в [ссылку](https://github.com/microsoft/Azure-Kinect-Samples/tree/master/body-tracking-samples/camera_space_transform_sample)цветового пространства; в примере 2 показано, как определить [ссылку](https://github.com/microsoft/Azure-Kinect-Samples/tree/master/body-tracking-samples/floor_detector_sample) на плоскость этажа

### <a name="v095"></a>v 0.9.5
* Функциями Поддержка C#. Оболочка C# упакована в пакет NuGet.
* Функциями Поддержка нескольких датчиков. Разрешено создание нескольких датчиков. Теперь пользователь может создать несколько датчиков для контроля текста на разных устройствах Azure Kinect.
* Функциями Поддержка многопотоковой обработки для режима ЦП. При работе в режиме ЦП все ядра будут использоваться для максимальной скорости.
* Функциями Добавить `gpu_device_id` в `k4abt_tracker_configuration_t` структуру. Разрешить пользователям указывать устройство GPU, отличное от значения по умолчанию, для запуска алгоритма отслеживания текста.
* [Исправление ошибки/критическое изменение] Исправьте опечатку в совместном имени. Измените совместное имя с `K4ABT_JOINT_SPINE_NAVAL` на `K4ABT_JOINT_SPINE_NAVEL` .

### <a name="v094"></a>v 0.9.4
* Функциями Поддержка добавления соединений. Пакет SDK предоставит сведения для трех дополнительных соединений для каждой руки: рука, ХАНДТИП, THUMB.
* Функциями Добавьте уровень достоверности прогноза для всех обнаруженных соединений.
* Функциями Добавление поддержки режима ЦП. Изменяя `cpu_only_mode` значение в `k4abt_tracker_configuration_t` , теперь пакет SDK может работать в режиме ЦП, который не требует от пользователя наличия мощной графической карты.

### <a name="v093"></a>v 0.9.3
* Функциями Опубликуйте новую модель DNN dnn_model_2_0. onnx, которая в значительной степени повышает надежность отслеживания текста.
* Функциями Отключение временного сглаживания по умолчанию. Отслеживание соединений будет более быстрым.
* Функциями Повышение точности гиперкарты индекса текста.
* [Исправление ошибки] Исправьте ошибку, так как параметр ориентации датчика не действует.
* [Исправление ошибки] Измените тип body_index_map с K4A_IMAGE_FORMAT_CUSTOM на K4A_IMAGE_FORMAT_CUSTOM8.
* [Известная ошибка] Два замкнутых тела могут объединяться в сегмент с одним экземпляром.

### <a name="v092"></a>v 0.9.2
* [Критическое изменение] Обновите, чтобы оно зависело от последней версии пакета SDK Azure Kinect Sensor 1.2.0.
* [Изменение API] `k4abt_tracker_create` функция начнет принимать `k4abt_tracker_configuration_t` входные данные. 
* [Изменение API] Измените API так, чтобы `k4abt_frame_get_timestamp_usec` `k4abt_frame_get_device_timestamp_usec` он был более конкретным и соответствующим в пакете SDK для датчика 1.2.0.
* Функциями Разрешить пользователям указывать ориентацию подключения датчика при создании средства отслеживания, чтобы обеспечить более точные результаты отслеживания текста при подключении с разными углами.
* Функциями Укажите новый API `k4abt_tracker_set_temporal_smoothing` , чтобы изменить величину временного сглаживания, которое пользователь хочет применить.
* Функциями Добавьте оболочку C++ k4abt. hpp.
* Функциями Добавьте заголовок определения версии k4abtversion. h.
* [Исправление ошибки] Исправление ошибки, которая привела к очень высокой загрузке ЦП.
* [Исправление ошибки] Устранение ошибки при аварийном завершении ведения журнала.

### <a name="v091"></a>v 0.9.1
* [Исправление ошибки] Устранение утечки памяти при уничтожении Tracker
* [Исправление ошибки] Улучшенные сообщения об ошибках для отсутствующих зависимостей
* [Исправление ошибки] Сбой без сбоев при создании второго экземпляра Tracker
* [Исправление ошибки] Переменные среды средства ведения журнала теперь работают правильно
* Поддержка Linux

### <a name="v090"></a>v 0.9.0

* [Критическое изменение] Понижение уровня зависимостей пакета SDK до CUDA 10,0 (от CUDA 10,1). ONNX Runtime официально поддерживает только до CUDA 10,0.
* [Критическое изменение] Переключение на среду выполнения ONNX вместо среды выполнения Tensorflow. Сокращает время запуска первого кадра и использование памяти. Он также сокращает двоичный размер пакета SDK.
* [Изменение API] Переименован `k4abt_tracker_queue_capture()` в `k4abt_tracker_enqueue_capture()`
* [Изменение API] `k4abt_frame_get_body()` Разбивается на две отдельные функции: `k4abt_frame_get_body_skeleton()` и `k4abt_frame_get_body_id()` . Теперь можно запросить идентификатор тела без постоянного копирования всей структуры схемы.
* [Изменение API] Добавлена  `k4abt_frame_get_timestamp_usec()` функция для упрощения шагов, позволяющих пользователям запрашивать отметку времени основного фрейма.
* Улучшена точность алгоритма отслеживания текста и отслеживание надежности.

## <a name="next-steps"></a>Дальнейшие действия

- [Обзор Azure Kinect DK](about-azure-kinect-dk.md)

- [Настройка Azure Kinect DK](set-up-azure-kinect-dk.md)

- [Настройка отслеживания текста Azure Kinect](body-sdk-setup.md)
