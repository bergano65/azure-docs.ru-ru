---
title: включение файла
description: включение файла
services: container-service
author: dlepow
ms.service: container-service
ms.topic: include
ms.date: 10/11/2018
ms.author: danlep
ms.custom: include file
ms.openlocfilehash: 4251f379c517d5ccfd0430987e3d5280208590ff
ms.sourcegitcommit: 3a7c1688d1f64ff7f1e68ec4bb799ba8a29a04a8
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/17/2018
ms.locfileid: "49400454"
---
| Ресурс | Ограничение по умолчанию |
| --- | :--- |
| Максимальное число кластеров в подписке | 100 |
| Максимальное число узлов в кластере | 100 |
| Максимальное число групп pod на один узел: [базовое сетевое взаимодействие с Kubenet][basic-networking] | 110 |
| Максимальное число групп pod на один узел: [расширенное сетевое взаимодействие с Azure CNI][advanced-networking] | Развертывание с помощью Azure CLI: 30<sup>1</sup><br />Шаблон Resource Manager: 30<sup>1</sup><br />Развертывание портала: 30 |

<sup>1</sup> При развертывании кластера AKS с помощью Azure CLI или шаблона Resource Manager это значение можно увеличить до **110 модулей pod на узел**. Вы не сможете увеличить количество модулей pod на узел, если кластер AKS уже развернут или вы развернули его на портале Azure.<br />

<!-- LINKS - Internal -->
[basic-networking]: ../articles/aks/concepts-network.md#basic-networking
[advanced-networking]: ../articles/aks/concepts-network.md#advanced-networking

<!-- LINKS - External -->
[azure-support]: https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest
