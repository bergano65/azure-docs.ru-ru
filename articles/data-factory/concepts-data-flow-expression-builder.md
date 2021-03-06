---
title: Построитель выражений в потоке данных сопоставления
description: Построение выражений с помощью построителя выражений при сопоставлении потоков данных в фабрике данных Azure
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.date: 12/9/2019
ms.openlocfilehash: 1dd782092ce91f7b71a3a2a6f2ed1646ee39a7e0
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/25/2019
ms.locfileid: "75444532"
---
# <a name="build-expressions-in-mapping-data-flow"></a>Выражения сборки в потоке данных сопоставления

В потоке данных сопоставления многие свойства преобразования задаются как выражения. Эти выражения состоят из значений столбцов, параметров, функций, операторов и литералов, результатом вычисления которых является тип данных Spark во время выполнения.

## <a name="open-expression-builder"></a>Открыть построитель выражений

Интерфейс редактирования выражений в службе "Фабрика данных Azure" называется построителем выражений. При вводе логики выражения фабрика данных использует функцию завершения кода [IntelliSense](https://docs.microsoft.com/visualstudio/ide/using-intellisense?view=vs-2019) для выделения, проверки синтаксиса и автозаполнения.

![Построитель выражений](media/data-flow/xpb1.png "Построитель выражений")

В таких преобразованиях, как производный столбец и фильтр, где выражения являются обязательными, откройте построитель выражений, выбрав синее поле выражения.

![Синее поле выражения](media/data-flow/expressionbox.png "Построитель выражений")

При ссылке на столбцы в условии сопоставления или группирования выражение может извлекать значения из столбцов. Чтобы создать выражение, выберите **вычисляемый столбец**.

![Параметр вычисляемого столбца](media/data-flow/computedcolumn.png "Построитель выражений")

В случаях, когда выражение или литеральное значение являются допустимыми входными данными, выберите **добавить динамическое содержимое** , чтобы построить выражение, результатом вычисления которого является литеральное значение.

![Параметр "добавить динамическое содержимое"](media/data-flow/add-dynamic-content.png "Построитель выражений")

## <a name="expression-language-reference"></a>Справочник по языку выражений

Сопоставление потоков данных имеет встроенные функции и операторы, которые можно использовать в выражениях. Список доступных функций см. [в разделе функции выражений в потоке данных сопоставления](data-flow-expression-functions.md).

## <a name="column-names-with-special-characters"></a>Имена столбцов с специальными символами

Если имена столбцов содержат специальные символы или пробелы, заключите имя в фигурные скобки, чтобы ссылаться на них в выражении.

```{[dbo].this_is my complex name$$$}```

## <a name="preview-expression-results"></a>Просмотр результатов выражения

Если включен [режим отладки](concepts-data-flow-debug-mode.md) , можно использовать динамический кластер Spark, чтобы просмотреть выполняемое в данный момент предварительное отображение выражения. При построении логики можно отлаживать выражение в режиме реального времени. 

![Выполняющаяся Предварительная версия](media/data-flow/exp4b.png "Предварительный просмотр данных выражения")

Выберите **Обновить** , чтобы обновить результаты выражения на основе реальной выборки источника.

![Кнопка "Обновить"](media/data-flow/exp5.png "Предварительный просмотр данных выражения")

## <a name="string-interpolation"></a>Интерполяция строк

Кавычки используются для заключения текста литеральной строки вместе с выражениями. Можно включать функции выражений, столбцы и параметры. Интерполяция строк полезна, чтобы избежать широкого использования объединения строк при включении параметров в строки запроса. Чтобы использовать синтаксис выражений, заключите его в фигурные скобки,

Некоторые примеры интерполяции строк:

* ```"My favorite movie is {iif(instr(title,', The')>0,"The {split(title,', The')[1]}",title)}"```

* ```"select * from {$tablename} where orderyear > {$year}"```

* ```"Total cost with sales tax is {round(totalcost * 1.08,2)}"```

## <a name="comment-expressions"></a>Выражения комментариев

Добавление комментариев к выражениям с помощью однострочного и многострочного синтаксиса комментариев.

![Синтаксис однострочных и многострочных комментариев](media/data-flow/comments.png "Комментарии")

Ниже приведены допустимые комментарии.

* ```/* This is my comment */```

* ```/* This is a```
*   ```multi-line comment */```
   
* ```// This is a single line comment```

Если вы поместили комментарий в верхней части выражения, он появится в текстовом поле преобразование, чтобы документировать выражения преобразования.

![Комментарий в текстовом поле преобразования](media/data-flow/comments2.png "Комментарии")

## <a name="regular-expressions"></a>Регулярные выражения

Многие функции языка выражений используют синтаксис регулярных выражений. При использовании функций регулярных выражений построитель выражений пытается интерпретировать обратную косую черту (\\) как последовательность escape-символов. При использовании обратных косых черт в регулярном выражении либо заключите все регулярное выражение в обратные (\`), либо используйте двойную обратную косую черту.

Пример, использующий обратные кавычки:

```
regex_replace('100 and 200', `(\d+)`, 'digits')
```

Пример, в котором используются двойные косые черты:

```
regex_replace('100 and 200', '(\\d+)', 'digits')
```

## <a name="address-array-indexes"></a>Индексы массива адресов

С помощью функций выражений, возвращающих массивы, используйте квадратные скобки ([]) для адресации конкретных индексов внутри этих объектов массива. Массив основан на единицах.

![Массив построителя выражений](media/data-flow/expb2.png "Предварительный просмотр данных выражения")

## <a name="keyboard-shortcuts"></a>Сочетания клавиш

* CTRL + K CTRL + C: Комментирование всей строки.
* CTRL + K Ctrl + U: раскомментировать.
* F1: предоставление команд справки по редактору.
* Alt + клавиша со стрелкой вниз: перемещение вниз текущей строки.
* Alt + клавиша со стрелкой вверх: перемещение вверх текущей строки.
* Ctrl + пробел: отображение контекстной справки.

## <a name="convert-to-dates-or-timestamps"></a>Преобразовать в даты или метки времени

Чтобы включить строковые литералы в выходные данные отметок времени, заключите преобразование в ```toString()```.

```toString(toTimestamp('12/31/2016T00:12:00', 'MM/dd/yyyy\'T\'HH:mm:ss'), 'MM/dd /yyyy\'T\'HH:mm:ss')```

Чтобы преобразовать миллисекунды из эпохи в дату или отметку времени, используйте `toTimestamp(<number of milliseconds>)`. Если время исходят в секундах, умножьте его на 1 000.

```toTimestamp(1574127407*1000l)```

Замыкающий символ "l" в конце предыдущего выражения означает преобразование в тип long в качестве встроенного синтаксиса.

## <a name="next-steps"></a>Дальнейшие действия

[Начало создания выражений преобразования данных](data-flow-expression-functions.md)
