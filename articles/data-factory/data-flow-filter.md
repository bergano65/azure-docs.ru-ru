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
ms.openlocfilehash: 32a40f3d4da93318c6d2ec25295c89a7b53141e4
ms.sourcegitcommit: 5e49f45571aeb1232a3e0bd44725cc17c06d1452
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/17/2020
ms.locfileid: "81606425"
---
# <a name="filter-transformation-in-mapping-data-flow"></a>Преобразование фильтра в потоке данных отображения

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Фильтр преобразует позволяет фильтровать строки на основе состояния. Выходной поток включает в себя все строки, соответствующие состоянию фильтрации. Преобразование фильтра аналогично положению WHERE в S'L.

## <a name="configuration"></a>Конфигурация

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
