---
title: Ограничения в отношении ресурсов и объектов в службах Azure Analysis Services | Документы Майкрософт
description: Описание ограничений в отношении ресурсов и объектов в службах Azure Analysis Services.
author: minewiskan
manager: kfile
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 08/23/2019
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 40a5b68a12724f2574af19bb10c276c54c5afba0
ms.sourcegitcommit: 4b8a69b920ade815d095236c16175124a6a34996
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/23/2019
ms.locfileid: "69997589"
---
# <a name="analysis-services-resource-and-object-limits"></a>Ограничения в отношении ресурсов и объектов в службах Analysis Services

В этой статье описываются ограничения в отношении ресурсов и объектов модели.

## <a name="tier-limits"></a>Ограничения по уровням

Сведения об ограничениях QPU и памяти для уровней Developer, Basic и Standard см. на [странице цен на Azure Analysis Services](https://azure.microsoft.com/pricing/details/analysis-services/).

## <a name="object-limits"></a>Ограничения на объекты

Это теоретические ограничения. При меньших значениях производительность будет снижена.

|Object|Максимальные размеры и количество|  
|------------|----------------------------|  
|Базы данных в экземпляре|16,000|  
|Совокупное число таблиц и столбцов в базе данных|16,000|  
|Строки в таблице|Без ограничений<br /><br /> **Внимание!** Ни один столбец в таблице не может содержать более 1 999 999 997 отдельных значений.|  
|Иерархии в таблице|15 999|  
|Уровни в иерархии|15,999|  
|Отношения|8000|  
|Ключевые столбцы во всех таблицах|15,999|  
|Меры в таблице|2^31-1 = 2,147,483,647|  
|Ячейки, возвращаемые запросом|2^31–1 = 2 147 483 647|  
|Размер записи исходного запроса|64 000|  
|Длина имени объекта|512 символов|  


