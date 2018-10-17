---
title: включение файла
description: включение файла
services: container-service
author: dlepow
ms.service: container-service
ms.topic: include
ms.date: 08/31/2018
ms.author: danlep
ms.custom: include file
ms.openlocfilehash: 71294824bd3dd5215c388cfcd44382c7eee123ad
ms.sourcegitcommit: 67abaa44871ab98770b22b29d899ff2f396bdae3
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/08/2018
ms.locfileid: "48874140"
---
| Ресурс | Ограничение по умолчанию |
| --- | :--- |
| Максимальное число кластеров в подписке | 100 |
| Максимальное число узлов в кластере | 100 |
| Максимальное число групп pod на один узел: [базовое сетевое взаимодействие с Kubenet][basic-networking] | 110 |
| Максимальное число групп pod на один узел: [расширенное сетевое взаимодействие с Azure CNI][advanced-networking] | Развертывание с помощью Azure CLI: 30<sup>1</sup><br />Шаблон Resource Manager: 30<sup>1</sup><br />Развертывание портала: 30 |

<sup>1</sup> Это значение можно изменить при развертывании кластера AKS с помощью Azure CLI или шаблона Resource Manager.<br />

<!-- LINKS - Internal -->
[basic-networking]: ../articles/aks/networking-overview.md#basic-networking
[advanced-networking]: ../articles/aks/networking-overview.md#advanced-networking

<!-- LINKS - External -->
[azure-support]: https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest
