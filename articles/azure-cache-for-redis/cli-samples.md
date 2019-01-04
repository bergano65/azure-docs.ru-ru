---
title: Примеры использования кэша Azure для Redis с помощью Azure CLI | Документация Майкрософт
description: Примеры Azure CLI для кэша Azure для Redis.
services: azure-cache-for-redis
documentationcenter: ''
author: wesmc7777
manager: cfowler
editor: ''
ms.assetid: 8d2de145-50c0-4f76-bf8f-fdf679f03698
ms.service: cache
ms.workload: tbd
ms.tgt_pltfrm: azure-cache-for-redis
ms.devlang: azurecli
ms.topic: article
ms.date: 04/14/2017
ms.author: wesmc
ms.openlocfilehash: 15166ae5eaab72849564ce8f03daf65eefae11f1
ms.sourcegitcommit: 7cd706612a2712e4dd11e8ca8d172e81d561e1db
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/18/2018
ms.locfileid: "53584043"
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
| [Подключение веб-приложения к кэшу Azure для Redis](./../app-service/scripts/cli-connect-to-redis.md) | Создает веб-приложение Azure и кэш Azure для Redis, а затем добавляет сведения о подключении Redis в параметры приложения. |
|**Удаление кэша**||
| [Удаление кэша](./scripts/delete-cache.md) | Удаляет экземпляр кэша Azure для Redis.  |
| | |

Дополнительные сведения об Azure CLI см. в статьях [Установка интерфейса командной строки Azure](https://docs.microsoft.com/cli/azure/install-azure-cli) и [Приступая к работе с Azure CLI](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli).
