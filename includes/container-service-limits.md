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
ms.openlocfilehash: 6b4678b381e769993b01bbedd1cb4c0aeefc0cc1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80334783"
---
| Ресурс | Ограничение |
| --- | :--- |
| Максимальные кластеры за подписку | 100 |
| Максимальные узлы на кластер с наборами виртуальной доступности машин и базовым балансом нагрузки SKU  | 100 |
| Максимальные узлы на кластер с наборами виртуальной шкалы машин и [балансом стандартной нагрузки SKU][standard-load-balancer] | 1000 (100 узлов на [пул узлов)][node-pool] |
| Максимальный стручки на узла: [Основные сети][basic-networking] с Kubenet | 110 |
| Максимальный стручки на узел: [Расширенные сети][advanced-networking] с интерфейсом сети контейнеров Azure | Развертывание с помощью Azure CLI: 30<sup>1</sup><br />Шаблон менеджера ресурсов Azure: 30<sup>1</sup><br />Развертывание портала: 30 |

<sup>1</sup> При развертывании кластера службы Azure Kubernetes (AKS) с шаблоном Azure CLI или шаблоном resource Manager это значение настраивается до 250 стручков на узел. Вы не можете настроить максимальные стручки на узел после развертывания кластера AKS или если вы развернете кластер с помощью портала Azure.<br />

<!-- LINKS - Internal -->
[basic-networking]: ../articles/aks/concepts-network.md#kubenet-basic-networking
[advanced-networking]: ../articles/aks/concepts-network.md#azure-cni-advanced-networking
[standard-load-balancer]: ../articles/load-balancer/load-balancer-standard-overview.md
[node-pool]: ../articles/aks/use-multiple-node-pools.md

<!-- LINKS - External -->
[azure-support]: https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest
