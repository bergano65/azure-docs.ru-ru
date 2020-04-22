---
title: Отправка сообщений из облака на устройство с помощью Центра Интернета вещей Azure (Python) | Документация Майкрософт
description: Сведения об отправке сообщений из облака на устройство из Центра Интернета вещей Azure с помощью пакетов SDK для Интернета вещей Azure для Python. Для получения сообщений, отправляемых из облака на устройство, следует изменить приложение имитации устройства, а для отправки таких сообщений следует изменить внутреннее приложение.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.devlang: python
ms.topic: conceptual
ms.date: 04/09/2020
ms.author: robinsh
ms.custom: mqtt
ms.openlocfilehash: f0760f6e61904295771ba349f8101e2d6dc6afe3
ms.sourcegitcommit: 31e9f369e5ff4dd4dda6cf05edf71046b33164d3
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/22/2020
ms.locfileid: "81759743"
---
# <a name="send-cloud-to-device-messages-with-iot-hub-python"></a>Отправка сообщений из облака на устройства с помощью Центра Интернета вещей (Python)

[!INCLUDE [iot-hub-selector-c2d](../../includes/iot-hub-selector-c2d.md)]

Центр Интернета вещей Azure — это полностью управляемая служба, которая обеспечивает надежный и защищенный двунаправленный обмен данными между миллионами устройств и серверной частью решения. [Телеметрия отправки с устройства на быстрый запуск концентратора IoT](quickstart-send-telemetry-python.md) показывает, как создать концентратор IoT, предоставить в нем идентификацию устройства и кодировать смоделированное приложение устройства, которое отправляет сообщения от устройства к облаку.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

Этот учебник основан на [отправке телеметрии с устройства на концентратор IoT.](quickstart-send-telemetry-python.md) В нем показано следующее:

* Отправка сообщений, передаваемых из облака на устройство, из серверной части решения на одно устройство через Центр Интернета вещей.

* Получение на устройстве сообщений, передаваемых из облака на устройство.

Дополнительные сведения о сообщениях, отправляемых из облака на устройство, см. в [руководстве разработчика для Центра Интернета вещей](iot-hub-devguide-messaging.md).

По завершении работы с этим руководством вы запустите два консольных приложения Python:

* **SimulatedDevice.py**, модифицированная версия приложения, созданного в [Телеметрии Отправить с устройства на концентратор IoT](quickstart-send-telemetry-python.md), который подключается к концентратору IoT и получает сообщения от облака к устройству.

* **SendCloudToDeviceMessage.py,** который отправляет сообщения от облака к устройству в смоделированное приложение устройства через IoT Hub.

[!INCLUDE [iot-hub-include-python-sdk-note](../../includes/iot-hub-include-python-sdk-note.md)]

## <a name="prerequisites"></a>Предварительные требования

[!INCLUDE [iot-hub-include-python-v2-installation-notes](../../includes/iot-hub-include-python-v2-installation-notes.md)]

* Убедитесь, что в брандмауэре открыт порт 8883. Образец устройства в этой статье использует протокол МЗТТ, который общается по порту 8883. В некоторых корпоративных и академических сетях этот порт может быть заблокирован. Дополнительные сведения и способы устранения этой проблемы см. в разделе о [подключении к Центру Интернета вещей по протоколу MQTT](iot-hub-mqtt-support.md#connecting-to-iot-hub).

## <a name="receive-messages-in-the-simulated-device-app"></a>Получение сообщений в приложении для имитации устройства

В этом разделе вы создадите консольное приложение Python для имитации устройства, получите сообщения из облака и передадите на устройство через Центр Интернета вещей.

1. Из командного запроса в рабочем каталоге установите **SDK-концентратор Azure IoT для Python:**

    ```cmd/sh
    pip install azure-iot-device
    ```

1. С помощью текстового редактора создайте файл под названием **SimulatedDevice.py**.

1. Добавьте следующие инструкции `import` и переменные в начало файла **SimulatedDevice.py**:

    ```python
    import threading
    import time
    from azure.iot.device import IoTHubDeviceClient

    RECEIVED_MESSAGES = 0
    ```

1. Добавьте приведенный ниже код в файл **SimulatedDevice.py**. Замените `{deviceConnectionString}` значение заполнителя строкой подключения устройства для устройства, созданного в телеметрии Отправки с устройства на быстрый запуск [концентратора IoT:](quickstart-send-telemetry-python.md)

    ```python
    CONNECTION_STRING = "{deviceConnectionString}"
    ```

1. Добавьте указанную ниже функцию для вывода в консоль полученных сообщений.

    ```python
    def message_listener(client):
        global RECEIVED_MESSAGES
        while True:
            message = client.receive_message()
            RECEIVED_MESSAGES += 1
            print("\nMessage received:")

            #print data and both system and application (custom) properties
            for property in vars(message).items():
                print ("    {0}".format(property))

            print( "Total calls received: {}".format(RECEIVED_MESSAGES))
            print()
    ```

1. Добавьте следующий код, чтобы инициализировать клиент и ожидать передачи сообщения из облака на устройство:

    ```python
    def iothub_client_sample_run():
        try:
            client = IoTHubDeviceClient.create_from_connection_string(CONNECTION_STRING)

            message_listener_thread = threading.Thread(target=message_listener, args=(client,))
            message_listener_thread.daemon = True
            message_listener_thread.start()

            while True:
                time.sleep(1000)

        except KeyboardInterrupt:
            print ( "IoT Hub C2D Messaging device sample stopped" )
    ```

1. Добавьте функцию main со следующим содержимым:

    ```python
    if __name__ == '__main__':
        print ( "Starting the Python IoT Hub C2D Messaging device sample..." )
        print ( "Waiting for C2D messages, press Ctrl-C to exit" )

        iothub_client_sample_run()
    ```

1. Сохраните и закройте файл **SimulatedDevice.py**.

## <a name="get-the-iot-hub-connection-string"></a>Получите строку соединения концентратора IoT

В этой статье создается бэкэнд-сервис для отправки сообщений от облака к устройству через созданный концентратор IoT, созданный в [телеметрии Отправки с устройства на концентратор IoT.](quickstart-send-telemetry-python.md) Для отправки сообщений об облаке и устройстве службе требуется разрешение **на подключение службы.** По умолчанию каждый концентратор IoT создается с помощью службы общего доступа, названной **службой,** которая предоставляет это разрешение.

[!INCLUDE [iot-hub-include-find-service-connection-string](../../includes/iot-hub-include-find-service-connection-string.md)]

## <a name="send-a-cloud-to-device-message"></a>Отправка сообщения из облака на устройство

В этом разделе вам предстоит создать консольное приложение Python, которое отправляет в приложение имитации устройства сообщения, передаваемые из облака на устройство. Вам нужен идентификатор устройства, добавленного в [телеметрию Отправки с устройства на быстрый запуск концентратора IoT.](quickstart-send-telemetry-python.md) Вам также нужна строка подключения концентратора IoT, которая была скопирована ранее в [строке подключения концентратора IoT.](#get-the-iot-hub-connection-string)

1. В рабочем каталоге откройте запрос команды и установите **SDK-концентратор Azure IoT для Python.**

   ```cmd/sh
   pip install azure-iot-hub
   ```

1. С помощью текстового редактора создайте файл под названием **SendCloudToDeviceMessage.py**.

1. Добавьте следующие инструкции `import` и переменные в начало файла **SendCloudToDeviceMessage.js**:

    ```python
    import random
    import sys
    from azure.iot.hub import IoTHubRegistryManager

    MESSAGE_COUNT = 2
    AVG_WIND_SPEED = 10.0
    MSG_TXT = "{\"service client sent a message\": %.2f}"
    ```

1. Добавьте приведенный ниже код в файл **SendCloudToDeviceMessage.js**. Замените `{iot hub connection string}` `{device id}` значения и значения заполнителя строкой соединения Концентратора IoT и идентификатором устройства, который вы отметили ранее:

    ```python
    CONNECTION_STRING = "{IoTHubConnectionString}"
    DEVICE_ID = "{deviceId}"
    ```

1. Добавьте следующий код для отправки сообщений на устройство:

    ```python
    def iothub_messaging_sample_run():
        try:
            # Create IoTHubRegistryManager
            registry_manager = IoTHubRegistryManager(CONNECTION_STRING)

            for i in range(0, MESSAGE_COUNT):
                print ( 'Sending message: {0}'.format(i) )
                data = MSG_TXT % (AVG_WIND_SPEED + (random.random() * 4 + 2))

                props={}
                # optional: assign system properties
                props.update(messageId = "message_%d" % i)
                props.update(correlationId = "correlation_%d" % i)
                props.update(contentType = "application/json")

                # optional: assign application properties
                prop_text = "PropMsg_%d" % i
                props.update(testProperty = prop_text)

                registry_manager.send_c2d_message(DEVICE_ID, data, properties=props)

            try:
                # Try Python 2.xx first
                raw_input("Press Enter to continue...\n")
            except:
                pass
                # Use Python 3.xx in the case of exception
                input("Press Enter to continue...\n")

        except Exception as ex:
            print ( "Unexpected error {0}" % ex )
            return
        except KeyboardInterrupt:
            print ( "IoT Hub C2D Messaging service sample stopped" )
    ```

1. Добавьте функцию main со следующим содержимым:

    ```python
    if __name__ == '__main__':
        print ( "Starting the Python IoT Hub C2D Messaging service sample..." )

        iothub_messaging_sample_run()
    ```

1. Сохраните и закройте файл **SendCloudToDeviceMessage.py**.

## <a name="run-the-applications"></a>Запуск приложений

Теперь все готово к запуску приложений.

1. В запросе команды в рабочем каталоге запустите следующую команду для прослушивания сообщений от облака к устройству:

    ```shell
    python SimulatedDevice.py
    ```

    ![Запуск приложения виртуального устройства](./media/iot-hub-python-python-c2d/device-1.png)

1. Откройте новый запрос команды в рабочем каталоге и запустите следующую команду для отправки сообщений об облаке к устройству:

    ```shell
    python SendCloudToDeviceMessage.py
    ```

    ![Запуск приложения для отправки команды из облака на устройство](./media/iot-hub-python-python-c2d/service.png)

1. Обратите внимание на сообщения, полученные устройством.

    ![Полученное сообщение](./media/iot-hub-python-python-c2d/device-2.png)

## <a name="next-steps"></a>Следующие шаги

В этом учебнике вы научились отправлять и получать сообщения с облака на устройство.

Примеры комплексных решений, в которых используется Центр Интернета вещей, см. в [документации по акселератору решения Azure IoT для удаленного мониторинга](https://azure.microsoft.com/documentation/suites/iot-suite/).

Дополнительные сведения о разработке решений с помощью Центра Интернета вещей см. в [руководстве разработчика для Центра Интернета вещей](iot-hub-devguide.md).
