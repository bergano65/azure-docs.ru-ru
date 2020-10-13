---
title: Пул емкости QoS вручную Azure NetApp Files | Документация Майкрософт
description: Содержит вводные сведения о пуле ресурсов ручного качества обслуживания вручную и ссылки на дополнительные сведения.
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
ms.topic: conceptual
ms.date: 09/23/2020
ms.author: b-juche
ms.openlocfilehash: 14b905c69f0dab933159b414028db3e985d314a3
ms.sourcegitcommit: 50802bffd56155f3b01bfb4ed009b70045131750
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/09/2020
ms.locfileid: "91935839"
---
# <a name="manual-qos-capacity-pool"></a>Пул емкости QoS вручную

В этой статье приводятся общие сведения о функциональных возможностях пула мощностей качества обслуживания вручную.

## <a name="how-manual-qos-differs-from-auto-qos"></a>Как качество обслуживания вручную отличается от автоматического качества обслуживания

[Тип QoS](azure-netapp-files-understand-storage-hierarchy.md#qos_types) является атрибутом пула ресурсов. Azure NetApp Files предоставляет два типа QoS пулов емкости — Auto (по умолчанию) и вручную.  

В пуле ресурсов *ручного обслуживания вручную* можно независимо назначать емкость и пропускную способность тома. Общая пропускная способность всех томов, созданных с помощью управляемого вручную пула емкости QoS, ограничена общей пропускной способностью пула. Она определяется сочетанием размера пула и пропускной способности на уровне службы. 

В пуле ресурсов *автоматического* качества обслуживания пропускная способность автоматически назначается томам в пуле, пропорционально квоте размера, назначенной томам.  

Дополнительные сведения о типах QoS см. в разделе [иерархия хранилища Azure NetApp Files](azure-netapp-files-understand-storage-hierarchy.md) и [рекомендации по производительности для Azure NetApp Files](azure-netapp-files-performance-considerations.md) .

## <a name="how-to-specify-the-manual-qos-type"></a>Как указать тип QoS вручную

При [создании пула ресурсов](azure-netapp-files-set-up-capacity-pool.md)можно указать, что пул емкости будет использовать тип QoS вручную.  Можно также [изменить существующий пул емкости](manage-manual-qos-capacity-pool.md#change-to-qos) , чтобы использовать тип QoS вручную. 

Установка для типа емкости значения QoS вручную является постоянным изменением. Невозможно преобразовать средство ручной емкости типа QoS в пул ресурсов автоматического качества обслуживания. 

Для использования типа QoS вручную требуется [зарегистрировать компонент](manage-manual-qos-capacity-pool.md#register-the-feature).  

## <a name="next-steps"></a>Дальнейшие шаги

* [Управление пулом емкости качества обслуживания вручную](manage-manual-qos-capacity-pool.md)
* [Настройка пула емкости](azure-netapp-files-set-up-capacity-pool.md)
* [Иерархия хранилища](azure-netapp-files-understand-storage-hierarchy.md) 
* [Уровни обслуживания для Azure NetApp Files](azure-netapp-files-service-levels.md)
* [Рекомендации по ускорению Azure NetApp Files](azure-netapp-files-performance-considerations.md)
* [Модель затрат для Azure NetApp Files](azure-netapp-files-cost-model.md)
* [Ограничения ресурсов для службы Azure NetApp Files](azure-netapp-files-resource-limits.md)
* [Создание тома NFS](azure-netapp-files-create-volumes.md)
* [Создание тома SMB](azure-netapp-files-create-volumes-smb.md)
* [Создание тома с двумя протоколами](create-volumes-dual-protocol.md)
* [Метрики для Azure NetApp Files](azure-netapp-files-metrics.md)
* [Устранение проблем с пулом ресурсов](troubleshoot-capacity-pools.md)
