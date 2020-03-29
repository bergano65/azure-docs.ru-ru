---
title: Преобразование фильтра в потоке данных отображения
description: Отфильтровать строки с помощью преобразования фильтра в картографировании Data Flow
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 10/16/2019
ms.openlocfilehash: 84b935d1646ea4f13d5926fc68797fd8efd03dea
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74930351"
---
# <a name="filter-transformation-in-mapping-data-flow"></a>Преобразование фильтра в потоке данных отображения

Фильтр преобразует позволяет фильтровать строки на основе состояния. Выходной поток включает в себя все строки, соответствующие состоянию фильтрации. Преобразование фильтра аналогично положению WHERE в S'L.

## <a name="configuration"></a>Параметр Configuration

Используйте builder выражения потока данных, чтобы ввести выражение для состояния фильтра. Чтобы открыть коммивояжера, нажмите на синюю коробку. Состояние фильтра должно быть типа булеана. Для получения дополнительной информации о том, как создать выражение, [см.](concepts-data-flow-expression-builder.md)

![Преобразование фильтра](media/data-flow/filter1.png "Преобразование фильтра")

## <a name="data-flow-script"></a>Скрипт потока данных

### <a name="syntax"></a>Синтаксис

```
<incomingStream>
    filter(
        <conditionalExpression>
    ) ~> <filterTransformationName>
```

### <a name="example"></a>Пример

Ниже приведена трансформация `FilterBefore1960` фильтра, которая `CleanData`требует входящего потока. Условие мгновеющего фильтра является выражением. `year <= 1960`

В Data Factory UX это преобразование выглядит как приведенное ниже изображение:

![Преобразование фильтра](media/data-flow/filter1.png "Преобразование фильтра")

Сценарий потока данных для этого преобразования находится в фрагменте ниже:

```
CleanData
    filter(
        year <= 1960
    ) ~> FilterBefore1960

```

## <a name="next-steps"></a>Дальнейшие действия

Отфильтровать столбцы с [выбранным преобразованием](data-flow-select.md)
