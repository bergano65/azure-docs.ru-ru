---
title: Руководством для разработчиков служб — Plug and Play IoT | Документация Майкрософт
description: Описание Plug and Play IoT для разработчиков служб
author: dominicbetts
ms.author: dobett
ms.date: 10/01/2020
ms.topic: conceptual
ms.service: iot-pnp
services: iot-pnp
zone_pivot_groups: programming-languages-set-ten
ms.openlocfilehash: df913716ff34a61e5bde4c0771ea8b7599db3d30
ms.sourcegitcommit: 59f506857abb1ed3328fda34d37800b55159c91d
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/24/2020
ms.locfileid: "92521403"
---
# <a name="iot-plug-and-play-service-developer-guide"></a>Руководством для разработчиков служб IoT Plug and Play

Plug and Play IoT позволяет создавать интеллектуальные устройства, которые объявляют свои возможности для приложений Интернета вещей Azure. Устройства IoT Plug and Play не нуждаются в ручной настройке, когда клиент подключает их к приложениям с поддержкой Интернета вещей Plug and Play.

IoT Plug and Play позволяет использовать устройства, которые объявили идентификатор модели с помощью центра Интернета вещей. Например, можно напрямую обращаться к свойствам и командам устройства.

Чтобы использовать устройство IoT Plug and Play, подключенное к центру Интернета вещей, воспользуйтесь одним из пакетов SDK для службы IoT:

## <a name="service-sdks"></a>Пакеты SDK службы

Используйте пакеты SDK службы Azure IoT в решении для взаимодействия с устройствами и модулями. Например, пакеты SDK для службы можно использовать для чтения и обновления свойств двойника и вызова команд. Поддерживаются следующие языки: C#, Java, Node.js и Python.

Пакеты SDK для служб позволяют получать доступ к сведениям об устройстве из решения, например рабочего стола или веб-приложения. Пакеты SDK для служб включают два пространства имен и объектные модели, которые можно использовать для получения идентификатора модели:

- Клиент службы центра Интернета вещей. Эта служба предоставляет идентификатор модели в качестве свойства двойникаа устройства.

- Клиент Digital двойников. Новый API Digital двойников работает с конструкциями модели на [языке двойников Definition Language (дтдл)](concepts-digital-twin.md) , такими как компоненты, свойства и команды. Интерфейсы API Digital двойника упрощают создание решений IoT Plug and Play решениями для сборщиков решений.

:::zone pivot="programming-language-csharp"

[!INCLUDE [iot-pnp-service-devguide-csharp](../../includes/iot-pnp-service-devguide-csharp.md)]

:::zone-end

:::zone pivot="programming-language-java"

[!INCLUDE [iot-pnp-service-devguide-java](../../includes/iot-pnp-service-devguide-java.md)]

:::zone-end

:::zone pivot="programming-language-javascript"

[!INCLUDE [iot-pnp-service-devguide-node](../../includes/iot-pnp-service-devguide-node.md)]

:::zone-end

:::zone pivot="programming-language-python"

[!INCLUDE [iot-pnp-service-devguide-python](../../includes/iot-pnp-service-devguide-python.md)]

:::zone-end

## <a name="next-steps"></a>Дальнейшие действия

Теперь, когда вы узнали о моделировании устройств, ниже приведены некоторые дополнительные ресурсы.

- [Язык определения цифровых двойников (DTDL)](https://github.com/Azure/opendigitaltwins-dtdl)
- [Пакет SDK для устройств для C](/azure/iot-hub/iot-c-sdk-ref/)
- [REST API IoT](/rest/api/iothub/device)
- [Компоненты модели](./concepts-components.md)
- [Установка и использование средств разработки ДТДЛ](howto-use-dtdl-authoring-tools.md)