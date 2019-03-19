---
author: wesmc7777
ms.author: wesmc
ms.service: iot-hub
ms.topic: include
ms.date: 10/26/2018
ms.openlocfilehash: 19331f35ea2fa773325ec61e728677e37767ab54
ms.sourcegitcommit: 15e9613e9e32288e174241efdb365fa0b12ec2ac
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/28/2019
ms.locfileid: "57011821"
---
> [!div class="op_single_selector"]
> * [Устройство: Служба node.js: Node.js](../articles/iot-hub/iot-hub-node-node-device-management-get-started.md) (Веб-приложения в Службе приложений Azure: Node.js)
> * [Устройство: C#Служба: C#](../articles/iot-hub/iot-hub-csharp-csharp-device-management-get-started.md)
> * [Устройство: Служба Java: Java](../articles/iot-hub/iot-hub-java-java-device-management-getstarted.md)
> * [Устройство: Служба Python: Python](../articles/iot-hub/iot-hub-python-python-device-management-get-started.md)

Внутренние приложения могут использовать примитивы Центра Интернета вещей, а именно [двойники устройств][lnk-devtwin] и [прямые методы][lnk-c2dmethod], чтобы удаленно запускать и отслеживать на устройствах действия по управлению ими. В этом руководстве показано, как серверное приложение и приложение для устройства взаимодействуют для запуска и отслеживания процесса удаленной перезагрузки устройства с помощью Центра Интернета вещей.

[!INCLUDE [iot-hub-basic](iot-hub-basic-whole.md)]

Прямой метод используется для запуска действий по управлению устройствами (таких как перезагрузка, сброс к параметрам по умолчанию и обновление встроенного ПО) из внутреннего приложения в облаке. Устройство отвечает за следующие действия:

* Обработка запроса метода, отправленного из Центра Интернета вещей.
* Инициализация соответствующего действия на определенном устройстве.
* Предоставление сведений об обновлении состояния в Центр Интернета вещей в *сообщаемых свойствах*.

Вы можете воспользоваться серверным приложением в облаке, чтобы выполнить запросы двойника устройства для получения сведений о ходе выполнения ваших действий по управлению устройствами.

[lnk-devtwin]: ../articles/iot-hub/iot-hub-devguide-device-twins.md
[lnk-c2dmethod]: ../articles/iot-hub/iot-hub-devguide-direct-methods.md
