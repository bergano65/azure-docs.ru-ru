---
title: Создание строки подключения устройства для IoT Central Azure | Документация Майкрософт
description: Как разработчик устройства, как создать строку подключения для устройства, которое должно подключаться к IoT Central приложению?
author: dominicbetts
ms.author: dobett
ms.date: 08/23/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: philmea
ms.openlocfilehash: 97c0332656b75c3c8d0cddecb41c7a15ac2f218c
ms.sourcegitcommit: 80dff35a6ded18fa15bba633bf5b768aa2284fa8
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/26/2019
ms.locfileid: "70019776"
---
# <a name="generate-a-device-connection-string-to-connect-to-an-azure-iot-central-application"></a>Создание строки подключения устройства для подключения к приложению IoT Central Azure

В этой статье описывается, как разработчик устройства создает строку подключения для устройства, которое должно подключаться к IoT Central приложению. Процедура, описанная в этой статье, показывает, как быстро подключить одно устройство с помощью подписанного URL-адрес (SAS). Этот подход удобен при экспериментах с IoT Central или тестированием устройств. Альтернативные подходы к использованию в рабочей среде см. в статье [Подключение устройства в Azure IOT Central](concepts-connectivity.md).

## <a name="prerequisites"></a>Предварительные требования

Чтобы выполнить действия, описанные в этой статье, необходимо следующее:

- Приложение Azure IoT Central. Дополнительные сведения см. в [кратком руководстве по созданию приложения](quick-deploy-iot-central.md).
- Компьютер разработки с [node. js](https://nodejs.org/) версии 8.0.0 или более поздней. Вы можете запустить `node --version` в командной строке, чтобы проверить версию. Node.js доступен для разных операционных систем.

## <a name="get-connection-information"></a>Получение сведений о подключении

Следующие шаги описывают, как получить сведения, необходимые для создания строки подключения SAS для устройства.

1. В **Device Explorer**найдите реальное устройство, которое вы хотите подключить к приложению:

    ![Выберите реальное устройство](media/howto-generate-connection-string/real-devices.png)

1. На странице **устройство** выберите **подключить**:

    ![Выбор подключения](media/howto-generate-connection-string/connect.png)

1. Запишите сведения о подключении, **идентификатор области**, **идентификатор устройства**и **первичный ключ устройства**, чтобы использовать их в следующих шагах:

    ![Сведения о подключении](media/howto-generate-connection-string/device-connect.png)

    Вы можете скопировать значения с этой страницы, чтобы сохранить их.

## <a name="generate-the-connection-string"></a>Создание строки подключения

[!INCLUDE [iot-central-howto-connection-string](../../includes/iot-central-howto-connection-string.md)]

## <a name="next-steps"></a>Следующие шаги

Теперь, когда вы создали строку подключения для реального устройства для подключения к приложению Azure IoT Central, предлагаем следующие действия:

* [подключить устройство MXChip IoT DevKit к приложению Azure IoT Central](howto-connect-devkit.md);
* [подключить устройство Raspberry Pi к приложению Azure IoT Central (Python)](howto-connect-raspberry-pi-python.md);
* [подключить устройство Raspberry Pi к приложению Azure IoT Central (C#)](howto-connect-raspberry-pi-csharp.md);
* [подключить устройство Windows 10 IoT Core к приложению Azure IoT Central](howto-connect-windowsiotcore.md);
* [Подключение универсального клиентского приложения к приложению Azure IoT Central (Node.js)](howto-connect-nodejs.md)