---
title: Устранение неполадок Azure IoT концентратор ошибка 403006 DeviceMaximumActiveFileUploadLimit
description: Понять, как исправить ошибку 403006 DeviceMaximumActiveFileUploadРазпревысил
author: jlian
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: troubleshooting
ms.date: 01/30/2020
ms.author: jlian
ms.openlocfilehash: 1e3c05e4cc3ccf34573b55d3729aded16e26d66e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76960845"
---
# <a name="403006-devicemaximumactivefileuploadlimitexceeded"></a>403006 DeviceMaximumActiveFileUploadLimitExceeded

В этой статье описаны причины и решения для **403006 DeviceMaximumActiveFileUploadLimitExceeded** ошибок.

## <a name="symptoms"></a>Симптомы

Ваш запрос загрузки файла не справляется с кодом ошибки **403006** и сообщение "Количество активных запросов загрузки файлов не может превышать 10".

## <a name="cause"></a>Причина

Каждый клиент устройства ограничен [10 одновременными загрузками файлов.](./iot-hub-devguide-quotas-throttling.md#other-limits) 

Вы можете легко превысить лимит, если устройство не уведомит IoT Hub о завершении загрузки файлов. Эта проблема обычно вызвана ненадежной боковой сетью устройства.

## <a name="solution"></a>Решение

Убедитесь, что устройство может оперативно [уведомить о завершении загрузки файла IoT Hub.](./iot-hub-devguide-file-upload.md#notify-iot-hub-of-a-completed-file-upload) Затем попробуйте [уменьшить токен SAS TTL для конфигурации загрузки файлов.](iot-hub-configure-file-upload.md)

## <a name="next-steps"></a>Дальнейшие действия

Чтобы узнать больше о загрузках файлов, смотрите [Загружать файлы с IoT концентратором](./iot-hub-devguide-file-upload.md) и [настроить ioT концентратор загружается с помощью портала Azure](./iot-hub-configure-file-upload.md).