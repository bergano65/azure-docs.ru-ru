---
title: Azure CLI примеры кэша Azure для Redis
description: Примеры Azure CLI для кэша Azure для Redis.
author: yegu-ms
ms.service: cache
ms.devlang: azurecli
ms.topic: conceptual
ms.date: 04/14/2017
ms.author: yegu
ms.openlocfilehash: 2b16b531639c826e0051ff45540fd583fd3869e5
ms.sourcegitcommit: 5a8c65d7420daee9667660d560be9d77fa93e9c9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/15/2019
ms.locfileid: "74121226"
---
# <a name="azure-cli-samples-for-azure-cache-for-redis"></a>Примеры Azure CLI для кэша Azure для Redis

В следующей таблице содержатся ссылки на скрипты Bash, созданные с помощью Azure CLI.

| | |
|---|---|
|**Создание кэша**||
| [Создание кэша](./scripts/create-cache.md) | Создает группу ресурсов и базовый уровень кэша Azure для Redis. |
| [Создание кэша уровня "Премиум" с кластеризацией](./scripts/create-premium-cache-cluster.md) | Создает группу ресурсов и кэш уровня "Премиум" с включенной кластеризацией.|
| [Получение сведений о кэше](./scripts/show-cache.md) | Возвращает сведения об экземпляре кэша Azure для Redis, включая состояние подготовки. |
| [Получение имени узла, портов и ключей](./scripts/cache-keys-ports.md) | Возвращает имя узла, порты и ключи для экземпляра кэша Azure для Redis. |
|**Веб-приложение и кэш**||
| [Подключите веб-приложения к кэшу Azure для Redis](./../app-service/scripts/cli-connect-to-redis.md) | Создает веб-приложение Azure и кэш Azure для Redis, а затем добавляет сведения о подключении Redis в параметры приложения. |
|**Удаление кэша**||
| [Удаление кэша](./scripts/delete-cache.md) | Удаляет экземпляр кэша Azure для Redis.  |
| | |

Дополнительные сведения об Azure CLI см. в статьях [Установка интерфейса командной строки Azure](https://docs.microsoft.com/cli/azure/install-azure-cli) и [Приступая к работе с Azure CLI](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli).
