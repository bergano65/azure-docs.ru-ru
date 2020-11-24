---
title: Руководством для разработчиков устройств (C) — Plug and Play IoT | Документация Майкрософт
description: Описание Plug and Play IoT для разработчиков устройств C
author: rido-min
ms.author: rmpablos
ms.date: 11/19/2020
ms.topic: conceptual
ms.service: iot-pnp
services: iot-pnp
zone_pivot_groups: programming-languages-set-twenty-six
ms.openlocfilehash: 8d8da39f038f465030a2dced092ab1b008e30e5e
ms.sourcegitcommit: b8eba4e733ace4eb6d33cc2c59456f550218b234
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/23/2020
ms.locfileid: "95511514"
---
# <a name="iot-plug-and-play-device-developer-guide"></a>Справочное руководством для разработчиков IoT Plug and Play

Plug and Play IoT позволяет создавать интеллектуальные устройства, которые объявляют свои возможности для приложений Интернета вещей Azure. Устройства IoT Plug and Play не нуждаются в ручной настройке, когда клиент подключает их к приложениям с поддержкой Интернета вещей Plug and Play.

Смарт-устройство может быть реализовано напрямую, использовать [модули](../iot-hub/iot-hub-devguide-module-twins.md)или использовать [модули IOT Edge](../iot-edge/about-iot-edge.md).

В этом руководстве описываются основные шаги, необходимые для создания устройства, модуля или IoT Edge модуля, который соответствует [соглашениям Plug and Play IOT](../iot-pnp/concepts-convention.md).

Чтобы создать модуль Plug and Play для устройства, модуля или IoT Edge IoT, выполните следующие действия.

1. Убедитесь, что устройство использует протокол MQTT или MQTT через WebSocket для подключения к центру Интернета вещей Azure.
1. Создайте модель [дтдл (Digital двойников Definition Language)](https://github.com/Azure/opendigitaltwins-dtdl) для описания устройства. Дополнительные сведения см. в разделе [Знакомство с компонентами в Plug and Playных моделях Интернета вещей](concepts-components.md).
1. Обновите устройство или модуль, чтобы объявить `model-id` об этом как часть подключения устройства.
1. Реализация телеметрии, свойств и команд с помощью [соглашений Plug and Play IOT](concepts-convention.md)

Когда ваша реализация устройства или модуля будет готова, воспользуйтесь [обозревателем Azure IOT](howto-use-iot-explorer.md) , чтобы проверить, соответствует ли устройство правилам Plug and Play IOT.

:::zone pivot="programming-language-ansi-c"

[!INCLUDE [iot-pnp-device-devguide-csharp](../../includes/iot-pnp-device-devguide-c.md)]

:::zone-end

:::zone pivot="programming-language-csharp"

[!INCLUDE [iot-pnp-device-devguide-csharp](../../includes/iot-pnp-device-devguide-csharp.md)]

:::zone-end

:::zone pivot="programming-language-java"

[!INCLUDE [iot-pnp-device-devguide-java](../../includes/iot-pnp-device-devguide-java.md)]

:::zone-end

:::zone pivot="programming-language-javascript"

[!INCLUDE [iot-pnp-device-devguide-node](../../includes/iot-pnp-device-devguide-node.md)]

:::zone-end

:::zone pivot="programming-language-python"

[!INCLUDE [iot-pnp-device-devguide-python](../../includes/iot-pnp-device-devguide-python.md)]

:::zone-end

## <a name="next-steps"></a>Следующие шаги

Теперь, когда вы узнали о разработке Plug and Play для устройств IoT, ниже приведены некоторые дополнительные ресурсы.

- [Язык определения цифровых двойников (DTDL)](https://github.com/Azure/opendigitaltwins-dtdl)
- [Пакет SDK для устройств для C](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/)
- [REST API IoT](https://docs.microsoft.com/rest/api/iothub/device)
- [Компоненты модели](concepts-components.md)
- [Установка и использование средств разработки ДТДЛ](howto-use-dtdl-authoring-tools.md)
- [Руководством для разработчиков служб IoT Plug and Play](concepts-developer-guide-service.md)
