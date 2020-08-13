---
title: Что собой представляет иерархия хранилища Azure NetApp Files | Документация Майкрософт
description: Описание иерархии хранилища, включая учетные записи Azure NetApp Files, пулов емкости и томов.
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
ms.topic: overview
ms.date: 08/10/2020
ms.author: b-juche
ms.openlocfilehash: 91fecbc68efec1adcee9a2c4013dea46f6da86af
ms.sourcegitcommit: d8b8768d62672e9c287a04f2578383d0eb857950
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/11/2020
ms.locfileid: "88066480"
---
# <a name="what-is-the-storage-hierarchy-of-azure-netapp-files"></a>Что собой представляет иерархия хранилища Azure NetApp Files

Перед созданием тома в Azure NetApp Files необходимо приобрести и настроить пул для подготовленной емкости.  Чтобы настроить емкость пула, необходимо иметь учетную запись NetApp. Понимание иерархии хранилища помогает настроить и управлять ресурсами Azure NetApp Files.

> [!IMPORTANT] 
> Azure NetApp Files в настоящее время не поддерживает миграцию ресурсов между подписками.

## <a name="netapp-accounts"></a><a name="azure_netapp_files_account"></a>Учетные записи NetApp

- Учетная запись NetApp служит в качестве административного группирования составных емкостей пулов.  
- Учетная запись NetApp отличается от учетной записи хранения Azure. 
- Учетная запись NetApp является региональной областью.   
- Можно иметь несколько учетных записей NetApp в регионе, но каждая учетная запись NetApp привязана только к одному региону.

## <a name="capacity-pools"></a><a name="capacity_pools"></a>Пулы емкости

- Емкость пула определяется по его производительности.  
- Емкость предоставляется в пределах фиксированных номеров SKU, которые вы приобрели (например, емкость 4 ТиБ).
- Пул емкости может содержать только один уровень службы.  
- Каждый пул емкости может принадлежать только одной учетной записи NetApp. Но в учетной записи NetApp может быть несколько пулов емкости.  
- Пул емкости нельзя перемещать между различными учетными записями NetApp.   
  Например, в [концептуальной схеме иерархии хранения](#conceptual_diagram_of_storage_hierarchy) ниже, Capacity Pool 1 невозможно переместить из учётной записи NetApp восточной части США в учётную запись NetApp западной части США 2.  
- Нельзя удалить пул емкости, пока не будут удалены все содержащиеся в нем тома.

## <a name="volumes"></a><a name="volumes"></a>Тома

- Том измеряется с использованием логической емкости и масштабируется. 
- Потребление емкости тома зависит от подготовленной емкости пула.
- Каждый том принадлежит только одному пулу, но пул может содержать несколько томов. 

## <a name="conceptual-diagram-of-storage-hierarchy"></a><a name="conceptual_diagram_of_storage_hierarchy"></a>Концептуальная схема иерархии хранения 
В следующем примере показаны связи подписки Azure, учетных записей NetApp, пулов емкости и томов.   

![Концептуальная схема иерархии хранения](../media/azure-netapp-files/azure-netapp-files-storage-hierarchy.png)

## <a name="next-steps"></a>Дальнейшие действия

- [Ограничения ресурсов для службы Azure NetApp Files](azure-netapp-files-resource-limits.md)
- [Регистрация в службе Azure NetApp Files](azure-netapp-files-register.md)
