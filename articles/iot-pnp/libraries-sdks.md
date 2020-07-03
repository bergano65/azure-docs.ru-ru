---
title: Библиотеки и пакеты SDK для Plug and Play IoT
description: Сведения о библиотеках устройств и служб, доступных для разработки решений IoT Plug and Play.
author: dominicbetts
ms.author: dobett
ms.date: 01/08/2020
ms.topic: reference
ms.service: iot-pnp
services: iot-pnp
ms.custom: mvc
ms.openlocfilehash: 394edc24cfeefb96cb2f412985cb990a2306bb09
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2020
ms.locfileid: "80064330"
---
# <a name="iot-plug-and-play-libraries-and-sdks"></a>Библиотеки и пакеты SDK для Plug and Play IoT

Библиотеки Plug and Play IoT и пакеты SDK позволяют разработчикам создавать решения IoT с помощью различных языков программирования на различных платформах. В следующей таблице приведены ссылки на примеры и краткие руководства, которые помогут вам приступить к работе.

## <a name="microsoft-supported-libraries-and-sdks"></a>Библиотеки и пакеты SDK, поддерживаемые корпорацией Майкрософт

| Платформа | Библиотека или пакет | Исходный код | Пример | Краткое руководство | Справочник |
| -------- | ------- | ----------- | ------ | ---------- | --------- |
| C/Linux  | [Пакет SDK для устройств на APT-GET](https://github.com/Azure/azure-iot-sdk-c/blob/public-preview/iothub_client/readme.md#aptgetpackage) | [GitHub](https://github.com/Azure/azure-iot-sdk-c/tree/public-preview) | [Примеры клиента Digital двойника](https://github.com/Azure/azure-iot-sdk-c/tree/public-preview/digitaltwin_client/samples) | [Подключение к Центру Интернета вещей](./quickstart-connect-pnp-device-c-linux.md) | [Справочные материалы](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/) |
| C/Windows  | [Пакет SDK для устройств на Vcpkg](https://github.com/Azure/azure-iot-sdk-c/blob/public-preview/doc/setting_up_vcpkg.md#setup-c-sdk-vcpkg-for-windows-development-environment) | [GitHub](https://github.com/Azure/azure-iot-sdk-c/tree/public-preview) | [Примеры клиента Digital двойника](https://github.com/Azure/azure-iot-sdk-c/tree/public-preview/digitaltwin_client/samples) | [Подключение к Центру Интернета вещей](./quickstart-connect-pnp-device-c-windows.md) | [Справочные материалы](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/) |
| C/mbed  | [Пакет SDK для устройств при ВНЕДРЕНии](https://github.com/Azure/azure-iot-sdk-c/blob/public-preview/iothub_client/readme.md#mbed) | [GitHub](https://github.com/Azure/azure-iot-sdk-c/tree/public-preview) | [Примеры клиента Digital двойника](https://github.com/Azure/azure-iot-sdk-c/tree/public-preview/digitaltwin_client/samples) |  | [Справочные материалы](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/) |
| C/Arduino  | [Пакет SDK для устройств в интегрированной среде разработки Arduino](https://github.com/Azure/azure-iot-sdk-c/blob/public-preview/iothub_client/readme.md#arduino) | [GitHub](https://github.com/Azure/azure-iot-sdk-c/tree/public-preview) | [Примеры клиента Digital двойника](https://github.com/Azure/azure-iot-sdk-c/tree/public-preview/digitaltwin_client/samples) |  | [Справочные материалы](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/) |
| C/iOS  | [Пакет SDK для устройств на Кокоапод](https://cocoapods.org/pods/AzureIoTHubClient) | [GitHub](https://github.com/Azure/azure-iot-sdk-c/tree/public-preview) | [Примеры клиента Digital двойника](https://github.com/Azure/azure-iot-sdk-c/tree/public-preview/digitaltwin_client/samples) |  | [Справочные материалы](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/) |
| C#    | [NuGet](https://www.nuget.org/packages/Microsoft.Azure.Devices.DigitalTwin.Client) | [GitHub](https://github.com/Azure/azure-iot-sdk-csharp) | [Примеры цифровых двойника](https://github.com/Azure-Samples/azure-iot-samples-csharp/tree/master/digitaltwin/Samples) | [Подключение к Центру Интернета вещей](./quickstart-connect-pnp-device-csharp.md) | [Справочные материалы](https://docs.microsoft.com/dotnet/api/overview/azure/iot/client?view=azure-dotnet) |
| Java   | [Maven](https://mvnrepository.com/artifact/com.microsoft.azure.sdk.iot/digital-twin-device-client-preview/1.0.0) | [GitHub](https://github.com/Azure/azure-iot-sdk-java) | [Примеры цифровых двойника](https://github.com/Azure-Samples/azure-iot-samples-java/tree/master/digital-twin/Samples) | [Подключение к Центру Интернета вещей](./quickstart-connect-pnp-device-java.md) | [Справочные материалы](https://docs.microsoft.com/java/api/com.microsoft.azure.sdk.iot.device) |
| Node.js | [npm](https://www.npmjs.com/package/azure-iot-digitaltwins-device) | [GitHub](https://github.com/Azure/azure-iot-sdk-node/tree/digitaltwins-preview) | [Примеры цифровых двойника](https://github.com/Azure/azure-iot-sdk-node/tree/digitaltwins-preview/digitaltwins/samples) | [Подключение к Центру Интернета вещей](./quickstart-connect-pnp-device-node.md) | [Справочные материалы](https://docs.microsoft.com/javascript/api/azure-iot-device/) |

## <a name="iot-hub-support"></a>Поддержка Центра Интернета вещей

Возможности устройств IoT Plug and Play поддерживаются только [центрами Интернета вещей уровня "бесплатный" и "Стандартный](../iot-hub/iot-hub-scaling.md)".

## <a name="next-steps"></a>Дальнейшие шаги

Помимо пакетов SDK и библиотек для устройств, для взаимодействия с репозиториями модели можно использовать интерфейсы API.