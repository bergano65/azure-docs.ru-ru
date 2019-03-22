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
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/07/2019
ms.locfileid: "57554131"
---
Для службы архивации Azure применяются указанные ниже ограничения.

| **Ограничение** | **По умолчанию** |
| --- | --- |
| Серверов или компьютеров, которые можно зарегистрировать в хранилище. | Windows Server/Windows клиента/System Center Data Protection Manager: 50. <br/><br/> Виртуальные машины IaaS: 1,000.  |
| Размер источника данных в хранилище. |54,400 Гбайт. Ограничение не применяется к архивации виртуальных Машин IaaS. |
| Резервных хранилищ в подписке Azure. |500 хранилищ для каждого региона. |
| Запланируйте ежедневное резервное копирование. |Windows Server или клиент Windows: Три дня.<br/> System Center DPM: Два в день. <br/> Виртуальные машины IaaS: Один раз в день.  |
| Диски данных, присоединенные к виртуальной Машине Azure для резервного копирования. | 16 |
| Диск отдельных данных, подключенный к виртуальной Машине Azure для резервного копирования.| 4095 ГБ|
