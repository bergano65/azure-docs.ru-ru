---
title: Встроенные прямые методы edgeAgent - Azure IoT Edge
description: Мониторинг и управление развертыванием IoT Edge с помощью встроенных прямых методов в модуле времени выполнения агента IoT Edge
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 03/02/2020
ms.topic: conceptual
ms.reviewer: veyalla
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 57b9d46918414cef9e8cbcffb941b98c98f985ff
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80240348"
---
# <a name="communicate-with-edgeagent-using-built-in-direct-methods"></a>Общайтесь с edgeAgent с помощью встроенных прямых методов

Мониторинг и управление развертыванием IoT Edge с помощью прямых методов, включенных в модуль агента IoT Edge. Прямые методы внедряются на устройстве, а затем могут быть вызваны из облака. Агент IoT Edge включает в себя прямые методы, которые помогают удаленно контролировать и управлять устройствами IoT Edge.

Для получения дополнительной информации о прямых методах, как их использовать и как их реализовать в собственных модулях, [см. Понять и вызвать прямые методы из IoT концентратора](../iot-hub/iot-hub-devguide-direct-methods.md).

Имена этих прямых методов обрабатываются безчувственным.

## <a name="ping"></a>Проверка связи

Метод **пинга** полезен для проверки того, работает ли IoT Edge на устройстве, или же устройство имеет открытое соединение с IoT Hub. Используйте этот прямой метод, чтобы пинг IoT Edge агента и получить его статус. Успешный пинг возвращает пустую полезную нагрузку и **"статус": 200**.

Пример:

```azurecli
az iot hub invoke-module-method --method-name 'ping' -n <hub name> -d <device name> -m '$edgeAgent'
```

На портале Azure вывешите метод `ping` с именем метода и пустой полезной нагрузкой `{}`JSON.

![Вызвать прямой метод 'пинг' на портале Azure](./media/how-to-edgeagent-direct-method/ping-direct-method.png)

## <a name="restart-module"></a>Модуль перезагрузки

Метод **RestartModule** позволяет удаленно управлять модулями, работающими на устройстве IoT Edge. Если модуль сообщает о сбой состояния или другом неработоспособном поведении, можно запустить агент IoT Edge для его перезапуска. Успешная команда перезагрузки возвращает пустую полезную нагрузку и **"статус": 200**.

Метод RestartModule доступен в версии IoT Edge 1.0.9 и позже. 

Вы можете использовать прямой метод RestartModule на любом модуле, работаемом на устройстве IoT Edge, включая сам модуль edgeAgent. Однако, если вы используете этот прямой метод для выключения edgeAgent, вы не получите результат успеха, так как соединение нарушается во время перезагрузки модуля.

Пример:

```azurecli
az iot hub invoke-module-method --method-name 'RestartModule' -n <hub name> -d <device name> -m '$edgeAgent' --method-payload \
'
    {
        "schemaVersion": "1.0",
        "id": "<module name>"
    }
'
```

На портале Azure вызвать метод с `RestartModule` именем метода и следующей полезной нагрузкой JSON:

```json
{
    "schemaVersion": "1.0",
    "id": "<module name>"
}
```

![Вызвать прямой метод 'RestartModule' на портале Azure](./media/how-to-edgeagent-direct-method/restartmodule-direct-method.png)

## <a name="experimental-methods"></a>Экспериментальные методы

Новые варианты прямого метода доступны в качестве экспериментальных функций для тестирования, в том числе:

* [UploadLogs](https://github.com/Azure/iotedge/blob/master/doc/built-in-logs-pull.md): Извлекайте журналы модулей и загружайте их в хранилище Azure Blob.
* [GetTaskStatus](https://github.com/Azure/iotedge/blob/master/doc/built-in-logs-pull.md#gettaskstatus): Проверьте состояние запроса журналов загрузки.
* [GetLogs](https://github.com/Azure/iotedge/blob/master/doc/built-in-logs-pull.md#getlogs): Извлекайте журналы модулей в строке в ответе прямого метода.

## <a name="next-steps"></a>Дальнейшие действия

[Свойства двойников модулей агента IoT Edge и центра IoT Edge](module-edgeagent-edgehub.md)
