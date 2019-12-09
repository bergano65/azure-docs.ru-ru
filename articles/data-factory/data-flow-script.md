---
title: Сценарий потока данных сопоставления
description: Общие сведения о языке кода программной части сценария потока данных фабрики данных
author: kromerm
ms.author: nimoolen
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 11/10/2019
ms.openlocfilehash: d861a4355158dfe18ac3aa40a7f98dc11ebda90b
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/08/2019
ms.locfileid: "74930257"
---
# <a name="data-flow-script-dfs"></a>Сценарий потока данных (DFS)

Сценарий потока данных (DFS) — это базовые метаданные, аналогичные языку программирования, используемому для выполнения преобразований, включенных в поток данных сопоставления. Каждое преобразование представлено рядом свойств, которые предоставляют необходимые сведения для правильного выполнения задания. Сценарий отображается и редактируется из ADF. для этого нажмите кнопку "Скрипт" на верхней ленте пользовательского интерфейса браузера.

![Кнопка скрипта](media/data-flow/scriptbutton.png "Кнопка скрипта")

Например, `allowSchemaDrift: true,` в преобразовании «источник» указывает службе включать все столбцы из исходного набора данных в потоке данных, даже если они не включены в проекцию схемы.

## <a name="use-cases"></a>Примеры использования
DFS автоматически создается интерфейсом пользователя. Можно нажать кнопку Скрипт, чтобы просмотреть и настроить скрипт. Кроме того, можно создавать скрипты за пределами пользовательского интерфейса ADF, а затем передавать их в командлет PowerShell. При отладке сложных потоков данных может оказаться проще проверять код программной части сценария, а не проверять представление графа пользовательского интерфейса для потоков.

Ниже приведены примеры использования.
- Программное создание многих похожих потоков данных, т. е. потоков данных с отметкой.
- Сложные выражения, которые трудно управлять в пользовательском интерфейсе или которые могут приводить к проблемам проверки.
- Отладка и более эффективное понимание различных ошибок, возвращаемых во время выполнения.

При создании скрипта потока данных для использования с PowerShell или API необходимо свернуть форматированный текст в одну строку. Символы табуляции и новой строки можно размещать в виде escape-символов. Но текст должен быть отформатирован в соответствии со свойством JSON. В нижней части пользовательского интерфейса редактора сценариев есть кнопка, которая будет форматировать сценарий как единую строку.

![Кнопка "Копировать"](media/data-flow/copybutton.png "кнопку "Копировать"")

## <a name="how-to-add-transforms"></a>Добавление преобразований
Добавление преобразований требует выполнения трех основных шагов: Добавление основных данных преобразования, перенаправление входного потока и перенаправление потока вывода. В качестве примера это можно увидеть проще всего.
Предположим, что начнем с простого источника данных приемника, как в следующем примере:

```
source(output(
        movieId as string,
        title as string,
        genres as string
    ),
    allowSchemaDrift: true,
    validateSchema: false) ~> source1
source1 sink(allowSchemaDrift: true,
    validateSchema: false) ~> sink1
```

Если мы решили добавить преобразование «Производный», сначала необходимо создать основной текст преобразования, имеющий простое выражение для добавления нового столбца в верхнем регистре с именем `upperCaseTitle`:
```
derive(upperCaseTitle = upper(title)) ~> deriveTransformationName
```

Затем мы принимаем существующую DFS и добавим преобразование:
```
source(output(
        movieId as string,
        title as string,
        genres as string
    ),
    allowSchemaDrift: true,
    validateSchema: false) ~> source1
derive(upperCaseTitle = upper(title)) ~> deriveTransformationName
source1 sink(allowSchemaDrift: true,
    validateSchema: false) ~> sink1
```

Теперь мы перенаправляем входящий поток, определив, какое преобразование должно быть поступило после (в данном случае `source1`) и скопировав имя потока в новое преобразование:
```
source(output(
        movieId as string,
        title as string,
        genres as string
    ),
    allowSchemaDrift: true,
    validateSchema: false) ~> source1
source1 derive(upperCaseTitle = upper(title)) ~> deriveTransformationName
source1 sink(allowSchemaDrift: true,
    validateSchema: false) ~> sink1
```

Наконец, мы выведем преобразование, которое мы хотим сделать после этого нового преобразования, и замените входной поток (в данном случае `sink1`) на имя выходного потока нашего нового преобразования:
```
source(output(
        movieId as string,
        title as string,
        genres as string
    ),
    allowSchemaDrift: true,
    validateSchema: false) ~> source1
source1 derive(upperCaseTitle = upper(title)) ~> deriveTransformationName
deriveTransformationName sink(allowSchemaDrift: true,
    validateSchema: false) ~> sink1
```

## <a name="dfs-fundamentals"></a>Основы DFS
DFS состоит из ряда подключенных преобразований, включая источники, приемники и различные другие, которые могут добавлять новые столбцы, фильтровать данные, объединять данные и многое другое. Обычно сценарий начинается с одного или нескольких источников, за которым следует много преобразований и заканчивается одним или несколькими приемниками.

Все источники имеют одинаковую базовую конструкцию:
```
source(
  source properties
) ~> source_name
```

Например, простой источник с тремя столбцами (Мовиеид, Title, жанры) будет выглядеть следующим образом:
```
source(output(
        movieId as string,
        title as string,
        genres as string
    ),
    allowSchemaDrift: true,
    validateSchema: false) ~> source1
```

Все преобразования, отличные от источников, имеют одну базовую конструкцию:
```
name_of_incoming_stream transformation_type(
  properties
) ~> new_stream_name
```

Например, простое производное преобразование, которое принимает столбец (заголовок) и перезаписывает его версией в верхнем регистре, будет выглядеть следующим образом:
```
source1 derive(
  title = upper(title)
) ~> derive1
```

И приемник без схемы будет просто:
```
derive1 sink(allowSchemaDrift: true,
    validateSchema: false) ~> sink1
```

## <a name="next-steps"></a>Дальнейшие действия

Проанализируйте потоки данных, начав с помощью [статьи общие сведения о потоках данных](concepts-data-flow-overview.md)
