---
title: Использование CLI для Azure Digital Twins
titleSuffix: Azure Digital Twins
description: Узнайте, как приступить к работе с и использовать интерфейс командной строки Azure Digital двойников.
author: baanders
ms.author: baanders
ms.date: 05/25/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 53b20ded8e4b4a003beff1ef8489ecd9ff3451ac
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: ru-RU
ms.lasthandoff: 07/02/2020
ms.locfileid: "84725309"
---
# <a name="use-the-azure-digital-twins-cli"></a>Использование CLI для Azure Digital Twins

Помимо управления экземпляром Azure Digital двойников в портал Azure, Azure Digital двойников имеет **интерфейс командной строки (CLI)** , который можно использовать для выполнения большинства основных действий со службой, в том числе:
* Управление экземпляром Digital двойников в Azure
* Управление моделями
* Управление цифровыми двойников
* Управление связями двойника
* Настройка конечных точек
* Управление [маршрутами](concepts-route-events.md)
* Настройка [безопасности](concepts-security.md) с помощью управления доступом на основе РОЛЕЙ (RBAC)

Команды Azure Digital двойников входят в [расширение Azure IOT для Azure CLI](https://github.com/Azure/azure-iot-cli-extension). Справочную документацию по этим командам можно просмотреть как часть `az iot` набора команд: [AZ DT](https://docs.microsoft.com/cli/azure/ext/azure-iot/dt?view=azure-cli-latest).

## <a name="deploy-and-validate"></a>Развертывание и проверка

В дополнение к обычному управлению экземпляром, интерфейс командной строки также является полезным инструментом для развертывания и проверки.
* Команды плоскости управления можно использовать, чтобы сделать развертывание нового экземпляра повторяющимся или автоматическим.
* Команды плоскости данных можно использовать для быстрой проверки значений в экземпляре и проверки того, что операции выполнены должным образом.

## <a name="next-steps"></a>Дальнейшие шаги

Альтернативу командам CLI см. в статье Управление экземпляром Digital двойников в Azure с помощью API и пакетов SDK:
* [Практические руководства. Использование API-интерфейсов и пакетов SDK для цифровых двойников Azure](how-to-use-apis-sdks.md)
