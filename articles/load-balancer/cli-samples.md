---
title: Примеры Azure CLI для Load Balancer
titlesuffix: Azure Load Balancer
description: Примеры Azure CLI
services: load-balancer
documentationcenter: load-balancer
author: asudbring
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.custom: seodec18
ms.tgt_pltfrm: ''
ms.workload: infrastructure
ms.date: 06/14/2018
ms.author: allensu
ms.openlocfilehash: 9567dc1425ea74a9f46912532c5b8e4e4afc2fdb
ms.sourcegitcommit: 9a699d7408023d3736961745c753ca3cec708f23
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/16/2019
ms.locfileid: "68275695"
---
# <a name="azure-cli-samples-for-load-balancer"></a>Примеры Azure CLI для Load Balancer

В следующей таблице содержатся ссылки на скрипты Bash, созданные с помощью Azure CLI.

| | |
|-|-|
| [Балансировка трафика на виртуальных машинах для обеспечения высокого уровня доступности](./scripts/load-balancer-linux-cli-sample-nlb.md) | Создает несколько виртуальных машин с высокодоступной конфигурацией с балансировкой нагрузки. |
| [Распределение нагрузки виртуальных машин в пределах зон доступности](./scripts/load-balancer-linux-cli-sample-zone-redundant-frontend.md) | Создает три виртуальные машины в разных зонах доступности в регионе и Load Balancer ценовой категории "Стандартный" с избыточным между зонами интерфейсным IP-адресом. Эта конфигурация подсистемы балансировки нагрузки позволяет защитить приложения и данные от маловероятных сбоев и потери всего центра обработки данных. |
|[Балансировка нагрузки виртуальных машин в пределах определенной зоны доступности](./scripts/load-balancer-linux-cli-sample-zonal-frontend.md)|Создает три виртуальные машины, Load Balancer ценовой категории "Стандартный" с зональным интерфейсным IP-адресом, который помогает сопоставить путь к данным, а также ресурсы в отдельной зоне указанного региона.|
| [Балансировка нагрузки нескольких веб-сайтов на виртуальных машинах](./scripts/load-balancer-linux-cli-load-balance-multiple-websites-vm.md) | Создание двух виртуальных машин с несколькими IP-конфигурациями, присоединенных к группе доступности Azure, которые доступны через Azure Load Balancer. |
| | |

