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
ms.openlocfilehash: 9e9c09c1825f5c8383a708e8bd343146396f878e
ms.sourcegitcommit: 23389df08a9f4cab1f3bb0f474c0e5ba31923f12
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/10/2019
ms.locfileid: "67185687"
---
Для службы архивации Azure применяются указанные ниже ограничения.

| **Ограничение** | **Default** |
| --- | --- |
| Серверы или компьютеры, которые могут быть зарегистрированы в хранилище. | Data Protection Manager Windows Server, Windows Client/System Center: 50. <br/><br/> Виртуальные машины IaaS: 1 000.  |
| Размер источника данных в хранилище хранилища. |54 400-ГБ максимум. Это ограничение не распространяется на резервное копирование виртуальных машин IaaS. |
| Резервные хранилища в подписке Azure. |500 хранилищ на регион. |
| Планирование ежедневного резервного копирования. |Windows Server или клиент Windows: Три дня.<br/> System Center DPM: Два дня. <br/> Виртуальные машины IaaS: Раз в день.  |
| Диски данных, подключенные к виртуальной машине Azure для резервного копирования. | 16 |
| Отдельный диск данных, подключенный к виртуальной машине Azure для резервного копирования.| 4 095 ГБ|
