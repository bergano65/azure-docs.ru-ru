---
title: Фильтрация событий для службы "Сетка событий Azure"
description: В статье описывается, как фильтровать события при создании подписки на службу "Сетка событий Azure".
ms.topic: conceptual
ms.date: 12/03/2020
ms.openlocfilehash: bc3e84037693fcd909961ba409871d947ef1de7d
ms.sourcegitcommit: 16c7fd8fe944ece07b6cf42a9c0e82b057900662
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/03/2020
ms.locfileid: "96574912"
---
# <a name="understand-event-filtering-for-event-grid-subscriptions"></a>Общие сведения о фильтрации событий для подписок на службу "Сетка событий Azure"

В статье описываются различные способы фильтрации событий, отправляемых в конечную точку. При создании подписки на события предлагается три варианта фильтрации:

* Типы событий
* по тому, что находится в начале или конце темы;
* с помощью расширенных полей и операторов.

## <a name="event-type-filtering"></a>Фильтрация по типу события

По умолчанию все [типы событий](event-schema.md) для источника события отправляются в конечную точку. Можно сделать так, чтобы в конечную точку отправлялись только определенные типы событий. Например, можно получать уведомления об обновлениях ресурсов, но не получать уведомления о других операциях, таких как удаление. В этом случае фильтрация выполняется по типу события `Microsoft.Resources.ResourceWriteSuccess`. Предоставьте массив с типами событий или укажите `All`, чтобы получать все типы событий для источника событий.

При фильтрации по типу события используется такой синтаксис JSON:

```json
"filter": {
  "includedEventTypes": [
    "Microsoft.Resources.ResourceWriteFailure",
    "Microsoft.Resources.ResourceWriteSuccess"
  ]
}
```

## <a name="subject-filtering"></a>Фильтрация по теме

Для простой фильтрации по теме укажите начальное или конечное значение для этой темы. Например, можно указать, что тема заканчивается на `.txt`, чтобы получать только события, связанные с загрузкой текстового файла в учетную запись хранения. Или можно выполнять фильтрацию по теме, начинающейся с `/blobServices/default/containers/testcontainer`, чтобы получать все события для этого контейнера, но не для других контейнеров в этой учетной записи хранения.

При публикации событий в пользовательских разделах создавайте темы для событий, чтобы подписчикам было проще определить, представляет ли событие для них интерес. Подписчики могут использовать свойство темы для фильтрации и маршрутизации событий. Можно добавить путь к расположению, в котором произошло событие, чтобы подписчики могли выполнять фильтрацию по сегментам этого пути. Путь позволяет подписчикам сузить или расширить область фильтрации. Например, если указать в теме трехсегментный путь, такой как `/A/B/C`, подписчики смогут выполнить фильтрацию по первому сегменту `/A`, чтобы получить широкий набор событий. Эти подписчики получат события с такими темами, как `/A/B/C` или `/A/D/E`. Другие подписчики могут выполнить фильтрацию по `/A/B`, чтобы получить более узкий набор событий.

Для фильтрации по теме используется следующий синтаксис JSON:

```json
"filter": {
  "subjectBeginsWith": "/blobServices/default/containers/mycontainer/log",
  "subjectEndsWith": ".jpg"
}

```

## <a name="advanced-filtering"></a>Расширенная фильтрация

Вариант расширенной фильтрации позволяет выполнять фильтрацию по значениям в полях данных и указывать оператор сравнения. При расширенной фильтрации можно указать следующее:

* operatorType — тип сравнения;
* ключ — поле в данных события, которое используется для фильтрации (это может быть число, логическое значение или строка);
* Values — значение или значения, сравниваемые с ключом.

Если указать один фильтр с несколькими значениями, то выполняется операция **или** , поэтому значением ключевого поля должно быть одно из следующих значений. Например:

```json
"advancedFilters": [
    {
        "operatorType": "StringContains",
        "key": "Subject",
        "values": [
            "/providers/microsoft.devtestlab/",
            "/providers/Microsoft.Compute/virtualMachines/"
        ]
    }
]
```

Если задано несколько различных фильтров, операция **и** выполняется, поэтому должно выполняться каждое условие фильтра. Ниже приведен пример. 

```json
"advancedFilters": [
    {
        "operatorType": "StringContains",
        "key": "Subject",
        "values": [
            "/providers/microsoft.devtestlab/"
        ]
    },
    {
        "operatorType": "StringContains",
        "key": "Subject",
        "values": [
            "/providers/Microsoft.Compute/virtualMachines/"
        ]
    }
]
```

### <a name="operators"></a>Операторы

Доступные для **чисел** операторы:

* NumberGreaterThan;
* NumberGreaterThanOrEquals;
* NumberLessThan;
* NumberGreaterThanOrEquals;
* NumberIn;
* NumberNotIn.

Ниже приведен допустимый оператор для **логических** значений. 
- BoolEquals

Ниже приведены доступные операторы для **строк** .

* StringContains;
* StringBeginsWith;
* StringEndsWith;
* StringIn;
* StringNotIn.

Все сравнения строк **не** учитывают регистр.

> [!NOTE]
> Если JSON события не содержит ключ расширенного фильтра, то Filter еваулатед, как **не соответствует** , для следующих операторов: 
> - NumberGreaterThan;
> - NumberGreaterThanOrEquals;
> - NumberLessThan;
> - NumberGreaterThanOrEquals;
> - NumberIn;
> - BoolEquals
> - StringContains;
> - StringBeginsWith;
> - StringEndsWith;
> - StringIn;
> 
>Фильтр еваулатед **в соответствии со** следующими операторами:
> - NumberNotIn.
> - StringNotIn.

### <a name="key"></a>Ключ

Для событий в схеме "Сетка событий" используются следующие значения для ключа:

* ID
* Раздел
* Тема
* EventType
* DataVersion;
* дата события (например Data.key1).

Для событий в схеме "События в облаке" используются следующие значения для ключа:

* EventId
* Источник
* EventType
* EventTypeVersion;
* дата события (например Data.key1).

Для пользовательских схем ввода используются поля данных событий (например, Data.key1).

### <a name="values"></a>Значения

Допустимые значения:

* number
* строка
* Логическое
* массиве

### <a name="limitations"></a>Ограничения

Расширенная фильтрация имеет такие ограничения:

* 5 дополнительных фильтров и 25 значений фильтров по всем подпискам на одну подписку на сетку событий
* 512 знаков для значения строки;
* пять значений для операторов **in** и **not in**;
* Ключи с символом **`.` (точкой)** . Пример: `http://schemas.microsoft.com/claims/authnclassreference` или `john.doe@contoso.com`. В настоящее время escape-символы в ключах не поддерживаются. 

Один ключ можно использовать в нескольких фильтрах.

### <a name="examples"></a>Примеры

### <a name="stringcontains"></a>StringContains;

```json
"advancedFilters": [{
    "operatorType": "StringContains",
    "key": "data.key1",
    "values": [
        "microsoft", 
        "azure"
    ]
}]
```

### <a name="stringbeginswith"></a>StringBeginsWith;

```json
"advancedFilters": [{
    "operatorType": "StringBeginsWith",
    "key": "data.key1",
    "values": [
        "event", 
        "grid"
    ]
}]
```

### <a name="stringendswith"></a>StringEndsWith;

```json
"advancedFilters": [{
    "operatorType": "StringEndsWith",
    "key": "data.key1",
    "values": [
        "jpg", 
        "jpeg", 
        "png"
    ]
}]
```

### <a name="stringin"></a>StringIn;

```json
"advancedFilters": [{
    "operatorType": "StringIn",
    "key": "data.key1",
    "values": [
        "exact", 
        "string", 
        "matches"
    ]
}]
```

### <a name="stringnotin"></a>StringNotIn.

```json
"advancedFilters": [{
    "operatorType": "StringNotIn",
    "key": "data.key1",
    "values": [
        "aws", 
        "bridge"
    ]
}]
```

### <a name="numberin"></a>NumberIn;

```json

"advancedFilters": [{
    "operatorType": "NumberIn",
    "key": "data.counter",
    "values": [
        5,
        1
    ]
}]

```

### <a name="numbernotin"></a>NumberNotIn.

```json
"advancedFilters": [{
    "operatorType": "NumberNotIn",
    "key": "data.counter",
    "values": [
        41,
        0,
        0
    ]
}]
```

### <a name="numberlessthan"></a>NumberLessThan;

```json
"advancedFilters": [{
    "operatorType": "NumberLessThan",
    "key": "data.counter",
    "value": 100
}]
```

### <a name="numbergreaterthan"></a>NumberGreaterThan;

```json
"advancedFilters": [{
    "operatorType": "NumberGreaterThan",
    "key": "data.counter",
    "value": 20
}]
```

### <a name="numberlessthanorequals"></a>NumberGreaterThanOrEquals;

```json
"advancedFilters": [{
    "operatorType": "NumberLessThanOrEquals",
    "key": "data.counter",
    "value": 100
}]
```

### <a name="numbergreaterthanorequals"></a>NumberGreaterThanOrEquals;

```json
"advancedFilters": [{
    "operatorType": "NumberGreaterThanOrEquals",
    "key": "data.counter",
    "value": 30
}]
```

### <a name="boolequals"></a>BoolEquals

```json
"advancedFilters": [{
    "operatorType": "BoolEquals",
    "key": "data.isEnabled",
    "value": true
}]
```


## <a name="next-steps"></a>Дальнейшие действия

* См. дополнительные сведения о фильтрации событий для Сетки событий с помощью [PowerShell и Azure CLI](how-to-filter-events.md).
* Сведения о том, как быстро приступить к использованию службы "Сетка событий", см. в разделе [Создание и перенаправление пользовательского события со службой "Сетка событий Azure"](custom-event-quickstart.md).
