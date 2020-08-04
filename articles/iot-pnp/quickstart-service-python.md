---
title: Взаимодействие с устройством IoT Plug and Play (предварительная версия), подключенным к решению Интернета вещей Azure (Python) | Документация Майкрософт
description: Подключение к устройству IoT Plug and Play (предварительная версия), подключенному к решению Azure IoT, а также взаимодействие с ним с помощью Python.
author: elhorton
ms.author: elhorton
ms.date: 7/13/2020
ms.topic: quickstart
ms.service: iot-pnp
services: iot-pnp
ms.custom: mvc
ms.openlocfilehash: e3f00bb601cce17721c5247941588be1c2de788d
ms.sourcegitcommit: 46f8457ccb224eb000799ec81ed5b3ea93a6f06f
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/28/2020
ms.locfileid: "87352733"
---
# <a name="quickstart-interact-with-an-iot-plug-and-play-preview-device-thats-connected-to-your-solution-python"></a>Краткое руководство. Взаимодействие с подключенным к решению устройством IoT Plug and Play (предварительная версия) с помощью Python

[!INCLUDE [iot-pnp-quickstarts-service-selector.md](../../includes/iot-pnp-quickstarts-service-selector.md)]

Предварительная версия IoT Plug and Play упрощает работу с Интернетом вещей, позволяя взаимодействовать с моделью устройства без информации о базовой реализации устройства. В этом кратком руководстве показано, как подключиться к устройству IoT Plug and Play, подключенному к решению, а также управлять им с помощью Python.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="prerequisites"></a>Предварительные требования

Для выполнения инструкций, приведенных в этом кратком руководстве, на компьютере для разработки необходимо установить Python 3.7. Вы можете скачать последнюю рекомендуемую версию для нескольких платформ на сайте [python.org](https://www.python.org/). Проверить версию Python можно с помощью следующей команды:  

```cmd/sh
python --version
```

Установите [пакет SDK предварительной версии для службы Python](https://pypi.org/project/azure-iot-hub/2.2.1rc0/), выполнив следующую команду:

```cmd/sh
pip3 install azure-iot-hub==2.2.1rc0
```

[!INCLUDE [iot-pnp-prepare-iot-hub.md](../../includes/iot-pnp-prepare-iot-hub.md)]

Выполните следующую команду, чтобы получить _строку подключения к Центру Интернета вещей_ для вашего концентратора. Запишите эту строку подключения. Вы будете использовать ее позже при работе с этим кратким руководством.

```azurecli-interactive
az iot hub show-connection-string --hub-name <YourIoTHubName> --output table
```

Выполните указанную ниже команду, чтобы получить _строку подключения устройства_, добавленного в центр. Запишите эту строку подключения. Вы будете использовать ее позже при работе с этим кратким руководством.

```azurecli-interactive
az iot hub device-identity show-connection-string --hub-name <YourIoTHubName> --device-id <YourDeviceID> --output
```

## <a name="run-the-sample-device"></a>Запуск примера устройства

В рамках этого краткого руководства вы сможете применить пример термостата, написанный на Python, в качестве устройства IoT Plug and Play. Чтобы запустить пример устройства, сделайте следующее:

1. Откройте окно терминала в любой папке. Выполните следующую команду, чтобы клонировать репозиторий GitHub для [пакета средств разработки Python для Интернета вещей Azure](https://github.com/Azure/azure-iot-sdk-python) в следующее расположение:

    ```cmd/sh
    git clone https://github.com/Azure/azure-iot-sdk-python
    ```

1. Это окно терминала используется в качестве терминала **устройства**. Перейдите в папку клонированного репозитория, а затем в папку */azure-iot-sdk-python/azure-iot-device/samples/pnp*.

1. Настройте _строку подключения к устройству_:

    ```cmd/sh
    set IOTHUB_DEVICE_CONNECTION_STRING=<YourDeviceConnectionString>
    ```

1. Запустите пример термостата с помощью следующей команды:

    ```cmd/sh
    python pnp_thermostat.py
    ```

1. Отобразится сообщение о том, что устройство отправило определенные сведения и находится в сети. Эти сообщения означают, что устройство начало отправлять в концентратор данные телеметрии и теперь готово получать команды и обновления свойств. Не закрывайте этот терминал, он понадобится вам, чтобы проверить работу примера службы.

## <a name="run-the-sample-solution"></a>Запуск примера решения

В рамках этого краткого руководства вы примените пример решения Интернета вещей на Python для взаимодействия с только что настроенным примером устройства.

1. Откройте другое окно терминала, которое будет терминалом **службы**. Пакет SDK для службы предоставляется в предварительной версии, поэтому эти примеры следует клонировать из [ветви предварительной версии пакета SDK для Python](https://github.com/Azure/azure-iot-sdk-python/tree/pnp-preview-refresh):

    ```cmd/sh
    git clone https://github.com/Azure/azure-iot-sdk-python -b pnp-preview-refresh
    ```

1. Перейдите в папку клонированной ветви репозитория, а затем в папку */azure-iot-sdk-python/azure-iot-hub/samples*.

1. Настройте переменные среды для идентификатора устройства и _строки подключения к Центру Интернета вещей_:

    ```cmd/sh
    set IOTHUB_CONNECTION_STRING=<YourIOTHubConnectionString>
    set IOTHUB_DEVICE_ID=<Your device ID>
    ```

1. В папке samples есть четыре файла примеров с префиксом `pnp`. В этих примерах показано, как применять каждый API для взаимодействия с устройствами IoT Plug and Play.

### <a name="get-digital-twin"></a>Получение цифрового двойника

Чтобы запустить этот пример, используйте следующую команду в терминале **службы**:

```cmd/sh
python pnp_get_digital_twin_sample.py
```

В выходных данных отобразится цифровой двойник устройства и соответствующий идентификатор модели:

```cmd/sh
{'$dtId': 'mySimpleThermostat', '$metadata': {'$model': 'dtmi:com:example:Thermostat;1'}}
Model Id: dtmi:com:example:Thermostat;1
```

В следующем фрагменте показан пример кода из файла *pnp_get_digital_twin_sample.py*:

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

В этом примере показано, как с помощью *обновления* изменить свойства цифрового двойника устройства. В следующем фрагменте кода из файла *pnp_update_digital_twin_sample.py* показано, как создать обновление:

```python
# If you already have a component thermostat1:
# patch = [{"op": "replace", "path": "/thermostat1/targetTemperature", "value": 42}]
patch = [{"op": "add", "path": "/targetTemperature", "value": 42}]
iothub_digital_twin_manager.update_digital_twin(device_id, patch)
print("Patch has been succesfully applied")
```

Чтобы запустить этот пример, используйте следующую команду в терминале **службы**:

```cmd/sh
python pnp_update_digital_twin_sample.py
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

Чтобы вызвать команду, запустите пример *pnp_invoke_command_sample.py*. В этом примере показано, как вызвать команду на простом устройстве термостата. Перед запуском этого примера задайте переменные среды `IOTHUB_COMMAND_NAME` и `IOTHUB_COMMAND_PAYLOAD` в терминале **службы**:

```cmd/sh
set IOTHUB_COMMAND_NAME="getMaxMinReport" # this is the relevant command for the thermostat sample
set IOTHUB_COMMAND_PAYLOAD="hello world" # this payload doesn't matter for this sample
```

Чтобы запустить этот пример, используйте следующую команду в терминале **службы**:
  
```cmd/sh
python pnp_invoke_command_sample.py
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

[!INCLUDE [iot-pnp-clean-resources.md](../../includes/iot-pnp-clean-resources.md)]

## <a name="next-steps"></a>Дальнейшие действия

Из этого краткого руководства вы узнали, как подключить устройство IoT Plug and Play к решению Интернета вещей. Дополнительные сведения о моделях устройства IoT Plug and Play см. в статье

> [!div class="nextstepaction"]
> [Руководство для разработчиков IoT Plug and Play (предварительная версия)](concepts-developer-guide.md)
