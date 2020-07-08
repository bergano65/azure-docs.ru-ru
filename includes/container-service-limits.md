---
title: Включить имя файла
description: включить файл
services: container-service
author: dlepow
ms.service: container-service
ms.topic: include
ms.date: 11/22/2019
ms.author: danlep
ms.custom: include file
ms.openlocfilehash: 6b4678b381e769993b01bbedd1cb4c0aeefc0cc1
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/02/2020
ms.locfileid: "80334783"
---
| Ресурс | Ограничение |
| --- | :--- |
| Максимальное число кластеров на подписку | 100 |
| Максимальное число узлов на кластер с группами доступности виртуальных машин и номером SKU "базовый Load Balancer"  | 100 |
| Максимальное число узлов на кластер с масштабируемыми наборами виртуальных машин и [Load Balancer (цен. Категория "Стандартный") SKU][standard-load-balancer] | 1000 (100 узлов на [пул узлов][node-pool]) |
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
