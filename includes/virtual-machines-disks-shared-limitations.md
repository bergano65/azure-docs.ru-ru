---
title: Включить имя файла
description: включить файл
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 06/03/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: ce964ac197fbff64bbb7cc36e8c2bf762f93663f
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/02/2020
ms.locfileid: "84337362"
---
В предварительной версии включение общих дисков доступно только для подмножества типов дисков. В настоящее время общие диски разрешены только для Ultra дисков и твердотельных накопителей уровня "Премиум". Для каждого управляемого диска с включенными общими дисками действуют следующие ограничения, упорядоченные по типу диска:

### <a name="ultra-disks"></a>Диски (цен. категория "Ультра")

У дисков Ultra есть отдельный список ограничений, не связанных с общими дисками. Сведения об ограничениях для Ultra Disk см. в статье [Использование Ultra дисков Azure](../articles/virtual-machines/linux/disks-enable-ultra-ssd.md).

При совместном использовании Ultra Disks они имеют следующие дополнительные ограничения.

- В настоящее время поддерживается только Azure Resource Manager или поддержка пакета SDK.
- В некоторых версиях отказоустойчивого кластера Windows Server можно использовать только базовые диски. Дополнительные сведения см. в разделе [требования к оборудованию отказоустойчивой кластеризации и параметры хранения](https://docs.microsoft.com/windows-server/failover-clustering/clustering-requirements).

### <a name="premium-ssds"></a>SSD (цен. категория "Премиум")

- В настоящее время поддерживается только в регионе "Западная Центральная часть США".
- Все виртуальные машины, совместно использующие диск, должны быть развернуты в одних и тех же [группах размещения](../articles/virtual-machines/windows/proximity-placement-groups.md).
- Можно включить только на дисках данных, а не на дисках ОС.
- В некоторых версиях отказоустойчивого кластера Windows Server можно использовать только базовые диски. Дополнительные сведения см. в разделе [требования к оборудованию отказоустойчивой кластеризации и параметры хранения](https://docs.microsoft.com/windows-server/failover-clustering/clustering-requirements).
- Кэширование узлов с доступом только для чтения недоступно для служб SSDs уровня "Премиум" с `maxShares>1` .
- Группы доступности и масштабируемые наборы виртуальных машин можно использовать только с параметром, равным `FaultDomainCount` 1.
- Поддержка Azure Backup и Azure Site Recovery пока недоступна.

Если вы заинтересованы в попытке использовать общие диски, [Зарегистрируйтесь для использования предварительной версии](https://aka.ms/AzureSharedDiskPreviewSignUp).
