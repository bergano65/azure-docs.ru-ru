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
ms.openlocfilehash: 60912adbecbfdb4e6bebd7ddfea59eafa09b985e
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/23/2019
ms.locfileid: "61477980"
---
Для учетной записи хранения доступны такие варианты репликации:

* [Локально избыточное хранилище (LRS)](../articles/storage/common/storage-redundancy-lrs.md): Стратегии простого и экономичного репликации. Данные реплицируются в одной единице масштабирования хранилища.
* [Хранилище, избыточное между зонами (ZRS)](../articles/storage/common/storage-redundancy-zrs.md). Репликация для высокого уровня доступности и устойчивости. Данные реплицируются синхронно в три зоны доступности. 
* [Геоизбыточное хранилище (GRS).](../articles/storage/common/storage-redundancy-grs.md) Репликация между регионами для защиты от недоступности всего региона.
* [Геоизбыточное хранилище с доступом для чтения (RA-GRS)](../articles/storage/common/storage-redundancy-grs.md#read-access-geo-redundant-storage): Репликация между регионами с доступом на чтение к реплике.