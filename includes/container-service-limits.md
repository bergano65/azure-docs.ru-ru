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
ms.openlocfilehash: a2729af6a689daa551fc01f585324d53a8770a9b
ms.sourcegitcommit: 23389df08a9f4cab1f3bb0f474c0e5ba31923f12
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/10/2019
ms.locfileid: "67185650"
---
| Resource | Ограничение по умолчанию |
| --- | :--- |
| Максимальное число кластеров на подписку | 100 |
| Максимальное число узлов на кластер | 100 |
| Максимальное число модулей Pod на узел: [Основные сетевые подключения][basic-networking] с кубенет | 110 |
| Максимальное число модулей Pod на узел: [Расширенная][advanced-networking] работа с сетевыми интерфейсами контейнеров Azure | Развертывание с помощью Azure CLI: 30<sup>1</sup><br />Шаблон Azure Resource Manager: 30<sup>1</sup><br />Развертывание портала: 30 |

<sup>1</sup> При развертывании кластера Azure Kubernetes Service (AKS) с Azure CLI или шаблоном диспетчер ресурсов это значение можно настроить до 250 модулей Pod на узел. Вы не можете настроить максимальное число модулей Pod на узел после развертывания кластера AKS или при развертывании кластера с помощью портал Azure.<br />

<!-- LINKS - Internal -->
[basic-networking]: ../articles/aks/concepts-network.md#kubenet-basic-networking
[advanced-networking]: ../articles/aks/concepts-network.md#azure-cni-advanced-networking

<!-- LINKS - External -->
[azure-support]: https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest
