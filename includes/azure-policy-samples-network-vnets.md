---
title: включение файла
description: включение файла
services: azure-policy
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 09/18/2018
ms.author: dacoulte
ms.custom: include file
ms.openlocfilehash: f93e22012a4855257f5372c1fc1dbc05ad29a6cd
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/18/2019
ms.locfileid: "67185434"
---
### <a name="virtual-networks"></a>Виртуальные сети

|  |  |
|---------|---------|
| [Разрешенные SKU для шлюзов приложений](../articles/governance/policy/samples/allowed-app-gateway-sku.md) | Требует, чтобы шлюзы приложений использовали утвержденный номер SKU. Нужно указать массив утвержденных номеров SKU. |
| [Допустимые номера SKU шлюзов виртуальной сети](../articles/governance/policy/samples/allowed-vnet-gateway-sku.md) | Требует, чтобы шлюзы виртуальной сети использовали утвержденный номер SKU. Нужно указать массив утвержденных номеров SKU. |
| [Допустимые номера SKU подсистемы балансировки нагрузки](../articles/governance/policy/samples/allowed-load-balancer-skus.md) | Требует, чтобы подсистемы балансировки нагрузки использовали утвержденный номер SKU. Нужно указать массив утвержденных номеров SKU. |
| [Запрет пиринга сетей для сети ExpressRoute](../articles/governance/policy/samples/no-peering-express-route-network.md) | Запрещает установку пиринга с сетью в указанной группе ресурсов. Используется, чтобы предотвратить подключение к центральной управляемой сетевой инфраструктуре. Нужно указать имя группы ресурсов для предотвращения сопоставления. |
| [Отсутствие таблицы определяемых пользователем маршрутов](../articles/governance/policy/samples/no-user-defined-route-table.md)  | Предотвращает развертывание виртуальных сетей с использованием таблицы определяемых пользователем маршрутов. |
| [Конкретная группа безопасности сети в каждой подсети](../articles/governance/policy/samples/nsg-on-subnet.md) | Требует, чтобы определенная группа безопасности сети использовалась для каждой виртуальной подсети. Вы можете указать идентификатор требуемой группы безопасности сети. |
| [Использование утвержденной подсети для сетевых интерфейсов виртуальной машины](../articles/governance/policy/samples/use-approved-subnet-vm-nics.md) | Требует, чтобы сетевые интерфейсы использовали утвержденную подсеть. Нужно указать идентификатор утвержденной подсети. |
| [Использование утвержденной виртуальной сети для сетевых интерфейсов виртуальной машины](../articles/governance/policy/samples/use-approved-vnet-vm-nics.md) | Требует, чтобы сетевые интерфейсы использовали утвержденную виртуальную сеть. Вы можете указать идентификатор утвержденной виртуальной сети. |