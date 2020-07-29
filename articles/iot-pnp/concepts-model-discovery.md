---
title: Реализация обнаружения модели предварительной версии IoT Plug and Play | Документация Майкрософт
description: В качестве построителя решений вы узнаете, как реализовать обнаружение модели Интернета вещей Plug and Play в решении.
author: prashmo
ms.author: prashmo
ms.date: 07/23/2020
ms.topic: conceptual
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: 364b85a8ead09858b97d5d7e6ca8c130b9960b2c
ms.sourcegitcommit: 46f8457ccb224eb000799ec81ed5b3ea93a6f06f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/28/2020
ms.locfileid: "87337387"
---
# <a name="implement-iot-plug-and-play-preview-model-discovery-in-an-iot-solution"></a>Реализация обнаружения модели предварительной версии IoT Plug and Play в решении IoT

В этой статье описывается, как можно реализовать обнаружение модели Интернета вещей Plug and Play Preview в решении IoT, как в построителе решений. Процедура обнаружения модели описывает, как:

- Устройства IoT Plug and Play регистрируют свои ИДЕНТИФИКАТОРы моделей.
- Решение IoT извлекает интерфейсы, реализованные устройством.

Существует две основные категории решения IoT:

- *Решение IOT, разработанное специально* , работает с известным набором моделей устройств IOT Plug and Play.

- *Решение IOT, управляемое моделью* , может работать с любым устройством Plug and Play IOT. Создание решения, управляемого моделью, является более сложным, но преимущество заключается в том, что ваше решение работает с любыми устройствами, добавленными в будущем.

    Чтобы создать решение IoT, управляемое моделью, необходимо создать логику на основе примитивов интерфейса Интернета вещей Plug and Play: телеметрии, свойств и команд. Логика решения представляет устройство путем объединения нескольких функций телеметрии, свойств и команд.

В этой статье описывается, как реализовать обнаружение модели в обоих типах решений.

## <a name="model-discovery"></a>Обнаружение модели

Для обнаружения модели, реализованной устройством, решение может получить идентификатор модели с помощью обнаружения на основе событий или обнаружения на основе двойника:

### <a name="event-based-discovery"></a>Обнаружение на основе событий

Когда устройство IoT Plug and Play подключается к центру Интернета вещей, оно регистрирует модель, которую он реализует. Эта регистрация приводит к уведомлению о [событии изменения цифрового двойникаа](concepts-digital-twin.md#digital-twin-change-events) . Сведения о том, как включить маршрутизацию для цифровых двойника событий, см. в статье [Использование маршрутизации сообщений центра Интернета вещей для отправки сообщений с устройства в облако в разные конечные точки](../iot-hub/iot-hub-devguide-messages-d2c.md#non-telemetry-events).

Решение может использовать событие, показанное в следующем фрагменте кода, чтобы узнать о подключении к IoT Plug and Play устройстве и получить его идентификатор модели:

```json
iothub-connection-device-id:sample-device
iothub-enqueuedtime:7/22/2020 8:02:27 PM
iothub-message-source:digitalTwinChangeEvents
correlation-id:100f322dc2c5
content-type:application/json-patch+json
content-encoding:utf-8
[
  {
    "op": "replace",
    "path": "/$metadata/$model",
    "value": "dtmi:com:example:TemperatureController;1"
  }
]
```

Это событие активируется при добавлении или обновлении идентификатора модели устройства.

### <a name="twin-based-discovery"></a>Обнаружение на основе двойника

Если решение хочет узнать о возможностях конкретного устройства, оно может использовать API [Get Digital двойника](https://docs.microsoft.com/rest/api/iothub/service/digitaltwin/getdigitaltwin) для получения информации.

В следующем фрагменте цифрового двойника `$metadata.$model` содержит идентификатор модели для устройства IoT Plug and Play:

```json
{
    "$dtId": "sample-device",
    "$metadata": {
        "$model": "dtmi:com:example:TemperatureController;1",
        "serialNumber": {
            "lastUpdateTime": "2020-07-17T06:10:31.9609233Z"
        }
    }
}
```

Решение также может использовать **Get двойника** для получения идентификатора модели из двойникаа устройства, как показано в следующем фрагменте кода:

```json
{
    "deviceId": "sample-device",
    "etag": "AAAAAAAAAAc=",
    "deviceEtag": "NTk0ODUyODgx",
    "status": "enabled",
    "statusUpdateTime": "0001-01-01T00:00:00Z",
    "connectionState": "Disconnected",
    "lastActivityTime": "2020-07-17T06:12:26.8402249Z",
    "cloudToDeviceMessageCount": 0,
    "authenticationType": "sas",
    "x509Thumbprint": {
        "primaryThumbprint": null,
        "secondaryThumbprint": null
    },
    "modelId": "dtmi:com:example:TemperatureController;1",
    "version": 15,
    "properties": {...}
    }
}
```

## <a name="model-resolution"></a>Разрешение модели

Решение использует разрешение модели для получения доступа к интерфейсам, составляющим модель, из идентификатора модели. 

- Решения могут использовать эти интерфейсы в качестве файлов в локальной папке. 
- Решения могут использовать [репозиторий модели](concepts-model-repository.md).

## <a name="next-steps"></a>Дальнейшие действия

Теперь, когда вы узнали об обнаружении модели для решения IoT, Узнайте больше о [платформе Azure IOT](overview-iot-plug-and-play.md) , чтобы использовать другие возможности для решения.

- [Взаимодействие с устройством из вашего решения](quickstart-service-node.md)
- [REST API IoT Digital двойника](https://docs.microsoft.com/rest/api/iothub/service/digitaltwin)
- [Обозреватель Azure IoT](howto-use-iot-explorer.md)
