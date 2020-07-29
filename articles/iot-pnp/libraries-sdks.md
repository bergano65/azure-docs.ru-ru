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
ms.openlocfilehash: 3331a0a9a8d3fb5d028d801d334daf2dbfa25235
ms.sourcegitcommit: 46f8457ccb224eb000799ec81ed5b3ea93a6f06f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/28/2020
ms.locfileid: "87337336"
---
# <a name="iot-plug-and-play-libraries-and-sdks"></a>Библиотеки и пакеты SDK для Plug and Play IoT

Библиотеки Plug and Play IoT и пакеты SDK позволяют разработчикам создавать решения IoT с помощью различных языков программирования на различных платформах. В следующей таблице приведены ссылки на примеры и краткие руководства, которые помогут вам приступить к работе.

## <a name="microsoft-sdks-for-iot-plug-and-play"></a>Пакеты SDK для Microsoft Plug and Play IoT

**Пакеты SDK для устройств**

| Язык | Пакет | Репозиторий кода | Примеры | Краткое руководство | Справочник |
|---|---|---|---|---|---|
| C-устройство | [vcpkg 1.3.9](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/setting_up_vcpkg.md) | [GitHub](https://github.com/Azure/azure-iot-sdk-c/releases/tag/2020-07-19) | [Примеры](https://github.com/Azure/azure-iot-sdk-c/tree/2020-07-19/iothub_client/samples/pnp) | [Подключение к Центру Интернета вещей](quickstart-connect-device-c.md) | [Ссылка](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/) |
| .NET — устройство | [1.27.0 NuGet](https://www.nuget.org/packages/Microsoft.Azure.Devices.Client/1.27.0) | [GitHub](https://github.com/Azure/azure-iot-sdk-csharp/tree/master/) | [Примеры](https://github.com/Azure/azure-iot-sdk-csharp/tree/master/iothub/device/samples/PnpDeviceSamples) | [Подключение к Центру Интернета вещей](quickstart-connect-device-csharp.md) | [Ссылка](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client?view=azure-dotnet) |
| Java — устройство | [Maven 1.24.0](https://mvnrepository.com/artifact/com.microsoft.azure.sdk.iot/iot-device-client/1.24.0) | [GitHub](https://github.com/Azure/azure-iot-sdk-java/tree/master/) | [Примеры](https://github.com/Azure/azure-iot-sdk-java/tree/master/device/iot-device-samples/pnp-device-sample) | [Подключение к Центру Интернета вещей](quickstart-connect-device-java.md) | [Ссылка](https://docs.microsoft.com/java/api/com.microsoft.azure.sdk.iot.device?view=azure-java-stable) |
| Python — устройство | [PIP 2.1.4](https://pypi.org/project/azure-iot-device/) | [GitHub](https://github.com/Azure/azure-iot-sdk-python/tree/master/) | [Примеры](https://github.com/Azure/azure-iot-sdk-python/tree/master/azure-iot-device/samples/pnp) | [Подключение к Центру Интернета вещей](quickstart-connect-device-python.md) | [Ссылка](https://docs.microsoft.com/python/api/azure-iot-device/azure.iot.device?view=azure-python) |
| Узел-устройство | [NPM 1.17.0](https://www.npmjs.com/package/azure-iot-device)  | [GitHub](https://github.com/Azure/azure-iot-sdk-node/tree/master/) | [Примеры](https://github.com/Azure/azure-iot-sdk-node/tree/master/device/samples/pnp) | [Подключение к Центру Интернета вещей](quickstart-connect-device-node.md) | [Ссылка](https://docs.microsoft.com/javascript/api/azure-iot-device/?view=azure-node-latest) |

**Пакеты SDK для службы (Предварительная версия)**

| Язык | Пакет | Репозиторий кода | Примеры | Краткое руководство | Справочник |
|---|---|---|---|---|---|
| .NET — Предварительная версия службы центра Интернета вещей | [NuGet 1.27.1-Preview-002](https://www.nuget.org/packages/Microsoft.Azure.Devices/1.27.1-preview-002 ) | [GitHub](https://github.com/Azure/azure-iot-sdk-csharp/tree/pnp-preview-refresh) | [Примеры](https://github.com/Azure/azure-iot-sdk-csharp/tree/pnp-preview-refresh/iothub/service/samples/PnpServiceSamples) | Нет | [Ссылка](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices?view=azure-dotnet) |
| Java — Предварительная версия службы центра Интернета вещей | [Maven 1.1.0](https://mvnrepository.com/artifact/com.microsoft.azure.sdk.iot/iot-service-client-preview/1.1.0) | [GitHub](https://github.com/Azure/azure-iot-sdk-java/tree/pnp-preview-refresh) | [Примеры](https://github.com/Azure/azure-iot-sdk-java/tree/pnp-preview-refresh/service/iot-service-samples/pnp-service-sample) | Нет | [Ссылка](https://docs.microsoft.com/java/api/com.microsoft.azure.sdk.iot.service?view=azure-java-stable) |
| Python — центр Интернета вещей или служба Digital двойников Service Preview | [PIP 2.2.1 RC1](https://pypi.org/project/azure-iot-hub/2.2.1rc1/) | [GitHub](https://github.com/Azure/azure-iot-sdk-python/tree/pnp-preview-refresh) | [Примеры](https://github.com/Azure/azure-iot-sdk-python/tree/pnp-preview-refresh/azure-iot-hub/samples) | [Взаимодействие с API Digital двойников для центра Интернета вещей](quickstart-service-python.md) | [Ссылка](https://docs.microsoft.com/python/api/azure-iot-hub/?view=azure-python) |
| Узел — центр Интернета вещей или служба Digital двойников Service Preview | [NPM 1.0.0-PnP-Refresh. 3](https://www.npmjs.com/package/azure-iot-digitaltwins-service/v/1.0.0-pnp-refresh.3) | [GitHub](https://github.com/Azure/azure-iot-sdk-node/tree/pnp-preview-refresh/) | [Примеры](https://github.com/Azure/azure-iot-sdk-node/tree/pnp-preview-refresh/digitaltwins/samples/service/javascript) | [Взаимодействие с API Digital двойников для центра Интернета вещей](quickstart-service-node.md) | [Ссылка](https://docs.microsoft.com/javascript/api/azure-iothub/?view=azure-node-latest) |

## <a name="next-steps"></a>Дальнейшие действия

Чтобы испытать пакеты SDK и библиотеки, ознакомьтесь с [руководством для разработчиков](concepts-developer-guide.md) и [краткими](quickstart-connect-device-c.md) руководствами и [краткими](quickstart-service-node.md)руководствами по службам.
