---
title: Преобразование «фильтр» в потоке данных сопоставления
description: Фильтрация строк с помощью преобразования «фильтр» в потоке данных сопоставления фабрики данных Azure
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 05/26/2020
ms.openlocfilehash: 8189228d6707812fb943e9925dc2bbf1b6da4972
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/02/2020
ms.locfileid: "84112809"
---
# <a name="filter-transformation-in-mapping-data-flow"></a>Преобразование «фильтр» в потоке данных сопоставления

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Преобразование «фильтр» позволяет фильтровать строки на основе условия. Выходной поток включает все строки, соответствующие условию фильтрации. Преобразование «фильтр» аналогично предложению WHERE в SQL.

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4xnxN]

## <a name="configuration"></a>Параметр Configuration

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

Ниже приведен пример преобразования фильтра с именем `FilterBefore1960` , которое принимается во входящем потоке `CleanData` . Условие фильтра является выражением `year <= 1960` .

В интерфейсе Фабрики данных это преобразование выглядит следующим образом:

![Преобразование «фильтр»](media/data-flow/filter1.png "Преобразование «фильтр»")

Скрипт потока данных для этого преобразования представлен в следующем фрагменте кода:

```
CleanData
    filter(
        year <= 1960
    ) ~> FilterBefore1960

```

## <a name="next-steps"></a>Дальнейшие действия

Фильтрация столбцов с помощью [преобразования «выбор](data-flow-select.md) »
