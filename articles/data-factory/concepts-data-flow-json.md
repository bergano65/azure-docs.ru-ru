---
title: JSON в потоке данных сопоставления
description: Поток данных сопоставления фабрики данных Azure имеет встроенные возможности для обработки документов JSON с иерархиями
author: kromerm
ms.author: makromer
ms.review: djpmsft
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 08/30/2019
ms.openlocfilehash: 153c7a1003c68526c960644bebcc4800e92edc3c
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/08/2019
ms.locfileid: "74928327"
---
# <a name="mapping-data-flow-json-handling"></a>Обработка потока данных с сопоставлением JSON

## <a name="creating-json-structures-in-derived-column"></a>Создание структур JSON в производном столбце

К потоку данных можно добавить сложный столбец с помощью построителя выражений производного столбца. В преобразовании «Производный столбец» добавьте новый столбец и откройте построитель выражений, щелкнув синюю рамку. Чтобы сделать столбец сложным, можно вручную ввести структуру JSON или использовать UX для добавления подстолбцов в интерактивном режиме.

### <a name="using-the-expression-builder-ux"></a>Использование UX построителя выражений

На боковой панели выходная схема наведите указатель мыши на столбец и щелкните значок плюса. Выберите **Добавить подстолбец** , чтобы сделать столбец сложным типом.

![Добавить подстолбец](media/data-flow/addsubcolumn.png "Добавить подстолбец")

Таким же образом можно добавлять дополнительные столбцы и подстолбцы. Для каждого несложного поля выражение можно добавить в редакторе выражений справа.

![Сложный столбец](media/data-flow/complexcolumn.png "Сложный столбец")

### <a name="entering-the-json-structure-manually"></a>Ввод структуры JSON вручную

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

#### <a name="sample-manual-script-for-complete-hierarchical-definition"></a>Пример ручного скрипта для полного иерархического определения
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

Использование набора данных JSON в качестве источника в потоке данных позволяет задать пять дополнительных параметров. Эти параметры можно найти в разделе **Параметры JSON** на вкладке **Параметры источника** .  

![Параметры JSON](media/data-flow/json-settings.png "Параметры JSON")

### <a name="default"></a>значение по умолчанию

По умолчанию данные JSON считываются в следующем формате.

```
{ "json": "record 1" }
{ "json": "record 2" }
{ "json": "record 3" }
```

### <a name="single-document"></a>Один документ

Если выбран **один документ** , то при сопоставлении потоки данных считывают один документ JSON из каждого файла. 

``` json
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

Если выбраны **имена столбцов без кавычек** , потоки данных сопоставления СЧИТЫВАЮТ столбцы JSON, которые не заключены в кавычки. 

```
{ json: "record 1" }
{ json: "record 2" }
{ json: "record 3" }
```

### <a name="has-comments"></a>Содержит комментарии

SELECT **содержит комментарии** , если данные JSON имеют комментарии C C++ или Style.

``` json
{ "json": /** comment **/ "record 1" }
{ "json": "record 2" }
{ /** comment **/ "json": "record 3" }
```

### <a name="single-quoted"></a>Одинарные кавычки

Выберите **одинарные кавычки** , если поля и значения JSON используют одинарные кавычки вместо двойных кавычек.

```
{ 'json': 'record 1' }
{ 'json': 'record 2' }
{ 'json': 'record 3' }
```

### <a name="backslash-escaped"></a>Обратная косая черта

Выберите **одинарные кавычки** , если символы обратной косой черты используются для экранирования символов в данных JSON.

```
{ "json": "record 1" }
{ "json": "\} \" \' \\ \n \\n record 2" }
{ "json": "record 3" }
```

## <a name="higher-order-functions"></a>Функции высшего порядка

Функция более высокого порядка — это функция, которая принимает в качестве аргумента одну или несколько функций. Ниже приведен список функций более высокого порядка, поддерживаемых при сопоставлении потоков данных, которые позволяют выполнять операции с массивами.

### <a name="filter"></a>фильтр
Фильтрует элементы из массива, который не соответствует указанному предикату. Фильтр принимает ссылку на один элемент в функции предиката как #item.

#### <a name="examples"></a>Примеры
```
filter([1, 2, 3, 4], #item > 2) => [3, 4]
filter(['a', 'b', 'c', 'd'], #item == 'a' || #item == 'b') => ['a', 'b']
```

### <a name="map"></a>map
Сопоставляет каждый элемент массива с новым элементом, используя предоставленное выражение. Функция map принимает в качестве #item ссылку на один элемент в функции выражения.

#### <a name="examples"></a>Примеры
```
map([1, 2, 3, 4], #item + 2) => [3, 4, 5, 6]
map(['a', 'b', 'c', 'd'], #item + '_processed') => ['a_processed', 'b_processed', 'c_processed', 'd_processed']
```

### <a name="reduce"></a>reduce
Накапливает элементы в массиве. Функция Reduce принимает ссылку на накапливаемое и один элемент в первой функции expression как #acc и #item и ждет, что результирующее значение #resultся для использования во второй функции выражения.

#### <a name="examples"></a>Примеры
```
reduce([1, 2, 3, 4], 0, #acc + #item, #result) => 10
reduce(['1', '2', '3', '4'], '0', #acc + #item, #result) => '01234'
reduce([1, 2, 3, 4], 0, #acc + #item, #result + 15) => 25
```

### <a name="sort"></a>sort
Сортирует массив с помощью предоставленной функции предиката. Функция Sort принимает ссылку на два последовательных элемента в функции выражения как #item1 и #item2.

#### <a name="examples"></a>Примеры
```
sort([4, 8, 2, 3], compare(#item1, #item2)) => [2, 3, 4, 8]
sort(['a3', 'b2', 'c1'],
        iif(right(#item1, 1) >= right(#item2, 1), 1, -1)) => ['c1', 'b2', 'a3']
sort(['a3', 'b2', 'c1'],
        iif(#item1 >= #item2, 1, -1)) => ['a3', 'b2', 'c1']
```

### <a name="contains"></a>contains
Возвращает значение true, если любой элемент в предоставленном массиве принимает значение true в указанном предикате. Функция Contains принимает ссылку на один элемент в функции предиката как #item.

#### <a name="examples"></a>Примеры
```
contains([1, 2, 3, 4], #item == 3) => true
contains([1, 2, 3, 4], #item > 5) => false
```

## <a name="next-steps"></a>Дальнейшие действия

* [Использование преобразования «Производный столбец» для создания иерархических структур](data-flow-derived-column.md)
