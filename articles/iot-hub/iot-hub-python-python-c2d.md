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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2020
ms.locfileid: "81759743"
---
# <a name="send-cloud-to-device-messages-with-iot-hub-python"></a>Отправка сообщений из облака на устройства с помощью Центра Интернета вещей (Python)

[!INCLUDE [iot-hub-selector-c2d](../../includes/iot-hub-selector-c2d.md)]

Центр Интернета вещей Azure — это полностью управляемая служба, которая обеспечивает надежный и защищенный двунаправленный обмен данными между миллионами устройств и серверной частью решения. В кратком руководстве [Отправка данных телеметрии с устройства в центр Интернета вещей](quickstart-send-telemetry-python.md) показано, как создать центр Интернета вещей, подготавливать в нем удостоверение устройства и создать код для приложения имитации устройства, которое отправляет сообщения, отправляемые с устройства в облако.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

Это руководство строится на [отправке данных телеметрии с устройства в центр Интернета вещей](quickstart-send-telemetry-python.md). В нем показано следующее:

* Отправка сообщений, передаваемых из облака на устройство, из серверной части решения на одно устройство через Центр Интернета вещей.

* Получение на устройстве сообщений, передаваемых из облака на устройство.

Дополнительные сведения о сообщениях, отправляемых из облака на устройство, см. в [руководстве разработчика для Центра Интернета вещей](iot-hub-devguide-messaging.md).

По завершении работы с этим руководством вы запустите два консольных приложения Python:

* **SimulatedDevice.py**— измененная версия приложения, созданная при [отправке данных телеметрии с устройства в центр Интернета вещей](quickstart-send-telemetry-python.md), который подключается к центру Интернета вещей и получает сообщения из облака на устройство.

* **SendCloudToDeviceMessage.py**, который отправляет сообщения из облака на устройство в приложение имитации устройства через центр Интернета вещей.

[!INCLUDE [iot-hub-include-python-sdk-note](../../includes/iot-hub-include-python-sdk-note.md)]

## <a name="prerequisites"></a>Предварительные условия

[!INCLUDE [iot-hub-include-python-v2-installation-notes](../../includes/iot-hub-include-python-v2-installation-notes.md)]

* Убедитесь, что в брандмауэре открыт порт 8883. В примере для устройства в этой статье используется протокол MQTT, который обменивается данными через порт 8883. В некоторых корпоративных и академических сетях этот порт может быть заблокирован. Дополнительные сведения и способы устранения этой проблемы см. в разделе о [подключении к Центру Интернета вещей по протоколу MQTT](iot-hub-mqtt-support.md#connecting-to-iot-hub).

## <a name="receive-messages-in-the-simulated-device-app"></a>Получение сообщений в приложении для имитации устройства

В этом разделе вы создадите консольное приложение Python для имитации устройства, получите сообщения из облака и передадите на устройство через Центр Интернета вещей.

1. В командной строке в рабочем каталоге установите **пакет SDK для устройств центра Интернета вещей Azure для Python**:

    ```cmd/sh
    pip install azure-iot-device
    ```

1. С помощью текстового редактора создайте файл с именем **SimulatedDevice.py**.

1. Добавьте следующие инструкции `import` и переменные в начало файла **SimulatedDevice.py**:

    ```python
    import threading
    import time
    from azure.iot.device import IoTHubDeviceClient

    RECEIVED_MESSAGES = 0
    ```

1. Добавьте приведенный ниже код в файл **SimulatedDevice.py**. Замените значение `{deviceConnectionString}` заполнителя строкой подключения устройства, созданной вами в кратком руководстве [Отправка данных телеметрии с устройства в центр Интернета вещей](quickstart-send-telemetry-python.md) :

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

## <a name="get-the-iot-hub-connection-string"></a>Получение строки подключения для центра Интернета вещей

В этой статье вы создадите серверную службу для отправки сообщений из облака на устройство через центр Интернета вещей, созданный в разделе [Отправка телеметрических данных с устройства в центр Интернета вещей](quickstart-send-telemetry-python.md). Для отправки сообщений из облака на устройство службе требуется разрешение на **Подключение к службе** . По умолчанию каждый центр Интернета вещей создается с помощью политики общего доступа с именем **Service** , предоставляющей это разрешение.

[!INCLUDE [iot-hub-include-find-service-connection-string](../../includes/iot-hub-include-find-service-connection-string.md)]

## <a name="send-a-cloud-to-device-message"></a>Отправка сообщения из облака на устройство

В этом разделе вам предстоит создать консольное приложение Python, которое отправляет в приложение имитации устройства сообщения, передаваемые из облака на устройство. Вам потребуется идентификатор устройства, добавленного в краткое руководство по [отправке данных телеметрии с устройства в центр Интернета вещей](quickstart-send-telemetry-python.md) . Вам также потребуется строка подключения центра Интернета вещей, скопированная ранее в [поле получение строки подключения для центра Интернета вещей](#get-the-iot-hub-connection-string).

1. В рабочем каталоге откройте командную строку и установите **пакет SDK службы центра Интернета вещей Azure для Python**.

   ```cmd/sh
   pip install azure-iot-hub
   ```

1. С помощью текстового редактора создайте файл с именем **SendCloudToDeviceMessage.py**.

1. Добавьте следующие инструкции `import` и переменные в начало файла **SendCloudToDeviceMessage.js**:

    ```python
    import random
    import sys
    from azure.iot.hub import IoTHubRegistryManager

    MESSAGE_COUNT = 2
    AVG_WIND_SPEED = 10.0
    MSG_TXT = "{\"service client sent a message\": %.2f}"
    ```

1. Добавьте приведенный ниже код в файл **SendCloudToDeviceMessage.js**. Замените значения `{iot hub connection string}` заполнителей и `{device id}` строкой подключения центра Интернета вещей и идентификатором устройства, записанным ранее:

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

1. В командной строке в рабочем каталоге выполните следующую команду, чтобы прослушать сообщения, отправляемые из облака на устройство:

    ```shell
    python SimulatedDevice.py
    ```

    ![Запуск приложения виртуального устройства](./media/iot-hub-python-python-c2d/device-1.png)

1. Откройте новую командную строку в рабочем каталоге и выполните следующую команду, чтобы отправить сообщения из облака на устройство:

    ```shell
    python SendCloudToDeviceMessage.py
    ```

    ![Запуск приложения для отправки команды из облака на устройство](./media/iot-hub-python-python-c2d/service.png)

1. Обратите внимание на сообщения, полученные устройством.

    ![Полученное сообщение](./media/iot-hub-python-python-c2d/device-2.png)

## <a name="next-steps"></a>Дальнейшие шаги

В этом учебнике вы научились отправлять и получать сообщения с облака на устройство.

Примеры комплексных решений, в которых используется Центр Интернета вещей, см. в [документации по акселератору решения Azure IoT для удаленного мониторинга](https://azure.microsoft.com/documentation/suites/iot-suite/).

Дополнительные сведения о разработке решений с помощью Центра Интернета вещей см. в [руководстве разработчика для Центра Интернета вещей](iot-hub-devguide.md).
