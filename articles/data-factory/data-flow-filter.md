---
title: Преобразование фильтра в потоке данных сопоставления фабрики данных Azure
description: Фильтрация строк с помощью преобразования «фильтр» в потоке данных сопоставления фабрики данных Azure
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.date: 10/16/2019
ms.openlocfilehash: 77587d007d9bfcdc1461201f676c79880bd89d6b
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/06/2019
ms.locfileid: "73676174"
---
# <a name="filter-transformation-in-mapping-data-flow"></a>Преобразование «фильтр» в потоке данных сопоставления

Преобразование «фильтр» позволяет фильтровать строки на основе условия. Выходной поток включает все строки, соответствующие условию фильтрации. Преобразование «фильтр» аналогично предложению WHERE в SQL.

## <a name="configuration"></a>Конфигурация

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

Ниже приведен пример преобразования «Условное разбиение» с именем `FilterBefore1960`, которое принимает во входном потоке `CleanData`. Условие фильтра — это `year <= 1960`выражения.

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
