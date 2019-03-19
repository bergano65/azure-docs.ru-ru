---
title: Общие сведения о пакетах SDK для Azure IoT | Документация Майкрософт
description: Руководство разработчика. Сведения и ссылки на различные пакеты SDK для устройств и служб Azure IoT, которые можно использовать для создания приложений для устройств и внутренних приложений.
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 09/14/2018
ms.openlocfilehash: fcb6f564c97632e4d62f7f052ec5ecc35a9e479a
ms.sourcegitcommit: 15e9613e9e32288e174241efdb365fa0b12ec2ac
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/28/2019
ms.locfileid: "57011251"
---
# <a name="understand-and-use-azure-iot-hub-sdks"></a>Понимание и использование пакетов SDK для Центра Интернета вещей Azure

Есть две категории пакетов средств разработки (SDK) для работы с Центром Интернета вещей:

* **Пакеты SDK для устройств Центра Интернета вещей** позволяют создавать приложения, работающие на ваших устройствах Интернета вещей, используя клиент устройства или клиент модуля. Эти приложения отправляют данные телеметрии в центр Интернета вещей и при необходимости получают из него сообщения, задания, методы или обновления двойников.  Вы также можете использовать клиент модуля для создания [модулей](../iot-edge/iot-edge-modules.md) для [среды выполнения Azure IoT Edge](../iot-edge/about-iot-edge.md).

* **Пакеты SDK для служб Центра Интернета вещей** дают возможность создавать серверные приложения, чтобы управлять Центром Интернета вещей, а также отправлять сообщения, планировать задания, вызывать прямые методы и отправлять обновления необходимых свойств на устройствах Интернета вещей или модулях.

Кроме того, мы также предоставляем набор пакетов SDK для работы с [Службой подготовки устройств](../iot-dps/about-iot-dps.md).
* **Пакеты SDK для устройств подготовки к работе** позволяют создавать приложения, которые запускаются на устройствах Интернета вещей, для связи со службой подготовки устройств.

* **Пакеты SDK для службы подготовки к работе** дают возможность создавать серверные приложения для управления вашей регистрации в Службе подготовки устройств.

Узнайте о [преимуществах разработки с использованием пакетов SDK для Azure IoT](https://azure.microsoft.com/blog/benefits-of-using-the-azure-iot-sdks-in-your-azure-iot-solution/).

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]


### <a name="os-platform-and-hardware-compatibility"></a>Платформа ОС и совместимость оборудования

Поддерживаемые платформы для пакетов SDK можно найти в [Поддержка платформ пакетов SDK для устройств Azure IoT](iot-hub-device-sdk-platform-support.md).

Дополнительные сведения о совместимости пакета SDK с определенными устройствами см. в [каталоге устройств, сертифицированных по программе Microsoft Azure Certified for IoT](https://catalog.azureiotsolutions.com/), или в описании для конкретного репозитория.

## <a name="azure-iot-hub-device-sdks"></a>Пакеты SDK для устройств Центра Интернета вещей Azure

Пакеты SDK для устройств Центра Интернета вещей Microsoft Azure содержат код, упрощающий построение приложений, которые подключаются к службам Центра Интернета вещей и управляются с помощью этих служб.

Пакет SDK для устройств Azure IoT для .NET: 

* Скачивание из [Nuget](https://www.nuget.org/packages/Microsoft.Azure.Devices.Client/).  Пространство имен — это Microsoft.Azure.Devices.Clients, который содержит клиенты устройств Центра Интернета вещей (DeviceClient, ModuleClient).
* [Исходный код](https://github.com/Azure/azure-iot-sdk-csharp).
* [Справочник по API](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices?view=azure-dotnet)
* [Справочник по модулям](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.moduleclient?view=azure-dotnet).

Пакет SDK для устройств Центра Интернета вещей Azure для C (ANSI C-C99):

* Установка из [apt-get, MBED, Arduino IDE или iOS](https://github.com/Azure/azure-iot-sdk-c/blob/master/readme.md#packages-and-libraries)
* [Исходный код](https://github.com/Azure/azure-iot-sdk-c).
* [Компиляция пакета SDK для устройств C](https://github.com/Azure/azure-iot-sdk-c/blob/master/iothub_client/readme.md#compiling-the-c-device-sdk).
* [Справочник по API](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/)
* [Справочник по модулям](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/iothub-module-client-h).
* [Перенос пакета SDK C для других платформ](https://github.com/Azure/azure-c-shared-utility/blob/master/devdoc/porting_guide.md)
* [Документация для разработчиков](https://github.com/Azure/azure-iot-sdk-c/tree/master/doc) содержит сведения о кроссплатформенной компиляции, начале работы на различных платформах и т. д.

Пакет SDK для устройств Azure IoT для Java: 

* добавление в проект [Maven](https://github.com/Azure/azure-iot-sdk-java/blob/master/doc/java-devbox-setup.md#for-the-device-sdk);
* [Исходный код](https://github.com/Azure/azure-iot-sdk-java).
* [Справочник по API](https://docs.microsoft.com/java/api/com.microsoft.azure.sdk.iot.device)
* [Справочник по модулям](https://docs.microsoft.com/java/api/com.microsoft.azure.sdk.iot.device.moduleclient?view=azure-java-stable).

Пакет SDK для устройств Azure IoT для Node.js: 

* установка из [npm](https://www.npmjs.com/package/azure-iot-device);
* [Исходный код](https://github.com/Azure/azure-iot-sdk-node).
* [Справочник по API](https://docs.microsoft.com/javascript/api/azure-iot-device/?view=azure-iot-typescript-latest)
* [Справочник по модулям](https://docs.microsoft.com/javascript/api/azure-iot-device/moduleclient?view=azure-node-latest).

Пакет SDK для устройств Azure IoT для Python: 

* установка из [pip](https://pypi.python.org/pypi/azure-iothub-device-client/);
* [Исходный код](https://github.com/Azure/azure-iot-sdk-python).
* Справочник по API: см. [Справочная документация по API для C](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/).

Пакет SDK для устройств Центра Интернета вещей для iOS: 

* установка из [CocoaPod](https://cocoapods.org/pods/AzureIoTHubClient);
* [Примеры](https://github.com/Azure-Samples/azure-iot-samples-ios)
* Справочник по API: см. [Справочная документация по API для C](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/).

## <a name="azure-iot-hub-service-sdks"></a>Пакеты SDK для службы Центра Интернета вещей Azure

Пакеты SDK для службы Интернета вещей Azure содержат код, который облегчает создание приложений, взаимодействующих непосредственно с Центром Интернета вещей, для управления устройствами и безопасностью.

Пакет SDK для службы Центра Интернета вещей для .NET:

* Скачивание из [Nuget](https://www.nuget.org/packages/Microsoft.Azure.Devices/).  Пространство имен — это Microsoft.Azure.Devices, который содержит клиенты службы центра Интернета вещей (RegistryManager, ServiceClients).
* [Исходный код](https://github.com/Azure/azure-iot-sdk-csharp).
* [Справочник по API](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices)

Пакет SDK для службы Центра Интернета вещей для Java: 

* добавление в проект [Maven](https://github.com/Azure/azure-iot-sdk-java/blob/master/doc/java-devbox-setup.md#for-the-service-sdk);
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

## <a name="microsoft-azure-provisioning-sdks"></a>Пакеты SDK для подготовки Microsoft Azure

**Пакеты SDK для подготовки Microsoft Azure** позволяют вам предоставлять устройства в Центр Интернета вещей с помощью [Службы подготовки устройств](../iot-dps/about-iot-dps.md).

Устройство подготовки Azure и пакеты SDK службы для C#:

* Скачивание из [пакета SDK для устройств](https://www.nuget.org/packages/Microsoft.Azure.Devices.Provisioning.Client/) и [пакета SDK службы](https://www.nuget.org/packages/Microsoft.Azure.Devices.Provisioning.Service/) из NuGet.
* [Исходный код](https://github.com/Azure/azure-iot-sdk-csharp/).
* [Справочник по API](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.provisioning.client?view=azure-dotnet)

Устройство подготовки Azure и пакеты SDK службы для C:

* Установка из [apt-get, MBED, Arduino IDE или iOS](https://github.com/Azure/azure-iot-sdk-c/blob/master/readme.md#packages-and-libraries)
* [Исходный код](https://github.com/Azure/azure-iot-sdk-c/blob/master/provisioning_client).
* [Справочник по API](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/)

Устройство подготовки Azure и пакеты SDK службы для Java:

* добавление в проект [Maven](https://github.com/Azure/azure-iot-sdk-java/blob/master/doc/java-devbox-setup.md#for-the-service-sdk);
* [Исходный код](https://github.com/Azure/azure-iot-sdk-java/blob/master/provisioning).
* [Справочник по API](https://docs.microsoft.com/java/api/com.microsoft.azure.sdk.iot.provisioning.device?view=azure-java-stable)

Устройство подготовки Azure и пакеты SDK службы для Node.js:

* [Исходный код](https://github.com/Azure/azure-iot-sdk-node/tree/master/provisioning).
* [Справочник по API](https://docs.microsoft.com/javascript/api/overview/azure/iothubdeviceprovisioning?view=azure-node-latest)
* Скачивание [пакета SDK для устройств](https://badge.fury.io/js/azure-iot-provisioning-device) и [пакета SDK службы](https://badge.fury.io/js/azure-iot-provisioning-service) из npm

Устройство подготовки Azure и пакеты SDK службы для Python:

* [Исходный код](https://github.com/Azure/azure-iot-sdk-python).
* Скачивание [пакета SDK для устройств](https://pypi.org/project/azure-iot-provisioning-device-client/) и [пакета SDK службы](https://pypi.org/project/azure-iothub-provisioningserviceclient/) из pip

## <a name="next-steps"></a>Дальнейшие действия

Пакеты SDK для Azure IoT также предоставляют набор инструментов для разработки:
* [iothub-diagnostics](https://github.com/Azure/iothub-diagnostics) — средство кроссплатформенной командной строки для диагностирования проблем, связанных с подключением к Центру Интернета вещей;
* [device-explorer](https://github.com/Azure/azure-iot-sdk-csharp/tree/master/tools/DeviceExplorer) — классическое приложение Windows для подключения к Центру Интернета вещей.

Соответствующие документы, относящиеся к разработке с помощью пакетов SDK для Azure IoT.
* Дополнительные сведения о том, как управлять подключениями и надежным обменом сообщениями с помощью пакетов SDK центра Интернета вещей, см. в [этой статье](iot-hub-reliability-features-in-sdks.md).
* Дополнительные сведения см. в статье [Разработка для мобильных устройств с помощью пакетов SDK для Центра Интернета вещей Azure](iot-hub-how-to-develop-for-mobile-devices.md).
* [Поддержка платформ пакетов SDK для устройств Azure IoT](iot-hub-device-sdk-platform-support.md)


Другие справочные статьи в руководстве разработчика для Центра Интернета вещей:

* [Конечные точки Центра Интернета вещей](iot-hub-devguide-endpoints.md)
* [Язык запросов Центра Интернета вещей для двойников устройств и двойников модулей, заданий и маршрутизации сообщений](iot-hub-devguide-query-language.md)
* [Квоты и регулирование](iot-hub-devguide-quotas-throttling.md)
* [Взаимодействие с Центром Интернета вещей с помощью протокола MQTT](iot-hub-mqtt-support.md)
* [Справочник по REST API Центра Интернета вещей](/rest/api/iothub/)
