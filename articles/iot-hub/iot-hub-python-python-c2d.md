---
title: Отправка сообщений из облака на устройство с помощью Центра Интернета вещей Azure (Python) | Документация Майкрософт
description: Сведения об отправке сообщений из облака на устройство из Центра Интернета вещей Azure с помощью пакетов SDK для Интернета вещей Azure для Python. Для получения сообщений, отправляемых из облака на устройство, следует изменить приложение имитации устройства, а для отправки таких сообщений следует изменить внутреннее приложение.
author: kgremban
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.devlang: python
ms.topic: conceptual
ms.date: 02/22/2019
ms.author: kgremban
ms.openlocfilehash: 7ac668bdbc3698be3ed2aa50a428cef84e68369a
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/08/2019
ms.locfileid: "59272876"
---
# <a name="send-cloud-to-device-messages-with-iot-hub-python"></a>Отправка сообщений из облака на устройства с помощью Центра Интернета вещей (Python)

[!INCLUDE [iot-hub-selector-c2d](../../includes/iot-hub-selector-c2d.md)]

## <a name="introduction"></a>Введение
Центр Интернета вещей Azure — это полностью управляемая служба, которая обеспечивает надежный и защищенный двунаправленный обмен данными между миллионами устройств и серверной частью решения. [Приступить к работе с центром Интернета вещей](quickstart-send-telemetry-python.md) кратком руководстве показано, как создать центр Интернета вещей, подготовить в нем удостоверение устройства и написать код приложения имитации устройства, которое отправляет сообщения с устройства в облако.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

Это руководство является логическим продолжением статьи [Приступая к работе с Центром Интернета вещей с использованием Java](quickstart-send-telemetry-python.md). В нем показано следующее:

* Отправка сообщений, передаваемых из облака на устройство, из серверной части решения на одно устройство через Центр Интернета вещей.

* Получение на устройстве сообщений, передаваемых из облака на устройство.

* Запрос из серверной части решения подтверждения доставки (*отзыва*) для сообщений, отправленных на устройство из Центра Интернета вещей.

Можно найти дополнительные сведения о сообщениях облаком и устройством в [руководство разработчика для центра Интернета вещей](iot-hub-devguide-messaging.md).

По завершении работы с этим руководством вы запустите два консольных приложения Python:

* **SimulatedDevice.py**, измененную версию приложения, созданного в руководстве по [началу работы с Центром Интернета вещей](quickstart-send-telemetry-python.md). Это приложение подключается к центру IoT и передает сообщения из облака на устройство.

* **SendCloudToDeviceMessage.py**, которое отправляет сообщение из облака через Центр Интернета вещей в приложение имитированного устройства, а затем получает подтверждение доставки.

> [!NOTE]
> В Центре Интернета вещей реализована поддержка для пакетов SDK для многих платформ устройств и языков (включая C, Java и Javascript). Эти пакеты работают на основе пакетов SDK для устройств Azure IoT. Пошаговые указания по связыванию устройства с кодом из этого руководства, а также по подключению к Центру Интернета вещей Azure см. в [центре разработчиков для Интернета вещей Azure](https://www.azure.com/develop/iot).
>

Для работы с этим учебником требуется:

* [Python 2.x или 3.x](https://www.python.org/downloads/). Обязательно используйте 32-разрядную или 64-разрядную версию установки согласно требованиям программы настройки. При появлении запроса во время установки обязательно добавьте Python в переменную среды соответствующей платформы. Если вы используете Python 2.x, может потребоваться [установка или обновление *pip* — системы управления пакетами Python](https://pip.pypa.io/en/stable/installing/).

* Если вы работаете с ОС Windows, потребуется [распространяемый пакет Visual C++](https://www.microsoft.com/download/confirmation.aspx?id=48145), чтобы разрешить использовать собственные библиотеки DLL из Python.

* Активная учетная запись Azure. Если ее нет, можно создать [бесплатную учетную запись](https://azure.microsoft.com/pricing/free-trial/) всего за несколько минут.

> [!NOTE]
> Сейчас пакеты *pip* для `azure-iothub-service-client` и `azure-iothub-device-client` доступны только для ОС Windows. Linux и Mac OS, см. в статье в Linux и Mac OS конкретных разделах на [Подготовка среды разработки для Python](https://github.com/Azure/azure-iot-sdk-python/blob/master/doc/python-devbox-setup.md) блога.
>

## <a name="receive-messages-in-the-simulated-device-app"></a>Получение сообщений в приложении для имитации устройства

В этом разделе вы создадите консольное приложение Python для имитации устройства, получите сообщения из облака и передадите на устройство через Центр Интернета вещей.

1. В текстовом редакторе создайте файл **SimulatedDevice.py**.

2. Добавьте следующие инструкции `import` и переменные в начало файла **SimulatedDevice.py**:

   ```python
    import time
    import sys
    import iothub_client
    from iothub_client import IoTHubClient, IoTHubClientError, IoTHubTransportProvider, IoTHubClientResult
    from iothub_client import IoTHubMessage, IoTHubMessageDispositionResult, IoTHubError

    RECEIVE_CONTEXT = 0
    WAIT_COUNT = 10
    RECEIVED_COUNT = 0
    RECEIVE_CALLBACKS = 0
    ```

3. Добавьте приведенный ниже код в файл **SimulatedDevice.py**. Замените заполнитель «{deviceConnectionString} значением строки подключения устройства для устройства, созданного в [приступить к работе с центром Интернета вещей](quickstart-send-telemetry-python.md) краткое руководство:

    ```python
    # choose AMQP or AMQP_WS as transport protocol
    PROTOCOL = IoTHubTransportProvider.AMQP
    CONNECTION_STRING = "{deviceConnectionString}"
    ```

4. Добавьте указанную ниже функцию для вывода в консоль полученных сообщений.

    ```python
    def receive_message_callback(message, counter):
        global RECEIVE_CALLBACKS
        message_buffer = message.get_bytearray()
        size = len(message_buffer)
        print ( "Received Message [%d]:" % counter )
        print ( "    Data: <<<%s>>> & Size=%d" % (message_buffer[:size].decode('utf-8'), size) )
        map_properties = message.properties()
        key_value_pair = map_properties.get_internals()
        print ( "    Properties: %s" % key_value_pair )
        counter += 1
        RECEIVE_CALLBACKS += 1
        print ( "    Total calls received: %d" % RECEIVE_CALLBACKS )
        return IoTHubMessageDispositionResult.ACCEPTED

    def iothub_client_init():
        client = IoTHubClient(CONNECTION_STRING, PROTOCOL)

        client.set_message_callback(receive_message_callback, RECEIVE_CONTEXT)

        return client

    def print_last_message_time(client):
        try:
            last_message = client.get_last_message_receive_time()
            print ( "Last Message: %s" % time.asctime(time.localtime(last_message)) )
            print ( "Actual time : %s" % time.asctime() )
        except IoTHubClientError as iothub_client_error:
            if iothub_client_error.args[0].result == IoTHubClientResult.INDEFINITE_TIME:
                print ( "No message received" )
            else:
                print ( iothub_client_error )
    ```

5. Добавьте следующий код, чтобы инициализировать клиент и ожидать передачи сообщения из облака на устройство:

    ```python
    def iothub_client_init():
        client = IoTHubClient(CONNECTION_STRING, PROTOCOL)

        client.set_message_callback(receive_message_callback, RECEIVE_CONTEXT)

        return client

    def iothub_client_sample_run():
        try:
            client = iothub_client_init()

            while True:
                print ( "IoTHubClient waiting for commands, press Ctrl-C to exit" )

                status_counter = 0
                while status_counter <= WAIT_COUNT:
                    status = client.get_send_status()
                    print ( "Send status: %s" % status )
                    time.sleep(10)
                    status_counter += 1

        except IoTHubError as iothub_error:
            print ( "Unexpected error %s from IoTHub" % iothub_error )
            return
        except KeyboardInterrupt:
            print ( "IoTHubClient sample stopped" )

        print_last_message_time(client)
    ```

6. Добавьте функцию main со следующим содержимым:

    ```python
    if __name__ == '__main__':
        print ( "Starting the IoT Hub Python sample..." )
        print ( "    Protocol %s" % PROTOCOL )
        print ( "    Connection string=%s" % CONNECTION_STRING )

        iothub_client_sample_run()
    ```

7. Сохраните и закройте файл **SimulatedDevice.py**.

## <a name="send-a-cloud-to-device-message"></a>Отправка сообщения из облака на устройство

В этом разделе вам предстоит создать консольное приложение Python, которое отправляет в приложение имитации устройства сообщения, передаваемые из облака на устройство. Требуется идентификатор устройства, устройства, добавленного при изучении [приступить к работе с центром Интернета вещей](quickstart-send-telemetry-python.md) быстрого запуска. Кроме того, нужна строка подключения для вашего экземпляра Центра Интернета вещей, которую можно найти на [портале Azure](https://portal.azure.com).

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

3. Добавьте приведенный ниже код в файл **SendCloudToDeviceMessage.js**. Замените значение заполнителя «{IoTHubConnectionString} строкой подключения центра Интернета вещей для центра, созданного в [приступить к работе с центром Интернета вещей](quickstart-send-telemetry-python.md) быстрого запуска. Замените заполнитель {deviceId}» с Идентификатором устройства, устройства, добавленного при изучении [приступить к работе с центром Интернета вещей](quickstart-send-telemetry-python.md) краткое руководство:

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

    ```
    pip install azure-iothub-device-client
    ```

2. В командной строке выполните приведенную ниже команду, чтобы прослушивать сообщения из облака на устройство.

    ```shell
    python SimulatedDevice.py
    ```

    ![Запуск приложения виртуального устройства](./media/iot-hub-python-python-c2d/simulated-device.png)

3. Откройте еще одно окно командной строки и установите в нем **пакет SDK службы Центра Интернета вещей Azure для Python**.

    ```
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

Примеры комплексных решений, в которых используется Центр Интернета вещей, см. в [документации по акселераторам решений для удаленного мониторинга Интернета вещей Azure](https://azure.microsoft.com/documentation/suites/iot-suite/).

Дополнительные сведения о разработке решений с помощью Центра Интернета вещей см. в [руководстве разработчика для Центра Интернета вещей](iot-hub-devguide.md).
