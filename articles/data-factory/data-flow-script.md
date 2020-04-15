---
title: Картирование сценария потока данных
description: Обзор языка кода кода снилой передачи данных Data Factory за языком
author: kromerm
ms.author: nimoolen
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 04/13/2020
ms.openlocfilehash: 251507c5740af69bd0818391dd2e8f857338b6cf
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/14/2020
ms.locfileid: "81313914"
---
# <a name="data-flow-script-dfs"></a>Скрипт потока данных (DFS)

Скрипт потока данных (DFS) — это базовые метаданные, аналогичные языку кодирования, который используется для выполнения преобразований, включенных в поток данных отображения. Каждое преобразование представлено рядом свойств, которые предоставляют необходимую информацию для правильного выполнения задания. Скрипт виден и отослал от ADF, нажав на кнопку "скрипт" на верхней ленте интерфейса браузера.

![Кнопка сценария](media/data-flow/scriptbutton.png "Кнопка сценария")

Например, `allowSchemaDrift: true,` в преобразовании источника служба должна включить все столбцы из исходного набора данных в поток данных, даже если они не включены в проекцию схемы.

## <a name="use-cases"></a>Варианты использования
DFS автоматически производится пользовательским интерфейсом. Вы можете нажать кнопку "Сценарий", чтобы просмотреть и настроить сценарий. Вы также можете создавать скрипты за пределами UI ADF, а затем передать их в cmdlet PowerShell. При отладке сложных потоков данных может быть проще сканировать код скрипта позади, а не сканировать графическое представление ваших потоков.

Вот несколько примеров использования случаев:
- Программное производство многих потоков данных, которые являются довольно похожими, т.е. "штамповки" потоков данных.
- Сложные выражения, которыми трудно управлять в uI или приводят к проблемам проверки.
- Отладка и лучшее понимание различных ошибок, возвращенных во время выполнения.

При построении скрипа потока данных для использования с PowerShell или API необходимо свернуть отформатированный текст в одну строку. Вы можете следить за вкладками и newlines как побег символов. Но текст должен быть отформатирован, чтобы поместиться внутри свойства JSON. В нижней части uI редактора скриптов имеется кнопка, которая будет форматить сценарий как единую строку для вас.

![Кнопка "Копировать"](media/data-flow/copybutton.png "Кнопка "Копировать"")

## <a name="how-to-add-transforms"></a>Как добавить преобразования
Добавление преобразований требует трех основных этапов: добавления данных преобразования ядра, перенаправление потока ввода и перенаправление потока вывода. Проще всего это увидеть в примере.
Допустим, мы начинаем с простого источника, чтобы потопить поток данных, как следующее:

```
source(output(
        movieId as string,
        title as string,
        genres as string
    ),
    allowSchemaDrift: true,
    validateSchema: false) ~> source1
source1 sink(allowSchemaDrift: true,
    validateSchema: false) ~> sink1
```

Если мы решим добавить преобразование получения, сначала нам нужно создать текст преобразования ядра, `upperCaseTitle`который имеет простое выражение, чтобы добавить новый столбец верхнего регистра под названием :
```
derive(upperCaseTitle = upper(title)) ~> deriveTransformationName
```

Затем мы берем существующий DFS и добавляем преобразование:
```
source(output(
        movieId as string,
        title as string,
        genres as string
    ),
    allowSchemaDrift: true,
    validateSchema: false) ~> source1
derive(upperCaseTitle = upper(title)) ~> deriveTransformationName
source1 sink(allowSchemaDrift: true,
    validateSchema: false) ~> sink1
```

И теперь мы перенаправляем входящий поток, определяя, какое преобразование мы хотим, чтобы новая трансформация наступила после (в данном случае), `source1`и копируя название потока в новое преобразование:
```
source(output(
        movieId as string,
        title as string,
        genres as string
    ),
    allowSchemaDrift: true,
    validateSchema: false) ~> source1
source1 derive(upperCaseTitle = upper(title)) ~> deriveTransformationName
source1 sink(allowSchemaDrift: true,
    validateSchema: false) ~> sink1
```

Наконец, мы определяем преобразование, которое мы хотим получить `sink1`после этого нового преобразования, и заменяем ее поток ввода (в данном случае) названием потока вывода нашей новой трансформации:
```
source(output(
        movieId as string,
        title as string,
        genres as string
    ),
    allowSchemaDrift: true,
    validateSchema: false) ~> source1
source1 derive(upperCaseTitle = upper(title)) ~> deriveTransformationName
deriveTransformationName sink(allowSchemaDrift: true,
    validateSchema: false) ~> sink1
```

## <a name="dfs-fundamentals"></a>Основы DFS
DFS состоит из ряда подключенных преобразований, включая источники, раковины и различные другие, которые могут добавлять новые столбцы, фильтровать данные, объединять данные и многое другое. Как правило, сценарий с начала с одним или несколько источников следуют многие преобразования и заканчивая одним или несколько раковин.

Источники все имеют те же основные конструкции:
```
source(
  source properties
) ~> source_name
```

Например, простой источник с тремя столбиками (movieId, название, жанры) будет:
```
source(output(
        movieId as string,
        title as string,
        genres as string
    ),
    allowSchemaDrift: true,
    validateSchema: false) ~> source1
```

Все преобразования, кроме источников, имеют одну и ту же основную конструкцию:
```
name_of_incoming_stream transformation_type(
  properties
) ~> new_stream_name
```

Например, простая трансформация получения, которая берет столбец (название) и перезаписывает его с верхней версией будет следующим образом:
```
source1 derive(
  title = upper(title)
) ~> derive1
```

И раковина без схемы будет просто:
```
derive1 sink(allowSchemaDrift: true,
    validateSchema: false) ~> sink1
```

## <a name="script-snippets"></a>Фрагменты сценария

Фрагменты скрипта являются кодом совместного использования сценария Data Flow, который можно использовать для обмена между потоками данных. В этом видео ниже рассказывается о том, как использовать фрагменты скриптов и использовать сценарий Data Flow для копирования и вставки фрагментов скрипта за графиками потока данных:

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4tA9b]


### <a name="aggregated-summary-stats"></a>Агрегированная сводная статистика
Добавьте агрегированную трансформацию в поток данных под названием "SummaryStats", а затем вставьте в этот код ниже для агрегированной функции в скрипте, заменив существующие SummaryStats. Это обеспечит общий шаблон для сводной статистики профиля данных.

```
aggregate(each(match(true()), $$+'_NotNull' = countIf(!isNull($$)), $$ + '_Null' = countIf(isNull($$))),
        each(match(type=='double'||type=='integer'||type=='short'||type=='decimal'), $$+'_stddev' = round(stddev($$),2), $$ + '_min' = min ($$), $$ + '_max' = max($$), $$ + '_average' = round(avg($$),2), $$ + '_variance' = round(variance($$),2)),
        each(match(type=='string'), $$+'_maxLength' = max(length($$)))) ~> SummaryStats
```
Вы также можете использовать приведенную ниже выборку для подсчета количества уникальных и количества различных строк в данных. Приведенный ниже пример можно вставить в поток данных с агрегированной трансформацией под названием ValueDistAgg. В этом примере используется столбец под названием "название". Не забудьте заменить "название" строкой столбца в данных, которые вы хотите использовать, чтобы получить значение рассчитывает.

```
aggregate(groupBy(title),
    countunique = count()) ~> ValueDistAgg
ValueDistAgg aggregate(numofunique = countIf(countunique==1),
        numofdistinct = countDistinct(title)) ~> UniqDist
```

### <a name="include-all-columns-in-an-aggregate"></a>Включить все столбцы в совокупность
Это общий агрегированный шаблон, который показывает, как можно сохранить оставшиеся столбцы в выходных метаданных при создании агрегатов. В этом случае мы ```first()``` используем функцию для выбора первого значения в каждой колонке, имя которого не является "кино". Чтобы использовать это, создайте агрегированную трансформацию, называемую DistinctRows, а затем вставьте это в свой сценарий поверх существующего агрегированного скрипта DistinctRows.

```
aggregate(groupBy(movie),
    each(match(name!='movie'), $$ = first($$))) ~> DistinctRows
```

### <a name="create-row-hash-fingerprint"></a>Создание строки хэш отпечатка 
Используйте этот код в скрипте потока данных ```DWhash``` для ```sha1``` создания нового производного столбца, который производит хэш из трех столбцов.

```
derive(DWhash = sha1(Name,ProductNumber,Color))
```

## <a name="next-steps"></a>Следующие шаги

Изучите потоки данных, начав со [статьи обзора потоков данных](concepts-data-flow-overview.md)
