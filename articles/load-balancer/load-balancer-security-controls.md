---
title: Security controls for Azure Load Balancer
description: A checklist of security controls for evaluating Load Balancer
services: load-balancer
author: asudbring
manager: KumudD
ms.service: load-balancer
ms.topic: conceptual
ms.date: 09/04/2019
ms.author: allensu
ms.openlocfilehash: 6043e574697489b6566641c352bc21a2b6d87f51
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/20/2019
ms.locfileid: "74214899"
---
# <a name="security-controls-for-azure-load-balancer"></a>Security controls for Azure Load Balancer

This article documents the security controls built into Azure Load Balancer.

[!INCLUDE [Security controls Header](../../includes/security-controls-header.md)]

## <a name="network"></a>Network

| Security control | Да/нет | Заметки |
|---|---|--|
| Service endpoint support| Н/Д | |
| VNet injection support| Н/Д | |
| Network Isolation and Firewalling support| Н/Д |  |
| Forced tunneling support| Н/Д | |

## <a name="monitoring--logging"></a>Monitoring & logging

| Security control | Да/нет | Заметки|
|---|---|--|
| Azure monitoring support (Log analytics, App insights, etc.)| ДА | See [Azure Monitor logs for public Basic Load Balancer](load-balancer-monitor-log.md). |
| Control and management plane logging and audit| ДА | See [Azure Monitor logs for public Basic Load Balancer](load-balancer-monitor-log.md). |
| Data plane logging and audit | Н/Д |  |

## <a name="identity"></a>Удостоверение

| Security control | Да/нет | Заметки|
|---|---|--|
| Authentication| Н/Д |  |
| Авторизация| Н/Д |  |

## <a name="data-protection"></a>Защита данных

| Security control | Да/нет | Заметки |
|---|---|--|
| Server-side encryption at rest: Microsoft-managed keys | Н/Д | |
| Encryption in transit (such as ExpressRoute encryption, in VNet encryption, and VNet-VNet encryption )| Н/Д | |
| Server-side encryption at rest: customer-managed keys (BYOK) | Н/Д | |
| Column level encryption (Azure Data Services)| Н/Д | |
| Вызовы API в зашифрованном виде| ДА | Via the [Azure Resource Manager](../azure-resource-manager/index.yml). |

## <a name="configuration-management"></a>Управление конфигурацией

| Security control | Да/нет | Заметки|
|---|---|--|
| Configuration management support (versioning of configuration, etc.)| Н/Д |  | 

## <a name="next-steps"></a>Дальнейшие действия

- Learn more about the [built-in security controls across Azure services](../security/fundamentals/security-controls.md).