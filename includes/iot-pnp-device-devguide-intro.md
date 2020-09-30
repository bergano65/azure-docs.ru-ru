---
author: dominicbetts
ms.author: dobett
ms.service: iot-pnp
ms.topic: include
ms.date: 09/24/2020
ms.openlocfilehash: 706e936590f5f47ee2989bf77b3caad67a8f28fc
ms.sourcegitcommit: a422b86148cba668c7332e15480c5995ad72fa76
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/30/2020
ms.locfileid: "91580066"
---
Plug and Play IoT позволяет создавать интеллектуальные устройства, которые объявляют свои возможности для приложений Интернета вещей Azure. Устройства IoT Plug and Play не нуждаются в ручной настройке, когда клиент подключает их к приложениям с поддержкой Интернета вещей Plug and Play.

Смарт-устройство может быть реализовано напрямую, использовать [модули](../articles/iot-hub/iot-hub-devguide-module-twins.md)или использовать [модули IOT Edge](../articles/iot-edge/about-iot-edge.md).

В этом руководстве описываются основные шаги, необходимые для создания устройства, модуля или IoT Edge модуля, который соответствует [соглашениям Plug and Play IOT](../articles/iot-pnp/concepts-convention.md).

Чтобы создать модуль Plug and Play для устройства, модуля или IoT Edge IoT, выполните следующие действия.

1. Убедитесь, что устройство использует протокол MQTT или MQTT через WebSocket для подключения к центру Интернета вещей Azure.
1. Создайте модель [дтдл (Digital двойников Definition Language)](https://github.com/Azure/opendigitaltwins-dtdl) для описания устройства. Дополнительные сведения см. в разделе [Знакомство с компонентами в Plug and Playных моделях Интернета вещей](../articles/iot-pnp/concepts-components.md).
1. Обновите устройство или модуль, чтобы объявить `model-id` об этом как часть подключения устройства.
1. Реализация телеметрии, свойств и команд с помощью [соглашений Plug and Play IOT](../articles/iot-pnp/concepts-convention.md)

Когда ваша реализация устройства или модуля будет готова, воспользуйтесь [обозревателем Azure IOT](../articles/iot-pnp/howto-use-iot-explorer.md) , чтобы проверить, соответствует ли устройство правилам Plug and Play IOT.
