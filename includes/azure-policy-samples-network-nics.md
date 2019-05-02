---
title: включение файла
description: включение файла
services: azure-policy
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
origin.date: 05/17/2018
ms.date: 11/12/2018
ms.author: v-biyu
ms.custom: include file
ms.openlocfilehash: f189843e865863d9b4088363e691ebc42ad9f2a9
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/23/2019
ms.locfileid: "60866033"
---
### <a name="network-interfaces"></a>Сетевые интерфейсы

|  |  |
|---------|---------|
| [Конкретная группа безопасности сети на каждой сетевой карте](../articles/governance/policy/samples/nsg-on-nic.md) | Требует, чтобы определенная группа безопасности сети использовалась для каждого виртуального сетевого интерфейса. Вы можете указать идентификатор требуемой группы безопасности сети. |
| [Использование утвержденной подсети для сетевых интерфейсов виртуальной машины](../articles/governance/policy/samples/use-approved-subnet-vm-nics.md) | Требует, чтобы сетевые интерфейсы использовали утвержденную подсеть. Нужно указать идентификатор утвержденной подсети. |
| [Использование утвержденной виртуальной сети для сетевых интерфейсов виртуальной машины](../articles/governance/policy/samples/use-approved-vnet-vm-nics.md) | Требует, чтобы сетевые интерфейсы использовали утвержденную виртуальную сеть. Вы можете указать идентификатор утвержденной виртуальной сети. |