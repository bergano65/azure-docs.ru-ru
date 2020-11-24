---
title: включить файл
description: включить файл
services: container-service
author: dlepow
ms.service: container-service
ms.topic: include
ms.date: 11/22/2019
ms.author: danlep
ms.custom: include file
ms.openlocfilehash: 32edd880e5e455393e60c87f4f963d71a3e59a20
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/24/2020
ms.locfileid: "95554879"
---
| Ресурс | Ограничение |
| --- | :--- |
| Максимальное количество кластеров на подписку | 100 |
| Максимальное количество узлов на кластер с группами доступности виртуальных машин и номером SKU Load Balancer категории "Базовый"  | 100 |
| Максимальное количество узлов на кластер с Масштабируемыми наборами виртуальных машин и [номером SKU Load Balancer категории "Базовый"][standard-load-balancer] | 1000 (100 узлов на [пул узлов][node-pool]) |
| Максимальное количество модулей pod на узел: [Базовая организация сети][basic-networking] с использованием Kubenet | 110 |
| Максимальное количество модулей pod на узел: [Расширенные возможности работы в сети][advanced-networking] благодаря Сетевому интерфейсу контейнеров Azure | Развертывание с помощью Azure CLI: 30<sup>1</sup><br />Шаблон Azure Resource Manager: 30<sup>1</sup><br />Развертывание портала: 30 |

<sup>1</sup> При развертывании кластера Службы Azure Kubernetes (AKS) с помощью Azure CLI или шаблона Resource Manager это значение можно увеличить до 250 модулей pod на узел. Вы не сможете увеличить количество модулей pod на узел, если кластер AKS уже развернут или вы развернули его на портале Azure.<br />

<!-- LINKS - Internal -->
[basic-networking]: ../articles/aks/concepts-network.md#kubenet-basic-networking
[advanced-networking]: ../articles/aks/concepts-network.md#azure-cni-advanced-networking
[standard-load-balancer]: ../articles/load-balancer/load-balancer-overview.md
[node-pool]: ../articles/aks/use-multiple-node-pools.md

<!-- LINKS - External -->
[azure-support]: https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest