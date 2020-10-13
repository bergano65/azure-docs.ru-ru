---
title: Руководством для разработчиков служб — Plug and Play IoT | Документация Майкрософт
description: Описание Plug and Play IoT для разработчиков служб
author: dominicbetts
ms.author: dobett
ms.date: 09/24/2020
ms.topic: conceptual
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: f082e4d4c6c71e460842f80a5aa17130b6a41279
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/09/2020
ms.locfileid: "91614229"
---
# <a name="iot-plug-and-play-service-developer-guide"></a>Руководством для разработчиков служб IoT Plug and Play

Plug and Play IoT позволяет создавать интеллектуальные устройства, которые объявляют свои возможности для приложений Интернета вещей Azure. Устройства IoT Plug and Play не нуждаются в ручной настройке, когда клиент подключает их к приложениям с поддержкой Интернета вещей Plug and Play.

IoT Plug and Play позволяет использовать устройства, которые объявили идентификатор модели с помощью центра Интернета вещей. Например, можно напрямую обращаться к свойствам и командам устройства.

Чтобы использовать устройство IoT Plug and Play, подключенное к центру Интернета вещей, используйте один из пакетов SDK для служб IoT или центр Интернета вещей REST API.

## <a name="service-sdks"></a>Пакеты SDK службы

Используйте пакеты SDK службы Azure IoT в решении для взаимодействия с устройствами и модулями. Например, пакеты SDK для службы можно использовать для чтения и обновления свойств двойника и вызова команд. Поддерживаются следующие языки: C#, Java, Node.js и Python.

Пакеты SDK для служб позволяют получать доступ к сведениям об устройстве из решения, например рабочего стола или веб-приложения. Пакеты SDK для служб включают два пространства имен и объектные модели, которые можно использовать для получения идентификатора модели:

- Клиент службы центра Интернета вещей. Эта служба предоставляет идентификатор модели в качестве свойства двойникаа устройства.

- Клиент службы Digital двойников. Новый API Digital двойников действует на высокоуровневых конструкциях, таких как компоненты, свойства и команды, в которых определена модель языка определения цифровых двойников. Интерфейсы API Digital двойника упрощают создание решений IoT Plug and Play решениями для сборщиков решений.

| Платформа | Клиент службы центра Интернета вещей | Клиент службы Digital двойников |
| -------- | ---------------------- | ---------------------------- |
| .NET     | [Документация](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.shared.twin.modelid?view=azure-dotnet#Microsoft_Azure_Devices_Shared_Twin_ModelId&preserve-view=true) <br/> [Примеры](https://github.com/Azure-Samples/azure-iot-samples-csharp/tree/master/iot-hub/Samples/service/PnpServiceSamples)| [Примеры](https://github.com/Azure-Samples/azure-iot-samples-csharp/tree/master/iot-hub/Samples/service/DigitalTwinClientSamples) |
| Java     | [Документация](https://docs.microsoft.com/java/api/com.microsoft.azure.sdk.iot.service.devicetwin.devicetwindevice?view=azure-java-stable&preserve-view=true) <br/> [Примеры](https://github.com/Azure/azure-iot-sdk-java/blob/master/service/iot-service-samples/pnp-service-sample)| [Примеры](https://github.com/Azure/azure-iot-sdk-java/tree/master/service/iot-service-samples/digitaltwin-service-samples) |
| Node.js  | [Документация](https://docs.microsoft.com/javascript/api/azure-iothub/twin?view=azure-node-latest&preserve-view=true) <br/> [Образец](https://github.com/Azure/azure-iot-sdk-node/blob/master/service/samples/javascript/twin.js)| [Документация](https://docs.microsoft.com/javascript/api/azure-iot-digitaltwins-service/?view=azure-node-latest&preserve-view=true) <br/> [Образец](https://github.com/Azure/azure-iot-sdk-node/blob/master/service/samples/javascript/get_digital_twin.js) |
| Python   | [Документация](https://docs.microsoft.com/python/api/azure-iot-hub/azure.iot.hub.iothubregistrymanager?view=azure-python&preserve-view=true) <br/> [Образец](https://github.com/Azure/azure-iot-sdk-python/blob/master/azure-iot-hub/samples/iothub_registry_manager_method_sample.py)| [Документация](https://docs.microsoft.com/python/api/azure-iot-hub/azure.iot.hub.iothubdigitaltwinmanager?view=azure-python&preserve-view=true) <br/> [Образец](https://github.com/Azure/azure-iot-sdk-python/blob/master/azure-iot-hub/samples/get_digital_twin_sample.py) |

## <a name="rest-api"></a>REST API

В следующих примерах для взаимодействия с подключенным устройством IoT Plug and Play используется REST API центра Интернета вещей. Текущая версия API — `2020-09-30` . Присоединитесь `?api-version=2020-09-30` к вызовам остальных PI.

> [!NOTE]
> Двойников модуля в настоящее время не поддерживаются `digitalTwins` API.

При вызове устройства термостата `t-123` вы получаете все свойства всех интерфейсов, реализованных устройством, с помощью вызова REST API Get:

```REST
GET /digitalTwins/t-123
```

Этот вызов будет включать свойство JSON `$metadata.$model` с идентификатором модели, объявленным устройством.

Доступ ко всем свойствам всех интерфейсов осуществляется с помощью `GET /DigitalTwin/{device-id}` шаблона REST API, где `{device-id}` — это идентификатор устройства:

```REST
GET /digitalTwins/{device-id}
```

Вы можете напрямую вызывать команды для устройств IoT Plug and Play. Если у `Thermostat` компонента на `t-123` устройстве есть `restart` команда, ее можно вызвать с помощью вызова REST API POST:

```REST
POST /digitalTwins/t-123/components/Thermostat/commands/restart
```

В общем случае команды можно вызывать с помощью этого шаблона REST API:

- `device-id`— идентификатор устройства.
- `component-name`: имя интерфейса из раздела Implements модели возможностей устройства.
- `command-name`: имя команды.

```REST
/digitalTwins/{device-id}/components/{component-name}/commands/{command-name}
```

## <a name="next-steps"></a>Дальнейшие шаги

Теперь, когда вы узнали о моделировании устройств, ниже приведены некоторые дополнительные ресурсы.

- [Язык определения цифровых двойников (DTDL)](https://github.com/Azure/opendigitaltwins-dtdl)
- [Пакет SDK для устройств для C](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/)
- [REST API IoT](https://docs.microsoft.com/rest/api/iothub/device)
- [Компоненты модели](./concepts-components.md)
- [Установка и использование средств разработки ДТДЛ](howto-use-dtdl-authoring-tools.md)
