---
title: Таблица совместимости операционных систем для SAP HANA (крупные экземпляры) | Документация Майкрософт
description: Матрица совместимости представляет собой совместимость различных версий операционной системы с различными типами оборудования (крупные экземпляры).
services: virtual-machines-linux
documentationcenter: ''
author: sasarava
manager: hrushib
editor: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 03/03/2020
ms.author: sasarava
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: aa19433ef6446932da3509694ccccd08538b964f
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2020
ms.locfileid: "78675637"
---
# <a name="compatible-operating-systems-for-hana-large-instances"></a>Совместимые операционные системы для крупных экземпляров HANA

## <a name="hana-large-instance-type-i"></a>Тип крупных экземпляров HANA I     
  | Операционная система | Доступность        | Номера SKU                                                          |
  |------------------|---------------------|---------------------------------------------------------------|
  | SLES 12 SP2      | Больше не предоставляются | S72, S72m, S96, S144, S144m, S192, S192m, S192xm              |
  | SLES 12 с пакетом обновления 3 (SP3)      | Доступно           | S72, S72m, S96, S144, S144m, S192, S192m, S192xm              |
  | SLES 12 SP4      | Доступно           | S72, S72m, S96, S144, S144m, S192, S192m, S192xm, S224, S224m |
  
### <a name="persistent-memory-skus"></a>Номера SKU энергонезависимой памяти
  | Операционная система | Доступность | Номера SKU                             |
  |------------------|--------------|----------------------------------|
  | SLES 12 SP4      | Доступно    | S224oo, S224om, S224ooo, S224oom |
  
## <a name="hana-large-instance-type-ii"></a>Тип крупных экземпляров HANA II     
  |  Операционная система       | Доступность        | Номера SKU                                                              |
  |-------------------------|---------------------|-------------------------------------------------------------------|
  | SLES 12 SP2             | Больше не предоставляются | S384, S384m, S384xm, S384xxm, S576m, S576xm, S768m, S768xm, S960m |
  | SLES 12 с пакетом обновления 3 (SP3)             | Доступно           | S384, S384m, S384xm, S384xxm, S576m, S576xm, S768m, S768xm, S960m |
  
## <a name="related-documents"></a>Связанные документы

- Дополнительные сведения о [доступных SKU](hana-available-skus.md)
- Сведения об [обновлении операционной системы](os-upgrade-hana-large-instance.md)
  

  
  
