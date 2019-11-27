---
title: включение файла
description: включение файла
services: container-service
author: dlepow
ms.service: container-service
ms.topic: include
ms.date: 11/22/2019
ms.author: danlep
ms.custom: include file
ms.openlocfilehash: 0695416c65eed2bbf0a19d5ed1ea0c53a7ece332
ms.sourcegitcommit: 8cf199fbb3d7f36478a54700740eb2e9edb823e8
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/25/2019
ms.locfileid: "74485493"
---
| Resource (Ресурс) | Ограничение по умолчанию |
| --- | :--- |
| Максимальное число кластеров на подписку | 100 |
| Максимальное число узлов на кластер с группами доступности виртуальных машин и номером SKU "базовый Load Balancer"  | 100 |
| Максимальное число узлов на кластер с масштабируемыми наборами виртуальных машин и [Load Balancer (цен. Категория "Стандартный") SKU][standard-load-balancer] | 800 (100 узлов на [пул узлов][node-pool]) |
| Максимальное число модулей Pod на узел: [Базовая сеть][basic-networking] с кубенет | 110 |
| Максимальное число модулей Pod на узел: [Расширенная][advanced-networking] работа с сетевыми интерфейсами контейнеров Azure | Развертывание с помощью Azure CLI: 30<sup>1</sup><br />Шаблон Azure Resource Manager: 30<sup>1</sup><br />Развертывание портала: 30 |

<sup>1</sup> При развертывании кластера Azure Kubernetes Service (AKS) с Azure CLI или шаблоном диспетчер ресурсов это значение можно настроить до 250 модулей Pod на узел. Вы не можете настроить максимальное число модулей Pod на узел после развертывания кластера AKS или при развертывании кластера с помощью портал Azure.<br />

<!-- LINKS - Internal -->
[basic-networking]: ../articles/aks/concepts-network.md#kubenet-basic-networking
[advanced-networking]: ../articles/aks/concepts-network.md#azure-cni-advanced-networking
[standard-load-balancer]: ../articles/load-balancer/load-balancer-standard-overview.md
[node-pool]: ../articles/aks/use-multiple-node-pools.md

<!-- LINKS - External -->
[azure-support]: https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest
