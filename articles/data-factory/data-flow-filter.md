---
title: Преобразование «фильтр» в потоке данных сопоставления
description: Фильтрация строк с помощью преобразования «фильтр» в потоке данных сопоставления фабрики данных Azure
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 10/16/2019
ms.openlocfilehash: 32a40f3d4da93318c6d2ec25295c89a7b53141e4
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2020
ms.locfileid: "81606425"
---
# <a name="filter-transformation-in-mapping-data-flow"></a>Преобразование «фильтр» в потоке данных сопоставления

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Преобразование «фильтр» позволяет фильтровать строки на основе условия. Выходной поток включает все строки, соответствующие условию фильтрации. Преобразование «фильтр» аналогично предложению WHERE в SQL.

## <a name="configuration"></a>Конфигурация

Используйте построитель выражений потока данных, чтобы ввести выражение для условия фильтра. Чтобы открыть построитель выражений, щелкните синюю рамку. Условие фильтра должно иметь тип Boolean. Дополнительные сведения о создании выражения см. в документации по [построителю выражений](concepts-data-flow-expression-builder.md) .

![Преобразование «фильтр»](media/data-flow/filter1.png "Преобразование «фильтр»")

## <a name="data-flow-script"></a>Скрипт потока данных

### <a name="syntax"></a>Синтаксис

```
<incomingStream>
    filter(
        <conditionalExpression>
    ) ~> <filterTransformationName>
```

### <a name="example"></a>Пример

Ниже приведен пример преобразования фильтра с именем `FilterBefore1960` , которое принимается во входящем `CleanData`потоке. Условие фильтра является выражением `year <= 1960`.

В интерфейсе фабрики данных это преобразование выглядит как на изображении ниже:

![Преобразование «фильтр»](media/data-flow/filter1.png "Преобразование «фильтр»")

Сценарий потока данных для этого преобразования находится в следующем фрагменте кода:

```
CleanData
    filter(
        year <= 1960
    ) ~> FilterBefore1960

```

## <a name="next-steps"></a>Дальнейшие шаги

Фильтрация столбцов с помощью [преобразования «выбор](data-flow-select.md) »
