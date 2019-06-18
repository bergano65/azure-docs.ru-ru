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
ms.openlocfilehash: f189843e865863d9b4088363e691ebc42ad9f2a9
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/18/2019
ms.locfileid: "67185433"
---
### <a name="network-interfaces"></a>Сетевые интерфейсы

|  |  |
|---------|---------|
| [Конкретная группа безопасности сети на каждой сетевой карте](../articles/governance/policy/samples/nsg-on-nic.md) | Требует, чтобы определенная группа безопасности сети использовалась для каждого виртуального сетевого интерфейса. Вы можете указать идентификатор требуемой группы безопасности сети. |
| [Использование утвержденной подсети для сетевых интерфейсов виртуальной машины](../articles/governance/policy/samples/use-approved-subnet-vm-nics.md) | Требует, чтобы сетевые интерфейсы использовали утвержденную подсеть. Нужно указать идентификатор утвержденной подсети. |
| [Использование утвержденной виртуальной сети для сетевых интерфейсов виртуальной машины](../articles/governance/policy/samples/use-approved-vnet-vm-nics.md) | Требует, чтобы сетевые интерфейсы использовали утвержденную виртуальную сеть. Вы можете указать идентификатор утвержденной виртуальной сети. |