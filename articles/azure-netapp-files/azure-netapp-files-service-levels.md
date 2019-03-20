---
title: Уровни обслуживания для Azure NetApp Files | Документация Майкрософт
description: Описывает производительность пропускной способности для уровней обслуживания Azure NetApp Files.
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
ms.date: 02/14/2019
ms.author: b-juche
ms.openlocfilehash: c2086eb0c5529d8e570a545e35fc716f70c7541f
ms.sourcegitcommit: 8ca6cbe08fa1ea3e5cdcd46c217cfdf17f7ca5a7
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/22/2019
ms.locfileid: "56668350"
---
# <a name="service-levels-for-azure-netapp-files"></a>Уровни обслуживания для Azure NetApp Files
Служба Azure NetApp Files поддерживает два уровня обслуживания: "Премиум" и "Стандартный". 

## <a name="Premium"></a>Хранилище класса "Премиум"

Хранилище класса *Премиум* предоставляет до 64 Миб/с пропускной способности на Тиб. Производительность пропускной способности индексируется относительно квоты тома. Например, том из хранилища класса "Премиум" с 2 ТиБ выделенной квоты (независимо от фактического потребления) имеет пропускную способность 128 МиБ/с.

## <a name="Standard"></a>Хранилище класса "Стандартный".

Хранилище класса*Стандартный* предоставляет до 16 Миб/с пропускной способности на ТиБ. Производительность пропускной способности индексируется относительно квоты тома. Например, том из хранилища класса "Стандартный" с 2 ТиБ выделенной квоты (независимо от фактического потребления) имеет пропускную способность 32 МиБ/с.

## <a name="next-steps"></a>Дальнейшие действия

- Сведения о ценах на разные уровни обслуживания см. на странице [Цены на службу Azure NetApp Files](https://azure.microsoft.com/pricing/details/storage/netapp/)
- [Настройка пула емкости](azure-netapp-files-set-up-capacity-pool.md)
