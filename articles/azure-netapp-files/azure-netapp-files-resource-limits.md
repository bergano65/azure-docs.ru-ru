---
title: Ограничения ресурсов для службы Azure NetApp Files | Документация Майкрософт
description: Описаны ограничения для ресурсов Azure NetApp Files, включая ограничения для учетных записей NetApp, пулов емкости, томов, моментальных снимков и делегированной подсети.
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: concepts
ms.date: 02/14/2019
ms.author: b-juche
ms.openlocfilehash: 196d85917e0a9900e141d58bff171beeb8540409
ms.sourcegitcommit: 9aa9552c4ae8635e97bdec78fccbb989b1587548
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/20/2019
ms.locfileid: "56430019"
---
# <a name="resource-limits-for-azure-netapp-files"></a>Ограничения ресурсов для службы Azure NetApp Files

Понимание ограничений ресурсов для службы Azure NetApp Files помогает в управлении томами.

- Каждая подписка Azure может включать до 10 учетных записей NetApp.
- Каждая учетная запись NetApp может включать до 25 пулов емкости.
- Каждый пул емкости может принадлежать только одной учетной записи NetApp.  
- Минимальный размер одного пула емкости составляет 4 Тиб, а максимальный — 500 Тиб. 
- Каждый пул емкости может включать до 500 томов.
- Минимальный размер одного тома составляет 100 ГиБ, а максимальный размер — 92 Тиб.
- Каждый том может включать до 255 моментальных снимков.
- Каждая виртуальная сеть Azure может обладать только одной подсетью делегированной в Azure NetApp Files.

**Дальнейшие действия**

[Общие сведения об иерархии хранилища Azure NetApp Files](azure-netapp-files-understand-storage-hierarchy.md)
