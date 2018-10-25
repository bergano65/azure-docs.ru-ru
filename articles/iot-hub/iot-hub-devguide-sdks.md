---
title: Общие сведения о пакетах SDK для Azure IoT | Документация Майкрософт
description: Руководство разработчика. Сведения и ссылки на различные пакеты SDK для устройств и служб Azure IoT, которые можно использовать для создания приложений для устройств и внутренних приложений.
author: dominicbetts
manager: timlt
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 09/14/2018
ms.author: dobett
ms.openlocfilehash: 1eeb0afdd5ffcbe00357914d6a98c8d0b3d452ec
ms.sourcegitcommit: 7bc4a872c170e3416052c87287391bc7adbf84ff
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/02/2018
ms.locfileid: "48017965"
---
# <a name="understand-and-use-azure-iot-hub-sdks"></a>Понимание и использование пакетов SDK для Центра Интернета вещей Azure

Есть три категории пакетов средств разработки программного обеспечения для работы с Центром Интернета вещей:

* **Пакеты SDK для устройств** позволяют создавать приложения, работающие на ваших устройствах Интернета вещей, используя клиент устройства или клиент модуля. Эти приложения отправляют данные телеметрии в центр Интернета вещей и при необходимости получают из него сообщения, задания, методы или обновления двойников.  Вы также можете использовать клиент модуля для создания [модулей](../iot-edge/iot-edge-modules.md) для [среды выполнения Azure IoT Edge](../iot-edge/about-iot-edge.md).

* **Пакеты SDK для служб** дают возможность управлять Центром Интернета вещей, а также отправлять сообщения, планировать задания, вызывать прямые методы и обновлять нужные свойства на устройствах Интернета вещей или модулях.

* **Пакеты SDK для подготовки устройств** позволяют вам предоставлять устройства в Центр Интернета вещей с помощью [Службы подготовки устройств](../iot-dps/about-iot-dps.md).

Узнайте о [преимуществах разработки с использованием пакетов SDK для Azure IoT](https://azure.microsoft.com/blog/benefits-of-using-the-azure-iot-sdks-in-your-azure-iot-solution/).

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

## <a name="azure-iot-device-sdks"></a>Пакеты SDK для устройств Azure IoT

Пакеты SDK для устройств Центра Интернета вещей Microsoft Azure содержат код, упрощающий построение устройств и приложений, которые подключаются к службам Центра Интернета вещей и управляются с помощью этих служб.

Пакет SDK для устройств Azure IoT для .NET: 

* установка из [Nuget](https://www.nuget.org/packages/Microsoft.Azure.Devices.Client/);
* [Исходный код](https://github.com/Azure/azure-iot-sdk-csharp).
* [Справочник по API](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices?view=azure-dotnet)
* [Справочник по модулям](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.moduleclient?view=azure-dotnet)

Пакет SDK для Центра Интернета вещей для C, написанный в соответствии со стандартом ANSI C (C99) для обеспечения переносимости и совместимости с широким диапазоном платформ:

* установка из [apt-get, MBED, Arduino IDE или Nuget](https://github.com/Azure/azure-iot-sdk-c/blob/master/readme.md);
* [Исходный код](https://github.com/Azure/azure-iot-sdk-c).
* [Справочник по API](https://azure.github.io/azure-iot-sdk-c/index.html)
* [Справочник по модулям](https://github.com/Azure/azure-iot-sdk-c/blob/master/iothub_client/inc/iothub_module_client.h)

Пакет SDK для устройств Azure IoT для Java: 

* добавление в проект [Maven](https://github.com/Azure/azure-iot-sdk-java/blob/master/doc/java-devbox-setup.md#for-the-device-sdk);
* [Исходный код](https://github.com/Azure/azure-iot-sdk-java).
* [Справочник по API](https://docs.microsoft.com/java/api/com.microsoft.azure.sdk.iot.device)
* [Справочник по модулям](https://docs.microsoft.com/java/api/com.microsoft.azure.sdk.iot.device._module_client?view=azure-java-stable).

Пакет SDK для устройств Azure IoT для Node.js: 

* установка из [npm](https://www.npmjs.com/package/azure-iot-device);
* [Исходный код](https://github.com/Azure/azure-iot-sdk-node).
* [Справочник по API](https://docs.microsoft.com/javascript/api/azure-iot-device/?view=azure-iot-typescript-latest)
* [Справочник по модулям](https://docs.microsoft.com/javascript/api/azure-iot-device/moduleclient?view=azure-node-latest).

Пакет SDK для устройств Azure IoT для Python: 

* установка из [pip](https://pypi.python.org/pypi/azure-iothub-device-client/);
* [Исходный код](https://github.com/Azure/azure-iot-sdk-python).
* Справочник по API: см. [Справочная документация по API для C](https://azure.github.io/azure-iot-sdk-c/index.html).

Пакет SDK для устройств Центра Интернета вещей для iOS: 

* установка из [CocoaPod](https://cocoapods.org/pods/AzureIoTHubClient);
* [Примеры](https://github.com/Azure-Samples/azure-iot-samples-ios)
* Справочник по API: см. [Справочная документация по API для C](https://azure.github.io/azure-iot-sdk-c/index.html).

> [!NOTE]
> Сведения об установке двоичных файлов и зависимостей на компьютере для разработки с помощью диспетчера пакетов, зависящего от языка или платформы, см. в файле сведений в репозиториях GitHub.
> 
> 

### <a name="os-platform-and-hardware-compatibility"></a>Платформа ОС и совместимость оборудования

Поддерживаемые платформы для пакетов SDK можно найти в [Поддержка платформ пакетов SDK для устройств Azure IoT](iot-hub-device-sdk-platform-support.md).

Дополнительные сведения о совместимости пакета SDK с определенными устройствами см. в [каталоге устройств, сертифицированных по программе Microsoft Azure Certified for IoT](https://catalog.azureiotsuite.com/), или в описании для конкретного репозитория.

## <a name="azure-iot-service-sdks"></a>Пакеты SDK для служб Azure IoT

Пакеты SDK для службы Интернета вещей Azure содержат код, который облегчает создание приложений, взаимодействующих непосредственно с Центром Интернета вещей, для управления устройствами и безопасностью.

Пакет SDK для службы Центра Интернета вещей для .NET:

* скачивание из [Nuget](https://www.nuget.org/packages/Microsoft.Azure.Devices/);
* [Исходный код](https://github.com/Azure/azure-iot-sdk-csharp).
* [Справочник по API](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices)

Пакет SDK для службы Центра Интернета вещей для Java: 

* добавление в проект [Maven](https://github.com/Azure/azure-iot-sdk-java/blob/master/doc/java-devbox-setup.md#for-the-service-sdk
);
* [Исходный код](https://github.com/Azure/azure-iot-sdk-java).
* [Справочник по API](https://docs.microsoft.com/java/api/com.microsoft.azure.sdk.iot.service)

Пакет SDK для службы Центра Интернета вещей для Node.js: 

* скачивание из [npm](https://www.npmjs.com/package/azure-iothub);
* [Исходный код](https://github.com/Azure/azure-iot-sdk-node).
* [Справочник по API](https://docs.microsoft.com/javascript/api/azure-iothub/?view=azure-iot-typescript-latest)

Пакет SDK для службы Центра Интернета вещей для Пакет SDK для Python: 

* скачивание из [pip](https://pypi.python.org/pypi/azure-iothub-service-client/);
* [Исходный код](https://github.com/Azure/azure-iot-sdk-python).

Пакет SDK для службы Центра Интернета вещей для C: 

* скачивание из [apt-get, MBED, Arduino IDE или Nuget](https://github.com/Azure/azure-iot-sdk-c/blob/master/readme.md);
* [Исходный код](https://github.com/Azure/azure-iot-sdk-c).

Пакет SDK для служб Центра Интернета вещей для iOS: 

* установка из [CocoaPod](https://cocoapods.org/pods/AzureIoTHubServiceClient);
* [Примеры](https://github.com/Azure-Samples/azure-iot-samples-ios)

> [!NOTE]
> Сведения об установке двоичных файлов и зависимостей на компьютере для разработки с помощью диспетчера пакетов, зависящего от языка или платформы, см. в файле сведений в репозиториях GitHub.

## <a name="device-provisioning-sdks"></a>Пакеты SDK для подготовки устройств

**Пакеты SDK для подготовки Microsoft Azure** позволяют вам предоставлять устройства в Центр Интернета вещей с помощью [Службы подготовки устройств](../iot-dps/about-iot-dps.md).

Устройство подготовки Azure и пакеты SDK службы для C#:

* [Устройство подготовки клиента SDK](https://github.com/Azure/azure-iot-sdk-csharp/blob/master/provisioning/device);
* [Служба подготовки клиента SDK](https://github.com/Azure/azure-iot-sdk-csharp/blob/master/provisioning/service).

Устройство подготовки Azure и пакеты SDK службы для Java:

* [Устройство подготовки клиента SDK](https://github.com/Azure/azure-iot-sdk-java/blob/master/provisioning/provisioning-device-client);
* [Служба подготовки клиента SDK](https://github.com/Azure/azure-iot-sdk-java/blob/master/provisioning/provisioning-service-client).

Устройство подготовки Azure и пакеты SDK службы для Node.js:

* [Устройство подготовки клиента SDK](https://github.com/Azure/azure-iot-sdk-node/tree/master/provisioning/device);
* [Служба подготовки клиента SDK](https://github.com/Azure/azure-iot-sdk-node/tree/master/provisioning/service).

Устройство подготовки Azure и пакеты SDK службы для Python:

* [Устройство подготовки клиента SDK](https://github.com/Azure/azure-iot-sdk-python/blob/master/provisioning_device_client);
* [Служба подготовки клиента SDK](https://github.com/Azure/azure-iot-sdk-python/tree/master/provisioning_service_client).

Устройство подготовки Azure и пакеты SDK службы для C:

* [Устройство подготовки клиента SDK](https://github.com/Azure/azure-iot-sdk-c/blob/master/provisioning_client);
* [Служба подготовки клиента SDK](https://github.com/Azure/azure-iot-sdk-c/blob/master/provisioning_service_client).

## <a name="next-steps"></a>Дополнительная информация

Пакеты SDK для Azure IoT также предоставляют набор инструментов для разработки:
* [iothub-diagnostics](https://github.com/Azure/iothub-diagnostics) — средство кроссплатформенной командной строки для диагностирования проблем, связанных с подключением к Центру Интернета вещей;
* [device-explorer](https://github.com/Azure/azure-iot-sdk-csharp/tree/master/tools/DeviceExplorer) — классическое приложение Windows для подключения к Центру Интернета вещей.

Другие справочные статьи в руководстве разработчика для Центра Интернета вещей:

* [Конечные точки Центра Интернета вещей](iot-hub-devguide-endpoints.md)
* [Язык запросов Центра Интернета вещей для двойников устройств и двойников модулей, заданий и маршрутизации сообщений](iot-hub-devguide-query-language.md)
* [Квоты и регулирование](iot-hub-devguide-quotas-throttling.md)
* [Взаимодействие с Центром Интернета вещей с помощью протокола MQTT](iot-hub-mqtt-support.md)
* [Справочник по REST API Центра Интернета вещей](/rest/api/iothub/)
* [Поддержка платформ пакетов SDK для устройств Azure IoT](iot-hub-device-sdk-platform-support.md)