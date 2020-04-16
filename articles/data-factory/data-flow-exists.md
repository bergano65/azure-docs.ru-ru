---
title: Существует трансформация в картировании потока данных
description: Проверка существующих строк с использованием существующей трансформации в картографировании Data Factory Azure
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 10/16/2019
ms.openlocfilehash: 512d9a688d7f808056a91b5bc0484c378af33948
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81413817"
---
# <a name="exists-transformation-in-mapping-data-flow"></a>Существует трансформация в картировании потока данных

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Преобразование существует — это преобразование фильтрации строк, которое проверяет наличие данных в другом источнике или потоке. Поток вывода включает в себя все строки в левом потоке, которые либо существуют, либо не существуют в правом потоке. Существующие преобразования похож ```SQL WHERE EXISTS``` ```SQL WHERE NOT EXISTS```на и .

## <a name="configuration"></a>Конфигурация

1. Выберите, какой поток данных вы проверяете на наличие в **правильном потоке.**
1. Укажите, ищите ли вы данные для существования или нет в настройках **типа Exist.**
1. Выберите, хотите ли вы **пользовательское выражение.**
1. Выберите ключевые столбцы, которые вы хотите сравнить, поскольку ваши существующие условия. По умолчанию поток данных ищет равенство между одним столбцом в каждом потоке. Для сравнения с помощью вычисленных значений нависнете над выпадением столбца и выберите **вычисленный столбец.**

![Существующие настройки](media/data-flow/exists.png "существует 1")

### <a name="multiple-exists-conditions"></a>Несколько существует условия

Чтобы сравнить несколько столбцов из каждого потока, добавьте новое условие существует, нажав значок plus рядом с существующей строкой. Каждое дополнительное условие соединено "и" заявлением. Сравнение двух столбцов совпадает со следующим выражением:

`source1@column1 == source2@column1 && source1@column2 == source2@column2`

### <a name="custom-expression"></a>Пользовательское выражение

Чтобы создать выражение свободной формы, содержащее операторов, не "и" и "равных", выберите поле **пользовательского выражения.** Введите пользовательское выражение через строитель выражения потока данных, нажав на синий ящик.

![Существует пользовательские настройки](media/data-flow/exists1.png "существует пользовательский")

## <a name="data-flow-script"></a>Скрипт потока данных

### <a name="syntax"></a>Синтаксис

```
<leftStream>, <rightStream>
    exists(
        <conditionalExpression>,
        negate: { true | false },
        broadcast: {'none' | 'left' | 'right' | 'both'}
    ) ~> <existsTransformationName>
```

### <a name="example"></a>Пример

Ниже приведена трансформация `checkForChanges` существует, которая `NameNorm2` занимает `TypeConversions`левый поток и правый поток.  Условие существует является `NameNorm2@EmpID == TypeConversions@EmpID && NameNorm2@Region == DimEmployees@Region` выражением, которое `EMPID` `Region` возвращается верно, если и столбцы в каждом потоке совпадает. Как мы проверяем на `negate` существование, является ложным. Мы не позволяем любое вещание `broadcast` в `'none'`вкладке оптимизации так имеет значение .

В Data Factory UX это преобразование выглядит как приведенное ниже изображение:

![Существует пример](media/data-flow/exists-script.png "Существует пример")

Сценарий потока данных для этого преобразования находится в фрагменте ниже:

```
NameNorm2, TypeConversions
    exists(
        NameNorm2@EmpID == TypeConversions@EmpID && NameNorm2@Region == DimEmployees@Region,
        negate:false,
        broadcast: 'none'
    ) ~> checkForChanges
```

## <a name="next-steps"></a>Дальнейшие действия

Аналогичные преобразования [Lookup](data-flow-lookup.md) и [присоединиться](data-flow-join.md).
