---
title: Библиотеки и пакеты SDK для Plug and Play IoT
description: Сведения о библиотеках устройств и служб, доступных для разработки решений IoT Plug and Play.
author: rido-min
ms.author: rmpablos
ms.date: 07/22/2020
ms.topic: reference
ms.service: iot-pnp
services: iot-pnp
ms.custom: mvc
ms.openlocfilehash: 6ea80c88f2c16614c8568bc6ccfa3f4308e954b0
ms.sourcegitcommit: a422b86148cba668c7332e15480c5995ad72fa76
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/30/2020
ms.locfileid: "91577311"
---
# <a name="microsoft-sdks-for-iot-plug-and-play"></a>Пакеты SDK для Microsoft Plug and Play IoT

Библиотеки Plug and Play IoT и пакеты SDK позволяют разработчикам создавать решения IoT с помощью различных языков программирования на различных платформах. В следующей таблице приведены ссылки на примеры и краткие руководства, которые помогут вам приступить к работе.

## <a name="device-sdks"></a>Пакеты SDK для устройств

| Язык | Пакет | Репозиторий кода | Примеры | Краткое руководство | Справочник |
|---|---|---|---|---|---|
| C-устройство | [vcpkg 1.3.9](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/setting_up_vcpkg.md) | [GitHub](https://github.com/Azure/azure-iot-sdk-c) | [Примеры](https://github.com/Azure/azure-iot-sdk-c/tree/master/iothub_client/samples/pnp) | [Подключение к Центру Интернета вещей](quickstart-connect-device-c.md) | [Ссылки](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/) |
| .NET — устройство | [1.31.0 NuGet](https://www.nuget.org/packages/Microsoft.Azure.Devices.Client) | [GitHub](https://github.com/Azure/azure-iot-sdk-csharp/tree/master/) | [Примеры](https://github.com/Azure-Samples/azure-iot-samples-csharp/tree/master/iot-hub/Samples/device/PnpDeviceSamples) | [Подключение к Центру Интернета вещей](quickstart-connect-device-csharp.md) | [Ссылки](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client?view=azure-dotnet&preserve-view=true) |
| Java — устройство | [Maven 1.25.0](https://mvnrepository.com/artifact/com.microsoft.azure.sdk.iot/iot-device-client) | [GitHub](https://github.com/Azure/azure-iot-sdk-java/tree/master/) | [Примеры](https://github.com/Azure/azure-iot-sdk-java/tree/master/device/iot-device-samples/pnp-device-sample) | [Подключение к Центру Интернета вещей](quickstart-connect-device-java.md) | [Ссылки](https://docs.microsoft.com/java/api/com.microsoft.azure.sdk.iot.device?view=azure-java-stable&preserve-view=true) |
| Python — устройство | [PIP 2.3.0](https://pypi.org/project/azure-iot-device/) | [GitHub](https://github.com/Azure/azure-iot-sdk-python/tree/master/) | [Примеры](https://github.com/Azure/azure-iot-sdk-python/tree/master/azure-iot-device/samples/pnp) | [Подключение к Центру Интернета вещей](quickstart-connect-device-python.md) | [Ссылки](https://docs.microsoft.com/python/api/azure-iot-device/azure.iot.device?view=azure-python&preserve-view=true) |
| Узел-устройство | [NPM 1.17.2](https://www.npmjs.com/package/azure-iot-device)  | [GitHub](https://github.com/Azure/azure-iot-sdk-node/tree/master/) | [Примеры](https://github.com/Azure/azure-iot-sdk-node/tree/master/device/samples/pnp) | [Подключение к Центру Интернета вещей](quickstart-connect-device-node.md) | [Ссылки](https://docs.microsoft.com/javascript/api/azure-iot-device/?view=azure-node-latest&preserve-view=true) |

## <a name="device-sdks-preview"></a>Пакеты SDK для устройств (Предварительная версия)

| Язык | Репозиторий кода/примеры |
|---|---|
|Пакет Azure SDK для Embedded| [GitHub](https://github.com/Azure/azure-sdk-for-c/#) |
|По промежуточного слоя Azure RTO IoT| [GitHub](https://github.com/azure-rtos/azure-iot-preview#) |
|Руководства по началу работы с Azure RTO | [GitHub](https://github.com/azure-rtos/getting-started) |

## <a name="service-sdks"></a>Пакеты SDK службы

| Язык | Пакет | Репозиторий кода | Примеры | Краткое руководство | Справочник |
|---|---|---|---|---|---|
| .NET — служба центра Интернета вещей | [1.31.0 NuGet](https://www.nuget.org/packages/Microsoft.Azure.Devices ) | [GitHub](https://github.com/Azure/azure-iot-sdk-csharp) | [Примеры](https://github.com/Azure-Samples/azure-iot-samples-csharp/tree/master/iot-hub/Samples/service/PnpServiceSamples) | Недоступно | [Ссылки](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices?view=azure-dotnet&preserve-view=true) |
| Java — служба центра Интернета вещей | [Maven](https://mvnrepository.com/artifact/com.microsoft.azure.sdk.iot/iot-service-client) | [GitHub](https://github.com/Azure/azure-iot-sdk-java) | [Примеры](https://github.com/Azure/azure-iot-sdk-java/tree/master/service/iot-service-samples/pnp-service-sample) | Недоступно | [Ссылки](https://docs.microsoft.com/java/api/com.microsoft.azure.sdk.iot.service?view=azure-java-stable&preserve-view=true) |
| Узел — Служба центра Интернета вещей | [NPM 1.13.0](https://www.npmjs.com/package/azure-iothub) | [GitHub](https://github.com/Azure/azure-iot-sdk-node) | [Примеры](https://github.com/Azure/azure-iot-sdk-node/tree/master/service/samples) | Недоступно | [Ссылки](https://docs.microsoft.com/javascript/api/azure-iothub/?view=azure-node-latest&preserve-view=true) |
| Python — Служба Digital двойников | [PIP 2.2.3](https://pypi.org/project/azure-iot-hub) | [GitHub](https://github.com/Azure/azure-iot-sdk-python) | [Примеры](https://github.com/Azure/azure-iot-sdk-python/tree/master/azure-iot-hub/samples) | [Взаимодействие с API Digital двойников для центра Интернета вещей](quickstart-service-python.md) | Недоступно |
| Узел — Служба Digital двойников | [NPM 1.13.0](https://www.npmjs.com/package/azure-iot-digitaltwins-service) | [GitHub](https://github.com/Azure/azure-iot-sdk-node) | [Примеры](https://github.com/Azure/azure-iot-sdk-node/tree/master/service/samples/javascript) | [Взаимодействие с API Digital двойников для центра Интернета вещей](quickstart-service-node.md) | Недоступно |

## <a name="next-steps"></a>Дальнейшие действия

Чтобы испытать пакеты SDK и библиотеки, ознакомьтесь с  [руководством для разработчиков](concepts-developer-guide-device-csharp.md) и [краткими](quickstart-connect-device-c.md) руководствами и [краткими](quickstart-service-node.md)руководствами по службам.
