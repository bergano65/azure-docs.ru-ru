---
title: Преобразование EXISTS в потоке данных сопоставления
description: Проверка наличия существующих строк с помощью преобразования "EXISTS" в потоке данных сопоставления фабрики данных Azure
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 10/16/2019
ms.openlocfilehash: efcc45dcf3565b70305323701810c49c4a720394
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/08/2019
ms.locfileid: "74930412"
---
# <a name="exists-transformation-in-mapping-data-flow"></a>Преобразование EXISTS в потоке данных сопоставления

Преобразование «EXISTS» — это преобразование «Фильтрация строк», которое проверяет, существуют ли ваши данные в другом источнике или потоке. Выходной поток включает все строки в левом потоке, которые либо существуют, либо не существуют в правильном потоке. Преобразование EXISTS похоже на ```SQL WHERE EXISTS``` и ```SQL WHERE NOT EXISTS```.

## <a name="configuration"></a>Настройка

1. Выберите поток данных для проверки существования в правильном раскрывающемся списке **потока** .
1. Укажите, нужно ли, чтобы данные были уже существуют или не существовали в параметре **типа EXISTS** .
1. Выберите, нужно ли использовать **пользовательское выражение**.
1. Выберите ключевые столбцы, которые необходимо сравнить как условия существования. По умолчанию поток данных ищет равенство между двумя столбцами в каждом потоке. Для сравнения с помощью вычисляемого значения наведите указатель мыши на раскрывающийся список столбцов и выберите **вычисляемый столбец**.

![Параметры EXISTS](media/data-flow/exists.png "Существует 1")

### <a name="multiple-exists-conditions"></a>Условия с несколькими условиями существования

Чтобы сравнить несколько столбцов из каждого потока, добавьте новое условие EXISTS, щелкнув значок «плюс» рядом с существующей строкой. Каждое дополнительное условие соединено оператором AND. Сравнение двух столбцов аналогично приведенному ниже выражению.

`source1@column1 == source2@column1 && source1@column2 == source2@column2`

### <a name="custom-expression"></a>Пользовательское выражение

Чтобы создать выражение произвольной формы, содержащее операторы, отличные от "and" и "Equals to", выберите поле **настраиваемого выражения** . Введите пользовательское выражение с помощью построителя выражений потока данных, щелкнув синюю рамку.

![Пользовательские параметры существуют](media/data-flow/exists1.png "существует настраиваемый")

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

Ниже приведен пример преобразования Exists с именем `checkForChanges`, которое принимает левый поток `NameNorm2` и правый поток `TypeConversions`.  Условие EXISTS является выражением `NameNorm2@EmpID == TypeConversions@EmpID && NameNorm2@Region == DimEmployees@Region`, которое возвращает значение true, если в каждом потоке совпадают столбцы `EMPID` и `Region`. После проверки существования `negate` имеет значение false. На вкладке "оптимизация" не включается широковещательная рассылка, поэтому `broadcast` имеет значение `'none'`.

В интерфейсе фабрики данных это преобразование выглядит как на изображении ниже:

![Пример EXISTS](media/data-flow/exists-script.png "Пример EXISTS")

Сценарий потока данных для этого преобразования находится в следующем фрагменте кода:

```
NameNorm2, TypeConversions
    exists(
        NameNorm2@EmpID == TypeConversions@EmpID && NameNorm2@Region == DimEmployees@Region,
        negate:false,
        broadcast: 'none'
    ) ~> checkForChanges
```

## <a name="next-steps"></a>Дальнейшие действия

Аналогичные преобразования: [Поиск](data-flow-lookup.md) и [соединение](data-flow-join.md).
