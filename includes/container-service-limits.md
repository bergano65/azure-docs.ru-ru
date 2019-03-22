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
ms.openlocfilehash: 62eb75ef18d3ac81be65783e57c21c0aefd7a429
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/07/2019
ms.locfileid: "57554772"
---
| Ресурс | Ограничение по умолчанию |
| --- | :--- |
| Максимальное кластеров на подписку | 100 |
| Максимальное число узлов в кластере | 100 |
| Максимальное модулей POD на каждом узле: [базовая организация сети][basic-networking] с помощью Kubenet | 110 |
| Максимальное модулей POD на каждом узле: [Усовершенствованной организации сети] [ advanced-networking] с сетевой интерфейс для контейнера Azure | Развертывание с помощью Azure CLI: 30<sup>1</sup><br />Шаблон Azure Resource Manager: 30<sup>1</sup><br />Развертывание портала: 30 |

<sup>1</sup>при развертывании кластера Azure Kubernetes Service (AKS) с помощью Azure CLI или шаблона Resource Manager, это значение задается до 110 модулей POD на каждом узле. После вы уже развернули кластер службы контейнеров AZURE, или если вы развертываете кластер с помощью портала Azure нельзя настроить максимальное модулей POD на каждом узле.<br />

<!-- LINKS - Internal -->
[basic-networking]: ../articles/aks/concepts-network.md#kubenet-basic-networking
[advanced-networking]: ../articles/aks/concepts-network.md#azure-cni-advanced-networking

<!-- LINKS - External -->
[azure-support]: https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest
