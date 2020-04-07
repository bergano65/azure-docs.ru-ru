---
title: Идентификация модуля и двойника модуля Azure IoT (Python)
description: Узнайте, как создать удостоверение модуля и обновить двойник модуля с помощью пакетов SDK Центра Интернета вещей для Python.
author: chrissie926
ms.service: iot-hub
services: iot-hub
ms.devlang: python
ms.topic: conceptual
ms.date: 04/03/2020
ms.author: menchi
ms.openlocfilehash: f846af548913e0cb3e872560e4b8438da306a255
ms.sourcegitcommit: 441db70765ff9042db87c60f4aa3c51df2afae2d
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/06/2020
ms.locfileid: "80756941"
---
# <a name="get-started-with-iot-hub-module-identity-and-module-twin-python"></a>Начало работы с IoT концентратора модуль идентичности и модуля twin (Python)

[!INCLUDE [iot-hub-selector-module-twin-getstarted](../../includes/iot-hub-selector-module-twin-getstarted.md)]

> [!NOTE]
> [Идентификаторы модулей и близнецы модулей](iot-hub-devguide-module-twins.md) похожи на идентификаторы устройств Azure IoT и близнецы устройств, но обеспечивают более тонкую детализацию. В то время как идентификаторы устройств Azure IoT Hub и близнецы устройств позволяют бэк-энд-приложению настроить устройство и обеспечить видимость условий устройства, идентификаторы модулей и близнецы модулей обеспечивают эти возможности для отдельных компонентов устройства. На устройствах с несколькими компонентами, такими как устройства на основе операционной системы или устройства прошивки, они позволяют изолированную конфигурацию и условия для каждого компонента.
>

В конце этого урока, у вас есть три приложения Python:

* **CreateModule**, который создает идентификацию устройства, идентификацию модуля и связанные с ними ключи безопасности для подключения клиентов устройства и модуля.

* **UpdateModuleTwinDesiredProperties**, который отправляет обновленный модуль близнецов желаемых свойств в ваш IoT концентратор.

* **ReceiveModuleTwinDesiredPropertiesPatch**, который получает модуль близнецов желаемых свойств патч на вашем устройстве.

[!INCLUDE [iot-hub-include-python-sdk-note](../../includes/iot-hub-include-python-sdk-note.md)]

## <a name="prerequisites"></a>Предварительные требования

[!INCLUDE [iot-hub-include-python-v2-installation-notes](../../includes/iot-hub-include-python-v2-installation-notes.md)]

## <a name="create-an-iot-hub"></a>Создание Центра Интернета вещей

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="get-the-iot-hub-connection-string"></a>Получите строку соединения концентратора IoT

В этой статье вы создаете бэк-энд службу, которая добавляет устройство в реестр идентификации, а затем добавляет модуль к этому устройству. Эта услуга требует **разрешения реестра написать** (который также включает в себя **реестр читать**). Вы также создаете службу, которая добавляет желаемые свойства к двойнику модуля для вновь созданного модуля. Эта служба нуждается в разрешении **на подключение службы.** Хотя существуют политики общего доступа по умолчанию, которые предоставляют эти разрешения индивидуально, в этом разделе вы создаете пользовательскую политику общего доступа, содержащую оба этих разрешения.

[!INCLUDE [iot-hub-include-find-service-regrw-connection-string](../../includes/iot-hub-include-find-service-regrw-connection-string.md)]

## <a name="create-a-device-identity-and-a-module-identity-in-iot-hub"></a>Создание удостоверения устройства и удостоверения модуля в Центре Интернета вещей

В этом разделе создается приложение службы Python, которое создает идентификацию устройства и удостоверение модуля в реестре идентификации в вашем концентраторе IoT. Устройство или модуль не могут подключиться к концентратору IoT, если у него нет входа в реестр идентификационных данных. Для получения дополнительной информации [см.](iot-hub-devguide-identity-registry.md) Запущенное консольное приложение создает уникальные идентификаторы и ключи одновременно для устройства и для модуля. Устройство и модуль применяют эти значения для идентификации при отправке сообщений с устройства в облако в Центр Интернета вещей. В идентификаторах учитывается регистр символов.

1. В запросе команды выполнить следующую команду для установки пакета **azure-iot-hub:**

    ```cmd/sh
    pip install azure-iot-hub
    ```

1. В запросе команды запустите следующую команду для установки пакета **msrest.** Этот пакет нужен для получения исключений **HTTPOperationError.**

    ```cmd/sh
    pip install msrest
    ```

1. Используя текстовый редактор, создайте файл, названный **CreateModule.py** в рабочем каталоге.

1. Добавьте следующий код в файл Python. Замените *YourIoTHubConnectionString* строкой соединения, скопированной в [строке подключения концентратора IoT.](#get-the-iot-hub-connection-string)

    ```python
    import sys
    from msrest.exceptions import HttpOperationError
    from azure.iot.hub import IoTHubRegistryManager

    CONNECTION_STRING = "YourIotHubConnectionString"
    DEVICE_ID = "myFirstDevice"
    MODULE_ID = "myFirstModule"

    try:
        # RegistryManager
        iothub_registry_manager = IoTHubRegistryManager(CONNECTION_STRING)

        try:
            # CreateDevice - let IoT Hub assign keys
            primary_key = ""
            secondary_key = ""
            device_state = "enabled"
            new_device = iothub_registry_manager.create_device_with_sas(
                DEVICE_ID, primary_key, secondary_key, device_state
            )
        except HttpOperationError as ex:
            if ex.response.status_code == 409:
                # 409 indicates a conflict. This happens because the device already exists.
                new_device = iothub_registry_manager.get_device(DEVICE_ID)
            else:
                raise

        print("device <" + DEVICE_ID +
              "> has primary key = " + new_device.authentication.symmetric_key.primary_key)

        try:
            # CreateModule - let IoT Hub assign keys
            primary_key = ""
            secondary_key = ""
            managed_by = ""
            new_module = iothub_registry_manager.create_module_with_sas(
                DEVICE_ID, MODULE_ID, managed_by, primary_key, secondary_key
            )
        except HttpOperationError as ex:
            if ex.response.status_code == 409:
                # 409 indicates a conflict. This happens because the module already exists.
                new_module = iothub_registry_manager.get_module(DEVICE_ID, MODULE_ID)
            else:
                raise

        print("device/module <" + DEVICE_ID + "/" + MODULE_ID +
              "> has primary key = " + new_module.authentication.symmetric_key.primary_key)

    except Exception as ex:
        print("Unexpected error {0}".format(ex))
    except KeyboardInterrupt:
        print("IoTHubRegistryManager sample stopped")
    ```

1. В запросе команды выполнить следующую команду:

    ```cmd/sh
    python CreateModule.py
    ```

Это приложение создает удостоверение устройства с идентификатором **myFirstDevice** и удостоверение модуля с идентификатором **myFirstModule** на устройстве **myFirstDevice**. (Если идентификатор устройства или модуля уже существует в реестре идентификации, код просто извлекает существующую информацию об устройстве или модуле.) Приложение отображает идентификатор и основной ключ для каждой идентификации.

> [!NOTE]
> В реестре удостоверений в Центре Интернета вещей хранятся только удостоверения устройств и модулей, необходимые для защиты доступа к Центру Интернета вещей. Реестр удостоверений хранит идентификаторы устройств и ключи, используемые в качестве учетных данных безопасности. Реестр удостоверений также хранит флаги включения и отключения для каждого устройства. Эти флаги можно использовать для отключения доступа для этого устройства. Если в приложении необходимо хранить другие метаданные для конкретного устройства, следует использовать хранилище конкретного приложения. У вас нет возможности включать и отключать удостоверения модулей. Для получения дополнительной информации [см.](iot-hub-devguide-identity-registry.md)
>

## <a name="update-the-module-twin-using-python-service-sdk"></a>Обновление двойника модуля с помощью службы Python SDK

В этом разделе создается приложение службы Python, которое обновляет желаемые свойства модуля twin.

1. В запросе команды выполнить следующую команду для установки пакета **azure-iot-hub.** Вы можете пропустить этот шаг, если вы установили пакет **azure-iot-hub** в предыдущем разделе.

    ```cmd/sh
    pip install azure-iot-hub
    ```

1. Используя текстовый редактор, создайте файл, названный **UpdateModuleTwinDesiredProperties.py** в рабочем каталоге.

1. Добавьте следующий код в файл Python. Замените *YourIoTHubConnectionString* строкой соединения, скопированной в [строке подключения концентратора IoT.](#get-the-iot-hub-connection-string)

    ```python
    import sys
    from azure.iot.hub import IoTHubRegistryManager
    from azure.iot.hub.models import Twin, TwinProperties

    CONNECTION_STRING = "YourIoTHubConnectionString"
    DEVICE_ID = "myFirstDevice"
    MODULE_ID = "myFirstModule"

    try:
        # RegistryManager
        iothub_registry_manager = IoTHubRegistryManager(CONNECTION_STRING)

        module_twin = iothub_registry_manager.get_module_twin(DEVICE_ID, MODULE_ID)
        print ( "" )
        print ( "Module twin properties before update    :" )
        print ( "{0}".format(module_twin.properties) )

        # Update twin
        twin_patch = Twin()
        twin_patch.properties = TwinProperties(desired={"telemetryInterval": 122})
        updated_module_twin = iothub_registry_manager.update_module_twin(
            DEVICE_ID, MODULE_ID, twin_patch, module_twin.etag
        )
        print ( "" )
        print ( "Module twin properties after update     :" )
        print ( "{0}".format(updated_module_twin.properties) )

    except Exception as ex:
        print ( "Unexpected error {0}".format(ex) )
    except KeyboardInterrupt:
        print ( "IoTHubRegistryManager sample stopped" )
    ```

## <a name="get-updates-on-the-device-side"></a>Получение обновлений на стороне устройства

В этом разделе вы создаете приложение Python, чтобы получить обновление желаемых свойств модуля на устройстве.

1. Получите строку соединения модуля. На [портале Azure](https://portal.azure.com/)перейдите на концентратор IoT и выберите **устройства IoT** в левом стеле. Выберите **myFirstDevice** из списка устройств и откройте его. Под **идентификаторами модуля**выберите **myFirstModule**. Скопируйте строку подключения модуля. Вам это нужно в следующем шаге.

   ![Сведения о модуле на портале Azure](./media/iot-hub-python-python-module-twin-getstarted/module-detail.png)

1. В запросе команды выполнить следующую команду для установки пакета **azure-iot-устройства:**

    ```cmd/sh
    pip install azure-iot-device
    ```

1. Используя текстовый редактор, создайте файл с именем **ReceiveModuleTwinDesiredPropertiesPatch.py** в рабочем каталоге.

1. Добавьте следующий код в файл Python. Замените *ВашModuleConnectionString* строкой соединения модуля, скопированной в шаге 1.

    ```python
    import time
    import threading
    from azure.iot.device import IoTHubModuleClient

    CONNECTION_STRING = "YourModuleConnectionString"


    def twin_update_listener(client):
        while True:
            patch = client.receive_twin_desired_properties_patch()  # blocking call
            print("")
            print("Twin desired properties patch received:")
            print(patch)

    def iothub_client_sample_run():
        try:
            module_client = IoTHubModuleClient.create_from_connection_string(CONNECTION_STRING)

            twin_update_listener_thread = threading.Thread(target=twin_update_listener, args=(module_client,))
            twin_update_listener_thread.daemon = True
            twin_update_listener_thread.start()

            while True:
                time.sleep(1000000)

        except KeyboardInterrupt:
            print("IoTHubModuleClient sample stopped")


    if __name__ == '__main__':
        print ( "Starting the IoT Hub Python sample..." )
        print ( "IoTHubModuleClient waiting for commands, press Ctrl-C to exit" )

        iothub_client_sample_run()
    ```

## <a name="run-the-apps"></a>Запуск приложений

В этом разделе вы запустите приложение **устройства ReceiveModuleDesiredPropertiesPatch,** а затем запустите приложение службы **UpdateModuleDesiredProperties** для обновления желаемых свойств модуля.

1. Откройте запрос команды и запустите приложение устройства:

    ```cmd/sh
    python ReceiveModuleTwinDesiredPropertiesPatch.py
    ```

   ![Первоначальный выход приложения устройства](./media/iot-hub-python-python-module-twin-getstarted/device-1.png)

1. Откройте отдельный запрос команды и запустите приложение службы:

    ```cmd/sh
    python UpdateModuleTwinDesiredProperties.py
    ```

    Обратите внимание, что желаемое свойство **TelemetryInterval** отображается в обновленном двойнике модуля в выходе приложения службы:

   ![Выход приложения обслуживания](./media/iot-hub-python-python-module-twin-getstarted/service.png)

    То же свойство отображается в желаемом патче свойств, полученном в выходе приложения устройства:

   ![Выход приложения устройства показывает желаемый патч свойств](./media/iot-hub-python-python-module-twin-getstarted/device-2.png)

## <a name="next-steps"></a>Дальнейшие действия

Чтобы продолжить знакомство с Центром Интернета вещей и изучить другие сценарии Интернета вещей, см. следующие ресурсы:

* [How to get started with device management (Node)](iot-hub-node-node-device-management-get-started.md) (Начало работы с управлением устройствами (Node))

* [Краткое руководство. Развертывание первого модуля IoT Edge на устройстве под управлением 64-разрядной ОС Linux](../iot-edge/tutorial-simulate-device-linux.md)