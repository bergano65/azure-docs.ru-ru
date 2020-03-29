---
title: Библиотеки и SDK Для воспроизведения IoT
description: Информация об устройствах и библиотеках услуг, доступных для разработки решений с поддержкой IoT Plug and Play.
author: dominicbetts
ms.author: dobett
ms.date: 01/08/2020
ms.topic: reference
ms.service: iot-pnp
services: iot-pnp
ms.custom: mvc
ms.openlocfilehash: 394edc24cfeefb96cb2f412985cb990a2306bb09
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80064330"
---
# <a name="iot-plug-and-play-libraries-and-sdks"></a>Библиотеки и SDK Для воспроизведения IoT

Библиотеки IoT Plug and Play и SDK позволяют разработчикам создавать решения IoT с использованием различных языков программирования на нескольких платформах. В следующей таблице приведены ссылки на образцы и быстрые запуски, которые помогут вам начать работу:

## <a name="microsoft-supported-libraries-and-sdks"></a>Библиотеки и SDK, поддерживаемые корпорацией Майкрософт

| Платформа | Библиотека/Пакет | Исходный код | Пример | Краткое руководство | Справочник |
| -------- | ------- | ----------- | ------ | ---------- | --------- |
| C/Linux  | [Устройство SDK на apt-get](https://github.com/Azure/azure-iot-sdk-c/blob/public-preview/iothub_client/readme.md#aptgetpackage) | [GitHub](https://github.com/Azure/azure-iot-sdk-c/tree/public-preview) | [Образцы клиентов Digital Twin](https://github.com/Azure/azure-iot-sdk-c/tree/public-preview/digitaltwin_client/samples) | [Подключение к Центру Интернета вещей](./quickstart-connect-pnp-device-c-linux.md) | [Справочник](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/) |
| C/Windows  | [Устройство SDK на Vcpkg](https://github.com/Azure/azure-iot-sdk-c/blob/public-preview/doc/setting_up_vcpkg.md#setup-c-sdk-vcpkg-for-windows-development-environment) | [GitHub](https://github.com/Azure/azure-iot-sdk-c/tree/public-preview) | [Образцы клиентов Digital Twin](https://github.com/Azure/azure-iot-sdk-c/tree/public-preview/digitaltwin_client/samples) | [Подключение к Центру Интернета вещей](./quickstart-connect-pnp-device-c-windows.md) | [Справочник](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/) |
| C/mbed  | [Устройство SDK на EMBED](https://github.com/Azure/azure-iot-sdk-c/blob/public-preview/iothub_client/readme.md#mbed) | [GitHub](https://github.com/Azure/azure-iot-sdk-c/tree/public-preview) | [Образцы клиентов Digital Twin](https://github.com/Azure/azure-iot-sdk-c/tree/public-preview/digitaltwin_client/samples) |  | [Справочник](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/) |
| C/Ардуино  | [Устройство SDK в Arduino IDE](https://github.com/Azure/azure-iot-sdk-c/blob/public-preview/iothub_client/readme.md#arduino) | [GitHub](https://github.com/Azure/azure-iot-sdk-c/tree/public-preview) | [Образцы клиентов Digital Twin](https://github.com/Azure/azure-iot-sdk-c/tree/public-preview/digitaltwin_client/samples) |  | [Справочник](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/) |
| C/iOS  | [Устройство SDK на какао-поду](https://cocoapods.org/pods/AzureIoTHubClient) | [GitHub](https://github.com/Azure/azure-iot-sdk-c/tree/public-preview) | [Образцы клиентов Digital Twin](https://github.com/Azure/azure-iot-sdk-c/tree/public-preview/digitaltwin_client/samples) |  | [Справочник](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/) |
| C#    | [NuGet](https://www.nuget.org/packages/Microsoft.Azure.Devices.DigitalTwin.Client) | [GitHub](https://github.com/Azure/azure-iot-sdk-csharp) | [Образцы цифровых близнецов](https://github.com/Azure-Samples/azure-iot-samples-csharp/tree/master/digitaltwin/Samples) | [Подключение к Центру Интернета вещей](./quickstart-connect-pnp-device-csharp.md) | [Справочник](https://docs.microsoft.com/dotnet/api/overview/azure/iot/client?view=azure-dotnet) |
| Java   | [Maven](https://mvnrepository.com/artifact/com.microsoft.azure.sdk.iot/digital-twin-device-client-preview/1.0.0) | [GitHub](https://github.com/Azure/azure-iot-sdk-java) | [Образцы цифровых близнецов](https://github.com/Azure-Samples/azure-iot-samples-java/tree/master/digital-twin/Samples) | [Подключение к Центру Интернета вещей](./quickstart-connect-pnp-device-java.md) | [Справочник](https://docs.microsoft.com/java/api/com.microsoft.azure.sdk.iot.device) |
| Node.js | [NPM](https://www.npmjs.com/package/azure-iot-digitaltwins-device) | [GitHub](https://github.com/Azure/azure-iot-sdk-node/tree/digitaltwins-preview) | [Образцы цифровых близнецов](https://github.com/Azure/azure-iot-sdk-node/tree/digitaltwins-preview/digitaltwins/samples) | [Подключение к Центру Интернета вещей](./quickstart-connect-pnp-device-node.md) | [Справочник](https://docs.microsoft.com/javascript/api/azure-iot-device/) |

## <a name="iot-hub-support"></a>Поддержка Центра Интернета вещей

Возможности устройства IoT Plug и Play поддерживаются только [бесплатными и стандартными концентраторами IoT.](../iot-hub/iot-hub-scaling.md)

## <a name="next-steps"></a>Дальнейшие действия

В дополнение к SD-файлам и библиотекам устройства можно использовать REST AIS для взаимодействия с репозиториями моделей.