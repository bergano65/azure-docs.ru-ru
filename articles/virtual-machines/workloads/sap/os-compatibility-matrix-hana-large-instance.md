---
title: Матрица совместимости операционных систем для SAP HANA (крупные экземпляры)
description: Матрица совместимости демонстрирует совместимость различных версий операционной системы с различными типами оборудования (для крупных экземпляров).
services: virtual-machines-linux
documentationcenter: ''
author: sasarava
manager: hrushib
editor: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 05/19/2020
ms.author: sasarava
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 3fbc6c7b8811f3cf46b4f31387c2181c8d085e39
ms.sourcegitcommit: 50673ecc5bf8b443491b763b5f287dde046fdd31
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/20/2020
ms.locfileid: "83684882"
---
# <a name="compatible-operating-systems-for-hana-large-instances"></a>Совместимые операционные системы для крупных экземпляров HANA

## <a name="hana-large-instance-type-i"></a>Тип I крупных экземпляров HANA     
  | Операционная система | Доступность        | Номера SKU                                                          |
  |------------------|---------------------|---------------------------------------------------------------|
  | SLES 12 SP2      | Больше не предоставляются | S72, S72m, S96, S144, S144m, S192, S192m, S192xm              |
  | SLES 12 с пакетом обновления 3 (SP3)      | Доступно           | S72, S72m, S96, S144, S144m, S192, S192m, S192xm              |
  | SLES 12 с пакетом обновления 4 (SP4)      | Доступно           | S72, S72m, S96, S144, S144m, S192, S192m, S192xm, S224, S224m |

  
### <a name="persistent-memory-skus"></a>Номера SKU энергонезависимой памяти
  | Операционная система | Доступность | Номера SKU                             |
  |------------------|--------------|----------------------------------|
  | SLES 12 с пакетом обновления 4 (SP4)      | Доступно    | S224oo, S224om, S224ooo, S224oom |
  
## <a name="hana-large-instance-type-ii"></a>Тип II крупных экземпляров HANA     
  |  Операционная система       | Доступность        | Номера SKU                                                              |
  |-------------------------|---------------------|-------------------------------------------------------------------|
  | SLES 12 SP2             | Больше не предоставляются | S384, S384m, S384xm, S384xxm, S576m, S576xm, S768m, S768xm и S960m |
  | SLES 12 с пакетом обновления 3 (SP3)             | Доступно           | S384, S384m, S384xm, S384xxm, S576m, S576xm, S768m, S768xm и S960m |
  | SLES 12 с пакетом обновления 4 (SP4)             | Доступно           | S384, S384m, S384xm, S384xxm, S576m, S576xm, S768m, S768xm и S960m |
  | SLES 12 с пакетом обновления 5 (SP5)             | Доступно           | S384, S384m, S384xm, S384xxm, S576m, S576xm, S768m, S768xm и S960m |
  
## <a name="related-documents"></a>Связанные документы

- Дополнительные сведения о [доступных SKU](hana-available-skus.md)
- Сведения об [обновлении операционной системы](os-upgrade-hana-large-instance.md)
  

  
  
