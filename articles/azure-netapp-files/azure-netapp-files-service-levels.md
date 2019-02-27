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
ms.topic: concepts
ms.date: 02/14/2019
ms.author: b-juche
ms.openlocfilehash: a10ac319fe362011531e00f832bb8e471fd56fdf
ms.sourcegitcommit: 9aa9552c4ae8635e97bdec78fccbb989b1587548
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/20/2019
ms.locfileid: "56431078"
---
# <a name="service-levels-for-azure-netapp-files"></a>Уровни обслуживания для Azure NetApp Files
Служба Azure NetApp Files поддерживает два уровня обслуживания: "Премиум" и "Стандартный". 

## <a name="Premium"></a>Хранилище класса "Премиум"

Хранилище класса *Премиум* предоставляет до 64 Миб/с пропускной способности на Тиб. Производительность пропускной способности индексируется относительно квоты тома. Например, том из хранилища класса "Премиум" с 2 ТиБ выделенной квоты (независимо от фактического потребления) имеет пропускную способность 128 МиБ/с.

## <a name="Standard"></a>Хранилище класса "Стандартный".

Хранилище класса*Стандартный* предоставляет до 16 Миб/с пропускной способности на ТиБ. Производительность пропускной способности индексируется относительно квоты тома. Например, том из хранилища класса "Стандартный" с 2 ТиБ выделенной квоты (независимо от фактического потребления) имеет пропускную способность 32 МиБ/с.

## <a name="next-steps"></a>Дополнительная информация

- Сведения о ценах на разные уровни обслуживания см. на странице [Цены на службу Azure NetApp Files](https://azure.microsoft.com/pricing/details/storage/netapp/)
- [Настройка пула емкости](azure-netapp-files-set-up-capacity-pool.md)
