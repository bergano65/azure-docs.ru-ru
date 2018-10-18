---
title: включение файла
description: включение файла
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 03/26/2018
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: b965fea3d4f166b1a801dda7cafd8e4190790c68
ms.sourcegitcommit: 1b561b77aa080416b094b6f41fce5b6a4721e7d5
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/17/2018
ms.locfileid: "45739510"
---
Для учетной записи хранения доступны такие варианты репликации:

* [Локально избыточное хранилище (LRS)](../articles/storage/common/storage-redundancy-lrs.md). Стратегия простой экономичной репликации. Данные реплицируются в одной единице масштабирования хранилища.
* [Хранилище, избыточное в пределах зоны (ZRS).](../articles/storage/common/storage-redundancy-zrs.md) Репликация, обеспечивающая высокий уровень доступности и устойчивости. Данные реплицируются синхронно в три зоны доступности. 
* [Геоизбыточное хранилище (GRS).](../articles/storage/common/storage-redundancy-grs.md) Репликация между регионами для защиты в случае недоступности целого региона.
* [Геоизбыточное хранилище с доступом на чтение (RA-GRS).](../articles/storage/common/storage-redundancy-grs.md#read-access-geo-redundant-storage) Репликация между регионами с доступом на чтение к реплике.