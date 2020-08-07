---
title: Регионы, поддерживающие Зоны доступности в Azure
description: Для создания высокодоступного и отказоустойчивого приложения в Azure Зоны доступности предоставляет физически разделенные зоны, которые можно использовать для выполнения ресурсов.
author: cynthn
ms.service: azure
ms.topic: article
ms.date: 08/05/2020
ms.author: cynthn
ms.custom: fasttrack-edit, mvc, references_regions
ms.openlocfilehash: cf1fc81ea63db21d2e864c00e1987eec3d376b59
ms.sourcegitcommit: 7fe8df79526a0067be4651ce6fa96fa9d4f21355
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/06/2020
ms.locfileid: "87853166"
---
# <a name="regions-that-support-availability-zones-in-azure"></a>Регионы, поддерживающие Зоны доступности в Azure

Зоны доступности являются предложением, обеспечивающим высокий уровень доступности и защищающим приложения и данные от сбоев центров обработки данных. Дополнительные сведения о Зоны доступности см. [в статье регионы и зоны доступности в Azure](az-overview.md).

В этом разделе перечислены службы и регионы Azure, которые поддерживают Зоны доступности.

Службы, доступные в каждом регионе, а также предстоящие планы по доступности, можно найти в [продуктах, доступных по регионам](https://azure.microsoft.com/global-infrastructure/services/).

## <a name="americas"></a>Северная и Южная Америка

| Служба | Центральная часть США | Восточная часть США | восточная часть США 2 | Западная часть США 2 |
| --- | :---: | :---: | :---: | :---: |
| **Среда выполнения приложений** |  |  |  |  |
| Виртуальные машины Linux             | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| Виртуальные машины Windows           | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| Масштабируемые наборы виртуальных машин Microsoft Azure         | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| Среда службы приложений Azure ILB | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| Служба Azure Kubernetes           | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| **Память** |  |  |  |  |
| Управляемые диски                      | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| Хранилище, избыточное в виде зоны             | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| **Сеть** |  |  |  |  |
| Стандартный IP-адрес                | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| Load Balancer (цен. категория "Стандартный")             | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| VPN-шлюз                        | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| Шлюз ExpressRoute               | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| Шлюз приложений (v2)           | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| Брандмауэр Azure                     | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| **Базы данных** |  |  |  |  |
| Azure Data Explorer                | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| База данных SQL                       | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | : heavy_check_mark: (Предварительная версия) |
| Кэш Redis для Azure              | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| Azure Cosmos DB                    | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| **Аналитика** |  |  |  |  |
| Центры событий                         | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| **Интеграция** |  |  |  |  |
| Служебная шина (только уровня "Премиум")    | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| Сетка событий Azure                         | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| **Удостоверение** |  |  |  |  |
| Доменные службы Azure AD           | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |

## <a name="europe"></a>Европа

| Служба | Центральная Франция | Северная Европа | южная часть Соединенного Королевства | Западная Европа |
| --- | :---: | :---: | :---: | :---: |
| **Среда выполнения приложений** |  |  |  |  |
| Виртуальные машины Linux             | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| Виртуальные машины Windows           | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| Масштабируемые наборы виртуальных машин Microsoft Azure         | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| Среда службы приложений Azure ILB | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| Служба Azure Kubernetes           | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| **Память** |  |  |  |  |
| Управляемые диски                      | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| Хранилище, избыточное в виде зоны             | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| **Сеть** |  |  |  |  |
| Стандартный IP-адрес                | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| Load Balancer (цен. категория "Стандартный")             | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| VPN-шлюз                        | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| Шлюз ExpressRoute               | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| Шлюз приложений (v2)           | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| Брандмауэр Azure                     | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| **Базы данных** |  |  |  |  |
| Azure Data Explorer                | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| База данных SQL                       | :heavy_check_mark: | : heavy_check_mark: (Предварительная версия) | :heavy_check_mark: | :heavy_check_mark: |
| Кэш Redis для Azure              | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| Azure Cosmos DB                    | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| **Аналитика** |  |  |  |  |
| Центры событий                         | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| **Интеграция**  |  |  |  |  |
| Служебная шина (только уровня "Премиум")    | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| Сетка событий Azure                         | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| **Удостоверение** |  |  |  |  |
| Доменные службы Azure AD           | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |

## <a name="asia-pacific"></a>Азиатско-Тихоокеанский регион

| Служба | Japan East | Southeast Asia | Восточная Австралия |
| --- | :---: | :---: | :---: |
| **Среда выполнения приложений** |  |  |  |
| Виртуальные машины Linux             | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| Виртуальные машины Windows           | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| Масштабируемые наборы виртуальных машин Microsoft Azure         | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| Среда службы приложений Azure ILB | :heavy_check_mark: | :heavy_check_mark: |  |
| Служба Azure Kubernetes           | :heavy_check_mark: | :heavy_check_mark: |  |
| **Память** |  |  |  |
| Управляемые диски                      | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| Хранилище, избыточное в виде зоны             | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| **Сеть** |  |  |  |
| Стандартный IP-адрес                | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| Load Balancer (цен. категория "Стандартный")             | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| VPN-шлюз                        | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| Шлюз ExpressRoute               | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| Шлюз приложений (v2)           | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| Брандмауэр Azure                     | :heavy_check_mark: | :heavy_check_mark: |  |
| **Базы данных** |  |  |  |
| Azure Data Explorer                | :heavy_check_mark: | :heavy_check_mark: |  |
| База данных SQL                       | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| Кэш Redis для Azure              | :heavy_check_mark: | :heavy_check_mark: |  |
| Azure Cosmos DB                    | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| **Аналитика** |  |  |  |
| Центры событий                         | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| **Интеграция** |  |  |  |
| Служебная шина (только уровня "Премиум")    | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| Сетка событий Azure                         | :heavy_check_mark: | :heavy_check_mark: |  |
| **Удостоверение** |  |  |  |
| Доменные службы Azure AD           | :heavy_check_mark: | :heavy_check_mark: |  |

## <a name="other"></a>Другое

Azure также предлагает поддержку Зоны доступности в следующих регионах:

- US Gov (Вирджиния)
- Северная часть ЮАР;
- Центрально-южная часть США
- Центральная Канада

Чтобы получить дополнительные сведения о поддержке Зоны доступности в этих четырех регионах, обратитесь к специалисту Майкрософт по продажам или клиентам или откройте запрос на техническую поддержку.

## <a name="next-steps"></a>Дальнейшие шаги

- [Регионы и зоны доступности в Azure](az-overview.md)
