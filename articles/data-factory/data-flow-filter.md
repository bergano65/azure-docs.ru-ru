---
title: Преобразование фильтра в потоке данных сопоставления фабрики данных Azure | Документация Майкрософт
description: Фильтрация строк с помощью преобразования «фильтр» в потоке данных сопоставления фабрики данных Azure
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.date: 10/16/2019
ms.openlocfilehash: a4dd53f37a8a963d05a3ad9c49769528e945f6a1
ms.sourcegitcommit: f29fec8ec945921cc3a89a6e7086127cc1bc1759
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/17/2019
ms.locfileid: "72527377"
---
# <a name="filter-transformation-in-mapping-data-flow"></a>Преобразование «фильтр» в потоке данных сопоставления

Преобразование «фильтр» позволяет фильтровать строки на основе условия. Выходной поток включает все строки, соответствующие условию фильтрации. Преобразование «фильтр» аналогично предложению WHERE в SQL.

## <a name="configuration"></a>Настройка

Используйте построитель выражений потока данных, чтобы ввести выражение для условия фильтра. Чтобы открыть построитель выражений, щелкните синюю рамку. Условие фильтра должно иметь тип Boolean. Дополнительные сведения о создании выражения см. в документации по [построителю выражений](concepts-data-flow-expression-builder.md) .

![Преобразование «фильтр»](media/data-flow/filter1.png "Преобразование «фильтр»")

## <a name="data-flow-script"></a>Сценарий потока данных

### <a name="syntax"></a>Синтаксис

```
<incomingStream>
    filter(
        <conditionalExpression>
    ) ~> <filterTransformationName>
```

### <a name="example"></a>Пример

Ниже приведен пример преобразования «Условное разбиение» с именем `FilterBefore1960`, которое принимает во входном потоке `CleanData`. Условие фильтра — это `year <= 1960` выражения.

В интерфейсе фабрики данных это преобразование выглядит как на изображении ниже:

![Преобразование «фильтр»](media/data-flow/filter1.png "Преобразование «фильтр»")

Сценарий потока данных для этого преобразования находится в следующем фрагменте кода:

```
CleanData
    filter(
        year <= 1960
    ) ~> FilterBefore1960

```

## <a name="next-steps"></a>Дальнейшие действия

Фильтрация столбцов с помощью [преобразования «выбор](data-flow-select.md) »
