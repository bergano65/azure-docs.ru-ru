---
title: Именованные сущности для здравоохранения
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: include
ms.date: 10/02/2020
ms.author: aahi
ms.openlocfilehash: 00c1c8ddab9214bf7698c21b05c24afa36ec20d9
ms.sourcegitcommit: 431bf5709b433bb12ab1f2e591f1f61f6d87f66c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/12/2021
ms.locfileid: "98147658"
---
## <a name="text-analytics-for-health-categories-entities-and-attributes"></a>Анализ текста категорий работоспособности, сущностей и атрибутов

[Анализ текста для определения работоспособности](../../how-tos/text-analytics-for-health.md) определяет медицинские понятия в следующих категориях.  (Обратите внимание, что в этом предварительном просмотре контейнера поддерживается только текст на английском языке, и в каждом образе контейнера предоставляется только одна версия модели.)


| Категория  | Описание  |
|---------|---------|
| [АНАТОМ](#anatomy) | Основные понятия, которые захватывают сведения об основных и Анатомик системах, сайтах, расположениях и регионах. |
 | [ДЕМОГРАФИЧЕСКИЕ данные](#demographics) | Основные понятия, которые захватывают сведения о полу и Age. |
 | [ПРОВЕРКИ](#examinations) | Основные понятия, которые захватывают сведения о процедурах диагностики и тестах. |
 | [GENOMICS](#genomics) | Основные понятия, в которых записываются сведения о генах и вариантах. |
 | [ЗДРАВООХРАНЕНИЯ](#healthcare) | Основные понятия, которые захватывают сведения об административных событиях, средах и профессиях здравоохранения. |
 | [МЕДИЦИНСКОЕ УСЛОВИЕ](#medical-condition) | Основные понятия, которые захватывают сведения об диагностиках, симптомах или знаках. |
 | [ЛЕЧЕНИЯ](#medication) | Основные понятия, в которых записываются сведения о лечения, включая имена лечения, классы, досаже и маршрут администрирования. |
 | [СОЦИАЛЬНЫХ](#social) | Основные понятия, которые захватывают сведения о медицинской релевантных аспектах, таких как отношение семьи. |
 | [ОБРАБАТЫВАТЬ](#treatment) | Основные понятия, которые захватывают сведения о процедурах серапеутик. |
  
Каждая категория может включать две группы концепций:

* **Сущности** — термины, которые фиксируют медицинские понятия, такие как диагностика, имя лечения или имя лечения.  Например, *брончитис* — это диагностика, а *аспирин* — имя лечения.  Упоминания в этой группе могут быть связаны с ИДЕНТИФИКАТОРом концепции УМЛС.
* **Атрибуты** — фразы, предоставляющие дополнительные сведения об обнаруженной сущности, например " *серьезный* " — квалификатор условия для *брончитис* или *81 mg* — это досаже для *аспирин*.  Упоминания в этой категории не будут связаны с ИДЕНТИФИКАТОРом концепции УМЛС.

Кроме того, служба распознает отношения между различными концепциями, включая отношения между атрибутами и сущностями, например *направление* к *структуре текста* или *досаже* *лечения имя* и отношения между сущностями, например в определении аббревиатур.

## <a name="anatomy"></a>Структура

### <a name="entities"></a>Сущности

Системы **BODY_STRUCTURE** -Body, Анатомик расположения или регионы, а также основные сайты. Например, ARM, завяз, Абдомен, нос, ливер, Head, респиратори система, лимфоцитес.

:::image type="content" source="../../media/ta-for-health/anatomy-entities-body-structure.png" alt-text="Пример сущности структуры Body.":::


:::image type="content" source="../../media/ta-for-health/anatomy-entities-body-structure-2.png" alt-text="Развернутый пример сущности структуры Body.":::

### <a name="attributes"></a>Атрибуты

Точки **направления направления,** например: Left, бокового смещения, Upper, апостериорные, характеризующая структуру текста.

:::image type="content" source="../../media/ta-for-health/anatomy-attributes.png" alt-text="Пример атрибута направления.":::

### <a name="supported-relations"></a>Поддерживаемые отношения

* **DIRECTION_OF_BODY_STRUCTURE**

## <a name="demographics"></a>Демографические данные

### <a name="entities"></a>Сущности

**Age** — все возрастные термины и фразы, включая данные о пациентах, членах семьи и других. Например, 40-летняя-Old, 51 Yo, 3 месяца Old, взрослого, новорожденный, елдерли, Титов, Minor, Ближний возраст.

:::image type="content" source="../../media/ta-for-health/age-entity.png" alt-text="Пример сущности Age.":::

:::image type="content" source="../../media/ta-for-health/age-entity-2.png" alt-text="Еще один пример сущности Age.":::


**Пол** -условия, которые открывают Пол субъекта. Например, «штекер», «женщина», «женщина», «жентлеман».

:::image type="content" source="../../media/ta-for-health/gender-entity.png" alt-text="Пример сущности пола.":::

### <a name="attributes"></a>Атрибуты

**RELATIONAL_OPERATOR** -фразы, которые выражают отношение между демографическими сущностями и дополнительными сведениями.

:::image type="content" source="../../media/ta-for-health/relational-operator.png" alt-text="Пример реляционного оператора.":::

## <a name="examinations"></a>Экзаменов

### <a name="entities"></a>Сущности

**EXAMINATION_NAME** — диагностические процедуры и тесты. Например, MRI, ЕКГ, ВИЧ Test, хемоглобин, плателетс Count, такие системы масштабирования, как *Бристол стул Scale*.

:::image type="content" source="../../media/ta-for-health/exam-name-entities.png" alt-text="Пример сущности экзамена.":::

:::image type="content" source="../../media/ta-for-health/exam-name-entities-2.png" alt-text="Еще один пример сущности «исследование имени».":::

### <a name="attributes"></a>Атрибуты

**Направление** — условия, характеризующие изучение.

:::image type="content" source="../../media/ta-for-health/exam-direction-attribute.png" alt-text="Пример атрибута Direction с сущностью имени для проверки.":::

**MEASUREMENT_UNIT** — единица проверки. Например, в *хемоглобин > 9,5 g/DL* термин *g/DL* является единицей для теста *хемоглобин* .

:::image type="content" source="../../media/ta-for-health/exam-unit-attribute.png" alt-text="Пример атрибута единицы измерения с сущностью имени для изучения.":::

**MEASUREMENT_VALUE** — значение для проверки. Например, в *хемоглобин > 9,5 g/DL* термин *9,5* является значением для теста *хемоглобин* .

:::image type="content" source="../../media/ta-for-health/exam-value-attribute.png" alt-text="Пример атрибута значения измерения с сущностью имени для изучения.":::

**RELATIONAL_OPERATOR** — фразы, которые выражают отношение между проверкой и дополнительной информацией. Например, необходимое значение измерения для проверки целевого объекта.

:::image type="content" source="../../media/ta-for-health/exam-relational-operator-attribute.png" alt-text="Пример реляционного оператора с сущностью &quot;исследование имени&quot;.":::

**Время** — временные термины, связанные с началом и (или) длиной (продолжительность) проверки. Например, когда произошел тест.

:::image type="content" source="../../media/ta-for-health/exam-time-attribute.png" alt-text="Пример атрибута времени с сущностью имени для проверки.":::

### <a name="supported-relations"></a>Поддерживаемые отношения

+ **DIRECTION_OF_EXAMINATION**
+   **RELATION_OF_EXAMINATION**
+   **TIME_OF_EXAMINATION**
+   **UNIT_OF_EXAMINATION**
+   **VALUE_OF_EXAMINATION**

## <a name="genomics"></a>Genomics

### <a name="entities"></a>Сущности

**Ген** — все упоминания генах. Например, МТРР, F2.

:::image type="content" source="../../media/ta-for-health/genomics-entities.png" alt-text="Пример сущности ген.":::

**Вариант** — все упоминания о вариантах ген. Например, c. 524C>T, (МТРР): r.1462_1557del96
  
## <a name="healthcare"></a>Здравоохранение

### <a name="entities"></a>Сущности
  
**ADMINISTRATIVE_EVENT** — события, связанные с системой здравоохранения, но с природой административного или административного администрирования. Например, регистрация, предоставление, пробная версия, ввод в процессе обучения, перемещение, поразрядное использование, больницы и больницы остаются в курсе. 

:::image type="content" source="../../media/ta-for-health/healthcare-event-entity.png" alt-text="Пример сущности события здравоохранения.":::

**CARE_ENVIRONMENT** — среда или расположение, на которых пациентов. Например, в экстренной комнате, кабинете врача, кардиое, хоспицее, больницы.

:::image type="content" source="../../media/ta-for-health/healthcare-environment-entity.png" alt-text="На этом снимке экрана показан пример сущности среды здравоохранения.":::

**HEALTHCARE_PROFESSION** — специалист по здравоохранения, лицензированный или не лицензированный. Например, дантисту, пасологист, неурологист, радиологист, фармаЦист, Нутритионист, Physical серапист, Чиропрактор.

:::image type="content" source="../../media/ta-for-health/healthcare-profession-entity.png" alt-text="На этом снимке экрана показан еще один пример сущности среды здравоохранения.":::

:::image type="content" source="../../media/ta-for-health/healthcare-profession-entity-2.png" alt-text="Еще один пример сущности среды здравоохранения.":::

## <a name="medical-condition"></a>Медицинское условие

### <a name="entities"></a>Сущности

**Диагностика** — болезни, синдром, опасные. Например, молочной железы рака, Алзеимер, ХТН, CHF, Спинал шнур травмы.

:::image type="content" source="../../media/ta-for-health/medical-condition-entity.png" alt-text="Пример сущности медицинских условий.":::

:::image type="content" source="../../media/ta-for-health/medical-condition-entity-2.png" alt-text="Еще один пример сущности медицинских условий.":::

**SYMPTOM_OR_SIGN** — субъективная или целевая свидетельство болезни или других диагностики. Например, сундука трудности, головной боль, диззинесс, поспешны, РЫДАЕТ, Абдомен было мягким, хорошим бовел звуковым, а также науришед.

:::image type="content" source="../../media/ta-for-health/medical-condition-symptom-entity.png" alt-text="Пример записи медицинских условий или сущности симптома.":::

:::image type="content" source="../../media/ta-for-health/medical-condition-symptom-entity-2.png" alt-text="Еще один пример сущности «медицинское условие» или «симптом».":::

### <a name="attributes"></a>Атрибуты

Условия качества **CONDITION_QUALIFIER** , используемые для описания медицинских условий. Все следующие подкатегории считаются квалификаторами:

1.  Выражения, связанные со временем. это термины, которые качественно описывают измерение времени, например внезапное, акутом, хронических, всегда. 
2.  Выражения качества. это термины, описывающие природу медицинских условий, например запись, четкость.
3.  Выражения серьезности: серьезные, умеренные, неуправляемые.
4.  Выражения екстенсивити: локальные, фокальные, диффузные.
5.  Выражения для излучения: испускаемые, излучение.
6.  Масштаб условия. в некоторых случаях условие характеризуется масштабом, который является конечным упорядоченным списком значений. Например, пациентов with этап III панкреатик рака.
7.  Курс условия: термин, относящийся к курсу или прогрессу условия, например улучшению, ворсенинг, устранение проблемы. 

:::image type="content" source="../../media/ta-for-health/condition-qualifier-diagnosis.png" alt-text="Пример атрибута квалификатора условия и сущности диагноза.":::

:::image type="content" source="../../media/ta-for-health/condition-qualifier-diagnosis-2.png" alt-text="Еще один пример атрибута квалификатора условия и сущности диагноза.":::

:::image type="content" source="../../media/ta-for-health/conditional-qualifier-symptom-medication.png" alt-text="Пример атрибута квалификатора Condition с сущностями симптома и лечения.":::

:::image type="content" source="../../media/ta-for-health/condition-qualifier-diagnosis-3.png" alt-text="На этом снимке экрана показан еще один пример атрибута квалификатора условия с сущностью диагноза.":::

:::image type="content" source="../../media/ta-for-health/condition-qualifier-symptom.png" alt-text="На этом снимке экрана показан дополнительный пример атрибута квалификатора условия с сущностью диагноза.":::

Правила **направления текста** , характеризующие состояние медицинских условий.

:::image type="content" source="../../media/ta-for-health/medical-condition-direction-attribute.png" alt-text="Пример атрибута Direction с сущностью &quot;медицинское условие&quot;.":::

**Периодичность — частота** возникновения или возникновения медицинских условий.

:::image type="content" source="../../media/ta-for-health/medical-condition-frequency-attribute.png" alt-text="Пример атрибута Frequency с сущностью &quot;медицинское условие&quot;.":::

:::image type="content" source="../../media/ta-for-health/medical-condition-frequency-attribute-2.png" alt-text="Еще один пример атрибута Direction с сущностью &quot;симптом&quot; или &quot;Sign&quot;.":::

**MEASUREMENT_UNIT** — единица, которая характеризует медицинскую ситуацию. Например, в *1,5 x2x1 cm тумор* термин « *cm* » является единицей измерения для *тумор*. 

:::image type="content" source="../../media/ta-for-health/medical-condition-measure-unit-attribute.png" alt-text="Пример атрибута единицы измерения с сущностью &quot;медицинское условие&quot;.":::

**MEASUREMENT_VALUE** — значение, которое характеризует медицинскую ситуацию. Например, в *1,5 x2x1 cm тумор* термин *1.5 x2x1* — это значение измерения для *тумор*. 

:::image type="content" source="../../media/ta-for-health/medical-condition-measure-value-attribute.png" alt-text="На снимке экрана показан пример атрибута Direction с сущностью &quot;симптом&quot; или &quot;Sign&quot;.":::

**RELATIONAL_OPERATOR** -фразы, которые выражают отношение между дополнительными сведениями об медицинских условиях. Например, значение времени или измерения. 

:::image type="content" source="../../media/ta-for-health/medical-condition-relational-operator.png" alt-text="На снимке экрана показан еще один пример атрибута Direction с сущностью &quot;симптом&quot; или &quot;Sign&quot;.":::

**Временные условия** , связанные с началом и (или) длиной (продолжительность) медицинских условий. Например, при запуске симптома (SES) или при возникновении болезни.

:::image type="content" source="../../media/ta-for-health/medical-condition-time-attribute.png" alt-text="На снимке экрана показан дополнительный пример атрибута Direction с сущностью &quot;симптом&quot; или &quot;Sign&quot;.":::

### <a name="supported-relations"></a>Поддерживаемые отношения

+ **DIRECTION_OF_CONDITION**
+   **QUALIFIER_OF_CONDITION**
+   **TIME_OF_CONDITION**
+   **UNIT_OF_CONDITION**
+   **VALUE_OF_CONDITION**

## <a name="medication"></a>Лечения

### <a name="entities"></a>Сущности

**MEDICATION_CLASS** — набор медикатионс с аналогичным механизмом действия, связанным режимом действия, аналогичной химическим структурой и/или используется для обработки одной и той же болезни. Например, ACE инхибитор, опиоид, антибиотикс, релиеверс.

:::image type="content" source="../../media/ta-for-health/medication-entities-class.png" alt-text="Пример сущности класса лечения.":::

**MEDICATION_NAME** – лечения упоминания, включая фирменные имена и названия, отличные от фирменных. Например, Адвил, ибупрофен.

:::image type="content" source="../../media/ta-for-health/medication-entities-name.png" alt-text="Пример сущности лечения Name.":::

### <a name="attributes"></a>Атрибуты

**Досаже** — объем лечения заказа. Например, дечлориде решение *1000 ml* для натрия.

:::image type="content" source="../../media/ta-for-health/medication-dosage.png" alt-text="Пример атрибута лечения досаже.":::

**Периодичность** — частота выполнения лечения.

:::image type="content" source="../../media/ta-for-health/medication-frequency.png" alt-text="Пример атрибута частоты лечения.":::

:::image type="content" source="../../media/ta-for-health/medication-frequency-2.png" alt-text="Еще один пример атрибута частоты лечения.":::

**MEDICATION_FORM** — форма лечения. Например, Solution, пилюля, капсула, планшет, Patch, GEL, Paste, пенопластовый, распылитель, падения, сируп.

:::image type="content" source="../../media/ta-for-health/medication-form.png" alt-text="Пример атрибута формы лечения.":::

**MEDICATION_ROUTE** — метод администрирования лечения. Например, устные, вагинал, IV, епидурал, topicd, инхалед.

:::image type="content" source="../../media/ta-for-health/medication-route.png" alt-text="Пример атрибута лечения Route.":::

**RELATIONAL_OPERATOR** -фразы, которые выражают отношение между лечения и дополнительной информацией. Например, требуемое значение измерения.

:::image type="content" source="../../media/ta-for-health/medication-relational-operator.png" alt-text="На снимке экрана показан пример атрибута реляционного оператора с сущностью лечения.":::

:::image type="content" source="../../media/ta-for-health/medication-time.png" alt-text="На снимке экрана показан еще один пример атрибута реляционного оператора с сущностью лечения.":::

### <a name="supported-relations"></a>Поддерживаемые отношения

+ **DOSAGE_OF_MEDICATION**
+   **FORM_OF_MEDICATION**
+   **FREQUENCY_OF_MEDICATION**
+   **ROUTE_OF_MEDICATION**
+   **TIME_OF_MEDICATION**

## <a name="social"></a>Социальные сети

### <a name="entities"></a>Сущности

**FAMILY_RELATION** — упоминание родственников субъекта. Например, отец, дочерний элемент, родственные элементы, родители.

:::image type="content" source="../../media/ta-for-health/family-relation.png" alt-text="На снимке экрана показан еще один пример атрибута времени обработки.":::

## <a name="treatment"></a>Обработка

### <a name="entities"></a>Сущности

**TREATMENT_NAME** — серапеутик процедуры. Например, завяз replacement хирургии, кость марров трансплант, Тави, диету.

:::image type="content" source="../../media/ta-for-health/treatment-entities-name.png" alt-text="Пример сущности имени лечения.":::

### <a name="attributes"></a>Атрибуты

Правила **направления,** которые характеризуют обработку.

:::image type="content" source="../../media/ta-for-health/treatment-direction.png" alt-text="На снимке экрана показан пример атрибута направления лечения.":::

**Периодичность** — частота возникновения или возникновения таких ситуаций.

:::image type="content" source="../../media/ta-for-health/treatment-frequency.png" alt-text="На снимке экрана показан еще один пример атрибута направления лечения.":::
 
**RELATIONAL_OPERATOR** -фразы, которые выражают связь между лечения и дополнительной информацией.  Например, сколько времени прошло из предыдущей процедуры.

:::image type="content" source="../../media/ta-for-health/treatment-relational-operator.png" alt-text="Пример атрибута реляционного оператора обработки.":::

**Временные условия** , связанные с началом и (или) длиной (продолжительность) обработки. Например, Дата присвоения обработки.

:::image type="content" source="../../media/ta-for-health/treatment-time.png" alt-text="На снимке экрана показан пример атрибута времени обработки.":::

### <a name="supported-relations"></a>Поддерживаемые отношения

+ **DIRECTION_OF_TREATMENT**
+   **TIME_OF_TREATMENT**
+   **FREQUENCY_OF_TREATMENT**
