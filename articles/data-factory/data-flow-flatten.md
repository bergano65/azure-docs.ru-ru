---
title: Разглашение в потоке карт данных
description: Денормализация иерархических данных с помощью сглаженной трансформации
author: kromerm
ms.author: makromer
ms.review: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.date: 03/09/2020
ms.openlocfilehash: a0e75957a0ab49394dab56f2b7fb847dee4b43cb
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81413679"
---
# <a name="flatten-transformation-in-mapping-data-flow"></a>Разглашение в потоке карт данных

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Используйте сплющенное преобразование, чтобы взять значения массива внутри иерархических структур, таких как JSON, и развернуть их в отдельные строки. Этот процесс известен как денормализация.

## <a name="configuration"></a>Конфигурация

Преобразование сглаженого содержит следующие параметры конфигурации

![Плоские настройки](media/data-flow/flatten1.png "Плоские настройки")

### <a name="unroll-by"></a>Развернутьпось

Выберите массив для разворачивать. Данные вывода будут иметь одну строку на единицу в каждом массиве. Если разворачивание массива в строке ввода является недействительным или пустым, будет один строка вывода с развернутыми значениями как null.

### <a name="unroll-root"></a>Корень разворачивания

По умолчанию преобразование сглаженого разворачивает массив в верхнюю часть иерархии, в ней он существует. Можно по желанию выбрать массив в качестве корня разворачивания. Корень разворачивания должен быть массивом сложных объектов, которые либо являются, либо содержат разворачивание по массиву. При выборе корня разворачивания данные вывода будут содержать по крайней мере одну строку на элементы в корне разворачивания. Если строка ввода не имеет элементов в корне разворачивания, она будет удалена из данных вывода. Выбор корня разворачивания всегда будет вывешным меньше или равное количество строк, чем поведение по умолчанию.

### <a name="flatten-mapping"></a>Плоский отображение

Подобно выбранной трансформации, выберите проекцию новой структуры из входящих полей и денормализации массива. Если отображается денормализованный массив, то выходной столбец будет быть тем же типом данных, что и массив. Если разворачивание массива по массиву представляет собой массив сложных объектов, содержащих подреймы, отображение элемента этого subarry выведет массив.

Обратитесь к вкладке проверки и предварительному просмотру данных для проверки вывода отображения.

## <a name="examples"></a>Примеры

Обратитесь к следующему объекту JSON для приведенных ниже примеров сплющеной трансформации

``` json
{
  "name":"MSFT","location":"Redmond", "satellites": ["Bay Area", "Shanghai"],
  "goods": {
    "trade":true, "customers":["government", "distributer", "retail"],
    "orders":[
        {"orderId":1,"orderTotal":123.34,"shipped":{"orderItems":[{"itemName":"Laptop","itemQty":20},{"itemName":"Charger","itemQty":2}]}},
        {"orderId":2,"orderTotal":323.34,"shipped":{"orderItems":[{"itemName":"Mice","itemQty":2},{"itemName":"Keyboard","itemQty":1}]}}
    ]}}
{"name":"Company1","location":"Seattle", "satellites": ["New York"],
  "goods":{"trade":false, "customers":["store1", "store2"],
  "orders":[
      {"orderId":4,"orderTotal":123.34,"shipped":{"orderItems":[{"itemName":"Laptop","itemQty":20},{"itemName":"Charger","itemQty":3}]}},
      {"orderId":5,"orderTotal":343.24,"shipped":{"orderItems":[{"itemName":"Chair","itemQty":4},{"itemName":"Lamp","itemQty":2}]}}
    ]}}
{"name": "Company2", "location": "Bellevue",
  "goods": {"trade": true, "customers":["Bank"], "orders": [{"orderId": 4, "orderTotal": 123.34}]}}
{"name": "Company3", "location": "Kirkland"}
```

### <a name="no-unroll-root-with-string-array"></a>Нет разворачивания корня с массивом строки

| Развернутьпось | Корень разворачивания | Проекция |
| --------- | ----------- | ---------- |
| goods.customers | None | name <br> клиент и товар. |

#### <a name="output"></a>Выходные данные

```
{ 'MSFT', 'government'}
{ 'MSFT', 'distributer'}
{ 'MSFT', 'retail'}
{ 'Company1', 'store'}
{ 'Company1', 'store2'}
{ 'Company2', 'Bank'}
{ 'Company3', null}
```

### <a name="no-unroll-root-with-complex-array"></a>Нет разворачивания корня со сложным массивом

| Развернутьпось | Корень разворачивания | Проекция |
| --------- | ----------- | ---------- |
| goods.orders.shipped.orderItems | None | name <br> orderId и товары.orders.orderId <br> itemName - goods.orders.shipped.orderItems.itemName <br> пунктий и товары.shipped.orderItems.item'ty <br> местоположение и местоположение |

#### <a name="output"></a>Выходные данные

```
{ 'MSFT', 1, 'Laptop', 20, 'Redmond'}
{ 'MSFT', 1, 'Charger', 2, 'Redmond'}
{ 'MSFT', 2, 'Mice', 2, 'Redmond'}
{ 'MSFT', 2, 'Keyboard', 1, 'Redmond'}
{ 'Company1', 4, 'Laptop', 20, 'Seattle'}
{ 'Company1', 4, 'Charger', 3, 'Seattle'}
{ 'Company1', 5, 'Chair', 4, 'Seattle'}
{ 'Company1', 5, 'Lamp', 2, 'Seattle'}
{ 'Company2', 4, null, null, 'Bellevue'}
{ 'Company3', null, null, null, 'Kirkland'}
```

### <a name="same-root-as-unroll-array"></a>Тот же корень, что и массив разворачивания

| Развернутьпось | Корень разворачивания | Проекция |
| --------- | ----------- | ---------- |
| товары.заказы | товары.заказы | name <br> goods.orders.shipped.orderItems.itemName <br> goods.customers <br> location |

#### <a name="output"></a>Выходные данные

```
{ 'MSFT', ['Laptop','Charger'], ['government','distributer','retail'], 'Redmond'}
{ 'MSFT', ['Mice', 'Keyboard'], ['government','distributer','retail'], 'Redmond'}
{ 'Company1', ['Laptop','Charger'], ['store', 'store2'], 'Seattle'}
{ 'Company1', ['Chair', 'Lamp'], ['store', 'store2'], 'Seattle'}
{ 'Company2', null, ['Bank'], 'Bellevue'}
```

### <a name="unroll-root-with-complex-array"></a>Развернуть корень со сложным массивом

| Развернутьпось | Корень разворачивания | Проекция |
| --------- | ----------- | ---------- |
| goods.orders.shipped.orderItem | товары.заказы |name <br> orderId и товары.orders.orderId <br> itemName - goods.orders.shipped.orderItems.itemName <br> пунктий и товары.shipped.orderItems.item'ty <br> местоположение и местоположение |

#### <a name="output"></a>Выходные данные

```
{ 'MSFT', 1, 'Laptop', 20, 'Redmond'}
{ 'MSFT', 1, 'Charger', 2, 'Redmond'}
{ 'MSFT', 2, 'Mice', 2, 'Redmond'}
{ 'MSFT', 2, 'Keyboard', 1, 'Redmond'}
{ 'Company1', 4, 'Laptop', 20, 'Seattle'}
{ 'Company1', 4, 'Charger', 3, 'Seattle'}
{ 'Company1', 5, 'Chair', 4, 'Seattle'}
{ 'Company1', 5, 'Lamp', 2, 'Seattle'}
{ 'Company2', 4, null, null, 'Bellevue'}
```

## <a name="data-flow-script"></a>Скрипт потока данных

### <a name="syntax"></a>Синтаксис

```
<incomingStream>
foldDown(unroll(<unroll cols>),
    mapColumn(
        name,
        each(<array>(type == '<arrayDataType>')),
        each(<array>, match(true())),
        location
    )) ~> <transformationName>
```

### <a name="example"></a>Пример

```
source foldDown(unroll(goods.orders.shipped.orderItems, goods.orders),
    mapColumn(
        name,
        orderId = goods.orders.orderId,
        itemName = goods.orders.shipped.orderItems.itemName,
        itemQty = goods.orders.shipped.orderItems.itemQty,
        location = location
    ),
    skipDuplicateMapInputs: false,
    skipDuplicateMapOutputs: false) 
```    

## <a name="next-steps"></a>Дальнейшие действия

* Используйте [преобразование Pivot](data-flow-pivot.md) для поворота строк к столбикам.
* Используйте [преобразование Unpivot](data-flow-unpivot.md) для поворота столбцов в строки.
