---
title: Основные понятия JSON потока данных сопоставления фабрики данных Azure
description: Поток данных сопоставления фабрики данных имеет встроенные возможности для обработки документов JSON с иерархиями
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.date: 08/30/2019
ms.openlocfilehash: cde42dda47d54c03c50895bc625f99c9350b53e3
ms.sourcegitcommit: d470d4e295bf29a4acf7836ece2f10dabe8e6db2
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/02/2019
ms.locfileid: "70210693"
---
# <a name="mapping-data-flow-json-handling"></a>Обработка потока данных с сопоставлением JSON

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

## <a name="creating-json-structures-in-expression-editor"></a>Создание структур JSON в редакторе выражений
### <a name="derived-column-transformation"></a>Преобразование «Производный столбец»
Добавление сложного столбца в поток данных упрощается с помощью редактора выражений производного столбца. После добавления нового столбца и открытия редактора существует два варианта: введите структуру JSON вручную или используйте пользовательский интерфейс для добавления подстолбцов в интерактивном режиме.

#### <a name="interactive-ui-json-design"></a>Проектирование интерактивного пользовательского интерфейса JSON
В боковой области Схема вывода новые подстолбцы можно добавить с помощью `+` меню: ![Добавить подстолбец](media/data-flow/addsubcolumn.png "Добавить подстолбец")

После этого новые столбцы и подстолбцы можно добавлять таким же образом. Для каждого несложного поля выражение можно добавить в редакторе выражений справа.

![Сложный столбец](media/data-flow/complexcolumn.png "Сложный столбец")

#### <a name="manual-json-design"></a>Ручная разработка JSON
Чтобы вручную добавить структуру JSON, добавьте новый столбец и введите выражение в редакторе. Выражение имеет следующий общий формат:
```
@(
    field1=0,
    field2=@(
        field1=0
    )
)
```
Если это выражение было указано для столбца с именем "Комплексколумн", оно будет записано в приемник как следующий код JSON:
```
{
    "complexColumn": {
        "field1": 0,
        "field2": {
            "field1": 0
        }
    }
}
```

#### <a name="sample-manual-dsl"></a>Пример ручного DSL
```
@(
    title=Title,
    firstName=FirstName,
    middleName=MiddleName,
    lastName=LastName,
    suffix=Suffix,
    contactDetails=@(
        email=EmailAddress,
        phone=Phone
    ),
    address=@(
        line1=AddressLine1,
        line2=AddressLine2,
        city=City,
        state=StateProvince,
        country=CountryRegion,
        postCode=PostalCode
    ),
    ids=[
        toString(CustomerID), toString(AddressID), rowguid
    ]
)
```

## <a name="source-format-options"></a>Параметры формата источника
### <a name="default"></a>Значение по умолчанию
```
{ "json": "record 1" }
{ "json": "record 2" }
{ "json": "record 3" }
```

### <a name="single-document"></a>Один документ
* Вариант 1
```
[
    {
        "json": "record 1"
    },
    {
        "json": "record 2"
    },
    {
        "json": "record 3"
    }
]
```

* Вариант 2
```
File1.json
{
    "json": "record 1"
}
File2.json
{
    "json": "record 2"
}
File3.json
{
    "json": "record 3"
}
```

### <a name="unquoted-column-names"></a>Имена столбцов без кавычек
```
{ json: "record 1" }
{ json: "record 2" }
{ json: "record 3" }
```

### <a name="has-comments"></a>Содержит комментарии
```
{ "json": /** comment **/ "record 1" }
{ "json": "record 2" }
{ /** comment **/ "json": "record 3" }
```

### <a name="single-quoted"></a>Одинарные кавычки
```
{ 'json': 'record 1' }
{ 'json': 'record 2' }
{ 'json': 'record 3' }
```

### <a name="backslash-escaped"></a>Обратная косая черта
```
{ "json": "record 1" }
{ "json": "\} \" \' \\ \n \\n record 2" }
{ "json": "record 3" }
```

# <a name="higher-order-functions"></a>Функции высшего порядка
## <a name="filter"></a>filter
Фильтрует элементы из массива, который не соответствует указанному предикату. Фильтр принимает ссылку на один элемент в функции предиката как #item.

### <a name="examples"></a>Примеры
```
filter([1, 2, 3, 4], #item > 2) => [3, 4]
filter(['a', 'b', 'c', 'd'], #item == 'a' || #item == 'b') => ['a', 'b']
```

## <a name="map"></a>карта
Сопоставляет каждый элемент массива с новым элементом, используя предоставленное выражение. Функция map принимает в качестве #item ссылку на один элемент в функции выражения.

### <a name="examples"></a>Примеры
```
map([1, 2, 3, 4], #item + 2) => [3, 4, 5, 6]
map(['a', 'b', 'c', 'd'], #item + '_processed') => ['a_processed', 'b_processed', 'c_processed', 'd_processed']
```

## <a name="reduce"></a>свести
Накапливает элементы в массиве. Функция Reduce принимает ссылку на накапливаемое и один элемент в первой функции expression как #acc и #item и ждет, что результирующее значение #resultся для использования во второй функции выражения.

### <a name="examples"></a>Примеры
```
reduce([1, 2, 3, 4], 0, #acc + #item, #result) => 10
reduce(['1', '2', '3', '4'], '0', #acc + #item, #result) => '01234'
reduce([1, 2, 3, 4], 0, #acc + #item, #result + 15) => 25
```

## <a name="sort"></a>sort
Сортирует массив с помощью предоставленной функции предиката. Функция Sort принимает ссылку на два последовательных элемента в функции выражения как #item1 и #item2.

### <a name="examples"></a>Примеры
```
sort([4, 8, 2, 3], compare(#item1, #item2)) => [2, 3, 4, 8]
sort(['a3', 'b2', 'c1'],
        iif(right(#item1, 1) >= right(#item2, 1), 1, -1)) => ['c1', 'b2', 'a3']
sort(['a3', 'b2', 'c1'],
        iif(#item1 >= #item2, 1, -1)) => ['a3', 'b2', 'c1']
```

## <a name="contains"></a>содержит
Возвращает значение true, если любой элемент в предоставленном массиве принимает значение true в указанном предикате. Функция Contains принимает ссылку на один элемент в функции предиката как #item.

### <a name="examples"></a>Примеры
```
contains([1, 2, 3, 4], #item == 3) => true
contains([1, 2, 3, 4], #item > 5) => false
```

## <a name="next-steps"></a>Следующие шаги

* [Использование преобразования «Производный столбец» для создания иерархических структур](data-flow-derived-column.md)
