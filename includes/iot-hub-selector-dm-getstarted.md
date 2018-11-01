---
author: dominicbetts
ms.service: iot-hub
ms.topic: include
ms.date: 10/26/2018
ms.author: dobett
ms.openlocfilehash: 13eddced155eab6dedfbce77330e7a178ecfb3cb
ms.sourcegitcommit: 48592dd2827c6f6f05455c56e8f600882adb80dc
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/26/2018
ms.locfileid: "50165251"
---
> [!div class="op_single_selector"]
> * [Устройство — служба Node.js — Node.js](../articles/iot-hub/iot-hub-node-node-device-management-get-started.md)
> * [Устройство — служба C# — C#](../articles/iot-hub/iot-hub-csharp-csharp-device-management-get-started.md)
> * [Устройство — служба Java — Java](../articles/iot-hub/iot-hub-java-java-device-management-getstarted.md)
> * [Устройство — служба Python — Python](../articles/iot-hub/iot-hub-python-python-device-management-get-started.md)

Внутренние приложения могут использовать примитивы Центра Интернета вещей, а именно [двойники устройств][lnk-devtwin] и [прямые методы][lnk-c2dmethod], чтобы удаленно запускать и отслеживать на устройствах действия по управлению ими. В этом руководстве показано, как серверное приложение и приложение для устройства взаимодействуют для запуска и отслеживания процесса удаленной перезагрузки устройства с помощью Центра Интернета вещей.

[!INCLUDE [iot-hub-basic](iot-hub-basic-whole.md)]

Прямой метод используется для запуска действий по управлению устройствами (таких как перезагрузка, сброс к параметрам по умолчанию и обновление встроенного ПО) из внутреннего приложения в облаке. Устройство отвечает за следующие действия:

* Обработка запроса метода, отправленного из Центра Интернета вещей.
* Инициализация соответствующего действия на определенном устройстве.
* Предоставление сведений об обновлении состояния в Центр Интернета вещей в *сообщаемых свойствах*.

Вы можете воспользоваться серверным приложением в облаке, чтобы выполнить запросы двойника устройства для получения сведений о ходе выполнения ваших действий по управлению устройствами.

[lnk-devtwin]: ../articles/iot-hub/iot-hub-devguide-device-twins.md
[lnk-c2dmethod]: ../articles/iot-hub/iot-hub-devguide-direct-methods.md
