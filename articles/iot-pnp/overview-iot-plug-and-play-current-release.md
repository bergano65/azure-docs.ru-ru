---
title: Текущий выпуск IoT Plug and Play | Документация Майкрософт
description: Узнайте, что входит в текущий выпуск IoT Plug and Play.
author: dominicbetts
ms.author: dobett
ms.date: 09/19/2020
ms.topic: overview
ms.custom: mvc
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: 4cfc9ca14a4b344423a27d9683114de5275fb20b
ms.sourcegitcommit: a422b86148cba668c7332e15480c5995ad72fa76
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/30/2020
ms.locfileid: "91580793"
---
# <a name="whats-in-the-current-iot-plug-and-play-release"></a>Чем является текущий выпуск IoT Plug and Play

В этой статье перечислены средства, пакеты SDK и API, поддерживающие текущий выпуск IoT Plug and Play. Отображаемые номера версий соответствуют номеру версии на момент выпуска общедоступной версии IoT Plug and Play. Номера версий могут увеличиваться по мере новых выпусков.

## <a name="modeling-language"></a>Язык моделирования

[Язык определения цифровых двойников (DTDL) версии 2](https://github.com/Azure/opendigitaltwins-dtdl)

Дополнительные сведения о том, как устройства IoT Plug and Play работают с DTDL, см. в статье [Соглашения IoT Plug and Play](concepts-convention.md).

## <a name="tools-and-utilities"></a>Средства и служебные программы

- Обозреватель Интернета вещей Azure версии 0.12.0.

    Дополнительные сведения см. в статье [Установка и использование обозревателя Интернета вещей Azure](howto-use-iot-explorer.md).

- Расширение VS Code версии 1.0.0.

    Дополнительные сведения см. в статье [Установка и использование средств разработки DTDL](howto-use-dtdl-authoring-tools.md).

- Расширение Visual Studio 2019 версии 1.0.0.

    Дополнительные сведения см. в статье [Установка и использование средств разработки DTDL](howto-use-dtdl-authoring-tools.md).

- Расширение Интернета вещей для Azure CLI версии 0.10.0.

    Дополнительные сведения см. в статье [Установка и использование расширения Интернета вещей Azure для Azure CLI](howto-use-iot-pnp-cli.md).

    > [!TIP]
    > Расширение Интернета вещей Azure содержит команды, помогающие сертифицировать устройства. См. раздел `az iot product -h`.



## <a name="libraries-and-sdks"></a>Библиотеки и пакеты SDK

Дополнительные сведения о библиотеках и пакетах SDK см. в статье [Пакеты SDK Microsoft для IoT Plug and Play](libraries-sdks.md).

- Пакет SDK для устройства C [VcPkg 2020-07-19](https://github.com/microsoft/vcpkg/tree/master/ports/azure-iot-sdk-c)
- Пакет SDK для устройств .NET [NuGet Microsoft.Azure.Devices.Client версии 1.30.0](https://www.nuget.org/packages/Microsoft.Azure.Devices.Client/)
- Пакет SDK для устройств Java [Maven IOT-Device-Client версии 1.25.0](https://search.maven.org/artifact/com.microsoft.azure.sdk.iot/iot-device-client)
- Пакет SDK для устройств Python [Pip azure-iot-device версии 2.2.0](https://pypi.org/project/azure-iot-device/)
- Пакет SDK для устройств Node.js [npm azure-iot-device версии 1.17.1](https://www.npmjs.com/package/azure-iot-device)
- .NET — служба Центра Интернета вещей [NuGet Microsoft.Azure.Devices версии 1.22.0](https://www.nuget.org/packages/Microsoft.Azure.Devices/)
- Java — служба Центра Интернета вещей [Maven iot-service-client версии 1.25.0](https://search.maven.org/artifact/com.microsoft.azure.sdk.iot/iot-service-client)
- Node.js — служба Центра Интернета вещей [npm azure-iothub версии 1.12.5](https://www.npmjs.com/package/azure-iothub)
- Python — служба Центра Интернета вещей или служба Digital Twins [Pip azure-iot-hub версии 2.2.2](https://pypi.org/project/azure-iot-hub/)
- Средство синтаксического анализа DTDL [NuGet](https://www.nuget.org/packages/Microsoft.Azure.DigitalTwins.Parser).

### <a name="preview"></a>Предварительный просмотр

- Пакет SDK Azure для Embedded [1.0.0-preview.5](https://github.com/Azure/azure-sdk-for-c/releases/tag/1.0.0-preview.5)
- ПО промежуточного слоя центра Интернета вещей для ОСРВ Azure [v6.0_beta3](https://github.com/azure-rtos/azure-iot-preview/releases/tag/v6.0_beta3)

## <a name="rest-apis"></a>Интерфейсы REST API

REST API [30.09.2020](https://docs.microsoft.com/rest/api/iothub).

Дополнительные сведения см. в статье [Руководство для разработчиков IoT Plug and Play](concepts-developer-guide-service.md).

## <a name="iot-hub"></a>Центр Интернета вещей

Центр Интернета вещей поддерживает IoT Plug and Play во всех регионах. IoT Plug and Play поддерживают только центры Интернета вещей уровня "Стандартный" или "Бесплатный".

## <a name="announcements"></a>Объявления

Сведения о текущих и предыдущих объявлениях IoT Plug and Play см. в следующих записях блога:

- [Обновление общедоступной предварительной версии (опубликовано 29 августа 2020 г.)](https://techcommunity.microsoft.com/t5/internet-of-things/add-quot-plug-and-play-quot-to-your-iot-solutions/ba-p/1548531)
- [Подготовка и сертификация устройств для IoT Plug and Play (опубликовано 26 августа 2020 г.)](https://azure.microsoft.com/blog/prepare-and-certify-your-devices-for-iot-plug-and-play/)
- [Теперь IoT Plug and Play доступен в предварительной версии (опубликовано 22 августа 2019 г.)](https://azure.microsoft.com/blog/iot-plug-and-play-is-now-available-in-preview/)
- [Сборка с помощью Azure IoT Central и IoT Plug and Play (опубликовано 7 мая 2019 г.)](https://azure.microsoft.com/blog/build-with-azure-iot-central-and-iot-plug-and-play/)

