---
title: Примеры Azure CLI для Load Balancer| Документация Майкрософт
description: Примеры Azure CLI
services: load-balancer
documentationcenter: load-balancer
author: KumudD
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: infrastructure
ms.date: 06/14/2018
ms.author: kumud
ms.openlocfilehash: 3973b541a0816473641f11391ddb59ca14618e30
ms.sourcegitcommit: 6135cd9a0dae9755c5ec33b8201ba3e0d5f7b5a1
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/31/2018
ms.locfileid: "50415293"
---
# <a name="azure-cli-samples-for-load-balancer"></a>Примеры Azure CLI для Load Balancer

В следующей таблице содержатся ссылки на скрипты Bash, созданные с помощью Azure CLI.

| | |
|-|-|
| [Балансировка трафика на виртуальных машинах для обеспечения высокого уровня доступности](./scripts/load-balancer-linux-cli-sample-nlb.md) | Создает несколько виртуальных машин с высокодоступной конфигурацией с балансировкой нагрузки. |
| [Балансировка нагрузки виртуальных машин в пределах зон доступности](./scripts/load-balancer-linux-cli-sample-zone-redundant-frontend.md) | Создает три виртуальные машины в разных зонах доступности в регионе и Load Balancer ценовой категории "Стандартный" с избыточным в пределах зоны интерфейсным IP-адресом. Эта конфигурация подсистемы балансировки нагрузки позволяет защитить приложения и данные от маловероятных сбоев и потери всего центра обработки данных. |
|[Балансировка нагрузки виртуальных машин в пределах определенной зоны доступности](./scripts/load-balancer-linux-cli-sample-zonal-frontend.md)|Создает три виртуальные машины, Load Balancer ценовой категории "Стандартный" с зональным интерфейсным IP-адресом, который помогает сопоставить путь к данным, а также ресурсы в отдельной зоне указанного региона.|
| [Балансировка нагрузки нескольких веб-сайтов на виртуальных машинах](./scripts/load-balancer-linux-cli-load-balance-multiple-websites-vm.md) | Создание двух виртуальных машин с несколькими IP-конфигурациями, присоединенных к группе доступности Azure, которые доступны через Azure Load Balancer. |
| | |

