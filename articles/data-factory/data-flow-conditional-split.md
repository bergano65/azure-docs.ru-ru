---
title: Условная трансформация разделения в потоке данных отображения
description: Разделение данных на различные потоки с помощью условной сплит-трансформации в картографии Data Factory Azure
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 10/16/2019
ms.openlocfilehash: 20def8ca51f21d914e7090999e8284244c5f3ec7
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81416496"
---
# <a name="conditional-split-transformation-in-mapping-data-flow"></a>Условная трансформация разделения в потоке данных отображения

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Условное разделение маршрутов преобразования строк данных в различные потоки на основе условий соответствия. Условная трансформация разделения аналогична структуре принятия решений CASE на языке программирования. Преобразование вычисляет выражения, и на основе результатов направляет строку данных в указанный поток.

## <a name="configuration"></a>Конфигурация

**Сплит на** настроеопределяет, перетекает ли строка данных в первый соответствующий поток или каждый поток, к который он совпадает.

Используйте builder выражения потока данных, чтобы ввести выражение для состояния сплита. Чтобы добавить новое условие, нажмите на значок plus в существующей строке. Поток по умолчанию может быть добавлен также для строк, которые не соответствуют никаким условием.

![условный раскол](media/data-flow/conditionalsplit1.png "условные варианты разделения")

## <a name="data-flow-script"></a>Скрипт потока данных

### <a name="syntax"></a>Синтаксис

```
<incomingStream>
    split(
        <conditionalExpression1>
        <conditionalExpression2>
        ...
        disjoint: {true | false}
    ) ~> <splitTx>@(stream1, stream2, ..., <defaultStream>)
```

### <a name="example"></a>Пример

Ниже приведенпримерк условный `SplitByYear` сплит-трансформация, `CleanData`названная, которая принимает входящий поток. Эта трансформация имеет `year < 1960` `year > 1980`два сплит-условия и . `disjoint`является ложным, поскольку данные переходят в первое условие сопоставления. Каждая строка, соответствующая `moviesBefore1960`первому условию, переходит в поток вывода. Все остальные строки, соответствующие второму условию, переходят в поток `moviesAFter1980`вывода. Все остальные строки протекают через поток `AllOtherMovies`по умолчанию.

В Data Factory UX это преобразование выглядит как приведенное ниже изображение:

![условный раскол](media/data-flow/conditionalsplit1.png "условные варианты разделения")

Сценарий потока данных для этого преобразования находится в фрагменте ниже:

```
CleanData
    split(
        year < 1960,
        year > 1980,
        disjoint: false
    ) ~> SplitByYear@(moviesBefore1960, moviesAfter1980, AllOtherMovies)
```

## <a name="next-steps"></a>Дальнейшие действия

Общие преобразования потока данных, используемые с условным разделением, являются [преобразованием соединения,](data-flow-join.md) [преобразованием поиска](data-flow-lookup.md)и [выбранным преобразованием](data-flow-select.md)
