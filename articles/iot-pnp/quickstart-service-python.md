---
title: Взаимодействие с устройством IoT Plug and Play, подключенным к решению Интернета вещей Azure (Python) | Документация Майкрософт
description: Подключение к устройству IoT Plug and Play, подключенному к решению Azure IoT, а также взаимодействие с ним с помощью Python.
author: elhorton
ms.author: elhorton
ms.date: 7/13/2020
ms.topic: quickstart
ms.service: iot-pnp
services: iot-pnp
ms.custom: mvc
ms.openlocfilehash: be5ff3e863752dfc187bd91257425af5e8de85c4
ms.sourcegitcommit: a422b86148cba668c7332e15480c5995ad72fa76
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/30/2020
ms.locfileid: "91574978"
---
# <a name="quickstart-interact-with-an-iot-plug-and-play-device-thats-connected-to-your-solution-python"></a>Краткое руководство. Взаимодействие с подключенным к решению устройством IoT Plug and Play с помощью Python

[!INCLUDE [iot-pnp-quickstarts-service-selector.md](../../includes/iot-pnp-quickstarts-service-selector.md)]

IoT Plug and Play упрощает работу с Интернетом вещей, позволяя взаимодействовать с моделью устройства без необходимости понимать базовую реализацию устройства. В этом кратком руководстве показано, как подключиться к устройству IoT Plug and Play, подключенному к решению, а также управлять им с помощью Python.

## <a name="prerequisites"></a>Предварительные требования

[!INCLUDE [iot-pnp-prerequisites](../../includes/iot-pnp-prerequisites.md)]

Для выполнения инструкций, приведенных в этом кратком руководстве, на компьютере для разработки необходимо установить Python 3.7. Вы можете скачать последнюю рекомендуемую версию для нескольких платформ на сайте [python.org](https://www.python.org/). Проверить версию Python можно с помощью следующей команды:  

```cmd/sh
python --version
```

Пакет **azure-iot-device** опубликован как PIP.

В локальной среде Python установите пакет следующим образом:

```cmd/sh
pip install azure-iot-device
```

Установите пакет **azure-iot-hub**, выполнив следующую команду:

```cmd/sh
pip install azure-iot-hub
```

## <a name="run-the-sample-device"></a>Запуск примера устройства

[!INCLUDE [iot-pnp-environment](../../includes/iot-pnp-environment.md)]

Дополнительные сведения о примере конфигурации см. в [образце файла сведений](https://github.com/Azure/azure-iot-sdk-python/blob/master/azure-iot-device/samples/pnp/README.md).

В рамках этого краткого руководства вы сможете применить пример термостата, написанный на Python, в качестве устройства IoT Plug and Play. Чтобы запустить пример устройства, сделайте следующее:

1. Откройте окно терминала в любой папке. Выполните следующую команду, чтобы клонировать репозиторий GitHub для [пакета средств разработки Python для Интернета вещей Azure](https://github.com/Azure/azure-iot-sdk-python) в следующее расположение:

    ```cmd/sh
    git clone https://github.com/Azure/azure-iot-sdk-python
    ```

1. Это окно терминала используется в качестве терминала **устройства**. Перейдите в папку клонированного репозитория, а затем в папку */azure-iot-sdk-python/azure-iot-device/samples/pnp*.

1. Запустите пример термостата с помощью следующей команды:

    ```cmd/sh
    python simple_thermostat.py
    ```

1. Отобразится сообщение о том, что устройство отправило определенные сведения и находится в сети. Эти сообщения означают, что устройство начало отправлять в концентратор данные телеметрии и теперь готово получать команды и обновления свойств. Не закрывайте этот терминал, он понадобится вам, чтобы проверить работу примера службы.

## <a name="run-the-sample-solution"></a>Запуск примера решения

В рамках этого краткого руководства вы примените пример решения Интернета вещей на Python для взаимодействия с только что настроенным примером устройства.

1. Откройте другое окно терминала, которое будет терминалом **службы**. 

1. Перейдите в папку */azure-iot-sdk-python/azure-iot-hub/samples* в клонированном репозитории SDK Python.

1. В папке samples есть четыре примера файлов, демонстрирующих операции с помощью класса Digital Twin Manager: *get_digital_twin_sample.py, update_digitial_twin_sample.py, invoke_command_sample.py и invoke_component_command_sample-.py*.  В этих примерах показано, как применять каждый API для взаимодействия с устройствами IoT Plug and Play.

### <a name="get-digital-twin"></a>Получение цифрового двойника

Во время прохождения статьи [Настройка среды для кратких руководств и учебников IoT Plug and Play](set-up-environment.md) вы создали две переменные среды, чтобы настроить пример для подключения к центру Интернета вещей и устройству:

* **IOTHUB_CONNECTION_STRING**: строка подключения центра Интернета вещей, которую вы записали ранее.
* **IOTHUB_DEVICE_ID**: `"my-pnp-device"`.

Чтобы запустить этот пример, используйте следующую команду в терминале **службы**:

```cmd/sh
python get_digital_twin_sample.py
```

В выходных данных отобразится цифровой двойник устройства и соответствующий идентификатор модели:

```cmd/sh
{'$dtId': 'mySimpleThermostat', '$metadata': {'$model': 'dtmi:com:example:Thermostat;1'}}
Model Id: dtmi:com:example:Thermostat;1
```

В следующем фрагменте показан пример кода из файла *get_digital_twin_sample.py*:

```python
    # Get digital twin and retrieve the modelId from it
    digital_twin = iothub_digital_twin_manager.get_digital_twin(device_id)
    if digital_twin:
        print(digital_twin)
        print("Model Id: " + digital_twin["$metadata"]["$model"])
    else:
        print("No digital_twin found")
```

### <a name="update-a-digital-twin"></a>Обновление цифрового двойника

В этом примере показано, как с помощью *обновления* изменить свойства цифрового двойника устройства. В следующем фрагменте кода из файла *update_digital_twin_sample.py* показано, как создать исправление:

```python
# If you already have a component thermostat1:
# patch = [{"op": "replace", "path": "/thermostat1/targetTemperature", "value": 42}]
patch = [{"op": "add", "path": "/targetTemperature", "value": 42}]
iothub_digital_twin_manager.update_digital_twin(device_id, patch)
print("Patch has been succesfully applied")
```

Чтобы запустить этот пример, используйте следующую команду в терминале **службы**:

```cmd/sh
python update_digital_twin_sample.py
```

Вы можете убедиться, что обновление успешно применено, в терминале **устройства**, где отображаются следующие выходные данные:

```cmd/sh
the data in the desired properties patch was: {'targetTemperature': 42, '$version': 2}
previous values
42
```

В терминале **службы** подтверждается, что обновление применено успешно:

```cmd/sh
Patch has been successfully applied
```

### <a name="invoke-a-command"></a>Вызов команды

Чтобы вызвать команду, запустите пример *invoke_command_sample.py*. В этом примере показано, как вызвать команду на простом устройстве термостата. Перед запуском этого примера задайте переменные среды `IOTHUB_COMMAND_NAME` и `IOTHUB_COMMAND_PAYLOAD` в терминале **службы**:

```cmd/sh
set IOTHUB_COMMAND_NAME="getMaxMinReport" # this is the relevant command for the thermostat sample
set IOTHUB_COMMAND_PAYLOAD="hello world" # this payload doesn't matter for this sample
```

Чтобы запустить этот пример, используйте следующую команду в терминале **службы**:
  
```cmd/sh
python invoke_command_sample.py
```

В терминале **службы** отобразится сообщение с подтверждением, полученное от устройства:

```cmd/sh
{"tempReport": {"avgTemp": 34.5, "endTime": "13/07/2020 16:03:38", "maxTemp": 49, "minTemp": 11, "startTime": "13/07/2020 16:02:18"}}
```

В терминале **устройства** вы увидите, что устройство успешно получило команду:

```cmd/sh
Command request received with payload
hello world
Will return the max, min and average temperature from the specified time hello to the current time
Done generating
{"tempReport": {"avgTemp": 34.2, "endTime": "09/07/2020 09:58:11", "maxTemp": 49, "minTemp": 10, "startTime": "09/07/2020 09:56:51"}}
Sent message
```

## <a name="next-steps"></a>Дальнейшие действия

Из этого краткого руководства вы узнали, как подключить устройство IoT Plug and Play к решению Интернета вещей. Дополнительные сведения о моделях устройства IoT Plug and Play см. в статье

> [!div class="nextstepaction"]
> [Руководство разработчика IoT Plug and Play](concepts-developer-guide-device-csharp.md)
