---
title: Начало работы с двойниками устройств Центра Интернета вещей (Python) | Документация Майкрософт
description: Добавление тегов и последующее использование запроса Центра Интернета вещей с помощью двойников устройств Центра Интернета вещей. Используйте пакеты SDK для Центра Интернета вещей Azure для Python, чтобы реализовать приложение имитации устройства и приложение службы, которые добавляют теги и выполняют запрос к Центру Интернета вещей.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.devlang: python
ms.topic: conceptual
ms.date: 03/11/2020
ms.author: robinsh
ms.custom: mqtt
ms.openlocfilehash: 0bb1371de827fbb68afd5d114f49afa4acec0deb
ms.sourcegitcommit: 31e9f369e5ff4dd4dda6cf05edf71046b33164d3
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/22/2020
ms.locfileid: "81759733"
---
# <a name="get-started-with-device-twins-python"></a>Начало работы с двойниками устройств (Python)

[!INCLUDE [iot-hub-selector-twin-get-started](../../includes/iot-hub-selector-twin-get-started.md)]

По завершении работы с этим руководством у вас будет два консольных приложения Python:

* **AddTagsAndQuery.py** — серверное приложение Python, которое добавляет теги и выполняет запросы к двойникам устройств.

* **ReportConnectivity.py** — приложение Python, которое имитирует устройство, подключающееся к Центру Интернета вещей с использованием созданного ранее удостоверения устройства, и сообщает об условиях подключения.

[!INCLUDE [iot-hub-include-python-sdk-note](../../includes/iot-hub-include-python-sdk-note.md)]

## <a name="prerequisites"></a>Предварительные требования

[!INCLUDE [iot-hub-include-python-v2-installation-notes](../../includes/iot-hub-include-python-v2-installation-notes.md)]

* Убедитесь, что в брандмауэре открыт порт 8883. Образец устройства в этой статье использует протокол МЗТТ, который общается по порту 8883. В некоторых корпоративных и академических сетях этот порт может быть заблокирован. Дополнительные сведения и способы устранения этой проблемы см. в разделе о [подключении к Центру Интернета вещей по протоколу MQTT](iot-hub-mqtt-support.md#connecting-to-iot-hub).

## <a name="create-an-iot-hub"></a>Создание Центра Интернета вещей

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-new-device-in-the-iot-hub"></a>Регистрация нового устройства в центре Интернета вещей

[!INCLUDE [iot-hub-include-create-device](../../includes/iot-hub-include-create-device.md)]

## <a name="get-the-iot-hub-connection-string"></a>Получите строку соединения концентратора IoT

[!INCLUDE [iot-hub-howto-twin-shared-access-policy-text](../../includes/iot-hub-howto-twin-shared-access-policy-text.md)]

[!INCLUDE [iot-hub-include-find-custom-connection-string](../../includes/iot-hub-include-find-custom-connection-string.md)]

## <a name="create-the-service-app"></a>Создание приложения службы

В этом разделе создается приложение консоли Python, которое добавляет метаданные о местоположении к близнецу устройства, связанного с вашим **«идентификатором устройства».** После этого оно запрашивает данные двойников устройств, хранящихся в Центре Интернета вещей, и выбирает устройства в городе Редмонд, которые передали уведомление о подключении по сети мобильной связи.

1. В рабочем каталоге откройте запрос команды и установите **SDK-концентратор Azure IoT для Python.**

   ```cmd/sh
   pip install azure-iot-hub
   ```

2. В текстовом редакторе создайте файл **AddTagsAndQuery.py**.

3. Добавьте следующий код для импорта необходимых модулей из пакета SDK службы:

   ```python
   import sys
   from time import sleep
   from azure.iot.hub import IoTHubRegistryManager
   from azure.iot.hub.models import Twin, TwinProperties, QuerySpecification, QueryResult
   ```

4. Добавьте следующий код. Заменить `[IoTHub Connection String]` строку соединения концентратора IoT, скопированную в [строке подключения концентратора IoT.](#get-the-iot-hub-connection-string) Заменить `[Device Id]` идентификатор устройства, зарегистрированный в [Регистрации, новое устройство в концентраторе IoT.](#register-a-new-device-in-the-iot-hub)
  
    ```python
    IOTHUB_CONNECTION_STRING = "[IoTHub Connection String]"
    DEVICE_ID = "[Device Id]"
    ```

5. Добавьте следующий код в файл **AddTagsAndQuery.py**:

    ```python
    def iothub_service_sample_run():
        try:
            iothub_registry_manager = IoTHubRegistryManager(IOTHUB_CONNECTION_STRING)

            new_tags = {
                    'location' : {
                        'region' : 'US',
                        'plant' : 'Redmond43'
                    }
                }

            twin = iothub_registry_manager.get_twin(DEVICE_ID)
            twin_patch = Twin(tags=new_tags, properties= TwinProperties(desired={'power_level' : 1}))
            twin = iothub_registry_manager.update_twin(DEVICE_ID, twin_patch, twin.etag)

            # Add a delay to account for any latency before executing the query
            sleep(1)

            query_spec = QuerySpecification(query="SELECT * FROM devices WHERE tags.location.plant = 'Redmond43'")
            query_result = iothub_registry_manager.query_iot_hub(query_spec, None, 100)
            print("Devices in Redmond43 plant: {}".format(', '.join([twin.device_id for twin in query_result.items])))

            print()

            query_spec = QuerySpecification(query="SELECT * FROM devices WHERE tags.location.plant = 'Redmond43' AND properties.reported.connectivity = 'cellular'")
            query_result = iothub_registry_manager.query_iot_hub(query_spec, None, 100)
            print("Devices in Redmond43 plant using cellular network: {}".format(', '.join([twin.device_id for twin in query_result.items])))

        except Exception as ex:
            print("Unexpected error {0}".format(ex))
            return
        except KeyboardInterrupt:
            print("IoT Hub Device Twin service sample stopped")
    ```

    Объект **IoTHubRegistryManager** предоставляет все методы, необходимые для взаимодействия с близнецами устройств из службы. Код сначала инициализирует объект **IoTHubRegistryManager,** затем обновляет устройство twin для **DEVICE_ID,** и, наконец, запускает два запроса. Первый выбирает только устройство близнецов устройств, расположенных на заводе **Redmond43,** а второй уточняет запрос, чтобы выбрать только устройства, которые также подключены через сотовую сеть.

6. Добавьте следующий код в конце файла **AddTagsAndQuery.py**, чтобы реализовать функцию **iothub_service_sample_run**.

    ```python
    if __name__ == '__main__':
        print("Starting the Python IoT Hub Device Twin service sample...")
        print()

        iothub_service_sample_run()
    ```

7. Запустите приложение, выполнив следующую команду:

    ```cmd/sh
    python AddTagsAndQuery.py
    ```

    В результатах запроса на все устройства, расположенные на фабрике **Redmond43**, отобразится одно устройство, а для запроса на ограничение результатов устройствами, использующими сеть мобильной связи, не отобразится ни одного устройства.

    ![первый запрос, показывающий все устройства в Редмонде](./media/iot-hub-python-twin-getstarted/service-1.png)

В следующем разделе рассказывается о том, как создать приложение устройства, которое сообщает сведения о подключении и изменяет результат запроса, описанного в предыдущем разделе.

## <a name="create-the-device-app"></a>Создание приложения устройства

В этом разделе вы создаете консоль Python приложение, которое подключается к концентратору в качестве **«идентификатора устройства»,** а затем обновляет свои свойства близнеца устройства, чтобы содержать информацию о том, что он подключен с помощью сотовой сети.

1. Из командного запроса в рабочем каталоге установите **SDK-концентратор Azure IoT для Python:**

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

4. Добавьте следующий код. Замените `[IoTHub Device Connection String]` значение заполнителя строкой подключения устройства, скопировавшей в [Регистрации, новое устройство в концентраторе IoT.](#register-a-new-device-in-the-iot-hub)

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
            print ( "IoT Hub Device Twin device sample stopped" )
    ```

    Объект **IoTHubModuleClient** предоставляет все методы, необходимые для взаимодействия с близнецами устройства с устройства. Предыдущий код, после того как он инициирует объект **IoTHubModuleClient,** получает устройство twin для вашего устройства и обновляет его сообщил свойство с информацией о подключении.

6. Добавьте следующий код в конце файла **ReportConnectivity.py**, чтобы реализовать функцию **iothub_client_sample_run**.

    ```python
    if __name__ == '__main__':
        print ( "Starting the Python IoT Hub Device Twin device sample..." )
        print ( "IoTHubModuleClient waiting for commands, press Ctrl-C to exit" )

        iothub_client_sample_run()
    ```

7. Запустите приложение устройства:

    ```cmd/sh
    python ReportConnectivity.py
    ```

    Вы должны увидеть подтверждение устройства близнец сообщил свойства были обновлены.

    ![обновление зарегистрированных свойств из приложения устройства](./media/iot-hub-python-twin-getstarted/device-1.png)

8. Теперь, когда устройство сообщило сведения о подключении, оно должно появиться в обоих запросах. Вернитесь обратно и повторно запустите запросы:

    ```cmd/sh
    python AddTagsAndQuery.py
    ```

    На этот раз **ваш «Идентификатор устройства»** должен появиться в результатах обоих запросов.

    ![второй запрос на сервисное приложение](./media/iot-hub-python-twin-getstarted/service-2.png)

    В приложении для устройств вы увидите подтверждение того, что получен ознакомительный патч с двумя свойствами, отправленный приложением службы.

    ![получать желаемые свойства в приложении устройства](./media/iot-hub-python-twin-getstarted/device-2.png)

## <a name="next-steps"></a>Следующие шаги

В этом руководстве мы настроили новый Центр Интернета вещей на портале Azure и создали удостоверение устройства в реестре удостоверений Центра Интернета вещей. Вы добавили метаданные устройства в качестве тегов из внутреннего приложения и написали код приложения имитации устройства, чтобы сообщить сведения о подключении в двойнике устройства. Вы также узнали, как запрашивать эти сведения, используя реестр.

Ознакомьтесь со следующими материалами, чтобы узнать как:

* Отправить телеметрию с устройств с помощью учебника [IoT Hub.](quickstart-send-telemetry-python.md)

* Настройка устройств с использованием желаемых свойств близнеца устройства с [использованием желаемых свойств для настройки устройств](tutorial-device-twins.md) учебник.

* управлять устройствами в интерактивном режиме, например включить вентилятор из управляемого пользователем приложения (руководство по [использованию прямых методов](quickstart-control-device-python.md)).
