---
title: включение файла
description: включение файла
services: backup
author: dcurwin
ms.service: backup
ms.topic: include
ms.date: 12/07/2018
ms.author: dacurwin
ms.custom: include file
ms.openlocfilehash: ca6c57b139b195b94dc7171336cfcc34d18b1872
ms.sourcegitcommit: 5aefc96fd34c141275af31874700edbb829436bb
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/04/2019
ms.locfileid: "74828743"
---
Для службы архивации Azure применяются указанные ниже ограничения.

| **Ограничение** | **По умолчанию** |
| --- | --- |
| Серверы или компьютеры, которые могут быть зарегистрированы в хранилище. | Windows Server, Windows Client/System Center Data Protection Manager: 50. <br/><br/> Виртуальные машины IaaS: 1 000.  |
| Размер источника данных в хранилище хранилища. |54 400-ГБ максимум. Это ограничение не распространяется на резервное копирование виртуальных машин IaaS. |
| Резервные хранилища в подписке Azure. |500 хранилищ на регион. |
| Планирование ежедневного резервного копирования. |Windows Server или клиент: три дня.<br/> System Center DPM: два дня. <br/> Виртуальные машины IaaS: один раз в день.  |
| Диски данных, подключенные к виртуальной машине Azure для резервного копирования. | 16 |
| Отдельный диск данных, подключенный к виртуальной машине Azure для резервного копирования.| 32 ТБ.|
