---
author: wesmc7777
ms.service: redis-cache
ms.topic: include
ms.date: 11/09/2018
ms.author: wesmc
ms.openlocfilehash: 01d6d933474d4a9c1e428b8df89fc766c9b40f20
ms.sourcegitcommit: 6b7c8b44361e87d18dba8af2da306666c41b9396
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/12/2018
ms.locfileid: "51572623"
---
| Ресурс | Ограничение |
| --- | --- |
| Объем кэша |530 ГБ |
| Базы данных |64 |
| Макс. число подключенных клиентов |40 000 |
| Реплики кэша Radis (для высокого уровня доступности) |1 |
| Сегменты кэша уровня "Премиум" с включенной кластеризацией |10 |

Ограничения и размер кэша Redis для Azure отличаются для каждой ценовой категории. Информацию о ценовых категориях и соответствующих им размерах вы можете найти на странице [Цены на кэш Redis для Azure](https://azure.microsoft.com/pricing/details/cache/).

Дополнительные сведения об ограничениях конфигурации кэша Redis для Azure см. в разделе [Конфигурация сервера Redis по умолчанию](../articles/redis-cache/cache-configure.md#default-redis-server-configuration).

Поскольку настройка и управление экземплярами кэша Redis для Azure осуществляется корпорацией Майкрософт, кэш Redis для Azure поддерживает не все команды Redis. Дополнительные сведения см. в разделе [Команды Redis не поддерживаются в кэше Redis для Azure](../articles/redis-cache/cache-configure.md#redis-commands-not-supported-in-azure-redis-cache).

