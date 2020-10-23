---
title: Использование CLI для Azure Digital Twins
titleSuffix: Azure Digital Twins
description: Узнайте, как приступить к работе с и использовать интерфейс командной строки Azure Digital двойников.
author: baanders
ms.author: baanders
ms.date: 05/25/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 34afc732d64a7aa29261989cc790c8a113208d96
ms.sourcegitcommit: 03713bf705301e7f567010714beb236e7c8cee6f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/21/2020
ms.locfileid: "92331776"
---
# <a name="use-the-azure-digital-twins-cli"></a>Использование CLI для Azure Digital Twins

Помимо управления экземпляром Azure Digital двойников в портал Azure, Azure Digital двойников имеет **интерфейс командной строки (CLI)** , который можно использовать для выполнения большинства основных действий со службой, в том числе:
* Управление экземпляром Digital двойников в Azure
* Управление моделями
* Управление цифровыми двойников
* Управление связями двойника
* Настройка конечных точек
* Управление [маршрутами](concepts-route-events.md)
* Настройка [безопасности](concepts-security.md) с помощью управления доступом на основе ролей Azure (Azure RBAC)

[!INCLUDE [digital-twins-known-issue-cloud-shell](../../includes/digital-twins-known-issue-cloud-shell.md)]

## <a name="uses-deploy-and-validate"></a>Использование (развертывание и проверка)

В дополнение к обычному управлению экземпляром, интерфейс командной строки также является полезным инструментом для развертывания и проверки.
* Команды плоскости управления можно использовать, чтобы сделать развертывание нового экземпляра повторяющимся или автоматическим.
* Команды плоскости данных можно использовать для быстрой проверки значений в экземпляре и проверки того, что операции выполнены должным образом.

## <a name="get-the-extension"></a>Получение расширения

Команды Azure Digital двойников входят в [расширение Azure IOT для Azure CLI](https://github.com/Azure/azure-iot-cli-extension). Полный список команд и их использование можно просмотреть в справочной документации по `az iot` набору команд: [ *AZ DT* Справочник по командам](/cli/azure/ext/azure-iot/dt?preserve-view=true&view=azure-cli-latest).

Вы можете убедиться, что у вас установлена последняя версия расширения, выполнив следующие действия. Эти команды можно выполнить в [Azure Cloud Shell](../cloud-shell/overview.md) или [локальном Azure CLI](/cli/azure/install-azure-cli?preserve-view=true&view=azure-cli-latest).

[!INCLUDE [digital-twins-cloud-shell-extensions.md](../../includes/digital-twins-cloud-shell-extensions.md)]

## <a name="next-steps"></a>Дальнейшие шаги

Изучите интерфейс командной строки и полный набор команд, используя справочные документы:
* [*AZ DT* Справочник по командам](/cli/azure/ext/azure-iot/dt?preserve-view=true&view=azure-cli-latest)