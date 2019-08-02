---
title: Начало работы с функцией управления Центром Интернета вещей Azure (Python) | Документация Майкрософт
description: Запуск удаленной перезагрузки устройства с помощью функции управления устройствами в Центре Интернета вещей. Используйте пакеты SDK для Центра Интернета вещей Azure для Python, чтобы реализовать приложение имитированного устройства, содержащее прямой метод и приложение службы, которое его вызывает.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.devlang: python
ms.topic: conceptual
ms.date: 07/30/2019
ms.author: robinsh
ms.openlocfilehash: daba6601c0da2bd6d1b76897e7ce1ff75da67b5b
ms.sourcegitcommit: fecb6bae3f29633c222f0b2680475f8f7d7a8885
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/30/2019
ms.locfileid: "68667976"
---
# <a name="get-started-with-device-management-python"></a>Начало работы с управлением устройствами (Python)

[!INCLUDE [iot-hub-selector-dm-getstarted](../../includes/iot-hub-selector-dm-getstarted.md)]

В этом учебнике описаны следующие процедуры.

* Создание экземпляра Центра Интернета вещей на портале Azure и удостоверения устройства в экземпляре Центра Интернета вещей.

* Создание приложения для имитации устройства с прямым методом, который позволяет выполнить перезагрузку устройства. Прямые методы вызываются из облака.

* Создание консольного приложения для Python, вызывающего прямой метод перезагрузки в приложении имитированного устройства с помощью Центра Интернета вещей.

По завершении работы с этим руководством у вас будет два консольных приложения Python:

* **dmpatterns_getstarted_device.py**, которое подключается к вашему центру Интернета вещей с ранее созданным идентификатором устройства, получает прямой метод перезагрузки, имитирует физическую перезагрузку и сообщает о времени последней перезагрузки.

* **dmpatterns_getstarted_service.py**, которое вызывает прямой метод в приложении для имитации устройства, выводит ответ и отображает обновленные сообщаемые свойства.

[!INCLUDE [iot-hub-include-python-sdk-note](../../includes/iot-hub-include-python-sdk-note.md)]

Ниже приведены инструкции по установке необходимых компонентов.

[!INCLUDE [iot-hub-include-python-installation-notes](../../includes/iot-hub-include-python-installation-notes.md)]

## <a name="create-an-iot-hub"></a>Создание Центра Интернета вещей

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

[!INCLUDE [iot-hub-get-started-create-device-identity](../../includes/iot-hub-get-started-create-device-identity.md)]

## <a name="create-a-simulated-device-app"></a>Создание приложения виртуального устройства

В этом разделе вы сделаете следующее:

* создадите консольное приложение Python, отвечающее на прямой метод, вызываемый из облака;

* смоделируете перезагрузку устройства;

* используете сообщаемые свойства в запросах двойника устройства для определения устройств и времени последней перезагрузки.

1. В текстовом редакторе создайте файл **dmpatterns_getstarted_device.py**.

2. Добавьте следующие инструкции `import` в начале файла **dmpatterns_getstarted_device.js**.

    ```python
    import random
    import time, datetime
    import sys

    import iothub_client
    from iothub_client import IoTHubClient, IoTHubClientError, IoTHubTransportProvider, IoTHubClientResult, IoTHubError, DeviceMethodReturnValue
    ```

3. Добавьте переменные, в том числе переменную **CONNECTION_STRING** и инициализацию клиента.  Замените connection string строкой подключения своего устройства.  

    ```python
    CONNECTION_STRING = "{deviceConnectionString}"
    PROTOCOL = IoTHubTransportProvider.MQTT

    CLIENT = IoTHubClient(CONNECTION_STRING, PROTOCOL)

    WAIT_COUNT = 5

    SEND_REPORTED_STATE_CONTEXT = 0
    METHOD_CONTEXT = 0

    SEND_REPORTED_STATE_CALLBACKS = 0
    METHOD_CALLBACKS = 0
    ```

4. Добавьте следующую функцию обратных вызовов, чтобы реализовать прямой метод на устройстве.

    ```python
    def send_reported_state_callback(status_code, user_context):
        global SEND_REPORTED_STATE_CALLBACKS

        print ( "Device twins updated." )

    def device_method_callback(method_name, payload, user_context):
        global METHOD_CALLBACKS

        if method_name == "rebootDevice":
            print ( "Rebooting device..." )
            time.sleep(20)

            print ( "Device rebooted." )

            current_time = str(datetime.datetime.now())
            reported_state = "{\"rebootTime\":\"" + current_time + "\"}"
            CLIENT.send_reported_state(reported_state, len(reported_state), send_reported_state_callback, SEND_REPORTED_STATE_CONTEXT)

            print ( "Updating device twins: rebootTime" )

        device_method_return_value = DeviceMethodReturnValue()
        device_method_return_value.response = "{ \"Response\": \"This is the response from the device\" }"
        device_method_return_value.status = 200

        return device_method_return_value
    ```

5. Запустите прослушиватель прямого метода и подождите.

    ```python
    def iothub_client_init():
        if CLIENT.protocol == IoTHubTransportProvider.MQTT or client.protocol == IoTHubTransportProvider.MQTT_WS:
            CLIENT.set_device_method_callback(device_method_callback, METHOD_CONTEXT)

    def iothub_client_sample_run():
        try:
            iothub_client_init()

            while True:
                print ( "IoTHubClient waiting for commands, press Ctrl-C to exit" )

                status_counter = 0
                while status_counter <= WAIT_COUNT:
                    time.sleep(10)
                    status_counter += 1

        except IoTHubError as iothub_error:
            print ( "Unexpected error %s from IoTHub" % iothub_error )
            return
        except KeyboardInterrupt:
            print ( "IoTHubClient sample stopped" )

    if __name__ == '__main__':
        print ( "Starting the IoT Hub Python sample..." )
        print ( "    Protocol %s" % PROTOCOL )
        print ( "    Connection string=%s" % CONNECTION_STRING )

        iothub_client_sample_run()
    ```

6. Сохраните и закройте файл **dmpatterns_getstarted_device.py**.

> [!NOTE]
> Для простоты в этом руководстве не реализуются политики повтора. В рабочем коде следует реализовать политики повторных попыток (например, с экспоненциальной задержкой), как указано в статье [Обработка временных сбоев](/azure/architecture/best-practices/transient-faults).

## <a name="get-the-iot-hub-connection-string"></a>Получение строки подключения для центра Интернета вещей

[!INCLUDE [iot-hub-howto-device-management-shared-access-policy-text](../../includes/iot-hub-howto-device-management-shared-access-policy-text.md)]

[!INCLUDE [iot-hub-include-find-service-connection-string](../../includes/iot-hub-include-find-service-connection-string.md)]

## <a name="trigger-a-remote-reboot-on-the-device-using-a-direct-method"></a>Активация удаленной перезагрузки на устройстве с помощью прямого метода

В этом разделе вы создадите консольное приложение Python, которое инициирует удаленное обновление устройства с помощью прямого метода. Приложение использует запросы двойника устройства для определения времени последней перезагрузки этого устройства.

1. В текстовом редакторе создайте файл **dmpatterns_getstarted_service.py**.

2. Добавьте следующие инструкции `import` в начале файла **dmpatterns_getstarted_service.py**.

    ```python
    import sys, time
    import iothub_service_client

    from iothub_service_client import IoTHubDeviceMethod, IoTHubError, IoTHubDeviceTwin
    ```

3. Добавьте следующие объявления переменных. Только замените значения заполнителей для параметров _IoTHubConnectionString_ и _deviceId_.

    ```python
    CONNECTION_STRING = "{IoTHubConnectionString}"
    DEVICE_ID = "{deviceId}"

    METHOD_NAME = "rebootDevice"
    METHOD_PAYLOAD = "{\"method_number\":\"42\"}"
    TIMEOUT = 60
    WAIT_COUNT = 10
    ```

4. Добавьте следующую функцию для вызова метода устройства, чтобы перезагрузить целевое устройство, а затем выполнить запрос к двойникам устройства и получить сведения о времени последней перезагрузки.

    ```python
    def iothub_devicemethod_sample_run():
        try:
            iothub_twin_method = IoTHubDeviceTwin(CONNECTION_STRING)
            iothub_device_method = IoTHubDeviceMethod(CONNECTION_STRING)

            print ( "" )
            print ( "Invoking device to reboot..." )

            response = iothub_device_method.invoke(DEVICE_ID, METHOD_NAME, METHOD_PAYLOAD, TIMEOUT)

            print ( "" )
            print ( "Successfully invoked the device to reboot." )

            print ( "" )
            print ( response.payload )

            while True:
                print ( "" )
                print ( "IoTHubClient waiting for commands, press Ctrl-C to exit" )

                status_counter = 0
                while status_counter <= WAIT_COUNT:
                    twin_info = iothub_twin_method.get_twin(DEVICE_ID)

                    if twin_info.find("rebootTime") != -1:
                        print ( "Last reboot time: " + twin_info[twin_info.find("rebootTime")+11:twin_info.find("rebootTime")+37])
                    else:
                        print ("Waiting for device to report last reboot time...")

                    time.sleep(5)
                    status_counter += 1

        except IoTHubError as iothub_error:
            print ( "" )
            print ( "Unexpected error {0}".format(iothub_error) )
            return
        except KeyboardInterrupt:
            print ( "" )
            print ( "IoTHubDeviceMethod sample stopped" )

    if __name__ == '__main__':
        print ( "Starting the IoT Hub Service Client DeviceManagement Python sample..." )
        print ( "    Connection string = {0}".format(CONNECTION_STRING) )
        print ( "    Device ID         = {0}".format(DEVICE_ID) )

        iothub_devicemethod_sample_run()
    ```

5. Сохраните и закройте файл **dmpatterns_getstarted_service.py**.

## <a name="run-the-apps"></a>Запуск приложений

Теперь все готово к запуску приложений.

1. В командной строке выполните следующую команду, чтобы начать прослушивание прямого метода перезагрузки.

    ```
    python dmpatterns_getstarted_device.py
    ```

2. В другой командной строке выполните следующую команду, чтобы активировать удаленную перезагрузку и выполнить запрос к двойнику устройства для поиска значения времени последней перезагрузки.

    ```
    python dmpatterns_getstarted_service.py
    ```

3. В консоли отобразится ответ устройства на прямой метод.

[!INCLUDE [iot-hub-dm-followup](../../includes/iot-hub-dm-followup.md)]