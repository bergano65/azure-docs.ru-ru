---
title: Начало работы с функцией управления Центром Интернета вещей Azure (Python) | Документация Майкрософт
description: Запуск удаленной перезагрузки устройства с помощью функции управления устройствами в Центре Интернета вещей. Используйте пакеты SDK для Центра Интернета вещей Azure для Python, чтобы реализовать приложение имитированного устройства, содержащее прямой метод и приложение службы, которое его вызывает.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.devlang: python
ms.topic: conceptual
ms.date: 08/20/2019
ms.author: robinsh
ms.openlocfilehash: 514f4b26a708a6fec30a1f54cfe6da6d1b58b79d
ms.sourcegitcommit: 428fded8754fa58f20908487a81e2f278f75b5d0
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/27/2019
ms.locfileid: "74555520"
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

## <a name="prerequisites"></a>Технические условия

[!INCLUDE [iot-hub-include-python-installation-notes](../../includes/iot-hub-include-python-installation-notes.md)]

## <a name="create-an-iot-hub"></a>Создание центра IoT

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-new-device-in-the-iot-hub"></a>Регистрация нового устройства в центре Интернета вещей

[!INCLUDE [iot-hub-get-started-create-device-identity](../../includes/iot-hub-get-started-create-device-identity.md)]

## <a name="create-a-simulated-device-app"></a>Создание приложения виртуального устройства

В этом разделе выполняются следующие действия:

* создадите консольное приложение Python, отвечающее на прямой метод, вызываемый из облака;

* смоделируете перезагрузку устройства;

* используете сообщаемые свойства в запросах двойника устройства для определения устройств и времени последней перезагрузки.

1. В командной строке выполните следующую команду, чтобы установить пакет **Azure-IOT-Device** :

    ```cmd/sh
    pip install azure-iot-device
    ```

2. С помощью текстового редактора создайте файл с именем **dmpatterns_getstarted_device. копировать** в рабочем каталоге.

3. Добавьте следующие инструкции `import` в начале файла **dmpatterns_getstarted_device.js**.

    ```python
    import threading
    import time
    import datetime
    from azure.iot.device import IoTHubDeviceClient, MethodResponse
    ```

4. Добавьте переменную **CONNECTION_STRING** . Замените значение заполнителя `{deviceConnectionString}` строкой подключения устройства. Вы скопировали эту строку подключения ранее в раздел [Регистрация нового устройства в центре Интернета вещей](#register-a-new-device-in-the-iot-hub).  

    ```python
    CONNECTION_STRING = "{deviceConnectionString}"
    ```

5. Добавьте следующую функцию обратных вызовов, чтобы реализовать прямой метод на устройстве.

    ```python
    def reboot_listener(client):
        while True:
            # Receive the direct method request
            method_request = client.receive_method_request("rebootDevice")  # blocking call

            # Act on the method by rebooting the device...
            print( "Rebooting device" )
            time.sleep(20)
            print( "Device rebooted")

            # ...and patching the reported properties
            current_time = str(datetime.datetime.now())
            reported_props = {"rebootTime": current_time}
            client.patch_twin_reported_properties(reported_props)
            print( "Device twins updated with latest rebootTime")

            # Send a method response indicating the method request was resolved
            resp_status = 200
            resp_payload = {"Response": "This is the response from the device"}
            method_response = MethodResponse(method_request.request_id, resp_status, resp_payload)
            client.send_method_response(method_response)
    ```

6. Запустите прослушиватель прямого метода и подождите.

    ```python
    def iothub_client_init():
        client = IoTHubDeviceClient.create_from_connection_string(CONNECTION_STRING)
        return client

    def iothub_client_sample_run():
        try:
            client = iothub_client_init()

            # Start a thread listening for "rebootDevice" direct method invocations
            reboot_listener_thread = threading.Thread(target=reboot_listener, args=(client,))
            reboot_listener_thread.daemon = True
            reboot_listener_thread.start()

            while True:
                time.sleep(1000)

        except KeyboardInterrupt:
            print ( "IoTHubDeviceClient sample stopped" )

    if __name__ == '__main__':
        print ( "Starting the IoT Hub Python sample..." )
        print ( "IoTHubDeviceClient waiting for commands, press Ctrl-C to exit" )

        iothub_client_sample_run()
    ```

7. Сохраните и закройте файл **dmpatterns_getstarted_device.py**.

> [!NOTE]
> Для простоты в этом руководстве не реализуются политики повтора. В рабочем коде следует реализовать политики повторных попыток (например, с экспоненциальной задержкой), как указано в статье [Обработка временных сбоев](/azure/architecture/best-practices/transient-faults).

## <a name="get-the-iot-hub-connection-string"></a>Получение строки подключения для центра Интернета вещей

[!INCLUDE [iot-hub-howto-device-management-shared-access-policy-text](../../includes/iot-hub-howto-device-management-shared-access-policy-text.md)]

[!INCLUDE [iot-hub-include-find-service-connection-string](../../includes/iot-hub-include-find-service-connection-string.md)]

## <a name="trigger-a-remote-reboot-on-the-device-using-a-direct-method"></a>Активация удаленной перезагрузки на устройстве с помощью прямого метода

В этом разделе вы создадите консольное приложение Python, которое инициирует удаленное обновление устройства с помощью прямого метода. Приложение использует запросы двойника устройства для определения времени последней перезагрузки этого устройства.

1. В командной строке выполните следующую команду, чтобы установить пакет **azure-iot-service-client**.

    ```cmd/sh
    pip install azure-iothub-service-client
    ```

   > [!NOTE]
   > Пакет PIP для Azure-iothub-Service-Client в настоящее время доступен только для ОС Windows. Сведения для Linux и Mac OS см. в разделах, посвященных Linux и Mac OS, в разделе [Подготовка среды разработки для Python](https://github.com/Azure/azure-iot-sdk-python/blob/v1-deprecated/doc/python-devbox-setup.md) POST.
   >

2. С помощью текстового редактора создайте файл с именем **dmpatterns_getstarted_service. копировать** в рабочем каталоге.

3. Добавьте следующие инструкции `import` в начале файла **dmpatterns_getstarted_service.py**.

    ```python
    import sys, time
    import iothub_service_client

    from iothub_service_client import IoTHubDeviceMethod, IoTHubError, IoTHubDeviceTwin
    ```

4. Добавьте следующие объявления переменных. Замените значение заполнителя `{IoTHubConnectionString}` строкой подключения центра Интернета вещей, скопированным ранее в поле [Получение строки подключения для центра Интернета вещей](#get-the-iot-hub-connection-string). Замените значение заполнителя `{deviceId}` ИДЕНТИФИКАТОРом устройства, зарегистрированным в окне [Регистрация нового устройства в центре Интернета вещей](#register-a-new-device-in-the-iot-hub).

    ```python
    CONNECTION_STRING = "{IoTHubConnectionString}"
    DEVICE_ID = "{deviceId}"

    METHOD_NAME = "rebootDevice"
    METHOD_PAYLOAD = "{\"method_number\":\"42\"}"
    TIMEOUT = 60
    WAIT_COUNT = 10
    ```

5. Добавьте следующую функцию для вызова метода устройства, чтобы перезагрузить целевое устройство, а затем выполнить запрос к двойникам устройства и получить сведения о времени последней перезагрузки.

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

6. Сохраните и закройте файл **dmpatterns_getstarted_service.py**.

## <a name="run-the-apps"></a>Запуск приложений

Теперь все готово для запуска приложений.

1. В командной строке выполните следующую команду, чтобы начать прослушивание прямого метода перезагрузки.

    ```cmd/sh
    python dmpatterns_getstarted_device.py
    ```

2. В другой командной строке выполните следующую команду, чтобы активировать удаленную перезагрузку и выполнить запрос к двойнику устройства для поиска значения времени последней перезагрузки.

    ```cmd/sh
    python dmpatterns_getstarted_service.py
    ```

3. В консоли отобразится ответ устройства на прямой метод.

   Ниже показан ответ устройства на прямой метод reboot:

   ![Выходные данные приложения имитации устройства](./media/iot-hub-python-python-device-management-get-started/device.png)

   Ниже показана служба, вызывающая прямой метод перезагрузки и выполняющая опрос двойникаа устройства для состояния:

   ![Вывод запуска службы перезагрузки](./media/iot-hub-python-python-device-management-get-started/service.png)

[!INCLUDE [iot-hub-dm-followup](../../includes/iot-hub-dm-followup.md)]