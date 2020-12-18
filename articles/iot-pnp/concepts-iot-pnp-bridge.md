---
title: Мост Plug and Play IoT | Документация Майкрософт
description: Сведения о Bridge Plug and Play центра Интернета вещей и о том, как использовать его для подключения существующих устройств, подключенных к шлюзу Windows или Linux, в качестве устройств IoT Plug and Play.
author: usivagna
ms.author: ugans
ms.date: 09/22/2020
ms.topic: conceptual
ms.service: iot-pnp
services: iot-pnp
ms.custom: mvc
ms.openlocfilehash: 34af380d057ad47811e394da1e7a29198e102920
ms.sourcegitcommit: d79513b2589a62c52bddd9c7bd0b4d6498805dbe
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/18/2020
ms.locfileid: "97672786"
---
# <a name="iot-plug-and-play-bridge"></a>Мост для IoT Plug and Play

Мост IoT Plug and Play — это приложение с открытым исходным кодом для подключения существующих устройств, подключенных к шлюзу Windows или Linux, в качестве устройств IoT Plug and Play. После установки и настройки приложения на компьютере под управлением Windows или Linux его можно использовать для подключения подключенных устройств к центру Интернета вещей. С помощью моста можно сопоставлять интерфейсы Интернета вещей Plug and Play с данными телеметрии, отправляемыми подключенными устройствами, работать со свойствами устройств и вызывать команды.

:::image type="content" source="media/concepts-iot-pnp-bridge/iot-pnp-bridge-high-level.png" alt-text="В левой части существует несколько подключенных датчиков (как проводные, так и беспроводные) на компьютер под управлением Windows или Linux, содержащий мост Интернета вещей Plug and Play. Затем мост IoT Plug and Play соединяется с центром Интернета вещей справа":::

Мост Интернета вещей Plug and Play можно развернуть в виде автономного исполняемого файла на любом устройстве Интернета вещей, промышленном ПК, сервере или шлюзе под управлением Windows 10 или Linux. Его также можно скомпилировать в код приложения. Простой JSON-файл конфигурации сообщает центру Интернета вещей Plug and Play Bridge, какие подключенные устройства и периферийное устройство должны быть доступны в Azure.

## <a name="supported-protocols-and-sensors"></a>Поддерживаемые протоколы и датчики

Мост IoT Plug and Play поддерживает следующие типы периферийных устройств по умолчанию со ссылками на документацию по адаптеру:

|Периферийные устройства|Windows|Linux|
|---------|---------|---------|
|[Адаптер датчика Bluetooth](https://github.com/Azure/iot-plug-and-play-bridge/blob/master/pnpbridge/docs/bluetooth_sensor_adapter.md) подключается к обнаруженным датчикам Bluetooth с низким энергопотреблением (BLE).       |Да|Нет|
|[Адаптер камеры](https://github.com/Azure/iot-plug-and-play-bridge/blob/master/pnpbridge/docs/camera_adapter.md) подключает камеры на устройстве с Windows 10.               |Да|Нет|
|[Адаптер Modbus](https://github.com/Azure/iot-plug-and-play-bridge/blob/master/pnpbridge/docs/modbus_adapters.md) подключает датчики на устройстве Modbus.              |Да|Да|
|[Адаптер MQTT](https://github.com/Azure/iot-plug-and-play-bridge/blob/master/pnpbridge/docs/mqtt_adapter.md) подключает устройства, ИСПОЛЬЗУЮЩИЕ брокер MQTT.                  |Да|Да|
|[Адаптер сериалпнп](https://github.com/Azure/iot-plug-and-play-bridge/blob/master/serialpnp/Readme.md) подключает устройства, взаимодействующие по последовательному подключению.               |Да|Да|
|[Периферийные устройства Windows USB](https://github.com/Azure/iot-plug-and-play-bridge/blob/master/pnpbridge/docs/coredevicehealth_adapter.md) используют список классов интерфейсов устройств, поддерживаемых адаптером, для подключения устройств с указанным идентификатором оборудования.  |Да|Не применимо|

Дополнительные сведения о расширении моста Plug and Play IoT для поддержки дополнительных протоколов устройств см. в статье [Создание, развертывание и расширение моста Plug and Play центра Интернета вещей](howto-build-deploy-extend-pnp-bridge.md).

## <a name="iot-plug-and-play-bridge-architecture"></a>Архитектура моста Plug and Play IoT

:::image type="content" source="media/concepts-iot-pnp-bridge/iot-pnp-bridge-components.png" alt-text="В левой части есть несколько флажков, указывающих на различные периферийные устройства, подключенные к компьютеру под управлением Windows или Linux, который содержит мост IoT Plug and Play. В верхней части — поле, обозначенное точками конфигурации в отношении моста. Затем мост соединяется с центром Интернета вещей в правой части схемы.":::

### <a name="iot-plug-and-play-bridge-adapters"></a>Адаптеры для мостов Plug and Play IoT

Мост IoT Plug and Play поддерживает набор адаптеров моста Интернета вещей Plug and Play для различных типов устройств. *Манифест адаптера* статически определяет адаптеры для моста.

Диспетчер адаптеров моста использует манифест для обнаружения и вызова функций адаптера. Диспетчер адаптеров вызывает только функцию Create для адаптеров моста, необходимых компонентам интерфейса, перечисленным в файле конфигурации. Для каждого компонента Plug and Play IoT создается экземпляр адаптера.

Адаптер моста создает и получает обработчик цифрового двойниканого интерфейса. Адаптер использует этот обработчик для привязки функции устройства к цифровому двойника.

Используя сведения в файле конфигурации, адаптер моста использует следующие методы для включения передачи данных с устройства в цифровое двойника через мост:

- Устанавливает коммуникационный канал напрямую.
- Создает наблюдатель устройств, чтобы ждать, пока канал связи станет доступным.

### <a name="configuration-file"></a>Файл конфигурации

Мост Интернета вещей Plug and Play использует файл конфигурации на основе JSON, который указывает:

- Как подключиться к центру Интернета вещей или IoT Central приложению: параметры включают строки подключения, параметры проверки подлинности или службу подготовки устройств (DPS).
- Расположение моделей возможностей центра Интернета вещей Plug and Play, используемых мостом. Модель определяет возможности устройства IoT Plug and Play, а также является статичной и неизменяемой.
- Список компонентов интерфейса Интернета вещей Plug and Play и следующие сведения для каждого компонента:
- Идентификатор интерфейса и имя компонента.
- Адаптер моста, необходимый для взаимодействия с компонентом.
- Сведения об устройстве, необходимые адаптеру моста для установления связи с устройством. Например, идентификатор оборудования или конкретные сведения о адаптере, интерфейсе или протоколе.
- Дополнительный подтип адаптера моста или Конфигурация интерфейса, если адаптер поддерживает несколько типов связи с аналогичными устройствами. В примере показано, как можно настроить компонент датчика Bluetooth:

    ```json
    {
      "_comment": "Component BLE sensor",
      "pnp_bridge_component_name": "blesensor1",
      "pnp_bridge_adapter_id": "bluetooth-sensor-pnp-adapter",
      "pnp_bridge_adapter_config": {
        "bluetooth_address": "267541100483311",
        "blesensor_identity" : "Blesensor1"
      }
    }
    ```

- Необязательный список параметров адаптера глобальных мостов. Например, адаптер моста датчика Bluetooth имеет словарь поддерживаемых конфигураций. Компонент интерфейса, для которого требуется адаптер датчика Bluetooth, может выбрать одну из следующих конфигураций `blesensor_identity` :

    ```json
    {
      "pnp_bridge_adapter_global_configs": {
        "bluetooth-sensor-pnp-adapter": {
          "Blesensor1" : {
            "company_id": "0x499",
            "endianness": "big",
            "telemetry_descriptor": [
              {
                "telemetry_name": "humidity",
                "data_parse_type": "uint8",
                "data_offset": 1,
                "conversion_bias": 0,
                "conversion_coefficient": 0.5
              },
              {
                "telemetry_name": "temperature",
                "data_parse_type": "int8",
                "data_offset": 2,
                "conversion_bias": 0,
                "conversion_coefficient": 1.0
              },
              {
                "telemetry_name": "pressure",
                "data_parse_type": "int16",
                "data_offset": 4,
                "conversion_bias": 0,
                "conversion_coefficient": 1.0
              },
              {
                "telemetry_name": "acceleration_x",
                "data_parse_type": "int16",
                "data_offset": 6,
                "conversion_bias": 0,
                "conversion_coefficient": 0.00980665
              },
              {
                "telemetry_name": "acceleration_y",
                "data_parse_type": "int16",
                "data_offset": 8,
                "conversion_bias": 0,
                "conversion_coefficient": 0.00980665
              },
              {
                "telemetry_name": "acceleration_z",
                "data_parse_type": "int16",
                "data_offset": 10,
                "conversion_bias": 0,
                "conversion_coefficient": 0.00980665
              }
            ]
          }
        }
      }
    }
    ```

## <a name="download-iot-plug-and-play-bridge"></a>Скачивание моста Plug and Play IoT

Вы можете скачать предварительно созданную версию моста с поддерживаемыми адаптерами в [IoT Plug and Play мостовыми выпусками](https://github.com/Azure/iot-plug-and-play-bridge/releases) и расширить список ресурсов для самого последнего выпуска. Скачайте последнюю версию приложения для вашей операционной системы.

Кроме того, вы можете загрузить и просмотреть исходный код [центра Интернета вещей Plug and Play Bridge на GitHub](https://github.com/Azure/iot-plug-and-play-bridge).

## <a name="next-steps"></a>Следующие шаги

Теперь, когда у вас есть общие сведения об архитектуре центра Интернета вещей Plug and Play, выполните следующие действия, чтобы получить дополнительные сведения:

- [Как использовать Plug and Play моста Интернета вещей](./howto-use-iot-pnp-bridge.md)
- [Создание, развертывание и расширение моста Plug and Play IoT](howto-build-deploy-extend-pnp-bridge.md)
- [Мост IoT Plug and Play на GitHub](https://github.com/Azure/iot-plug-and-play-bridge)
