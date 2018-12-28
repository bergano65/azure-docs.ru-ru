---
title: включение файла
description: включение файла
services: backup
author: rayne-wiselman
ms.service: backup
ms.topic: include
ms.date: 12/07/2018
ms.author: raynew
ms.custom: include file
ms.openlocfilehash: 63922eb623576379058c9a8a367d6e52249115f2
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/08/2018
ms.locfileid: "53109099"
---
Для службы архивации Azure применяются указанные ниже ограничения.

| **Ограничение** | **По умолчанию** |
| --- | --- |
| Количество серверов или компьютеров, которые можно зарегистрировать в хранилище | Windows Server, клиент Windows, System Center DPM: 50 <br/><br/> Виртуальных машин IaaS: 1000  |
| Размер источника данных в хранилище |Максимум 54 400 ГБ. Это ограничение не применяется при резервном копировании виртуальных машин IaaS. |
| Резервных хранилищ в подписке Azure |500 хранилищ на регион |
| Расписание ежедневного резервного копирования |Windows Server или клиент Windows: три раза в день<br/> System Center DPM: два раза в день <br/> Виртуальные машины IaaS: один раз в день  |
| Диски данных, подключенные к виртуальной машине Azure для резервного копирования | 32 |
| Отдельный диск данных, подключенный к виртуальной машине Azure для резервного копирования| 4095 ГБ|



