---
author: wesmc7777
ms.author: wesmc
ms.service: iot-hub
ms.topic: include
ms.date: 10/26/2018
ms.openlocfilehash: 60c0c700dde5afda2ca93a92b334cbc4f0134043
ms.sourcegitcommit: fecb6bae3f29633c222f0b2680475f8f7d7a8885
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/30/2019
ms.locfileid: "68667988"
---
> [!div class="op_single_selector"]
> * [Node.js](../articles/iot-hub/iot-hub-node-node-device-management-get-started.md)
> * [.NET](../articles/iot-hub/iot-hub-csharp-csharp-device-management-get-started.md)
> * [Java](../articles/iot-hub/iot-hub-java-java-device-management-getstarted.md)
> * [Python](../articles/iot-hub/iot-hub-python-python-device-management-get-started.md)

Серверные приложения могут использовать примитивы центра Интернета вещей Azure, такие как [двойникаы устройств][lnk-devtwin] и [прямые методы][lnk-c2dmethod], для удаленного запуска и мониторинга действий по управлению устройствами на устройствах. В этом руководстве показано, как серверное приложение и приложение для устройства взаимодействуют для запуска и отслеживания процесса удаленной перезагрузки устройства с помощью Центра Интернета вещей.

[!INCLUDE [iot-hub-basic](iot-hub-basic-whole.md)]

Прямой метод используется для запуска действий по управлению устройствами (таких как перезагрузка, сброс к параметрам по умолчанию и обновление встроенного ПО) из внутреннего приложения в облаке. Устройство отвечает за следующие действия:

* Обработка запроса метода, отправленного из Центра Интернета вещей.
* Инициализация соответствующего действия на определенном устройстве.
* Предоставление сведений об обновлении состояния в Центр Интернета вещей в *сообщаемых свойствах*.

Вы можете воспользоваться серверным приложением в облаке, чтобы выполнить запросы двойника устройства для получения сведений о ходе выполнения ваших действий по управлению устройствами.

[lnk-devtwin]: ../articles/iot-hub/iot-hub-devguide-device-twins.md
[lnk-c2dmethod]: ../articles/iot-hub/iot-hub-devguide-direct-methods.md
