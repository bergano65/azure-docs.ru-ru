---
title: Приступая к работе с удостоверением и двойником модуля Центра Интернета вещей Azure (Python) | Документация Майкрософт
description: Узнайте, как создать удостоверение модуля и обновить двойник модуля с помощью пакетов SDK Центра Интернета вещей для Python.
author: chrissie926
ms.service: iot-hub
services: iot-hub
ms.devlang: python
ms.topic: conceptual
ms.date: 07/30/2019
ms.author: menchi
ms.openlocfilehash: b54b81a78072664ef47ace917fb3db85a5b1f297
ms.sourcegitcommit: aaa82f3797d548c324f375b5aad5d54cb03c7288
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/29/2019
ms.locfileid: "70147612"
---
# <a name="get-started-with-iot-hub-module-identity-and-module-twin-python"></a>Начало работы с удостоверениями модулей центра Интернета вещей и модулем двойника (Python)

[!INCLUDE [iot-hub-selector-module-twin-getstarted](../../includes/iot-hub-selector-module-twin-getstarted.md)]

> [!NOTE]
> [Удостоверение и двойник модуля](iot-hub-devguide-module-twins.md) аналогичны удостоверению и двойнику устройства Центра Интернета вещей Azure, однако они предоставляют более высокую степень детализации. В то время как удостоверение и двойник устройства Центра Интернета вещей Azure позволяют серверному приложению настраивать устройство и отображать состояние устройства, удостоверение и двойник модуля предоставляют эти возможности для отдельных компонентов устройства. Благодаря этому можно изолировать конфигурацию и условия для каждого компонента на совместимых устройствах с несколькими компонентами, например устройствах на основе операционной системы или устройствах со встроенным ПО.
>

По завершении работы с этим руководством у вас будет два приложения Python.

* **CreateIdentities** — создает удостоверение устройства и модуля, а также соответствующий ключ безопасности для подключения к клиентам устройства и модуля.

* **UpdateModuleTwinReportedProperties** — отправляет обновленные сообщаемые свойства двойника модуля в Центр Интернета вещей.

[!INCLUDE [iot-hub-include-python-sdk-note](../../includes/iot-hub-include-python-sdk-note.md)]

## <a name="prerequisites"></a>Предварительные требования

[!INCLUDE [iot-hub-include-python-installation-notes](../../includes/iot-hub-include-python-installation-notes.md)]

## <a name="create-an-iot-hub"></a>Создание Центра Интернета вещей

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="get-the-iot-hub-connection-string"></a>Получение строки подключения для центра Интернета вещей

[!INCLUDE [iot-hub-howto-module-twin-shared-access-policy-text](../../includes/iot-hub-howto-module-twin-shared-access-policy-text.md)]

[!INCLUDE [iot-hub-include-find-registryrw-connection-string](../../includes/iot-hub-include-find-registryrw-connection-string.md)]

## <a name="create-a-device-identity-and-a-module-identity-in-iot-hub"></a>Создание удостоверения устройства и удостоверения модуля в Центре Интернета вещей

В этом разделе объясняется, как создать приложение Python, создающее удостоверение устройства и удостоверение модуля в реестре удостоверений Центра Интернета вещей. Устройства и модули не могут подключаться к Центру Интернета вещей, если в реестре удостоверений для них нет соответствующей записи. Дополнительные сведения см. в разделе "реестр удостоверений" раздела Azure для [разработчиков центра Интернета вещей](iot-hub-devguide-identity-registry.md). Запущенное консольное приложение создает уникальные идентификаторы и ключи одновременно для устройства и для модуля. Устройство и модуль применяют эти значения для идентификации при отправке сообщений с устройства в облако в Центр Интернета вещей. В идентификаторах учитывается регистр символов.

Добавьте в файл Python следующий код:

```python
import sys
import iothub_service_client
from iothub_service_client import IoTHubRegistryManager, IoTHubRegistryManagerAuthMethod, IoTHubError

CONNECTION_STRING = "YourConnString"
DEVICE_ID = "myFirstDevice"
MODULE_ID = "myFirstModule"

try:
    # RegistryManager
    iothub_registry_manager = IoTHubRegistryManager(CONNECTION_STRING)

    # CreateDevice
    primary_key = ""
    secondary_key = ""
    auth_method = IoTHubRegistryManagerAuthMethod.SHARED_PRIVATE_KEY
    new_device = iothub_registry_manager.create_device(
        DEVICE_ID, primary_key, secondary_key, auth_method)
    print("new_device <" + DEVICE_ID +
          "> has primary key = " + new_device.primaryKey)

    # CreateModule
    new_module = iothub_registry_manager.create_module(
        DEVICE_ID, primary_key, secondary_key, MODULE_ID, auth_method)
    print("device/new_module <" + DEVICE_ID + "/" + MODULE_ID +
          "> has primary key = " + new_module.primaryKey)

except IoTHubError as iothub_error:
    print("Unexpected error {0}".format(iothub_error))
except KeyboardInterrupt:
    print("IoTHubRegistryManager sample stopped")
```

Это приложение создает удостоверение устройства с идентификатором **myFirstDevice** и удостоверение модуля с идентификатором **myFirstModule** на устройстве **myFirstDevice**. (Если такой идентификатор модуля уже существует в реестре удостоверений, код просто извлекает сведения о существующем модуле.) Затем приложение отображает первичный ключ для данного удостоверения. Этот ключ будет использоваться в приложении виртуального модуля для подключения к Центру Интернета вещей.

> [!NOTE]
> В реестре удостоверений в Центре Интернета вещей хранятся только удостоверения устройств и модулей, необходимые для защиты доступа к Центру Интернета вещей. Реестр удостоверений хранит идентификаторы устройств и ключи, используемые в качестве учетных данных безопасности. Реестр удостоверений также хранит флаги включения и отключения для каждого устройства. Эти флаги можно использовать для отключения доступа для этого устройства. Если в приложении необходимо хранить другие метаданные для конкретного устройства, следует использовать хранилище конкретного приложения. У вас нет возможности включать и отключать удостоверения модулей. Дополнительные сведения см. в разделе [Руководство разработчика для Центра Интернета вещей](iot-hub-devguide-identity-registry.md).
>

## <a name="update-the-module-twin-using-python-device-sdk"></a>Обновление двойника модуля с помощью пакета SDK для устройства Python

В этом разделе объясняется, как создать приложение Python на имитированном устройстве, которое обновляет сообщаемые свойства двойника модуля.

1. **Получение строки подключения модуля** — теперь, если вы входите в [портал Azure](https://portal.azure.com/). Перейдите к Центру Интернета вещей и щелкните "Устройства IoT". Найдите устройство myFirstDevice, откройте его и вы увидите, что модуль myFirstModule успешно создан. Скопируйте строку подключения модуля. Она понадобится на следующем шаге.

   ![Сведения о модуле на портале Azure](./media/iot-hub-python-python-module-twin-getstarted/module-detail.png)

2. **Создание приложения Упдатемодулетвинрепортедпропертиес**

   Добавьте следующие инструкции `using` в начало файла **Program.cs** :

    ```python
    import sys
    import iothub_service_client
    from iothub_service_client import IoTHubRegistryManager, IoTHubRegistryManagerAuthMethod, IoTHubDeviceTwin, IoTHubError

    CONNECTION_STRING = "FILL IN CONNECTION STRING"
    DEVICE_ID = "MyFirstDevice"
    MODULE_ID = "MyFirstModule"

    UPDATE_JSON = "{\"properties\":{\"desired\":{\"telemetryInterval\":122}}}"

    try:
        iothub_twin = IoTHubDeviceTwin(CONNECTION_STRING)

        twin_info = iothub_twin.get_twin(DEVICE_ID, MODULE_ID)
        print ( "" )
        print ( "Twin before update    :" )
        print ( "{0}".format(twin_info) )

        twin_info_updated = iothub_twin.update_twin(DEVICE_ID, MODULE_ID, UPDATE_JSON)
        print ( "" )
        print ( "Twin after update     :" )
        print ( "{0}".format(twin_info_updated) )

    except IoTHubError as iothub_error:
        print ( "Unexpected error {0}".format(iothub_error) )
    except KeyboardInterrupt:
        print ( "IoTHubRegistryManager sample stopped" )
    ```

В этом примере кода показано, как извлечь двойник модуля и обновить сообщаемые свойства с помощью протокола AMQP.

## <a name="get-updates-on-the-device-side"></a>Получение обновлений на стороне устройства

Помимо приведенного выше кода, вы можете добавить следующий блок кода, чтобы получать сообщения об обновлении двойника на устройстве.

```python
import random
import time
import sys
import iothub_client
from iothub_client import IoTHubModuleClient, IoTHubClientError, IoTHubTransportProvider, IoTHubClientResult

PROTOCOL = IoTHubTransportProvider.AMQP
CONNECTION_STRING = ""


def module_twin_callback(update_state, payload, user_context):
    print("")
    print("Twin callback called with:")
    print("updateStatus: %s" % update_state)
    print("context: %s" % user_context)
    print("payload: %s" % payload)


try:
    module_client = IoTHubModuleClient(CONNECTION_STRING, PROTOCOL)
    module_client.set_module_twin_callback(module_twin_callback, 1234)

    print("Waiting for incoming twin messages.  Hit Control-C to exit.")
    while True:

        time.sleep(1000000)

except IoTHubError as iothub_error:
    print("Unexpected error {0}".format(iothub_error))
except KeyboardInterrupt:
    print("module client sample stopped")
```

## <a name="next-steps"></a>Следующие шаги

Чтобы продолжить знакомство с Центром Интернета вещей и изучить другие сценарии Интернета вещей, см. следующие ресурсы:

* [Начало работы с управлением устройствами (Node)](iot-hub-node-node-device-management-get-started.md)

* [Краткое руководство. Развертывание первого модуля IoT Edge на устройстве под управлением 64-разрядной ОС Linux](../iot-edge/tutorial-simulate-device-linux.md)