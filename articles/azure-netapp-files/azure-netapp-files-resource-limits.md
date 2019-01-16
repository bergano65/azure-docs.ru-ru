---
title: Ограничения ресурсов для службы Azure NetApp Files | Документация Майкрософт
description: Описаны ограничения для ресурсов Azure NetApp Files, включая ограничения для пулов емкости, томов и делегированной подсети.
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
ms.topic: reference
ms.date: 01/03/2019
ms.author: b-juche
ms.openlocfilehash: f34afb1df2ae38353f29a80bfb6798c16856dbeb
ms.sourcegitcommit: d61faf71620a6a55dda014a665155f2a5dcd3fa2
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/04/2019
ms.locfileid: "54056738"
---
# <a name="resource-limits-for-azure-netapp-files"></a>Ограничения ресурсов для службы Azure NetApp Files
Понимание ограничений ресурсов для службы Azure NetApp Files помогает в управлении томами.

## <a name="capacity_pools"></a>Пулы емкости

- Минимальный размер одного пула емкости составляет 4 Тиб, а максимальный — 500 Тиб. 
- Каждый пул емкости может принадлежать только одной учетной записи NetApp. Но в учетной записи NetApp может быть несколько пулов емкости.  

## <a name="volumes"></a>Тома

- Минимальный размер одного тома составляет 100 ГиБ, а максимальный размер — 92 Тиб.
- Максимальное количество томов на одну подписку в регионе — 100.  

## <a name="delegated_subnet"></a>Делегированная подсеть 

В каждой виртуальной сети Azure можно делегировать только одну подсеть для Azure NetApp Files.

## <a name="next-steps"></a>Дополнительная информация

[Общие сведения об иерархии хранилища Azure NetApp Files](azure-netapp-files-understand-storage-hierarchy.md)
