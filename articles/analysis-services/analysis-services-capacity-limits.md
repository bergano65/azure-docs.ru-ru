---
title: Ограничения в отношении ресурсов и объектов в службах Azure Analysis Services | Документы Майкрософт
description: В этой статье описываются ограничения ресурсов и объектов для сервера Azure Analysis Services.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 10/30/2019
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: f309c9863eb2f3065251537380a2977839f990d8
ms.sourcegitcommit: fad3aaac5af8c1b3f2ec26f75a8f06e8692c94ed
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/27/2020
ms.locfileid: "73573211"
---
# <a name="analysis-services-resource-and-object-limits"></a>Ограничения в отношении ресурсов и объектов в службах Analysis Services

В этой статье описываются ограничения в отношении ресурсов и объектов модели.

## <a name="tier-limits"></a>Ограничения по уровням

Сведения об ограничениях QPU и памяти для уровней Developer, Basic и Standard см. на [странице цен на Azure Analysis Services](https://azure.microsoft.com/pricing/details/analysis-services/).

## <a name="object-limits"></a>Ограничения на объекты

Это теоретические ограничения. При меньших значениях производительность будет снижена.

|Объект|Максимальный размер или количество|  
|------------|----------------------------|  
|Баз данных в экземпляре|16 000|  
|Совокупное число таблиц и столбцов в базе данных|16 000|  
|Строки в таблице|Неограниченно<br /><br /> **Предупреждение**. Ни один столбец в таблице не может содержать более 1 999 999 997 отдельных значений.|  
|Иерархии в таблице|15 999|  
|Уровни в иерархии|15 999|  
|Отношения|8000|  
|Ключевые столбцы во всех таблицах|15 999|  
|Меры в таблице|2^31–1 = 2 147 483 647|  
|Ячеек, возвращаемых запросом|2^31–1 = 2 147 483 647|  
|Размер записи в исходном запросе|64 000|  
|Длина имен объектов|512 символов|  


