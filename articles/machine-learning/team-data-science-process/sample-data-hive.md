---
title: Выборка данных в таблицах Azure HDInsight Hive — командный процесс обработки и анализа данных
description: Уменьшение выборки данных, хранящихся в таблицах Hive в Azure HDInsight с помощью запросов Hive для сокращения до более удобного для анализа размера данных.
services: machine-learning
author: marktab
manager: cgronlun
editor: cgronlun
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 11/13/2017
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: c417950e07ae3c6922aa260a3ef40d862870aa1e
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/31/2019
ms.locfileid: "55452334"
---
# <a name="sample-data-in-azure-hdinsight-hive-tables"></a>Выборка данных в таблицах Azure HDInsight Hive
Из этой статье вы узнаете, как уменьшить выборку данных, хранящихся в таблицах Hive в Azure HDInsight. Для этих целей мы будем использовать запросы Hive, с помощью которых мы уменьшим размер данных для более удобного их анализа. Мы рассмотрим три распространенных метода выборки:

* Универсальная случайная выборка
* Случайная выборка по группам
* Стратифицированная выборка

**Для чего нужна выборка данных?**
Если размер набора данных, который планируется проанализировать, слишком большой, обычно рекомендуется уменьшить выборку данных до размера, который останется репрезентативным и будет более управляемым. Уменьшение выборки способствует пониманию данных, их исследованию и проектированию признаков. Роль этой операции в процессе обработки и анализа данных группы состоит в том, чтобы сделать возможным быстрое прототипирование функций обработки данных и моделей машинного обучения.

Эта задача выборки является одним из этапов [процесса обработки и анализа данных группы (TDSP)](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/).

## <a name="how-to-submit-hive-queries"></a>Отправка запросов Hive
Запросы Hive можно отправлять из командной строки Hadoop на головном узле кластера Hadoop. Для этого войдите на головной узел кластера Hadoop и откройте командную строку Hadoop. После этого вы сможете отправлять запросы Hive. Инструкции по отправке запросов Hive из командной строки Hadoop см. в разделе [Отправка запросов Hive](move-hive-tables.md#submit).

## <a name="uniform"></a> Универсальная случайная выборка
Универсальная случайная выборка означает, что каждая строка в наборе данных может попасть в выборку с одинаковой вероятностью. Это можно реализовать, добавив дополнительное поле rand() в набор данных во внутреннем запросе "select", а также во внешнем запросе "select" с условной зависимостью от этого случайного поля.

Вот пример запроса:

    SET sampleRate=<sample rate, 0-1>;
    select
        field1, field2, …, fieldN
    from
        (
        select
            field1, field2, …, fieldN, rand() as samplekey
        from <hive table name>
        )a
    where samplekey<='${hiveconf:sampleRate}'

Здесь `<sample rate, 0-1>` указывает долю записей, которые пользователи желают включить в выборку.

## <a name="group"></a> Случайная выборка по группам
При выборке данных о категориях может быть необходимо либо включить, либо исключить все экземпляры определенного значения переменной категории. Такого рода выборка называется выборкой по группам. Например, у вас есть переменная категории *Штат* со значениями NY, MA, CA, NJ и PA и вы хотите, чтобы записи для одного и того же штата были вместе, независимо от их включения в выборку.

Вот пример запроса с выборкой по группам:

    SET sampleRate=<sample rate, 0-1>;
    select
        b.field1, b.field2, …, b.catfield, …, b.fieldN
    from
        (
        select
            field1, field2, …, catfield, …, fieldN
        from <table name>
        )b
    join
        (
        select
            catfield
        from
            (
            select
                catfield, rand() as samplekey
            from <table name>
            group by catfield
            )a
        where samplekey<='${hiveconf:sampleRate}'
        )c
    on b.catfield=c.catfield

## <a name="stratified"></a>Стратифицированная выборка
Случайная выборка является стратифицированной по отношению к переменной категории, когда выбранные данные имеют значения этой категории, содержащиеся в такой же пропорции, как и в родительской популяции. Используя тот же пример, предположим, что данные содержат следующие наблюдения по штатам: Нью-Джерси — 100 наблюдений, Нью-Йорк — 60 наблюдений и Вашингтон — 300 наблюдений. Если указать коэффициент стратифицированной выборки 0,5, то в полученной выборке должно быть примерно по 50, 30 и 150 наблюдений из штатов NJ, NY и WA соответственно.

Вот пример запроса:

    SET sampleRate=<sample rate, 0-1>;
    select
        field1, field2, field3, ..., fieldN, state
    from
        (
        select
            field1, field2, field3, ..., fieldN, state,
            count(*) over (partition by state) as state_cnt,
              rank() over (partition by state order by rand()) as state_rank
          from <table name>
        ) a
    where state_rank <= state_cnt*'${hiveconf:sampleRate}'


Сведения о дополнительных методах выборки, доступных в Hive, см. на странице [руководства по выборке](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+Sampling).

