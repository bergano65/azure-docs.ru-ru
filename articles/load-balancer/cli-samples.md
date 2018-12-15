---
title: Примеры Azure CLI для Load Balancer
titlesuffix: Azure Load Balancer
description: Примеры Azure CLI
services: load-balancer
documentationcenter: load-balancer
author: KumudD
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.custom: seodec18
ms.tgt_pltfrm: ''
ms.workload: infrastructure
ms.date: 06/14/2018
ms.author: kumud
ms.openlocfilehash: b66a61be8995806db60effcaf6c1c92ce6359164
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/08/2018
ms.locfileid: "53084742"
---
# <a name="azure-cli-samples-for-load-balancer"></a>Примеры Azure CLI для Load Balancer

В следующей таблице содержатся ссылки на скрипты Bash, созданные с помощью Azure CLI.

| | |
|-|-|
| [Балансировка трафика на виртуальных машинах для обеспечения высокого уровня доступности](./scripts/load-balancer-linux-cli-sample-nlb.md) | Создает несколько виртуальных машин с высокодоступной конфигурацией с балансировкой нагрузки. |
| [Балансировка нагрузки виртуальных машин в пределах зон доступности](./scripts/load-balancer-linux-cli-sample-zone-redundant-frontend.md) | Создает три виртуальные машины в разных зонах доступности в регионе и Load Balancer ценовой категории "Стандартный" с избыточным между зонами интерфейсным IP-адресом. Эта конфигурация подсистемы балансировки нагрузки позволяет защитить приложения и данные от маловероятных сбоев и потери всего центра обработки данных. |
|[Балансировка нагрузки виртуальных машин в пределах определенной зоны доступности](./scripts/load-balancer-linux-cli-sample-zonal-frontend.md)|Создает три виртуальные машины, Load Balancer ценовой категории "Стандартный" с зональным интерфейсным IP-адресом, который помогает сопоставить путь к данным, а также ресурсы в отдельной зоне указанного региона.|
| [Балансировка нагрузки нескольких веб-сайтов на виртуальных машинах](./scripts/load-balancer-linux-cli-load-balance-multiple-websites-vm.md) | Создание двух виртуальных машин с несколькими IP-конфигурациями, присоединенных к группе доступности Azure, которые доступны через Azure Load Balancer. |
| | |

