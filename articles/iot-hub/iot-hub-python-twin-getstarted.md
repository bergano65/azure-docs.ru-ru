---
title: Начало работы с двойниками устройств Центра Интернета вещей (Python) | Документация Майкрософт
description: Добавление тегов и последующее использование запроса Центра Интернета вещей с помощью двойников устройств Центра Интернета вещей. Используйте пакеты SDK для Центра Интернета вещей Azure для Python, чтобы реализовать приложение имитации устройства и приложение службы, которые добавляют теги и выполняют запрос к Центру Интернета вещей.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.devlang: python
ms.topic: conceptual
ms.date: 08/26/2019
ms.author: robinsh
ms.openlocfilehash: 729371ede8df3dc21f70bc5742de52f2be874507
ms.sourcegitcommit: 11265f4ff9f8e727a0cbf2af20a8057f5923ccda
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/08/2019
ms.locfileid: "72029999"
---
# <a name="get-started-with-device-twins-python"></a>Начало работы с двойниками устройств (Python)

[!INCLUDE [iot-hub-selector-twin-get-started](../../includes/iot-hub-selector-twin-get-started.md)]

По завершении работы с этим руководством у вас будет два консольных приложения Python:

* **AddTagsAndQuery.py** — серверное приложение Python, которое добавляет теги и выполняет запросы к двойникам устройств.

* **ReportConnectivity.py** — приложение Python, которое имитирует устройство, подключающееся к Центру Интернета вещей с использованием созданного ранее удостоверения устройства, и сообщает об условиях подключения.

[!INCLUDE [iot-hub-include-python-sdk-note](../../includes/iot-hub-include-python-sdk-note.md)]

## <a name="prerequisites"></a>Предварительные требования

[!INCLUDE [iot-hub-include-python-installation-notes](../../includes/iot-hub-include-python-installation-notes.md)]

## <a name="create-an-iot-hub"></a>Создание Центра Интернета вещей

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-new-device-in-the-iot-hub"></a>Регистрация нового устройства в центре Интернета вещей

[!INCLUDE [iot-hub-include-create-device](../../includes/iot-hub-include-create-device.md)]

## <a name="get-the-iot-hub-connection-string"></a>Получение строки подключения для центра Интернета вещей

[!INCLUDE [iot-hub-howto-twin-shared-access-policy-text](../../includes/iot-hub-howto-twin-shared-access-policy-text.md)]

[!INCLUDE [iot-hub-include-find-custom-connection-string](../../includes/iot-hub-include-find-custom-connection-string.md)]

## <a name="create-the-service-app"></a>Создание приложения службы

В этом разделе вы создадите консольное приложение Python, которое добавляет метаданные расположения в двойника устройства, связанные с **{Device ID}** . После этого оно запрашивает данные двойников устройств, хранящихся в Центре Интернета вещей, и выбирает устройства в городе Редмонд, которые передали уведомление о подключении по сети мобильной связи.

1. В рабочем каталоге откройте командную строку и установите **пакет SDK службы центра Интернета вещей Azure для Python**.

   ```cmd/sh
   pip install azure-iothub-service-client
   ```

   > [!NOTE]
   > Пакет PIP для Azure-iothub-Service-Client в настоящее время доступен только для ОС Windows. Сведения для Linux и Mac OS см. в разделах, посвященных Linux и Mac OS, в разделе [Подготовка среды разработки для Python](https://github.com/Azure/azure-iot-sdk-python/blob/master/doc/python-devbox-setup.md) POST.
   >

2. В текстовом редакторе создайте файл **AddTagsAndQuery.py**.

3. Добавьте следующий код для импорта необходимых модулей из пакета SDK службы:

   ```python
   import sys
   import iothub_service_client
   from iothub_service_client import IoTHubRegistryManager, IoTHubRegistryManagerAuthMethod
   from iothub_service_client import IoTHubDeviceTwin, IoTHubError
   ```

4. Добавьте следующий код. Замените `[IoTHub Connection String]` строкой подключения центра Интернета вещей, скопированной в [поле получение строки подключения центра Интернета вещей](#get-the-iot-hub-connection-string). Замените `[Device Id]` ИДЕНТИФИКАТОРом устройства, зарегистрированным в окне [Регистрация нового устройства в центре Интернета вещей](#register-a-new-device-in-the-iot-hub).
  
    ```python
    CONNECTION_STRING = "[IoTHub Connection String]"
    DEVICE_ID = "[Device Id]"

    UPDATE_JSON = "{\"properties\":{\"desired\":{\"location\":\"Redmond\"}}}"

    UPDATE_JSON_SEARCH = "\"location\":\"Redmond\""
    UPDATE_JSON_CLIENT_SEARCH = "\"connectivity\":\"cellular\""
    ```

5. Добавьте следующий код в файл **AddTagsAndQuery.py**:

    ```python
    def iothub_service_sample_run():
        try:
            iothub_registry_manager = IoTHubRegistryManager(CONNECTION_STRING)

            iothub_registry_statistics = iothub_registry_manager.get_statistics()
            print ( "Total device count                       : {0}".format(iothub_registry_statistics.totalDeviceCount) )
            print ( "Enabled device count                     : {0}".format(iothub_registry_statistics.enabledDeviceCount) )
            print ( "Disabled device count                    : {0}".format(iothub_registry_statistics.disabledDeviceCount) )
            print ( "" )

            number_of_devices = iothub_registry_statistics.totalDeviceCount
            dev_list = iothub_registry_manager.get_device_list(number_of_devices)

            iothub_twin_method = IoTHubDeviceTwin(CONNECTION_STRING)

            for device in range(0, number_of_devices):
                if dev_list[device].deviceId == DEVICE_ID:
                    twin_info = iothub_twin_method.update_twin(dev_list[device].deviceId, UPDATE_JSON)

            print ( "Devices in Redmond: " )
            for device in range(0, number_of_devices):
                twin_info = iothub_twin_method.get_twin(dev_list[device].deviceId)

                if twin_info.find(UPDATE_JSON_SEARCH) > -1:
                    print ( dev_list[device].deviceId )

            print ( "" )

            print ( "Devices in Redmond using cellular network: " )
            for device in range(0, number_of_devices):
                twin_info = iothub_twin_method.get_twin(dev_list[device].deviceId)

                if twin_info.find(UPDATE_JSON_SEARCH) > -1:
                    if twin_info.find(UPDATE_JSON_CLIENT_SEARCH) > -1:
                        print ( dev_list[device].deviceId )

        except IoTHubError as iothub_error:
            print ( "Unexpected error {0}".format(iothub_error) )
            return
        except KeyboardInterrupt:
            print ( "IoTHub sample stopped" )
    ```

    Объект **Registry** позволяет получить все методы, необходимые для взаимодействия с двойниками устройства из службы. Этот код инициализирует объект **Registry**, записывает идентификатор устройства **deviceId** в двойник устройства и выполняет два запроса. Первый запрос предназначен для выбора только двойников устройств, расположенных в фабрике **Redmond43**, а второй уточняет условия первого запроса и выбирает устройства, подключенные по сети мобильной связи.

6. Добавьте следующий код в конце файла **AddTagsAndQuery.py**, чтобы реализовать функцию **iothub_service_sample_run**.

    ```python
    if __name__ == '__main__':
        print ( "Starting the IoT Hub Device Twins Python service sample..." )

        iothub_service_sample_run()
    ```

7. Запустите приложение, выполнив следующую команду:

    ```cmd/sh
    python AddTagsAndQuery.py
    ```

    В результатах запроса на все устройства, расположенные на фабрике **Redmond43**, отобразится одно устройство, а для запроса на ограничение результатов устройствами, использующими сеть мобильной связи, не отобразится ни одного устройства.

    ![Первый запрос, показывающий все устройства в Редмонде](./media/iot-hub-python-twin-getstarted/service-1.png)

В следующем разделе рассказывается о том, как создать приложение устройства, которое сообщает сведения о подключении и изменяет результат запроса, описанного в предыдущем разделе.

## <a name="create-the-device-app"></a>Создание приложения устройства

В этом разделе вы создадите консольное приложение Python, которое подключается к концентратору в качестве **идентификатора {Device ID}** , а затем обновит сообщаемые свойства двойникаа устройства, чтобы они содержали сведения, подключенные с помощью сотовой сети.

1. В командной строке в рабочем каталоге установите **пакет SDK для устройств центра Интернета вещей Azure для Python**:

    ```cmd/sh
    pip install azure-iot-device
    ```

2. В текстовом редакторе создайте файл **ReportConnectivity.py**.

3. Добавьте следующий код, чтобы импортировать необходимые модули из пакета SDK устройства.

    ```python
    import time
    import threading
    from azure.iot.device import IoTHubModuleClient
    ```

4. Добавьте следующий код. Замените значение заполнителя `[IoTHub Device Connection String]` строкой подключения устройства, скопированной в раздел [Регистрация нового устройства в центре Интернета вещей](#register-a-new-device-in-the-iot-hub).

    ```python
    CONNECTION_STRING = "[IoTHub Device Connection String]"
    ```

5. Добавьте следующий код в файл **ReportConnectivity.py**, чтобы реализовать функциональные возможности двойника устройства:

    ```python
    def twin_update_listener(client):
        while True:
            patch = client.receive_twin_desired_properties_patch()  # blocking call
            print("Twin patch received:")
            print(patch)

    def iothub_client_init():
        client = IoTHubModuleClient.create_from_connection_string(CONNECTION_STRING)
        return client

    def iothub_client_sample_run():
        try:
            client = iothub_client_init()

            twin_update_listener_thread = threading.Thread(target=twin_update_listener, args=(client,))
            twin_update_listener_thread.daemon = True
            twin_update_listener_thread.start()

            # Send reported 
            print ( "Sending data as reported property..." )
            reported_patch = {"connectivity": "cellular"}
            client.patch_twin_reported_properties(reported_patch)
            print ( "Reported properties updated" )

            while True:
                time.sleep(1000000)
        except KeyboardInterrupt:
            print ( "IoTHubClient sample stopped" )
    ```

    Объект **Client** позволяет получить все методы, необходимые для взаимодействия с двойниками устройства с устройства. После инициализации объекта **Client** предыдущий код извлекает двойник для вашего устройства и обновляет его сообщаемое свойство, используя сведения о подключении.

6. Добавьте следующий код в конце файла **ReportConnectivity.py**, чтобы реализовать функцию **iothub_client_sample_run**.

    ```python
    if __name__ == '__main__':
        print ( "Starting the IoT Hub Device Twins Python client sample..." )
        print ( "IoTHubModuleClient waiting for commands, press Ctrl-C to exit" )

        iothub_client_sample_run()
    ```

7. Запустите приложение устройства:

    ```cmd/sh
    python ReportConnectivity.py
    ```

    Вы увидите сообщение, подтверждающее обновление двойников устройств.

    ![Обновление двойников](./media/iot-hub-python-twin-getstarted/device-1.png)

8. Теперь, когда устройство сообщило сведения о подключении, оно должно появиться в обоих запросах. Вернитесь обратно и повторно запустите запросы:

    ```cmd/sh
    python AddTagsAndQuery.py
    ```

    На этот раз ваш **{Device ID}** должен отображаться в результатах обоих запросов.

    ![второй запрос](./media/iot-hub-python-twin-getstarted/service-2.png)

## <a name="next-steps"></a>Следующие шаги

В этом руководстве мы настроили новый Центр Интернета вещей на портале Azure и создали удостоверение устройства в реестре удостоверений Центра Интернета вещей. Вы добавили метаданные устройства в качестве тегов из внутреннего приложения и написали код приложения имитации устройства, чтобы сообщить сведения о подключении в двойнике устройства. Вы также узнали, как запрашивать эти сведения, используя реестр.

Ознакомьтесь со следующими материалами, чтобы узнать как:

* отправить данные телеметрии с устройств (руководство [Подключение устройства к Центру Интернета вещей с помощью Java](quickstart-send-telemetry-python.md));

* Настройка устройств с использованием требуемых свойств двойникаа устройства с помощью учебника [Использование требуемых свойств для настройки устройств](tutorial-device-twins.md) .

* Управление устройствами в интерактивном режиме (например, включение вентилятора из управляемого пользователем приложения) с помощью учебника [Использование прямых методов](quickstart-control-device-python.md) .
