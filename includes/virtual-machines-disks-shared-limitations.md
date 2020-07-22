---
title: Включить имя файла
description: включить файл
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 07/14/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: d2cf7dbcd97c8f740447607eaf443bc3ea4a6733
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/20/2020
ms.locfileid: "86500614"
---
Включение общих дисков доступно только для подмножества типов дисков. В настоящее время общие диски разрешены только для Ultra дисков и твердотельных накопителей уровня "Премиум". Для каждого управляемого диска с включенными общими дисками действуют следующие ограничения, упорядоченные по типу диска:

### <a name="ultra-disks"></a>Диски категории "Ультра"

У дисков Ultra есть отдельный список ограничений, не связанных с общими дисками. Сведения об ограничениях для Ultra Disk см. в статье [Использование Ultra дисков Azure](../articles/virtual-machines/linux/disks-enable-ultra-ssd.md).

При совместном использовании Ultra Disks они имеют следующие дополнительные ограничения.

- В настоящее время поддерживается только Azure Resource Manager или поддержка пакета SDK. 
- В некоторых версиях отказоустойчивого кластера Windows Server можно использовать только базовые диски. Дополнительные сведения см. в разделе [требования к оборудованию отказоустойчивой кластеризации и параметры хранения](https://docs.microsoft.com/windows-server/failover-clustering/clustering-requirements).

### <a name="premium-ssds"></a>диски SSD ценовой категории "Премиум";

- В настоящее время поддерживается только в регионе "Западная Центральная часть США".
- В настоящее время поддерживается только Azure Resource Manager или поддержка пакета SDK. 
- Можно включить только на дисках данных, а не на дисках ОС.
- Кэширование узлов с **доступом только для чтения** недоступно для служб SSDS уровня "Премиум" с `maxShares>1` .
- Для уровня "Премиум" в `maxShares>1` .
- При использовании групп доступности и масштабируемых наборов виртуальных машин с общими дисками Azure [Выравнивание домена сбоя хранилища](https://docs.microsoft.com/azure/virtual-machines/windows/manage-availability#use-managed-disks-for-vms-in-an-availability-set) с доменом сбоя виртуальной машины не применяется к общему диску данных.
- При использовании групп размещения с учетом расположения [(ППГ)](../articles/virtual-machines/windows/proximity-placement-groups.md)все виртуальные машины, совместно использующие диск, должны быть частью одного ППГ.
- В некоторых версиях отказоустойчивого кластера Windows Server можно использовать только базовые диски. Дополнительные сведения см. в разделе [требования к оборудованию отказоустойчивой кластеризации и параметры хранения](https://docs.microsoft.com/windows-server/failover-clustering/clustering-requirements).
- Поддержка Azure Backup и Azure Site Recovery пока недоступна.

Если вы заинтересованы в попытке использовать общие диски, [Зарегистрируйтесь для получения доступа](https://aka.ms/AzureSharedDiskGASignUp).