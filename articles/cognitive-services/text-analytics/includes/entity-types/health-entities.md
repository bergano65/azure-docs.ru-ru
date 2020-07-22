---
title: Именованные сущности для здравоохранения
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 06/15/2020
ms.author: aahi
ms.openlocfilehash: 906e8e9e6cde80a2151c6b8671f64854cb431250
ms.sourcegitcommit: d7008edadc9993df960817ad4c5521efa69ffa9f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/08/2020
ms.locfileid: "86108515"
---
## <a name="health-entity-categories"></a>Категории сущностей работоспособности:

[Анализ текста для работоспособности](../../how-tos/text-analytics-for-health.md)возвращаются следующие категории сущностей.  Обратите внимание, что в этом предварительном просмотре контейнера поддерживается только текст на английском языке, и в каждом образе контейнера предоставляется только одна версия модели.

### <a name="named-entity-recognition"></a>Распознавание именованных сущностей

|Категория  |Описание   |
|----------|--------------|
| ИНТЕРВАЛ | Устаревают. |
| BODY_STRUCTURE | Части пользовательского текста, включая Органс и другие структуры. | 
| CONDITION_QUALIFIER | Уровни условий, такие как *умеренное*, *Расширенное*или *рассеянное*. | 
| ДИАГНОСТИКИ | Медицинские условия. Например, « *натяжение* ». | 
| ДВУХ | Такие направления, как *Left* или *Антериор*. | 
| досаже | Размер или количество лечения.  | 
| EXAMINATION_NAME | Метод или процедура проверки. | 
| EXAMINATION_RELATION | связь между единицей измерения и проверкой.  | 
| EXAMINATION_UNIT | Единица измерения для изучения. | 
| EXAMINATION_VALUE | Значение единицы измерения для изучения. | 
| FAMILY_RELATION | Фамилиал связь, например *связанное*.  | 
| ЧАСТОТА | Частоты.   | 
| GENDER | Разные половые принадлежности. | 
| ГЕН | Сущность ген, например *TP53*.   | 
| MEDICATION_CLASS | Классы лечения. Например, *антибиотикс*.  | 
| MEDICATION_NAME  | Универсальная и фирменная символика с именем медикатионс.| 
| ROUTE_OR_MODE  | Метод администрирования лечения. | 
| SYMPTOM_OR_SIGN  | Признаков заболеваний. | 
| TIME  | Указанное. Например, "8 лет" или "2:10:30 на этот день" |
| TREATMENT_NAME  | Имена лечения. | 
| VARIANT  | Вариант генетических сущности ген | 

### <a name="relation-extraction"></a>Извлечение связей

Извлечение связей определяет осмысленные соединения между понятиями, упомянутыми в тексте. Например, связь "время условия" обнаруживается путем связывания имени условия с временем. Анализ текста для работоспособности может обозначать следующие отношения:

* DIRECTION_OF_BODY_STRUCTURE  
* TIME_OF_CONDITION
* QUALIFIER_OF_CONDITION  
* DOSAGE_OF_MEDICATION 
* FORM_OF_MEDICATION  
* ROUTE_OR_MODE_OF_MEDICATION   
* STRENGTH_OF_MEDICATION 
* ADMINISTRATION_RATE_OF_MEDICATION   
* FREQUENCY_OF_MEDICATION 
* TIME_OF_MEDICATION 
* TIME_OF_TREATMENT 
* FREQUENCY_OF_TREATMENT  
* VALUE_OF_EXAMINATION
* UNIT_OF_EXAMINATION 
* RELATION_OF_EXAMINATION 
* TIME_OF_EXAMINATION  
* СОКРАЩЕНИЕ 
