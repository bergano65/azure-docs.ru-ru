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
ms.openlocfilehash: 23c25953d2f493d2dd799bfd11dbbb69db002d1b
ms.sourcegitcommit: a65b424bdfa019a42f36f1ce7eee9844e493f293
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/04/2019
ms.locfileid: "55736067"
---
| Ресурс | Ограничение по умолчанию |
| --- | :--- |
| Максимальное число кластеров в подписке | 100 |
| Максимальное число узлов в кластере | 100 |
| Максимальное число модулей на узле: [базовая организация сети][basic-networking] с помощью Kubenet | 110 |
| Максимальное число модулей на узле: [расширенные сетевые функции][advanced-networking] с Azure CNI | Развертывание с помощью Azure CLI: 30<sup>1</sup><br />Шаблон Resource Manager: 30<sup>1</sup><br />Развертывание портала: 30 |

<sup>1</sup> При развертывании кластера AKS с помощью Azure CLI или шаблона Resource Manager это значение можно увеличить до **110 модулей pod на узел**. Вы не сможете увеличить количество модулей pod на узел, если кластер AKS уже развернут или вы развернули его на портале Azure.<br />

<!-- LINKS - Internal -->
[basic-networking]: ../articles/aks/concepts-network.md#kubenet-basic-networking
[advanced-networking]: ../articles/aks/concepts-network.md#azure-cni-advanced-networking

<!-- LINKS - External -->
[azure-support]: https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest
