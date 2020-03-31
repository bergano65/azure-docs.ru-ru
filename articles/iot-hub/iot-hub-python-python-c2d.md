---
title: Отправка сообщений из облака на устройство с помощью Центра Интернета вещей Azure (Python) | Документация Майкрософт
description: Сведения об отправке сообщений из облака на устройство из Центра Интернета вещей Azure с помощью пакетов SDK для Интернета вещей Azure для Python. Для получения сообщений, отправляемых из облака на устройство, следует изменить приложение имитации устройства, а для отправки таких сообщений следует изменить внутреннее приложение.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.devlang: python
ms.topic: conceptual
ms.date: 07/30/2019
ms.author: robinsh
ms.openlocfilehash: 3613062cf8765a4aec3327b660bb5818898f2dd1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77110418"
---
# <a name="send-cloud-to-device-messages-with-iot-hub-python"></a>Отправка сообщений из облака на устройства с помощью Центра Интернета вещей (Python)

[!INCLUDE [iot-hub-selector-c2d](../../includes/iot-hub-selector-c2d.md)]

Центр Интернета вещей Azure — это полностью управляемая служба, которая обеспечивает надежный и защищенный двунаправленный обмен данными между миллионами устройств и серверной частью решения. [Телеметрия отправки с устройства на быстрый запуск концентратора IoT](quickstart-send-telemetry-python.md) показывает, как создать концентратор IoT, предоставить в нем идентификацию устройства и кодировать смоделированное приложение устройства, которое отправляет сообщения от устройства к облаку.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

Этот учебник основан на [отправке телеметрии с устройства на концентратор IoT.](quickstart-send-telemetry-python.md) В нем показано следующее:

* Отправка сообщений, передаваемых из облака на устройство, из серверной части решения на одно устройство через Центр Интернета вещей.

* Получение на устройстве сообщений, передаваемых из облака на устройство.

* От задней части решения — запрос подтверждения доставки *(обратной связи)* для сообщений, отправленных на устройство из Концентратора IoT.

Дополнительные сведения о сообщениях, отправляемых из облака на устройство, см. в [руководстве разработчика для Центра Интернета вещей](iot-hub-devguide-messaging.md).

По завершении работы с этим руководством вы запустите два консольных приложения Python:

* **SimulatedDevice.py**, модифицированная версия приложения, созданного в [Телеметрии Отправить с устройства на концентратор IoT](quickstart-send-telemetry-python.md), который подключается к концентратору IoT и получает сообщения от облака к устройству.

* **SendCloudToDeviceMessage.py,** который отправляет сообщение об ламента к устройству в смоделированное приложение устройства через IoT Hub, а затем получает подтверждение его доставки.

[!INCLUDE [iot-hub-include-python-sdk-note](../../includes/iot-hub-include-python-sdk-note.md)]

## <a name="prerequisites"></a>Предварительные требования

[!INCLUDE [iot-hub-include-python-installation-notes](../../includes/iot-hub-include-python-installation-notes.md)]

* Убедитесь, что в брандмауэре открыт порт 8883. Образец устройства в этой статье использует протокол МЗТТ, который общается по порту 8883. В некоторых корпоративных и академических сетях этот порт может быть заблокирован. Дополнительные сведения и способы устранения этой проблемы см. в разделе о [подключении к Центру Интернета вещей по протоколу MQTT](iot-hub-mqtt-support.md#connecting-to-iot-hub).

## <a name="receive-messages-in-the-simulated-device-app"></a>Получение сообщений в приложении для имитации устройства

В этом разделе вы создадите консольное приложение Python для имитации устройства, получите сообщения из облака и передадите на устройство через Центр Интернета вещей.

1. В текстовом редакторе создайте файл **SimulatedDevice.py**.

2. Добавьте следующие инструкции `import` и переменные в начало файла **SimulatedDevice.py**:

   ```python
    import threading
    from azure.iot.device import IoTHubDeviceClient

    RECEIVED_MESSAGES = 0
    ```

3. Добавьте приведенный ниже код в файл **SimulatedDevice.py**. Замените значение заполнителя «устройствоСоединение» строкой подключения к устройству, созданной в телеметрии Отправки с устройства на быстрый запуск [концентратора IoT:](quickstart-send-telemetry-python.md)

    ```python
    CONNECTION_STRING = "{deviceConnectionString}"
    ```

4. Добавьте указанную ниже функцию для вывода в консоль полученных сообщений.

    ```python
    def message_listener(client):
        global RECEIVED_MESSAGES
        while True:
            message = client.receive_message()
            RECEIVED_MESSAGES += 1
            print("Message received")
            print( "    Data: <<{}>>".format(message.data) )
            print( "    Properties: {}".format(message.custom_properties))
            print( "    Total calls received: {}".format(RECEIVED_MESSAGES))
    ```

5. Добавьте следующий код, чтобы инициализировать клиент и ожидать передачи сообщения из облака на устройство:

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
            print ( "IoTHubDeviceClient sample stopped" )
    ```

6. Добавьте функцию main со следующим содержимым:

    ```python
    if __name__ == '__main__':
        print ( "Starting the IoT Hub Python sample..." )
        print ( "IoTHubDeviceClient waiting for commands, press Ctrl-C to exit" )

        iothub_client_sample_run()
    ```

7. Сохраните и закройте файл **SimulatedDevice.py**.

## <a name="get-the-iot-hub-connection-string"></a>Получите строку соединения концентратора IoT

В этой статье вы создаете бэкэнд-сервис для отправки сообщений от облака к устройству через созданный концентратор IoT, созданный в [телеметрии От устройства к концентратору IoT.](quickstart-send-telemetry-python.md) Для отправки сообщений об облаке и устройстве службе требуется разрешение **на подключение службы.** По умолчанию каждый концентратор IoT создается с помощью службы общего доступа, названной **службой,** которая предоставляет это разрешение.

[!INCLUDE [iot-hub-include-find-service-connection-string](../../includes/iot-hub-include-find-service-connection-string.md)]

## <a name="send-a-cloud-to-device-message"></a>Отправка сообщения из облака на устройство

В этом разделе вам предстоит создать консольное приложение Python, которое отправляет в приложение имитации устройства сообщения, передаваемые из облака на устройство. Вам нужен идентификатор устройства, добавленного в [телеметрию Отправки с устройства на быстрый запуск концентратора IoT.](quickstart-send-telemetry-python.md) Вам также нужна строка соединения концентратора IoT, которая была скопирована ранее в [строке подключения концентратора IoT.](#get-the-iot-hub-connection-string)

1. В текстовом редакторе создайте файл **SendCloudToDeviceMessage.py**.

2. Добавьте следующие инструкции `import` и переменные в начало файла **SendCloudToDeviceMessage.js**:

    ```python
    import random
    import sys
    import iothub_service_client
    from iothub_service_client import IoTHubMessaging, IoTHubMessage, IoTHubError

    OPEN_CONTEXT = 0
    FEEDBACK_CONTEXT = 1
    MESSAGE_COUNT = 1
    AVG_WIND_SPEED = 10.0
    MSG_TXT = "{\"service client sent a message\": %.2f}"
    ```

3. Добавьте приведенный ниже код в файл **SendCloudToDeviceMessage.js**. Замените значения заполнителя «строка подключения концентратора» и «устройство id» строкой заполнителя строкой концентратора IoT и идентификатором устройства, который вы отметили ранее:

    ```python
    CONNECTION_STRING = "{IoTHubConnectionString}"
    DEVICE_ID = "{deviceId}"
    ```

4. Добавьте указанную ниже функцию для вывода ответных сообщений в консоль.

    ```python
    def open_complete_callback(context):
        print ( 'open_complete_callback called with context: {0}'.format(context) )

    def send_complete_callback(context, messaging_result):
        context = 0
        print ( 'send_complete_callback called with context : {0}'.format(context) )
        print ( 'messagingResult : {0}'.format(messaging_result) )
    ```

5. Добавьте приведенный ниже код для отправки сообщения на устройство и обработки сообщения о доставке, получаемого после подтверждения устройством получения сообщения из облака на устройство.

    ```python
    def iothub_messaging_sample_run():
        try:
            iothub_messaging = IoTHubMessaging(CONNECTION_STRING)

            iothub_messaging.open(open_complete_callback, OPEN_CONTEXT)

            for i in range(0, MESSAGE_COUNT):
                print ( 'Sending message: {0}'.format(i) )
                msg_txt_formatted = MSG_TXT % (AVG_WIND_SPEED + (random.random() * 4 + 2))
                message = IoTHubMessage(bytearray(msg_txt_formatted, 'utf8'))

                # optional: assign ids
                message.message_id = "message_%d" % i
                message.correlation_id = "correlation_%d" % i
                # optional: assign properties
                prop_map = message.properties()
                prop_text = "PropMsg_%d" % i
                prop_map.add("Property", prop_text)

                iothub_messaging.send_async(DEVICE_ID, message, send_complete_callback, i)

            try:
                # Try Python 2.xx first
                raw_input("Press Enter to continue...\n")
            except:
                pass
                # Use Python 3.xx in the case of exception
                input("Press Enter to continue...\n")

            iothub_messaging.close()

        except IoTHubError as iothub_error:
            print ( "Unexpected error {0}" % iothub_error )
            return
        except KeyboardInterrupt:
            print ( "IoTHubMessaging sample stopped" )
    ```

6. Добавьте функцию main со следующим содержимым:

    ```python
    if __name__ == '__main__':
        print ( "Starting the IoT Hub Service Client Messaging Python sample..." )
        print ( "    Connection string = {0}".format(CONNECTION_STRING) )
        print ( "    Device ID         = {0}".format(DEVICE_ID) )

        iothub_messaging_sample_run()
    ```

7. Сохраните и закройте файл **SendCloudToDeviceMessage.py**.

## <a name="run-the-applications"></a>Запуск приложений

Теперь все готово к запуску приложений.

1. Откройте окно командной строки и установите **пакет SDK устройства Центра Интернета вещей Azure для Python**.

    ```shell
    pip install azure-iothub-device-client
    ```

2. В командной строке выполните приведенную ниже команду, чтобы прослушивать сообщения из облака на устройство.

    ```shell
    python SimulatedDevice.py
    ```

    ![Запуск приложения виртуального устройства](./media/iot-hub-python-python-c2d/simulated-device.png)

3. Откройте еще одно окно командной строки и установите в нем **пакет SDK службы Центра Интернета вещей Azure для Python**.

    ```shell
    pip install azure-iothub-service-client
    ```

4. В командной строке выполните приведенную ниже команду, чтобы отправить сообщение из облака на устройство и ожидать подтверждение доставки.

    ```shell
    python SendCloudToDeviceMessage.py
    ```

    ![Запуск приложения для отправки команды из облака на устройство](./media/iot-hub-python-python-c2d/send-command.png)

5. Обратите внимание на сообщения, принятые устройством.

    ![Полученное сообщение](./media/iot-hub-python-python-c2d/message-received.png)

## <a name="next-steps"></a>Дальнейшие действия

В этом учебнике вы научились отправлять и получать сообщения с облака на устройство.

Примеры комплексных решений, в которых используется Центр Интернета вещей, см. в [документации по акселератору решения Azure IoT для удаленного мониторинга](https://azure.microsoft.com/documentation/suites/iot-suite/).

Дополнительные сведения о разработке решений с помощью Центра Интернета вещей см. в [руководстве разработчика для Центра Интернета вещей](iot-hub-devguide.md).
