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
ms.openlocfilehash: 9ad1208283527f35e04dede2706fd0e424096dc5
ms.sourcegitcommit: c61777f4aa47b91fb4df0c07614fdcf8ab6dcf32
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/14/2019
ms.locfileid: "54300688"
---
Для службы архивации Azure применяются указанные ниже ограничения.

| **Ограничение** | **По умолчанию** |
| --- | --- |
| Количество серверов или компьютеров, которые можно зарегистрировать в хранилище | Windows Server, клиент Windows, System Center DPM: 50 <br/><br/> Виртуальных машин IaaS: 1000  |
| Размер источника данных в хранилище |Максимум 54 400 ГБ. Это ограничение не применяется при резервном копировании виртуальных машин IaaS. |
| Резервных хранилищ в подписке Azure |500 хранилищ на регион |
| Расписание ежедневного резервного копирования |Windows Server или клиент Windows: три раза в день<br/> System Center DPM: два раза в день <br/> Виртуальные машины IaaS: один раз в день  |
| Диски данных, подключенные к виртуальной машине Azure для резервного копирования | 16 |
| Отдельный диск данных, подключенный к виртуальной машине Azure для резервного копирования| 4095 ГБ|
