---
title: Создание строки подключения устройства для центра Интернета вещей Azure | Документация Майкрософт
description: Как разработчик устройств как я могу создать строку подключения для устройства, которому должен подключиться к приложению IoT Central?
author: dominicbetts
ms.author: dobett
ms.date: 04/09/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: philmea
ms.openlocfilehash: 3a5e8d15d9a705892fe54c50e9b79e6d42af78d9
ms.sourcegitcommit: ef20235daa0eb98a468576899b590c0bc1a38394
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/09/2019
ms.locfileid: "59426738"
---
# <a name="generate-a-device-connection-string-to-connect-to-an-azure-iot-central-application"></a>Создание строки подключения устройства для подключения к приложению Azure IoT Central

В этой статье описывается как, как разработчик устройств, чтобы создать строку подключения для устройства, которому должен подключиться к приложению IoT Central. Процедура, описанная в этой статье показано, как быстро выполнить подключение одного устройства с помощью подписи общего доступа (SAS). Этот подход полезен в тех случаях, когда будете экспериментировать с IoT Central или тестирование устройств. Альтернативные подходы для использования в рабочей среде, см. в разделе [подключение устройств в Azure IoT Central](concepts-connectivity.md).

## <a name="prerequisites"></a>Технические условия

Чтобы выполнить действия, описанные в этой статье, необходимо следующее:

1. Приложение Azure IoT Central. Дополнительные сведения см. в [кратком руководстве по созданию приложения](quick-deploy-iot-central.md).
1. Компьютер для разработки с [Node.js](https://nodejs.org/) версии 8.0.0 или более поздней версии. Вы можете запустить `node --version` в командной строке, чтобы проверить версию. Node.js доступен для разных операционных систем.

## <a name="get-connection-information"></a>Получение сведений о подключении

Следующие шаги описывают, как получить сведения, необходимые для создания строки подключения SAS для устройства:

1. В **Explorer**, найти реального устройства, требуется подключение к приложению:

    ![Выберите физическое устройство](media/howto-generate-connection-string/real-devices.png)

1. На **устройства** выберите **Connect**:

    ![Выберите подключение](media/howto-generate-connection-string/connect.png)

1. Запомните или запишите сведения о подключении, **идентификатор области**, **идентификатор устройства**, и **устройства первичный ключ**, чтобы использовать в следующих шагах:

    ![Сведения о подключении](media/howto-generate-connection-string/device-connect.png)

    Можно скопировать значения на этой странице, чтобы сохранить.

## <a name="generate-the-connection-string"></a>Создать строку подключения

[!INCLUDE [iot-central-howto-connection-string](../../includes/iot-central-howto-connection-string.md)]

## <a name="next-steps"></a>Дальнейшие действия

Теперь, созданной разработчиком строку подключения для реального устройства для подключения к приложению Azure IoT Central, ниже приведены предлагаемые дальнейшие действия.

* [Подготовить и подключить устройство DevKit (C)](howto-connect-devkit.md)
* [Подготовка и подключение Raspberry Pi (Python)](howto-connect-raspberry-pi-python.md)
* [Подготовка и подключение Raspberry Pi (C#)](howto-connect-raspberry-pi-csharp.md)
* [Подготовка и подключение устройства Windows 10 IoT core (C#)](howto-connect-windowsiotcore.md)
* [Подключение универсального клиента Node.js к приложению Azure IoT Central](howto-connect-nodejs.md)