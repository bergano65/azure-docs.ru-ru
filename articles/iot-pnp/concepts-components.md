---
title: Общие сведения о компонентах в Plug and Playных моделях Интернета вещей | Документация Майкрософт
description: Разница между моделями ДТДЛ Интернета вещей Plug and Play, которые используют компоненты и модели, которые не используют компоненты.
author: ericmitt
ms.author: ericmitt
ms.date: 07/07/2020
ms.topic: conceptual
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: e7b24dd9cdbd11b56545f85ac233665f8fa4adfe
ms.sourcegitcommit: a422b86148cba668c7332e15480c5995ad72fa76
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/30/2020
ms.locfileid: "91574285"
---
# <a name="iot-plug-and-play-components-in-models"></a>Компоненты Plug and Play IoT в моделях

В соглашениях Plug and Play IoT устройство — это устройство IoT Plug and Play, если оно представляет идентификатор модели ДТДЛ (Digital двойников Definition Language) при подключении к центру Интернета вещей.

В следующем фрагменте кода показаны некоторые примеры идентификаторов моделей:

```json
 "@id": "dtmi:com:example:TemperatureController;1"
 "@id": "dtmi:com:example:Thermostat;1",
```

## <a name="no-components"></a>Нет компонентов

В простой модели не используются внедренные или каскадные компоненты. В нем содержатся сведения о заголовке и разделе "содержимое" для определения телеметрии, свойств и команд.

В следующем примере показана часть простой модели, которая не использует компоненты.

```json
{
  "@context": "dtmi:dtdl:context;2",
  "@id": "dtmi:com:example:Thermostat;1",
  "@type": "Interface",
  "displayName": "Thermostat",
  "description": "Reports current temperature and provides desired temperature control.",
  "contents": [
    {
      "@type": [
        "Telemetry",
        "Temperature"
      ],
      "name": "temperature",
      "displayName": "Temperature",
      "description": "Temperature in degrees Celsius.",
      "schema": "double",
      "unit": "degreeCelsius"
    },
    {
      "@type": [
        "Property",
...
```

Хотя модель явно не определяет компонент, она ведет себя так, как если бы он был единственным _компонентом по умолчанию_со всеми определениями телеметрии, свойств и команд.

На следующем снимке экрана показано, как модель отображается в средстве Azure IoT Explorer.

:::image type="content" source="media/concepts-components/default-component.png" alt-text="Компонент по умолчанию в Azure IoT Explorer":::

Идентификатор модели хранится в свойстве двойникаа устройства, как показано на следующем снимке экрана:

:::image type="content" source="media/concepts-components/twin-model-id.png" alt-text="Компонент по умолчанию в Azure IoT Explorer":::

Модель ДТДЛ без компонентов является удобным упрощением для устройства или IoT Edge модуля с единым набором телеметрии, свойств и команд. Модель, которая не использует компоненты, упрощает перенос существующего устройства или модуля в центр Интернета вещей Plug and Play устройство или модуль. вы создаете модель ДТДЛ, которая описывает реальное устройство или модуль без необходимости определять какие-либо компоненты.

> [!TIP]
> Модуль может быть устройством [module] (.. /ИОТ-ХУБ/ИОТ-ХУБ-девгуиде-модуле-Твинс.МД или [модуль IOT Edge](../iot-edge/about-iot-edge.md).

## <a name="multiple-components"></a>Несколько компонентов

Компоненты позволяют создать интерфейс модели в виде сборки других интерфейсов.

Например, интерфейс [термостата](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/samples/Thermostat.json) определяется как модель. Этот интерфейс можно включить в качестве одного или нескольких компонентов при определении [модели контроллера температуры](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/samples/TemperatureController.json). В следующем примере эти компоненты называются `thermostat1` и `thermostat2` .

Для модели ДТДЛ с несколькими компонентами существует два или более раздела компонентов. Каждый раздел имеет `@type` значение `Component` и явно ссылается на схему, как показано в следующем фрагменте кода:

```json
{
  "@context": "dtmi:dtdl:context;2",
  "@id": "dtmi:com:example:TemperatureController;1",
  "@type": "Interface",
  "displayName": "Temperature Controller",
  "description": "Device with two thermostats and remote reboot.",
  "contents": [
...
    {
      "@type" : "Component",
      "schema": "dtmi:com:example:Thermostat;1",
      "name": "thermostat1",
      "displayName": "Thermostat One",
      "description": "Thermostat One of Two."
    },
    {
      "@type" : "Component",
      "schema": "dtmi:com:example:Thermostat;1",
      "name": "thermostat2",
      "displayName": "Thermostat Two",
      "description": "Thermostat Two of Two."
    },
    {
      "@type": "Component",
      "schema": "dtmi:azure:DeviceManagement:DeviceInformation;1",
      "name": "deviceInformation",
      "displayName": "Device Information interface",
      "description": "Optional interface with basic device hardware information."
    }
...
```

Эта модель содержит три компонента, определенные в разделе содержимого — два `Thermostat` `DeviceInformation` компонента и компонент. Существует также компонент по умолчанию.

## <a name="next-steps"></a>Дальнейшие действия

Теперь, когда вы узнали о компонентах модели, вот некоторые дополнительные ресурсы:

- [Установка и использование средств разработки ДТДЛ](howto-use-dtdl-authoring-tools.md)
- [Digital двойников Definition Language v2 (ДТДЛ)](https://github.com/Azure/opendigitaltwins-dtdl)
- [Репозитории модели](./concepts-model-repository.md)