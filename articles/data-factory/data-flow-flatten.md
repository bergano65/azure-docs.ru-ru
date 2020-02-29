---
title: Преобразование «Сведение потока данных сопоставления»
description: Преобразование «обработка потока данных» для сопоставления фабрики данных Azure
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/25/2020
ms.openlocfilehash: 415a093fd8a8fbe27e1d240b061548e18f2ca6b6
ms.sourcegitcommit: 1f738a94b16f61e5dad0b29c98a6d355f724a2c7
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/28/2020
ms.locfileid: "78164735"
---
# <a name="azure-data-factory-flatten-transformation"></a>Преобразование "сведение" в фабрике данных Azure

Преобразование «Сведение» можно использовать для сведения значений массива внутри иерархической структуры в новые строки, по сути, денормализацию данных.

![Панель элементов преобразования](media/data-flow/flatten5.png "Панель элементов преобразования")

![Преобразование «Сведение 1»](media/data-flow/flatten7.png "Преобразование «Сведение 1»")

## <a name="unroll-by"></a>Отменить развертывание

Сначала выберите столбец массива, который вы хотите раскрутить, и сведение.

![Параметры преобразования «Сведение»](media/data-flow/flatten1.png "Параметры преобразования «Сведение»")

## <a name="unroll-root"></a>Развернуть корень

По умолчанию ADF выполняет сведение структуры в выбранном выше массиве. Можно также выбрать другую часть иерархии, чтобы отменить развертывание. Параметр "unrollный корень" является необязательным.

## <a name="input-columns"></a>Входные столбцы

Наконец, выберите проекцию новой структуры, основанную на входящих полях, а также нормализованную невыполненную гистограмму.

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
source(output(
        name as string,
        location as string,
        satellites as string[],
        goods as (trade as boolean, customers as string[], orders as (orderId as string, orderTotal as double, shipped as (orderItems as (itemName as string, itemQty as string)[]))[])
    ),
    allowSchemaDrift: true,
    validateSchema: false) ~> source2
source2 foldDown(unroll(goods.orders.shipped.orderItems),
    mapColumn(
        name,
        each(goods.orders, match(type == 'integer')),
        each(goods.orders.shipped.orderItems, match(true())),
        location
    )) ~> Flatten1
Flatten1 sink(allowSchemaDrift: true,
    validateSchema: false,
    skipDuplicateMapInputs: true,
    skipDuplicateMapOutputs: true) ~> sink1
```    

## <a name="next-steps"></a>Следующие шаги

* Используйте [Преобразование «Сведение](data-flow-pivot.md) » для сведения строк в столбцы.
* Используйте [Преобразование отменить сведение](data-flow-unpivot.md) для сведения столбцов в строки.
