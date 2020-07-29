---
title: Соглашения Plug and Play IoT | Документация Майкрософт
description: Описание соглашений Plug and Play IoT, которое использует устройства при отправке телеметрии и свойствах, а также на обработку команд и обновлений свойств.
author: rido-min
ms.author: rmpablos
ms.date: 07/10/2020
ms.topic: conceptual
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: b06edb83e85ef6d56cf8bf8f91551fe99535eba1
ms.sourcegitcommit: 46f8457ccb224eb000799ec81ed5b3ea93a6f06f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/28/2020
ms.locfileid: "87352421"
---
# <a name="iot-plug-and-play-conventions"></a>Соглашения Plug and Play IoT

При обмене сообщениями с центром Интернета вещей на устройствах предварительной версии IoT Plug and Play должны следовать набору соглашений. Устройства с предварительной версией IoT Plug and Play используют протокол MQTT для обмена данными с центром Интернета вещей.

Вы описываете телеметрию, свойства и команды, которые устройство IoT Plug and Play реализует с помощью _модели_ [Digital двойников Definition Language v2 (дтдл)](https://github.com/Azure/opendigitaltwins-dtdl) . В этой статье есть два типа модели, на которые ссылается:

- **Нет компонента** — модель без компонентов. Модель объявляет данные телеметрии, свойства и команды в качестве свойств верхнего уровня в разделе содержимого основного интерфейса.
- **Несколько компонентов** — модель, состоящая из двух или более интерфейсов. Главный интерфейс с данными телеметрии, свойствами и командами. Один или несколько интерфейсов, объявленных как компоненты с дополнительными данными телеметрии, свойствами и командами.

Дополнительные сведения см. [в разделе IoT Plug and Play Components in Models](concepts-components.md).

## <a name="model-discovery"></a>Обнаружение модели

Чтобы объявить реализуемую модель, устройство IoT Plug and Play включает идентификатор модели в пакет подключения MQTT, добавляя `model-id` к `USERNAME` полю.

Для обнаружения модели, реализуемой устройством, служба может получить идентификатор модели из:

- Поле двойникаа устройства `modelId` .
- Поле Digital двойника `$metadata.$model` .
- Уведомление об изменении цифрового двойникаа.

## <a name="telemetry"></a>Телеметрия

Данные телеметрии, отправляемые с устройства без компонента, не нуждаются в дополнительных метаданных. Система добавляет `dt-dataschema` свойство.

Данные телеметрии, отправляемые с устройства с несколькими компонентами, должны добавляться `$.sub` в качестве свойства сообщения. Система добавляет `dt-subject` `dt-dataschema` Свойства и.

## <a name="read-only-properties"></a>Свойства только для чтения

### <a name="sample-no-component-read-only-property"></a>Образец нет компонента свойство только для чтения

Устройство может отправить любой допустимый код JSON, следующий за правилами ДТДЛ v2.

:::row:::
   :::column span="":::
      **DTDL**

      ```json
      "@context": "dtmi:dtdl:context;2",
      "@id": "dtmi:example: Thermostat;1",
      "@type": "Interface",
      "contents": [
        {
          "@type": "Property",
          "name": "temperature",
          "schema": "double"
        }
      ]
      ```
   :::column-end:::
   :::column span="":::
      **Пример полезных данных**

      ```json
      "reported" :
      {
        "temperature" : 21.3
      }
      ```
   :::column-end:::
:::row-end:::

### <a name="sample-multiple-components-read-only-property"></a>Пример использования нескольких компонентов свойство только для чтения

Устройство должно добавить `{"__t": "c"}` маркер, чтобы указать, что элемент ссылается на компонент.

:::row:::
   :::column span="":::
      **DTDL**

      ```json
      "@context": "dtmi:dtdl:context;2",
      "@id": "dtmi:com:example:TemperatureController;1",
      "@type": "Interface",
      "displayName": "Temperature Controller",
      "contents": [
        {
          "@type" : "Component",
          "schema": "dtmi:com:example:Thermostat;1",
          "name": "thermostat1"
        }
      ]

      "@context": "dtmi:dtdl:context;2",
      "@id": "dtmi:com:example:Thermostat;1",
      "@type": "Interface",
      "contents": [
        {
          "@type": "Property",
          "name": "temperature",
          "schema": "double"
        }
      ]
      ```
   :::column-end:::
   :::column span="":::
      **Сообщаемое свойство**

      ```json
      "reported": {
        "thermostat1": {
          "__t": "c",
          "temperature": 21.3
        }
      }
      ```
   :::column-end:::
:::row-end:::

## <a name="writable-properties"></a>Доступные для записи свойства

Устройство должно подтвердить, что оно получило свойство, отправив сообщаемое свойство. Сообщаемое свойство должно включать:

- `value`— значение, полученное устройством.
- `ac`— код подтверждения, использующий код состояния HTTP.
- `av`— Версия подтверждения, относящаяся к `$version` требуемому свойству.
- `ad`— Необязательное описание подтверждения.

### <a name="sample-no-component-writable-property"></a>Пример свойства, доступного для записи без компонента

Устройство может отправить любой допустимый код JSON, следующий за правилами ДТДЛ v2:

:::row:::
   :::column span="":::
      **DTDL**

      ```json
      "@context": "dtmi:dtdl:context;2",
      "@id": "dtmi:example: Thermostat;1",
      "@type": "Interface",
      "contents": [
        {
          "@type": "Property",
          "name": "targetTemperature",
          "schema": "double",
          "writable": true
        }
      ]
      ```
   :::column-end:::
   :::column span="":::
      **Требуемое свойство**

      ```json
      "desired" :
      {
        "targetTemperature" : 21.3
      },
      "$version" : 3
      ```
   :::column-end:::
   :::column span="":::
      **Сообщаемое свойство**

      ```json
      "reported": {
        "targetTemperature": {
          "value": 21.3,
          "ac": 200,
          "av": 3,
          "ad": "complete"
       }
     }
      ```
   :::column-end:::
:::row-end:::

### <a name="sample-multiple-components-writable-property"></a>Пример свойства, записываемого несколькими компонентами

Устройство должно добавить `{"__t": "c"}` маркер, чтобы указать, что элемент ссылается на компонент.

Маркер отправляется только для обновлений на уровне компонентов, поэтому устройства не должны проверку этого флага.

Устройство должно подтвердить, что оно получило свойство, отправив сообщаемое свойство:

:::row:::
   :::column span="":::
      **DTDL**

      ```json
      "@context": "dtmi:dtdl:context;2",
      "@id": "dtmi:com:example:TemperatureController;1",
      "@type": "Interface",
      "displayName": "Temperature Controller",
      "contents": [
        {
          "@type" : "Component",
          "schema": "dtmi:com:example:Thermostat;1",
          "name": "thermostat1"
        }
      ]

      "@context": "dtmi:dtdl:context;2",
      "@id": "dtmi:com:example:Thermostat;1",
      "@type": "Interface",
      "contents": [
        {
          "@type": "Property",
          "name": "targetTemperature",
          "schema": "double",
          "writable": true
        }
      ]
      ```
   :::column-end:::
   :::column span="":::
      **Требуемое свойство**

      ```json
      "desired": {
        "thermostat1": {
          "__t": "c",
          "targetTemperature": 21.3
        }
      },
      "$version" : 3
      ```
   :::column-end:::
   :::column span="":::
      **Сообщаемое свойство**

      ```json
      "reported": {
        "thermostat1": {
          "__t": "c",
          "targetTemperature": {
            "value": 23,
            "ac": 200,
            "av": 3,
            "ad": "complete"
          }
        }
      }
      ```
   :::column-end:::
:::row-end:::

## <a name="commands"></a>Команды

Ни один из интерфейсов компонентов не использует имя команды без префикса.

На устройстве интерфейсы с несколькими компонентами используют имена команд в следующем формате: `componentName*commandName` .

## <a name="next-steps"></a>Дальнейшие действия

Теперь, когда вы узнали о соглашениях IoT Plug and Play, ниже приведены некоторые дополнительные ресурсы.

- [Язык определения цифровых двойников (ДТДЛ)](https://github.com/Azure/opendigitaltwins-dtdl)
- [Пакет SDK для устройств для C](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/)
- [REST API IoT](https://docs.microsoft.com/rest/api/iothub/device)
- [Компоненты модели](./concepts-components.md)
