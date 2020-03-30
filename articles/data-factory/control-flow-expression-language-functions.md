---
title: Выражение и функции на фабрике данных Azure
description: В этой статье содержится информация о выражениях и функциях, которые можно использовать при создании сущностей фабрики данных.
services: data-factory
documentationcenter: ''
author: djpmsft
ms.author: daperlov
ms.reviewer: maghan
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 11/25/2019
ms.openlocfilehash: 9ef4b569fd8413d2825374c963fb272dd450cf0e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74533143"
---
# <a name="expressions-and-functions-in-azure-data-factory"></a>Выражения и функции в фабрике данных Azure

> [!div class="op_single_selector" title1="Выберите используемую версию службы "Фабрика данных":"]
> * [Версия 1](v1/data-factory-functions-variables.md)
> * [Текущая версия](control-flow-expression-language-functions.md)

В этой статье содержатся сведения о выражениях и функциях, поддерживаемых службой "Фабрика данных Azure". 

## <a name="expressions"></a>Выражения

Значения JSON в определении могут быть литералами или выражениями, которые оцениваются в среде выполнения. Пример:  
  
```json
"name": "value"
```

 или диспетчер конфигурации служб  
  
```json
"name": "@pipeline().parameters.password"
```

Выражения могут встречаться в любом месте строкового значения JSON и всегда возвращают другое значение JSON. Если значение JSON является выражением, текст выражения извлекается без знака @ (\@). Если требуется строковый литерал, начинающийся с \@, его необходимо экранировать с помощью \@\@. В примерах ниже показано, как вычисляются выражения.  
  
|Значение JSON|Результат|  
|----------------|------------|  
|"parameters"|Возвращаются символы в виде 'parameters'.|  
|"parameters[1]"|Возвращаются символы в виде 'parameters[1]'.|  
|"\@\@"|Возвращается строка из 1 символа, содержащая символ "\@".|  
|" \@"|Возвращается строка из 2 символов, содержащая символ "\@".|  
  
 Выражения также могут содержаться внутри строк, где они заключаются в структуру `@{ ... }`, при использовании *интерполяции строк*. Например: `"name" : "First Name: @{pipeline().parameters.firstName} Last Name: @{pipeline().parameters.lastName}"`  
  
 При использовании интерполяции строки результатом всегда будет строка. Предположим, `myNumber` определено как `42`, а `myString` — как `foo`:  
  
|Значение JSON|Результат|  
|----------------|------------|  
|"\@pipeline().parameters.myString"| Возвращает `foo` как строку.|  
|"\@{pipeline().parameters.myString}"| Возвращает `foo` как строку.|  
|"\@pipeline().parameters.myNumber"| Возвращает `42` как *номер*.|  
|"\@{pipeline().parameters.myNumber}"| Возвращает `42` как *строку*.|  
|Answer is: @{pipeline().parameters.myNumber}| Возвращает строку `Answer is: 42`.|  
|"\@concat('Answer is: ', string(pipeline().parameters.myNumber))"| Возвращает строку `Answer is: 42`.|  
|"Answer is: \@\@{pipeline().parameters.myNumber}"| Возвращает строку `Answer is: @{pipeline().parameters.myNumber}`.|  
  
## <a name="examples"></a>Примеры

### <a name="a-dataset-with-a-parameter"></a>Набор данных с параметром
В следующем примере BlobDataset принимает параметр с именем **path**. Его значение используется для установки значения для свойства **folderPath** с помощью выражения: `dataset().path`. 

```json
{
    "name": "BlobDataset",
    "properties": {
        "type": "AzureBlob",
        "typeProperties": {
            "folderPath": "@dataset().path"
        },
        "linkedServiceName": {
            "referenceName": "AzureStorageLinkedService",
            "type": "LinkedServiceReference"
        },
        "parameters": {
            "path": {
                "type": "String"
            }
        }
    }
}
```

### <a name="a-pipeline-with-a-parameter"></a>Конвейер с параметром
В следующем примере конвейер принимает параметры **inputPath** и **outputPath**. **Путь** к параметризованному набору данных большого двоичного объекта задается с помощью значений этих параметров. Синтаксис, используемый здесь: `pipeline().parameters.parametername`. 

```json
{
    "name": "Adfv2QuickStartPipeline",
    "properties": {
        "activities": [
            {
                "name": "CopyFromBlobToBlob",
                "type": "Copy",
                "inputs": [
                    {
                        "referenceName": "BlobDataset",
                        "parameters": {
                            "path": "@pipeline().parameters.inputPath"
                        },
                        "type": "DatasetReference"
                    }
                ],
                "outputs": [
                    {
                        "referenceName": "BlobDataset",
                        "parameters": {
                            "path": "@pipeline().parameters.outputPath"
                        },
                        "type": "DatasetReference"
                    }
                ],
                "typeProperties": {
                    "source": {
                        "type": "BlobSource"
                    },
                    "sink": {
                        "type": "BlobSink"
                    }
                }
            }
        ],
        "parameters": {
            "inputPath": {
                "type": "String"
            },
            "outputPath": {
                "type": "String"
            }
        }
    }
}
```
### <a name="tutorial"></a>Учебник
Этот [учебник](https://azure.microsoft.com/mediahandler/files/resourcefiles/azure-data-factory-passing-parameters/Azure%20data%20Factory-Whitepaper-PassingParameters.pdf) рассказывает о том, как передавать параметры между конвейером и деятельностью, а также между действиями.

  
## <a name="functions"></a>Функции

Внутри выражений можно вызывать функции. В следующих разделах предоставлены сведения о функциях, которые могут использоваться в выражении.  

## <a name="string-functions"></a>Строковые функции  

Для работы со строками вы можете использовать эти строковые функции, а также некоторые [функции для коллекций](#collection-functions).
Строковые функции работают только со строками.

| Строковая функция | Задача |
| --------------- | ---- |
| [concat](control-flow-expression-language-functions.md#concat) | Объединяет две или более строк и возвращает объединенную строку. |
| [концыС](control-flow-expression-language-functions.md#endswith) | Проверяет, заканчивается ли строка определенной подстрокой. |
| [Guid](control-flow-expression-language-functions.md#guid) | Создает глобально уникальный идентификатор (GUID) в виде строки. |
| [Indexof](control-flow-expression-language-functions.md#indexof) | Возвращает начальную позицию подстроки. |
| [Lastindexof](control-flow-expression-language-functions.md#lastindexof) | Возвращает начальную позицию последнего вхождения подстроки. |
| [Заменить](control-flow-expression-language-functions.md#replace) | Заменяет подстроку указанной строкой и возвращает обновленную строку. |
| [Сплит](control-flow-expression-language-functions.md#split) | Возвращает массив, содержащий подстроки, разделенные запятыми, из большей строки, основываясь на указанном символе разделителя в исходной строке. |
| [начинаетсяс](control-flow-expression-language-functions.md#startswith) | Проверяет, начинается ли строка с определенной подстроки. |
| [Подстроки](control-flow-expression-language-functions.md#substring) | Возвращает символы из строки, начиная с указанной позиции. |
| [в Толоуэр](control-flow-expression-language-functions.md#toLower) | Возвращает строку символов в нижнем регистре. |
| [Toupper](control-flow-expression-language-functions.md#toUpper) | Возвращает строку символов в верхнем регистре. |
| [Обрезать](control-flow-expression-language-functions.md#trim) | Удаляет все начальные и конечные пробелы и возвращает обновленную строку. |

## <a name="collection-functions"></a>Функции для коллекций

Для работы с коллекциями, обычно массивами, строками, а иногда и словарями, вы можете использовать следующие функции.

| Функция для коллекций | Задача |
| ------------------- | ---- |
| [Содержит](control-flow-expression-language-functions.md#contains) | Проверяет наличие определенного элемента в коллекции. |
| [Пустой](control-flow-expression-language-functions.md#empty) | Проверяет, является ли коллекция пустой. |
| [Первый](control-flow-expression-language-functions.md#first) | Возвращает первый элемент из коллекции. |
| [Пересечения](control-flow-expression-language-functions.md#intersection) | Возвращает коллекцию, которая содержит *только* общие элементы в указанных коллекциях. |
| [Присоединиться к](control-flow-expression-language-functions.md#join) | Возвращает строку, содержащую *все* элементы из массива, в которой каждый символ отделен разделителем. |
| [Последний](control-flow-expression-language-functions.md#last) | Возвращает последний элемент из коллекции. |
| [длина](control-flow-expression-language-functions.md#length) | Возвращает число элементов в строке или массиве. |
| [Пропустить](control-flow-expression-language-functions.md#skip) | Удаляет элементы из начала коллекции и возвращает *все другие элементы*. |
| [Принять](control-flow-expression-language-functions.md#take) | Возвращает элементы, расположенные в начале коллекции. |
| [Союза](control-flow-expression-language-functions.md#union) | Возвращает коллекцию, которая содержит *все* элементы из указанных коллекций. | 

## <a name="logical-functions"></a>Логические функции  

Эти функции подходят для условий и могут использоваться для определения логики любого типа.  
  
| Функция логического сравнения | Задача |
| --------------------------- | ---- |
| [И](control-flow-expression-language-functions.md#and) | Проверяет, истинны ли все выражения. |
| [equals](control-flow-expression-language-functions.md#equals) | Проверяет, эквивалентны ли оба значения. |
| [greater](control-flow-expression-language-functions.md#greater) | Проверяет, является ли первое значение большим, чем второе. |
| [greaterOrEquals](control-flow-expression-language-functions.md#greaterOrEquals) | Проверяет, является ли первое значение большим, чем второе, или равным ему. |
| [if](control-flow-expression-language-functions.md#if) | Проверьте, какое значение имеет выражение: true или false. Возвращает указанное значение на основе результата. |
| [Меньше](control-flow-expression-language-functions.md#less) | Проверяет, является ли первое значение меньшим, чем второе. |
| [lessOrEquals](control-flow-expression-language-functions.md#lessOrEquals) | Проверяет, является ли первое значение меньшим, чем второе, или равным ему. |
| [Не](control-flow-expression-language-functions.md#not) | Проверяет, имеет ли выражение значение false. |
| [Или](control-flow-expression-language-functions.md#or) | Проверяет, является ли хотя бы одно выражение истинным. |
  
## <a name="conversion-functions"></a>Функции преобразования  

 Эти функции используются для преобразования между собственными типами языка:  
-   строка
-   Целое число
-   FLOAT
-   Логическое
-   arrays;
-   dictionaries;

| Функция преобразования | Задача |
| ------------------- | ---- |
| [Массива](control-flow-expression-language-functions.md#array) | Возвращает массив из одного экземпляра указанных входных данных. Для нескольких входов см. [createArray.](control-flow-expression-language-functions.md#createArray) |
| [base64](control-flow-expression-language-functions.md#base64) | Возвращает версию строки с кодировкой base64 для заданной строки. |
| [base64ToBinary](control-flow-expression-language-functions.md#base64ToBinary) | Возвращает двоичную версию строки с кодировкой base64. |
| [base64ToString](control-flow-expression-language-functions.md#base64ToString) | Возвращает строковую версию строки с кодировкой base64. |
| [Двоичном](control-flow-expression-language-functions.md#binary) | Возвращает двоичную версию входного значения. |
| [bool](control-flow-expression-language-functions.md#bool) | Возвращает логическую версию входного значения. |
| [coalesce](control-flow-expression-language-functions.md#coalesce) | Возвращает первое ненулевое значение из одного или нескольких параметров. |
| [createArray](control-flow-expression-language-functions.md#createArray) | Возвращает массив из нескольких экземпляров входных данных. |
| [dataUri](control-flow-expression-language-functions.md#dataUri) | Возвращает URI данных входного значения. |
| [dataUriToBinary](control-flow-expression-language-functions.md#dataUriToBinary) | Возвращает двоичную версию строки URI данных. |
| [dataUriToString](control-flow-expression-language-functions.md#dataUriToString) | Возвращает строковую версию URI данных. |
| [decodeBase64](control-flow-expression-language-functions.md#decodeBase64) | Возвращает строковую версию строки с кодировкой base64. |
| [decodeDataUri](control-flow-expression-language-functions.md#decodeDataUri) | Возвращает двоичную версию строки URI данных. |
| [decodeUriComponent](control-flow-expression-language-functions.md#decodeUriComponent) | Возвращает строку, которая заменяет escape-символы декодированными версиями. |
| [encodeUriComponent](control-flow-expression-language-functions.md#encodeUriComponent) | Возвращает строку, которая заменяет символы, опасные для URL-адреса, escape-символами. |
| [Плавать](control-flow-expression-language-functions.md#float) | Возвращает значение с плавающей запятой в качестве входного значения. |
| [Int](control-flow-expression-language-functions.md#int) | Возвращает целочисленную версию строки. |
| [Json](control-flow-expression-language-functions.md#json) | Возвращает значение типа JSON либо объект для строки или XML. |
| [Строка](control-flow-expression-language-functions.md#string) | Возвращает строковую версию входного значения. |
| [uriComponent](control-flow-expression-language-functions.md#uriComponent) | Возвращает кодированную версию URI для входного значения, заменив символы, опасные для URL-адреса, на escape-символы. |
| [uriComponentToBinary](control-flow-expression-language-functions.md#uriComponentToBinary) | Возвращает двоичную версию строки с закодированным URI. |
| [uriComponentToString](control-flow-expression-language-functions.md#uriComponentToString) | Возвращает строковую версию строки с закодированным URI. |
| [xml](control-flow-expression-language-functions.md#xml) | Возвращает XML-версию строки. |
| [Xpath](control-flow-expression-language-functions.md#xpath) | Проверяет XML на наличие узлов или значений, которые соответствуют выражению XPath, и возвращает соответствующие узлы или значения. |

## <a name="math-functions"></a>Математические функции  
 Эти функции могут использоваться для любого типа чисел: **целых чисел** и **чисел с плавающей запятой**.  

| Математическая функция | Задача |
| ------------- | ---- |
| [добавление](control-flow-expression-language-functions.md#add) | Возвращает результат сложения двух чисел. |
| [div](control-flow-expression-language-functions.md#div) | Возвращает результат деления двух чисел. |
| [max](control-flow-expression-language-functions.md#max) | Возвращает наибольшее значение из набора чисел или массива. |
| [Мин](control-flow-expression-language-functions.md#min) | Возвращает наименьшее значение из набора чисел или массива. |
| [Мод](control-flow-expression-language-functions.md#mod) | Возвращает остаток результата деления двух чисел. |
| [mul](control-flow-expression-language-functions.md#mul) | Возвращает результат умножения двух чисел. |
| [Рэнд](control-flow-expression-language-functions.md#rand) | Возвращает случайное целое число из указанного диапазона. |
| [Диапазон](control-flow-expression-language-functions.md#range) | Возвращает массив целых чисел, который начинается с заданного целого числа. |
| [Sub](control-flow-expression-language-functions.md#sub) | Вычитает второе число из первого числа и возвращает результат. |
  
## <a name="date-functions"></a>Функции данных  

| Функция даты и времени | Задача |
| --------------------- | ---- |
| [addDays](control-flow-expression-language-functions.md#addDays) | Добавляет количество дней к метке времени. |
| [addhours](control-flow-expression-language-functions.md#addHours) | Добавляет количество часов к метке времени. |
| [addminutes](control-flow-expression-language-functions.md#addMinutes) | Добавляет количество минут к метке времени. |
| [addseconds](control-flow-expression-language-functions.md#addSeconds) | Добавляет количество секунд к метке времени. |
| [addToTime](control-flow-expression-language-functions.md#addToTime) | Добавляет количество единиц времени к метке времени. Смотрите также [getFutureTime](control-flow-expression-language-functions.md#getFutureTime). |
| [convertFromUtc](control-flow-expression-language-functions.md#convertFromUtc) | Преобразовывает метку времени формата UTC в целевой часовой пояс. |
| [convertTimeZone](control-flow-expression-language-functions.md#convertTimeZone) | Преобразовывает метку времени из исходного часового пояса в целевой. |
| [convertToUtc](control-flow-expression-language-functions.md#convertToUtc) | Преобразует метку времени с исходным часовым поясом в формат UTC. |
| [dayOfMonth](control-flow-expression-language-functions.md#dayOfMonth) | Возвращает компонент дня месяца из метки времени. |
| [Dayofweek](control-flow-expression-language-functions.md#dayOfWeek) | Возвращает компонент дня недели из метки времени. |
| [dayOfYear](control-flow-expression-language-functions.md#dayOfYear) | Возвращает компонент дня года из метки времени. |
| [formatDateTime](control-flow-expression-language-functions.md#formatDateTime) | Возвращает дату из метки времени. |
| [getFutureTime](control-flow-expression-language-functions.md#getFutureTime) | Возвращает текущую метку времени, а также указанные единицы времени. См. раздел [addToTime](control-flow-expression-language-functions.md#addToTime). |
| [getPastTime](control-flow-expression-language-functions.md#getPastTime) | Возвращает текущую метку времени, вычитая указанные единицы времени. См. раздел [subtractFromTime](control-flow-expression-language-functions.md#subtractFromTime). |
| [startOfDay](control-flow-expression-language-functions.md#startOfDay) | Возвращает начало дня для метки времени. |
| [startOfHour](control-flow-expression-language-functions.md#startOfHour) | Возвращает начало часа для метки времени. |
| [startOfMonth](control-flow-expression-language-functions.md#startOfMonth) | Возвращает начало месяца для метки времени. |
| [subtractFromTime](control-flow-expression-language-functions.md#subtractFromTime) | Вычитает количество единиц времени из метки времени. См. раздел [getPastTime](control-flow-expression-language-functions.md#getPastTime). |
| [ticks](control-flow-expression-language-functions.md#ticks) | Возвращает значение свойства `ticks` для указанной метки времени. |
| [utcnow](control-flow-expression-language-functions.md#utcNow) | Возвращает текущую метку времени в виде строки. |

## <a name="function-reference"></a>Ссылка на функции

В этом разделе перечислены все доступные функции в алфавитном порядке.

<a name="add"></a>

### <a name="add"></a>add

Возвращает результат сложения двух чисел.

```
add(<summand_1>, <summand_2>)
```

| Параметр | Обязательно | Тип | Описание |
| --------- | -------- | ---- | ----------- |
| <*summand_1*>, <*summand_2*> | Да | Целое число, число с плавающей запятой или смешанного типа | Числа для добавления |
|||||

| Возвращаемое значение | Тип | Описание |
| ------------ | -----| ----------- |
| <*результат-сумма*> | Целое число и число с плавающей запятой | Возвращает результат сложения указанных чисел |
||||

*Пример*

В этом примере добавляются указанные числа:

```
add(1, 1.5)
```

Возвращается такой результат: `2.5`

<a name="addDays"></a>

### <a name="adddays"></a>addDays

Добавляет количество дней к метке времени.

```
addDays('<timestamp>', <days>, '<format>'?)
```

| Параметр | Обязательно | Тип | Описание |
| --------- | -------- | ---- | ----------- |
| <*Timestamp*> | Да | Строка | Строка, содержащая метку времени |
| <*Дней*> | Да | Целое число | Положительное или отрицательное число дней для добавления |
| <*Формат*> | нет | Строка | Либо [один описатель формата](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings), либо [пользовательский шаблон формата](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings). Формат по умолчанию для метки времени — ["o"](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (ГГГГ-ММ-ДДTЧЧ:ММ:fffffffK), который соответствует [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) и сохраняет информацию о часовом поясе. |
|||||

| Возвращаемое значение | Тип | Описание |
| ------------ | ---- | ----------- |
| <*обновленная штамповка времени*> | Строка | Метка времени и указанное число дней  |
||||

*Пример 1*

В этом примере к указанной метке времени добавляются 10 дней:

```
addDays('2018-03-15T13:00:00Z', 10)
```

Возвращается такой результат: `"2018-03-25T00:00:0000000Z"`

*Пример 2*

В этом примере от указанной метки времени отнимается пять дней:

```
addDays('2018-03-15T00:00:00Z', -5)
```

Возвращается такой результат: `"2018-03-10T00:00:0000000Z"`

<a name="addHours"></a>

### <a name="addhours"></a>addhours

Добавляет количество часов к метке времени.

```
addHours('<timestamp>', <hours>, '<format>'?)
```

| Параметр | Обязательно | Тип | Описание |
| --------- | -------- | ---- | ----------- |
| <*Timestamp*> | Да | Строка | Строка, содержащая метку времени |
| <*Часов*> | Да | Целое число | Положительное или отрицательное число часов для добавления |
| <*Формат*> | нет | Строка | Либо [один описатель формата](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings), либо [пользовательский шаблон формата](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings). Формат по умолчанию для метки времени — ["o"](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (ГГГГ-ММ-ДДTЧЧ:ММ:fffffffK), который соответствует [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) и сохраняет информацию о часовом поясе. |
|||||

| Возвращаемое значение | Тип | Описание |
| ------------ | ---- | ----------- |
| <*обновленная штамповка времени*> | Строка | Метка времени и указанное число часов  |
||||

*Пример 1*

В этом примере к указанной метке времени добавляется 10 часов:

```
addHours('2018-03-15T00:00:00Z', 10)
```

Возвращается такой результат: `"2018-03-15T10:00:0000000Z"`

*Пример 2*

В этом примере от указанной метки времени отнимается пять часов:

```
addHours('2018-03-15T15:00:00Z', -5)
```

Возвращается такой результат: `"2018-03-15T10:00:0000000Z"`

<a name="addMinutes"></a>

### <a name="addminutes"></a>addminutes

Добавляет количество минут к метке времени.

```
addMinutes('<timestamp>', <minutes>, '<format>'?)
```

| Параметр | Обязательно | Тип | Описание |
| --------- | -------- | ---- | ----------- |
| <*Timestamp*> | Да | Строка | Строка, содержащая метку времени |
| <*Минут*> | Да | Целое число | Положительное или отрицательное число минут для добавления |
| <*Формат*> | нет | Строка | Либо [один описатель формата](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings), либо [пользовательский шаблон формата](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings). Формат по умолчанию для метки времени — ["o"](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (ГГГГ-ММ-ДДTЧЧ:ММ:fffffffK), который соответствует [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) и сохраняет информацию о часовом поясе. |
|||||

| Возвращаемое значение | Тип | Описание |
| ------------ | ---- | ----------- |
| <*обновленная штамповка времени*> | Строка | Метка времени и указанное число минут |
||||

*Пример 1*

В этом примере к указанной метке времени добавляется 10 минут:

```
addMinutes('2018-03-15T00:10:00Z', 10)
```

Возвращается такой результат: `"2018-03-15T00:20:00.0000000Z"`

*Пример 2*

В этом примере от указанной метки времени отнимается пять минут:

```
addMinutes('2018-03-15T00:20:00Z', -5)
```

Возвращается такой результат: `"2018-03-15T00:15:00.0000000Z"`

<a name="addSeconds"></a>

### <a name="addseconds"></a>addseconds

Добавляет количество секунд к метке времени.

```
addSeconds('<timestamp>', <seconds>, '<format>'?)
```

| Параметр | Обязательно | Тип | Описание |
| --------- | -------- | ---- | ----------- |
| <*Timestamp*> | Да | Строка | Строка, содержащая метку времени |
| <*Секунд*> | Да | Целое число | Положительное или отрицательное число секунд для добавления |
| <*Формат*> | нет | Строка | Либо [один описатель формата](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings), либо [пользовательский шаблон формата](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings). Формат по умолчанию для метки времени — ["o"](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (ГГГГ-ММ-ДДTЧЧ:ММ:fffffffK), который соответствует [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) и сохраняет информацию о часовом поясе. |
|||||

| Возвращаемое значение | Тип | Описание |
| ------------ | ---- | ----------- |
| <*обновленная штамповка времени*> | Строка | Метка времени и указанное число секунд  |
||||

*Пример 1*

В этом примере к указанной метке времени добавляется 10 секунд:

```
addSeconds('2018-03-15T00:00:00Z', 10)
```

Возвращается такой результат: `"2018-03-15T00:00:10.0000000Z"`

*Пример 2*

В этом примере от указанной метки времени отнимается пять секунд:

```
addSeconds('2018-03-15T00:00:30Z', -5)
```

Возвращается такой результат: `"2018-03-15T00:00:25.0000000Z"`

<a name="addToTime"></a>

### <a name="addtotime"></a>addToTime

Добавляет количество единиц времени к метке времени.
См. раздел [getFutureTime()](#getFutureTime).

```
addToTime('<timestamp>', <interval>, '<timeUnit>', '<format>'?)
```

| Параметр | Обязательно | Тип | Описание |
| --------- | -------- | ---- | ----------- |
| <*Timestamp*> | Да | Строка | Строка, содержащая метку времени |
| <*Интервал*> | Да | Целое число | Число единиц времени для добавления |
| <*timeUnit*> | Да | Строка | Единицы времени для использования с *интервалом*: "секунда", "минута", "час", "день", "неделя", "месяц", "год" |
| <*Формат*> | нет | Строка | Либо [один описатель формата](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings), либо [пользовательский шаблон формата](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings). Формат по умолчанию для метки времени — ["o"](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (ГГГГ-ММ-ДДTЧЧ:ММ:fffffffK), который соответствует [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) и сохраняет информацию о часовом поясе. |
|||||

| Возвращаемое значение | Тип | Описание |
| ------------ | ---- | ----------- |
| <*обновленная штамповка времени*> | Строка | Метка времени и указанное число единиц времени  |
||||

*Пример 1*

В этом примере к указанной метке времени добавляется 1 день:

```
addToTime('2018-01-01T00:00:00Z', 1, 'Day')
```

Возвращается такой результат: `"2018-01-02T00:00:00.0000000Z"`

*Пример 2*

В этом примере к указанной метке времени добавляется 1 день:

```
addToTime('2018-01-01T00:00:00Z', 1, 'Day', 'D')
```

И возвращается результат с использованием необязательного формата "D": `"Tuesday, January 2, 2018"`

<a name="and"></a>

### <a name="and"></a>и

Проверяет, истинны ли все выражения.
Возвращает значение true, если все выражения имеют значения, и false, если хотя бы одно выражение имеет значение false.

```
and(<expression1>, <expression2>, ...)
```

| Параметр | Обязательно | Тип | Описание |
| --------- | -------- | ---- | ----------- |
| <*expression1*>, <*expression2*>, ... | Да | Логическое | Выражения, которые следует проверить |
|||||

| Возвращаемое значение | Тип | Описание |
| ------------ | -----| ----------- |
| true или false | Логическое | Возвращает значение true, если все выражения имеют значение true. Возвращает значение false, если хотя бы одно выражение имеет значение false. |
||||

*Пример 1*

В этих примерах проверяется, все ли указанные логические значения верны:

```
and(true, true)
and(false, true)
and(false, false)
```

И возвращаются следующие результаты:

* В первом примере оба выражения условия выполнены, поэтому возвращается `true`.
* Во втором примере одно из выражений имеет значение false, поэтому возвращается `false`.
* В третьем примере оба выражения имеют значения false, поэтому возвращается `false`.

*Пример 2*

В этих примерах проверяется, все ли указанные выражения верны:

```
and(equals(1, 1), equals(2, 2))
and(equals(1, 1), equals(1, 2))
and(equals(1, 2), equals(1, 3))
```

И возвращаются следующие результаты:

* В первом примере оба выражения условия выполнены, поэтому возвращается `true`.
* Во втором примере одно из выражений имеет значение false, поэтому возвращается `false`.
* В третьем примере оба выражения имеют значения false, поэтому возвращается `false`.

<a name="array"></a>

### <a name="array"></a>массиве

Возвращает массив из одного экземпляра указанных входных данных.
Для использования нескольких входных данных см. раздел [createArray()](#createArray).

```
array('<value>')
```

| Параметр | Обязательно | Тип | Описание |
| --------- | -------- | ---- | ----------- |
| <*Значение*> | Да | Строка | Строка для создания массива |
|||||

| Возвращаемое значение | Тип | Описание |
| ------------ | ---- | ----------- |
| (<*значение*>) | Array | Массив, содержащий один экземпляр указанных входных данных |
||||

*Пример*

В этом примере создается массив из строки "hello":

```
array('hello')
```

Возвращается такой результат: `["hello"]`

<a name="base64"></a>

### <a name="base64"></a>base64

Возвращает версию строки с кодировкой base64 для заданной строки.

```
base64('<value>')
```

| Параметр | Обязательно | Тип | Описание |
| --------- | -------- | ---- | ----------- |
| <*Значение*> | Да | Строка | Строка входных данных |
|||||

| Возвращаемое значение | Тип | Описание |
| ------------ | ---- | ----------- |
| <*base64-строка*> | Строка | Версия строки входных данных с кодировкой base64 |
||||

*Пример*

В этом примере строка "hello" преобразуется в строку с кодировкой base64:

```
base64('hello')
```

Возвращается такой результат: `"aGVsbG8="`

<a name="base64ToBinary"></a>

### <a name="base64tobinary"></a>base64ToBinary

Возвращает двоичную версию строки с кодировкой base64.

```
base64ToBinary('<value>')
```

| Параметр | Обязательно | Тип | Описание |
| --------- | -------- | ---- | ----------- |
| <*Значение*> | Да | Строка | Преобразуемая строка с кодировкой base64 |
|||||

| Возвращаемое значение | Тип | Описание |
| ------------ | ---- | ----------- |
| <*бинарный для базы64-строка*> | Строка | Двоичная версия строки с кодировкой base64 |
||||

*Пример*

В этом примере строка "aGVsbG8=" в кодировке base64 преобразуется в двоичную строку:

```
base64ToBinary('aGVsbG8=')
```

Возвращается такой результат:

`"0110000101000111010101100111001101100010010001110011100000111101"`

<a name="base64ToString"></a>

### <a name="base64tostring"></a>base64ToString

Возвращает декодированную версию строки с кодировкой base64.
Используйте эту функцию вместо [decodeBase64()](#decodeBase64).
Хотя обе функции работают одинаково, `base64ToString()` является предпочтительной.

```
base64ToString('<value>')
```

| Параметр | Обязательно | Тип | Описание |
| --------- | -------- | ---- | ----------- |
| <*Значение*> | Да | Строка | Декодируемая строка с кодировкой base64. |
|||||

| Возвращаемое значение | Тип | Описание |
| ------------ | ---- | ----------- |
| <*расшифрованная база64-строка*> | Строка | Строковая версия строки с кодировкой base64 |
||||

*Пример*

В этом примере строка "aGVsbG8=" в кодировке base64 преобразуется в простую строку:

```
base64ToString('aGVsbG8=')
```

Возвращается такой результат: `"hello"`

<a name="binary"></a>

### <a name="binary"></a>binary

Возвращает двоичную версию строки.

```
binary('<value>')
```

| Параметр | Обязательно | Тип | Описание |
| --------- | -------- | ---- | ----------- |
| <*Значение*> | Да | Строка | Преобразуемая строка |
|||||

| Возвращаемое значение | Тип | Описание |
| ------------ | ---- | ----------- |
| <*бинарное для вхожей-значения*> | Строка | Двоичная версия указанной строки |
||||

*Пример*

В этом примере строка "hello" преобразуется в двоичную строку:

```
binary('hello')
```

Возвращается такой результат:

`"0110100001100101011011000110110001101111"`

<a name="bool"></a>

### <a name="bool"></a>bool

Возвращает логическую версию значения.

```
bool(<value>)
```

| Параметр | Обязательно | Тип | Описание |
| --------- | -------- | ---- | ----------- |
| <*Значение*> | Да | Любой | Значение, которое необходимо преобразовать |
|||||

| Возвращаемое значение | Тип | Описание |
| ------------ | ---- | ----------- |
| true или false | Логическое | Логическая версия указанного значения |
||||

*Пример*

В этих примерах указанные значения преобразуются в логические:

```
bool(1)
bool(0)
```

И возвращаются следующие результаты:

* Первый пример: `true`.
* Второй пример: `false`.

<a name="coalesce"></a>

### <a name="coalesce"></a>coalesce

Возвращает первое ненулевое значение из одного или нескольких параметров.
Пустые строки, пустые массивы и пустые объекты не имеют значение null.

```
coalesce(<object_1>, <object_2>, ...)
```

| Параметр | Обязательно | Тип | Описание |
| --------- | -------- | ---- | ----------- |
| <*object_1*>, <*object_2*>, ... | Да | Любой, могут быть разные типы | Один или несколько элементов для проверки на наличие значения NULL |
|||||

| Возвращаемое значение | Тип | Описание |
| ------------ | ---- | ----------- |
| <*первый-не-нул-пункт*> | Любой | Первый элемент или значение, которое не равно NULL. Если все параметры равны NULL, эта функция возвращает значение NULL. |
||||

*Пример*

Эти примеры возвращают первое значение, не равное NULL, из указанных значений или равное NULL, когда все значения равны NULL:

```
coalesce(null, true, false)
coalesce(null, 'hello', 'world')
coalesce(null, null, null)
```

И возвращаются следующие результаты:

* Первый пример: `true`.
* Второй пример: `"hello"`.
* Третий пример: `null`.

<a name="concat"></a>

### <a name="concat"></a>concat

Объединяет две или более строк и возвращает объединенную строку.

```
concat('<text1>', '<text2>', ...)
```

| Параметр | Обязательно | Тип | Описание |
| --------- | -------- | ---- | ----------- |
| <*text1*>, <*text2*>, ... | Да | Строка | По крайней мере две строки для объединения |
|||||

| Возвращаемое значение | Тип | Описание |
| ------------ | ---- | ----------- |
| <*text1text2...*> | Строка | Строка, созданная из объединенных входных строк |
||||

*Пример*

В этом примере объединяются строки "Hello" и "World":

```
concat('Hello', 'World')
```

Возвращается такой результат: `"HelloWorld"`

<a name="contains"></a>

### <a name="contains"></a>contains

Проверяет наличие определенного элемента в коллекции.
Возвращает true, когда элемент найден, и false, когда не найден.
Эта функция учитывает регистр.

```
contains('<collection>', '<value>')
contains([<collection>], '<value>')
```

В частности, эта функция работает с такими типами коллекций:

* *строка*, чтобы найти *подстроку*.;
* *массив*, чтобы найти *значение*;
* *словарь*, чтобы найти *ключ*.

| Параметр | Обязательно | Тип | Описание |
| --------- | -------- | ---- | ----------- |
| <*Коллекции*> | Да | Строка, массив или словарь | Коллекция для проверки |
| <*Значение*> | Да | Строка, массив или словарь соответственно | Искомый элемент |
|||||

| Возвращаемое значение | Тип | Описание |
| ------------ | ---- | ----------- |
| true или false | Логическое | Возвращает true, если элемент найден. В противном случае возвращает значение false. |
||||

*Пример 1*

В этом примере строка "hello world" проверяется на наличие подстроки "world" и возвращается значение true:

```
contains('hello world', 'world')
```

*Пример 2*

В этом примере строка "hello world" проверяется на наличие подстроки "universe" и возвращается значение false:

```
contains('hello world', 'universe')
```

<a name="convertFromUtc"></a>

### <a name="convertfromutc"></a>convertFromUtc

Преобразовывает метку времени формата UTC в целевой часовой пояс.

```
convertFromUtc('<timestamp>', '<destinationTimeZone>', '<format>'?)
```

| Параметр | Обязательно | Тип | Описание |
| --------- | -------- | ---- | ----------- |
| <*Timestamp*> | Да | Строка | Строка, содержащая метку времени |
| <*пункт назначения Время*> | Да | Строка | Имя целевого часового пояса. Для имен часового пояса см. [значения индекса часового пояса Майкрософт,](https://support.microsoft.com/help/973627/microsoft-time-zone-index-values)но, возможно, вам придется удалить любые знаки препинания из названия часового пояса. |
| <*Формат*> | нет | Строка | Либо [один описатель формата](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings), либо [пользовательский шаблон формата](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings). Формат по умолчанию для метки времени — ["o"](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (ГГГГ-ММ-ДДTЧЧ:ММ:fffffffK), который соответствует [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) и сохраняет информацию о часовом поясе. |
|||||

| Возвращаемое значение | Тип | Описание |
| ------------ | ---- | ----------- |
| <*преобразованный таймштамп*> | Строка | Метка времени, преобразованная в целевой часовой пояс |
||||

*Пример 1*

В этом примере метка времени преобразуется в указанный часовой пояс:

```
convertFromUtc('2018-01-01T08:00:00.0000000Z', 'Pacific Standard Time')
```

Возвращается такой результат: `"2018-01-01T00:00:00.0000000"`

*Пример 2*

В этом примере метка времени преобразуется в указанный часовой пояс и формат:

```
convertFromUtc('2018-01-01T08:00:00.0000000Z', 'Pacific Standard Time', 'D')
```

Возвращается такой результат: `"Monday, January 1, 2018"`

<a name="convertTimeZone"></a>

### <a name="converttimezone"></a>convertTimeZone

Преобразовывает метку времени из исходного часового пояса в целевой.

```
convertTimeZone('<timestamp>', '<sourceTimeZone>', '<destinationTimeZone>', '<format>'?)
```

| Параметр | Обязательно | Тип | Описание |
| --------- | -------- | ---- | ----------- |
| <*Timestamp*> | Да | Строка | Строка, содержащая метку времени |
| <*источникTime'one*> | Да | Строка | Имя исходного часового пояса. Для имен часового пояса см. [значения индекса часового пояса Майкрософт,](https://support.microsoft.com/help/973627/microsoft-time-zone-index-values)но, возможно, вам придется удалить любые знаки препинания из названия часового пояса. |
| <*пункт назначения Время*> | Да | Строка | Имя целевого часового пояса. Для имен часового пояса см. [значения индекса часового пояса Майкрософт,](https://support.microsoft.com/help/973627/microsoft-time-zone-index-values)но, возможно, вам придется удалить любые знаки препинания из названия часового пояса. |
| <*Формат*> | нет | Строка | Либо [один описатель формата](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings), либо [пользовательский шаблон формата](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings). Формат по умолчанию для метки времени — ["o"](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (ГГГГ-ММ-ДДTЧЧ:ММ:fffffffK), который соответствует [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) и сохраняет информацию о часовом поясе. |
|||||

| Возвращаемое значение | Тип | Описание |
| ------------ | ---- | ----------- |
| <*преобразованный таймштамп*> | Строка | Метка времени, преобразованная в целевой часовой пояс |
||||

*Пример 1*

В этом примере исходный часовой пояс преобразуется в целевой:

```
convertTimeZone('2018-01-01T08:00:00.0000000Z', 'UTC', 'Pacific Standard Time')
```

Возвращается такой результат: `"2018-01-01T00:00:00.0000000"`

*Пример 2*

В этом примере часовой пояс преобразуется в указанный часовой пояс и формат:

```
convertTimeZone('2018-01-01T80:00:00.0000000Z', 'UTC', 'Pacific Standard Time', 'D')
```

Возвращается такой результат: `"Monday, January 1, 2018"`

<a name="convertToUtc"></a>

### <a name="converttoutc"></a>convertToUtc

Преобразует метку времени с исходным часовым поясом в формат UTC.

```
convertToUtc('<timestamp>', '<sourceTimeZone>', '<format>'?)
```

| Параметр | Обязательно | Тип | Описание |
| --------- | -------- | ---- | ----------- |
| <*Timestamp*> | Да | Строка | Строка, содержащая метку времени |
| <*источникTime'one*> | Да | Строка | Имя исходного часового пояса. Для имен часового пояса см. [значения индекса часового пояса Майкрософт,](https://support.microsoft.com/help/973627/microsoft-time-zone-index-values)но, возможно, вам придется удалить любые знаки препинания из названия часового пояса. |
| <*Формат*> | нет | Строка | Либо [один описатель формата](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings), либо [пользовательский шаблон формата](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings). Формат по умолчанию для метки времени — ["o"](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (ГГГГ-ММ-ДДTЧЧ:ММ:fffffffK), который соответствует [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) и сохраняет информацию о часовом поясе. |
|||||

| Возвращаемое значение | Тип | Описание |
| ------------ | ---- | ----------- |
| <*преобразованный таймштамп*> | Строка | Метка времени, которая преобразуются в формат UTC |
||||

*Пример 1*

В этом примере метка времени преобразуется в формат UTC:

```
convertToUtc('01/01/2018 00:00:00', 'Pacific Standard Time')
```

Возвращается такой результат: `"2018-01-01T08:00:00.0000000Z"`

*Пример 2*

В этом примере метка времени преобразуется в формат UTC:

```
convertToUtc('01/01/2018 00:00:00', 'Pacific Standard Time', 'D')
```

Возвращается такой результат: `"Monday, January 1, 2018"`

<a name="createArray"></a>

### <a name="createarray"></a>createArray

Возвращает массив из нескольких экземпляров входных данных.
Массивы из одного экземпляра входных данных см. в разделе [array()](#array).

```
createArray('<object1>', '<object2>', ...)
```

| Параметр | Обязательно | Тип | Описание |
| --------- | -------- | ---- | ----------- |
| <*object1*>, <*object2*>, ... | Да | Любой, но не смешанный | По крайней мере два элемента для создания массива |
|||||

| Возвращаемое значение | Тип | Описание |
| ------------ | ---- | ----------- |
| «<*object1*>, <*object2*>, ...» | Array | Массив, созданный из всех входных элементов |
||||

*Пример*

В этом примере создается массив из этих входных элементов:

```
createArray('h', 'e', 'l', 'l', 'o')
```

Возвращается такой результат: `["h", "e", "l", "l", "o"]`

<a name="dataUri"></a>

### <a name="datauri"></a>dataUri

Возвращает URI данных для строки.

```
dataUri('<value>')
```

| Параметр | Обязательно | Тип | Описание |
| --------- | -------- | ---- | ----------- |
| <*Значение*> | Да | Строка | Преобразуемая строка |
|||||

| Возвращаемое значение | Тип | Описание |
| ------------ | ---- | ----------- |
| <*данные-uri*> | Строка | URI данных для входной строки |
||||

*Пример*

В этом примере создается URI данных для строки "hello":

```
dataUri('hello')
```

Возвращается такой результат: `"data:text/plain;charset=utf-8;base64,aGVsbG8="`

<a name="dataUriToBinary"></a>

### <a name="datauritobinary"></a>dataUriToBinary

Возвращает двоичную версию URI данных.
Используйте эту функцию вместо [decodeDataUri()](#decodeDataUri).
Хотя обе функции работают одинаково, `dataUriBinary()` является предпочтительной.

```
dataUriToBinary('<value>')
```

| Параметр | Обязательно | Тип | Описание |
| --------- | -------- | ---- | ----------- |
| <*Значение*> | Да | Строка | URI данных для преобразования |
|||||

| Возвращаемое значение | Тип | Описание |
| ------------ | ---- | ----------- |
| <*двоичные данные-ури*> | Строка | Двоичная версия URI данных |
||||

*Пример*

В этом примере создается двоичная версия этого URI данных:

```
dataUriToBinary('data:text/plain;charset=utf-8;base64,aGVsbG8=')
```

Возвращается такой результат:

`"01100100011000010111010001100001001110100111010001100101011110000111010000101111011100000
1101100011000010110100101101110001110110110001101101000011000010111001001110011011001010111
0100001111010111010101110100011001100010110100111000001110110110001001100001011100110110010
10011011000110100001011000110000101000111010101100111001101100010010001110011100000111101"`

<a name="dataUriToString"></a>

### <a name="datauritostring"></a>dataUriToString

Возвращает строковую версию URI данных.

```
dataUriToString('<value>')
```

| Параметр | Обязательно | Тип | Описание |
| --------- | -------- | ---- | ----------- |
| <*Значение*> | Да | Строка | URI данных для преобразования |
|||||

| Возвращаемое значение | Тип | Описание |
| ------------ | ---- | ----------- |
| <*строка для данных-ури*> | Строка | Строковая версия URI данных |
||||

*Пример*

В этом примере создается строка этого URI данных:

```
dataUriToString('data:text/plain;charset=utf-8;base64,aGVsbG8=')
```

Возвращается такой результат: `"hello"`

<a name="dayOfMonth"></a>

### <a name="dayofmonth"></a>dayOfMonth

Возвращает день месяца из метки времени.

```
dayOfMonth('<timestamp>')
```

| Параметр | Обязательно | Тип | Описание |
| --------- | -------- | ---- | ----------- |
| <*Timestamp*> | Да | Строка | Строка, содержащая метку времени |
|||||

| Возвращаемое значение | Тип | Описание |
| ------------ | ---- | ----------- |
| <*день за месяц*> | Целое число | День месяца из указанной метки времени |
||||

*Пример*

В этом примере возвращается число дня месяца из этой метки времени:

```
dayOfMonth('2018-03-15T13:27:36Z')
```

Возвращается такой результат: `15`

<a name="dayOfWeek"></a>

### <a name="dayofweek"></a>dayOfWeek

Возвращает день недели из метки времени.

```
dayOfWeek('<timestamp>')
```

| Параметр | Обязательно | Тип | Описание |
| --------- | -------- | ---- | ----------- |
| <*Timestamp*> | Да | Строка | Строка, содержащая метку времени |
|||||

| Возвращаемое значение | Тип | Описание |
| ------------ | ---- | ----------- |
| <*день за неделю*> | Целое число | День недели из указанной метки времени, где воскресенье имеет значение 0, понедельник — 1 и т. д. |
||||

*Пример*

В этом примере возвращается номер дня недели из этой метки времени:

```
dayOfWeek('2018-03-15T13:27:36Z')
```

Возвращается такой результат: `3`

<a name="dayOfYear"></a>

### <a name="dayofyear"></a>dayOfYear

Возвращает день года из метки времени.

```
dayOfYear('<timestamp>')
```

| Параметр | Обязательно | Тип | Описание |
| --------- | -------- | ---- | ----------- |
| <*Timestamp*> | Да | Строка | Строка, содержащая метку времени |
|||||

| Возвращаемое значение | Тип | Описание |
| ------------ | ---- | ----------- |
| <*день в году*> | Целое число | День года из указанной метки времени |
||||

*Пример*

В этом примере возвращается число дня года из этой метки времени:

```
dayOfYear('2018-03-15T13:27:36Z')
```

Возвращается такой результат: `74`

<a name="decodeBase64"></a>

### <a name="decodebase64"></a>decodeBase64

Возвращает декодированную версию строки с кодировкой base64.
Используйте [base64ToString()](#base64ToString) вместо `decodeBase64()`.
Хотя обе функции работают одинаково, `base64ToString()` является предпочтительной.

```
decodeBase64('<value>')
```

| Параметр | Обязательно | Тип | Описание |
| --------- | -------- | ---- | ----------- |
| <*Значение*> | Да | Строка | Декодируемая строка с кодировкой base64. |
|||||

| Возвращаемое значение | Тип | Описание |
| ------------ | ---- | ----------- |
| <*расшифрованная база64-строка*> | Строка | Строковая версия строки с кодировкой base64 |
||||

*Пример*

В этом примере создается строка для строки с кодировкой base64:

```
decodeBase64('aGVsbG8=')
```

Возвращается такой результат: `"hello"`

<a name="decodeDataUri"></a>

### <a name="decodedatauri"></a>decodeDataUri

Возвращает двоичную версию URI данных.
Используйте [dataUriToBinary()](#dataUriToBinary) вместо `decodeDataUri()`.
Хотя обе функции работают одинаково, `dataUriToBinary()` является предпочтительной.

```
decodeDataUri('<value>')
```

| Параметр | Обязательно | Тип | Описание |
| --------- | -------- | ---- | ----------- |
| <*Значение*> | Да | Строка | Декодируемая строка URI данных |
|||||

| Возвращаемое значение | Тип | Описание |
| ------------ | ---- | ----------- |
| <*двоичные данные-ури*> | Строка | Двоичная версия строки URI данных |
||||

*Пример*

В этом примере возвращается двоичная версия этого URI данных:

```
decodeDataUri('data:text/plain;charset=utf-8;base64,aGVsbG8=')
```

Возвращается такой результат:

`"01100100011000010111010001100001001110100111010001100101011110000111010000101111011100000
1101100011000010110100101101110001110110110001101101000011000010111001001110011011001010111
0100001111010111010101110100011001100010110100111000001110110110001001100001011100110110010
10011011000110100001011000110000101000111010101100111001101100010010001110011100000111101"`

<a name="decodeUriComponent"></a>

### <a name="decodeuricomponent"></a>decodeUriComponent

Возвращает строку, которая заменяет escape-символы декодированными версиями.

```
decodeUriComponent('<value>')
```

| Параметр | Обязательно | Тип | Описание |
| --------- | -------- | ---- | ----------- |
| <*Значение*> | Да | Строка | Строка с декодируемыми escape-символами |
|||||

| Возвращаемое значение | Тип | Описание |
| ------------ | ---- | ----------- |
| <*расшифровано-ури*> | Строка | Обновленная строка с декодированными escape-символами |
||||

*Пример*

В этом примере escape-символы в этой строке заменяются декодированными версиями:

```
decodeUriComponent('http%3A%2F%2Fcontoso.com')
```

Возвращается такой результат: `"https://contoso.com"`

<a name="div"></a>

### <a name="div"></a>div

Возвращает целочисленный результат деления двух чисел.
Чтобы получить остаток, см. раздел [mod()](#mod).

```
div(<dividend>, <divisor>)
```

| Параметр | Обязательно | Тип | Описание |
| --------- | -------- | ---- | ----------- |
| <*Дивидендов*> | Да | Целое число и число с плавающей запятой | Число, которое нужно поделить на *делитель* |
| <*Делитель*> | Да | Целое число и число с плавающей запятой | Число, которое делит *дивиденды,* но не может быть 0 |
|||||

| Возвращаемое значение | Тип | Описание |
| ------------ | ---- | ----------- |
| <*коэффициент-результат*> | Целое число | Целое число в результате деления первого числа на второе |
||||

*Пример*

В обоих примерах первое число делится на второе:

```
div(10, 5)
div(11, 5)
```

Возвращается результат: `2`

<a name="encodeUriComponent"></a>

### <a name="encodeuricomponent"></a>encodeUriComponent

Возвращает кодированную версию URI для строки, заменив символы, опасные для URL-адреса, на escape-символы.
Используйте [uriComponent()](#uriComponent) вместо `encodeUriComponent()`.
Хотя обе функции работают одинаково, `uriComponent()` является предпочтительной.

```
encodeUriComponent('<value>')
```

| Параметр | Обязательно | Тип | Описание |
| --------- | -------- | ---- | ----------- |
| <*Значение*> | Да | Строка | Строка для преобразования в формат закодированного URI |
|||||

| Возвращаемое значение | Тип | Описание |
| ------------ | ---- | ----------- |
| <*закодированный-ури*> | Строка | Строка с закодированным URI, содержащая escape-символы |
||||

*Пример*

В этом примере создается версия с закодированным URI для этой строки:

```
encodeUriComponent('https://contoso.com')
```

Возвращается такой результат: `"http%3A%2F%2Fcontoso.com"`

<a name="empty"></a>

### <a name="empty"></a>empty

Проверяет, является ли коллекция пустой.
Возвращает значение true, если коллекция пуста, или false, если нет.

```
empty('<collection>')
empty([<collection>])
```

| Параметр | Обязательно | Тип | Описание |
| --------- | -------- | ---- | ----------- |
| <*Коллекции*> | Да | Строка, массив или объект | Коллекция для проверки |
|||||

| Возвращаемое значение | Тип | Описание |
| ------------ | ---- | ----------- |
| true или false | Логическое | Возвращает значение true, если коллекция пуста. В противном случае возвращает значение false. |
||||

*Пример*

В этих примерах проверяется, являются ли указанные коллекции пустыми:

```
empty('')
empty('abc')
```

И возвращаются следующие результаты:

* В первом примере передается пустая строка, поэтому функция возвращает `true`.
* Во втором примере передается строка "abc", поэтому функция возвращает `false`.

<a name="endswith"></a>

### <a name="endswith"></a>endsWith

Проверяет, заканчивается ли строка определенной подстрокой.
Возвращает true, если подстрока найдена, или возвращает false, если нет.
Эта функция не учитывает регистр.

```
endsWith('<text>', '<searchText>')
```

| Параметр | Обязательно | Тип | Описание |
| --------- | -------- | ---- | ----------- |
| <*Текст*> | Да | Строка | Проверяемая строка |
| <*searchText*> | Да | Строка | Конечная подстрока для поиска |
|||||

| Возвращаемое значение | Тип | Описание |
| ------------ | ---- | ----------- |
| true или false  | Логическое | Возвращает значение true, если конечная подстрока обнаружена. В противном случае возвращает значение false. |
||||

*Пример 1*

В этом примере проверяется, заканчивается ли строка "hello world" строкой "world":

```
endsWith('hello world', 'world')
```

Возвращается такой результат: `true`

*Пример 2*

В этом примере проверяется, заканчивается ли строка "hello world" строкой "universe":

```
endsWith('hello world', 'universe')
```

Возвращается такой результат: `false`

<a name="equals"></a>

### <a name="equals"></a>equals

Проверяет, эквивалентны ли оба значения, выражения или объекта.
Возвращается значение true, если они эквивалентны, или false, если нет.

```
equals('<object1>', '<object2>')
```

| Параметр | Обязательно | Тип | Описание |
| --------- | -------- | ---- | ----------- |
| <*object1*>, <*object2*> | Да | Различные | Значения, выражения или объекты для сравнения |
|||||

| Возвращаемое значение | Тип | Описание |
| ------------ | ---- | ----------- |
| true или false | Логическое | Возвращает значение true, если оба значения эквивалентны. В противном случае возвращает значение false. |
||||

*Пример*

В этих примерах проверяется, являются ли указанные входные данные эквивалентными.

```
equals(true, 1)
equals('abc', 'abcd')
```

И возвращаются следующие результаты:

* В первом примере оба значения эквивалентны, поэтому функция возвращает `true`.
* Во втором примере оба значения не эквивалентны, поэтому функция возвращает `false`.

<a name="first"></a>

### <a name="first"></a>first

Возвращает первый элемент из строки или массива.

```
first('<collection>')
first([<collection>])
```

| Параметр | Обязательно | Тип | Описание |
| --------- | -------- | ---- | ----------- |
| <*Коллекции*> | Да | Строка или массив | Коллекция, где нужно найти первый элемент. |
|||||

| Возвращаемое значение | Тип | Описание |
| ------------ | ---- | ----------- |
| <*первый предмет коллекции*> | Любой | Первый элемент в коллекции |
||||

*Пример*

В этих примерах выполняется поиск первого элемента в этих коллекциях:

```
first('hello')
first(createArray(0, 1, 2))
```

И возвращаются следующие результаты:

* Первый пример: `"h"`.
* Второй пример: `0`.

<a name="float"></a>

### <a name="float"></a>FLOAT

Преобразует строковую версию числа с плавающей запятой в фактическое число с плавающей запятой.

```
float('<value>')
```

| Параметр | Обязательно | Тип | Описание |
| --------- | -------- | ---- | ----------- |
| <*Значение*> | Да | Строка | Строка с допустимым числом с плавающей запятой, которую нужно преобразовать |
|||||

| Возвращаемое значение | Тип | Описание |
| ------------ | ---- | ----------- |
| <*поплавок-стоимость*> | Float | Число с плавающей запятой для указанной строки |
||||

*Пример*

В этом примере создается строковая версия для этого числа с плавающей запятой:

```
float('10.333')
```

Возвращается такой результат: `10.333`

<a name="formatDateTime"></a>

### <a name="formatdatetime"></a>formatDateTime

Возвращает метку времени в указанном формате.

```
formatDateTime('<timestamp>', '<format>'?)
```

| Параметр | Обязательно | Тип | Описание |
| --------- | -------- | ---- | ----------- |
| <*Timestamp*> | Да | Строка | Строка, содержащая метку времени |
| <*Формат*> | нет | Строка | Либо [один описатель формата](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings), либо [пользовательский шаблон формата](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings). Формат по умолчанию для метки времени — ["o"](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (ГГГГ-ММ-ДДTЧЧ:ММ:fffffffK), который соответствует [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) и сохраняет информацию о часовом поясе. |
|||||

| Возвращаемое значение | Тип | Описание |
| ------------ | ---- | ----------- |
| <*переформатированный штамп времени*> | Строка | Обновленная метка времени в указанном формате |
||||

*Пример*

В этом примере метка времени преобразуется в указанный формат:

```
formatDateTime('03/15/2018 12:00:00', 'yyyy-MM-ddTHH:mm:ss')
```

Возвращается такой результат: `"2018-03-15T12:00:00"`

<a name="getFutureTime"></a>

### <a name="getfuturetime"></a>getFutureTime

Возвращает текущую метку времени, а также указанные единицы времени.

```
getFutureTime(<interval>, <timeUnit>, <format>?)
```

| Параметр | Обязательно | Тип | Описание |
| --------- | -------- | ---- | ----------- |
| <*Интервал*> | Да | Целое число | Число единиц времени для добавления |
| <*timeUnit*> | Да | Строка | Единицы времени для использования с *интервалом*: "секунда", "минута", "час", "день", "неделя", "месяц", "год" |
| <*Формат*> | нет | Строка | Либо [один описатель формата](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings), либо [пользовательский шаблон формата](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings). Формат по умолчанию для метки времени — ["o"](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (ГГГГ-ММ-ДДTЧЧ:ММ:fffffffK), который соответствует [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) и сохраняет информацию о часовом поясе. |
|||||

| Возвращаемое значение | Тип | Описание |
| ------------ | ---- | ----------- |
| <*обновленная штамповка времени*> | Строка | Текущая метка времени и указанное число единиц времени |
||||

*Пример 1*

Допустим, текущая метка времени такая: "2018-03-01T00:00:00.0000000Z".
В этом примере к этой метке времени добавляется пять дней:

```
getFutureTime(5, 'Day')
```

Возвращается такой результат: `"2018-03-06T00:00:00.0000000Z"`

*Пример 2*

Допустим, текущая метка времени такая: "2018-03-01T00:00:00.0000000Z".
В этом примере добавляется пять дней и результат преобразуется в формат "D":

```
getFutureTime(5, 'Day', 'D')
```

Возвращается такой результат: `"Tuesday, March 6, 2018"`

<a name="getPastTime"></a>

### <a name="getpasttime"></a>getPastTime

Возвращает текущую метку времени, вычитая указанные единицы времени.

```
getPastTime(<interval>, <timeUnit>, <format>?)
```

| Параметр | Обязательно | Тип | Описание |
| --------- | -------- | ---- | ----------- |
| <*Интервал*> | Да | Целое число | Число единиц времени для вычитания |
| <*timeUnit*> | Да | Строка | Единицы времени для использования с *интервалом*: "секунда", "минута", "час", "день", "неделя", "месяц", "год" |
| <*Формат*> | нет | Строка | Либо [один описатель формата](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings), либо [пользовательский шаблон формата](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings). Формат по умолчанию для метки времени — ["o"](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (ГГГГ-ММ-ДДTЧЧ:ММ:fffffffK), который соответствует [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) и сохраняет информацию о часовом поясе. |
|||||

| Возвращаемое значение | Тип | Описание |
| ------------ | ---- | ----------- |
| <*обновленная штамповка времени*> | Строка | Текущая метка времени за вычетом указанного числа единиц времени |
||||

*Пример 1*

Допустим, текущая метка времени такая: "2018-02-01T00:00:00.0000000Z".
В этом примере от указанной метки времени отнимается пять дней.

```
getPastTime(5, 'Day')
```

Возвращается такой результат: `"2018-01-27T00:00:00.0000000Z"`

*Пример 2*

Допустим, текущая метка времени такая: "2018-02-01T00:00:00.0000000Z".
В этом примере отнимается пять дней и результат преобразуется в формат "D":

```
getPastTime(5, 'Day', 'D')
```

Возвращается такой результат: `"Saturday, January 27, 2018"`

<a name="greater"></a>

### <a name="greater"></a>greater

Проверяет, является ли первое значение большим, чем второе.
Возвращает значение true, если первое значение больше, или значение false, если меньше.

```
greater(<value>, <compareTo>)
greater('<value>', '<compareTo>')
```

| Параметр | Обязательно | Тип | Описание |
| --------- | -------- | ---- | ----------- |
| <*Значение*> | Да | Целое число, число с плавающей запятой или строка | Проверяет, является ли первое значение большим, чем второе |
| <*Compareto*> | Да | Целое число, число с плавающей запятой или строка соответственно | Значение сравнения |
|||||

| Возвращаемое значение | Тип | Описание |
| ------------ | ---- | ----------- |
| true или false | Логическое | Возвращает значение true, если первое значение большим, чем второе. Возвращает значение false, если первое значение меньше или равно второму. |
||||

*Пример*

В этих примерах проверяется, является ли первое значение большим, чем второе.

```
greater(10, 5)
greater('apple', 'banana')
```

И возвращаются следующие результаты:

* Первый пример: `true`.
* Второй пример: `false`.

<a name="greaterOrEquals"></a>

### <a name="greaterorequals"></a>greaterOrEquals

Проверяет, является ли первое значение большим, чем второе, или равным ему.
Возвращает true, если первое значение больше или равно, или значение false, если первое значение меньше.

```
greaterOrEquals(<value>, <compareTo>)
greaterOrEquals('<value>', '<compareTo>')
```

| Параметр | Обязательно | Тип | Описание |
| --------- | -------- | ---- | ----------- |
| <*Значение*> | Да | Целое число, число с плавающей запятой или строка | Проверяет, является ли первое значение большим, чем второе, или равным ему |
| <*Compareto*> | Да | Целое число, число с плавающей запятой или строка соответственно | Значение сравнения |
|||||

| Возвращаемое значение | Тип | Описание |
| ------------ | ---- | ----------- |
| true или false | Логическое | Возвращает значение true, если первое значение больше или равно второму. Возвращает значение false, если первое значение меньше, чем второе. |
||||

*Пример*

В этих примерах проверяется, является ли первое значение большим чем второе, или равным ему.

```
greaterOrEquals(5, 5)
greaterOrEquals('apple', 'banana')
```

И возвращаются следующие результаты:

* Первый пример: `true`.
* Второй пример: `false`.

<a name="guid"></a>

### <a name="guid"></a>guid

Создает глобально уникальный идентификатор (GUID) в виде строки, например, "c2ecc88d-88c8-4096-912c-d6f2e2b138ce":

```
guid()
```

Кроме того, вы можете указать другой формат для GUID, отличный от формата по умолчанию ("D"), который состоит из 32 цифр, разделенных дефисом.

```
guid('<format>')
```

| Параметр | Обязательно | Тип | Описание |
| --------- | -------- | ---- | ----------- |
| <*Формат*> | нет | Строка | Один [указатель формата](https://msdn.microsoft.com/library/97af8hh4) для возвращенного идентификатора GUID. По умолчанию имеет формат "D", но можно использовать "N", "D", "B", "P" или "X". |
|||||

| Возвращаемое значение | Тип | Описание |
| ------------ | ---- | ----------- |
| <*GUID-стоимость*> | Строка | Случайно сгенерированный GUID |
||||

*Пример*

В этом примере создается тот же идентификатор GUID, но в виде 32 цифр, разделенных дефисом и заключенных в круглые скобки:

```
guid('P')
```

Возвращается такой результат: `"(c2ecc88d-88c8-4096-912c-d6f2e2b138ce)"`

<a name="if"></a>

### <a name="if"></a>if

Проверьте, какое значение имеет выражение: true или false.
Возвращает указанное значение на основе результата.

```
if(<expression>, <valueIfTrue>, <valueIfFalse>)
```

| Параметр | Обязательно | Тип | Описание |
| --------- | -------- | ---- | ----------- |
| <*Выражение*> | Да | Логическое | Выражение для вычисления |
| <*valueIfTrue*> | Да | Любой | Возвращаемое значение, если выражение истинно |
| <*valueIfFalse*> | Да | Любой | Возвращаемое значение, если выражение ложно |
|||||

| Возвращаемое значение | Тип | Описание |
| ------------ | ---- | ----------- |
| <*указанная-доходность-стоимость*> | Любой | Возвращает указанное значение в зависимости от результата выражения: true или false. |
||||

*Пример*

В этом примере возвращается `"yes"`, потому что указанное выражение возвращает значение true.
В противном случае в примере возвращается `"no"`:

```
if(equals(1, 1), 'yes', 'no')
```

<a name="indexof"></a>

### <a name="indexof"></a>indexOf

Возвращает начальную позицию или значение индекса для подстроки.
Эта функция не учитывает регистр, и индексы начинаются с числа 0.

```
indexOf('<text>', '<searchText>')
```

| Параметр | Обязательно | Тип | Описание |
| --------- | -------- | ---- | ----------- |
| <*Текст*> | Да | Строка | Строки, которая содержит подстроку для поиска |
| <*searchText*> | Да | Строка | Подстрока для поиска |
|||||

| Возвращаемое значение | Тип | Описание |
| ------------ | ---- | ----------- |
| <*индекс-значение*>| Целое число | Начальное положение или значение индекса указанной подстроки. <p>Если строка не найдена, возвращается -1. |
||||

*Пример*

Этот пример находит начальное значение индекса для подстроки "world" в строке "hello world":

```
indexOf('hello world', 'world')
```

Возвращается такой результат: `6`

<a name="int"></a>

### <a name="int"></a>INT

Возвращает целочисленную версию строки.

```
int('<value>')
```

| Параметр | Обязательно | Тип | Описание |
| --------- | -------- | ---- | ----------- |
| <*Значение*> | Да | Строка | Преобразуемая строка |
|||||

| Возвращаемое значение | Тип | Описание |
| ------------ | ---- | ----------- |
| <*несомненный результат*> | Целое число | Целочисленная версия указанной строки |
||||

*Пример*

В этом примере создается целочисленная версия строки "10":

```
int('10')
```

Возвращается такой результат: `10`

<a name="json"></a>

### <a name="json"></a>json

Возвращает значение типа JSON либо объект для строки или XML.

```
json('<value>')
```

| Параметр | Обязательно | Тип | Описание |
| --------- | -------- | ---- | ----------- |
| <*Значение*> | Да | Строка или XML | Строка или XML для преобразования |
|||||

| Возвращаемое значение | Тип | Описание |
| ------------ | ---- | ----------- |
| <*JSON-результат*> | Собственный тип или объект JSON | Значение собственного типа или объекта JSON для заданной строки или XML. Если строка имеет значение NULL, функция возвращает пустой объект. |
||||

*Пример 1*

В этом примере эта строка преобразуется в значение JSON:

```
json('[1, 2, 3]')
```

Возвращается такой результат: `[1, 2, 3]`

*Пример 2*

В этом примере эта строка преобразуется в JSON:

```
json('{"fullName": "Sophia Owen"}')
```

Возвращается такой результат:

```
{
  "fullName": "Sophia Owen"
}
```

*Пример 3*

В этом примере этот XML преобразуется в JSON:

```
json(xml('<?xml version="1.0"?> <root> <person id='1'> <name>Sophia Owen</name> <occupation>Engineer</occupation> </person> </root>'))
```

Возвращается такой результат:

```json
{
   "?xml": { "@version": "1.0" },
   "root": {
      "person": [ {
         "@id": "1",
         "name": "Sophia Owen",
         "occupation": "Engineer"
      } ]
   }
}
```

<a name="intersection"></a>

### <a name="intersection"></a>пересечению

Возвращает коллекцию, которая содержит *только* общие элементы в указанных коллекциях.
Чтобы появиться в результатах, элемент должен находиться во всех коллекциях, переданных этой функции.
Если один или несколько элементов имеют одинаковое имя, в результатах появляется последний элемент с таким именем.

```
intersection([<collection1>], [<collection2>], ...)
intersection('<collection1>', '<collection2>', ...)
```

| Параметр | Обязательно | Тип | Описание |
| --------- | -------- | ---- | ----------- |
| <*collection1*>, <*collection2*>, ... | Да | Массив или объект, но не оба типа | Коллекции, из которых нужно получить *только* общие элементы |
|||||

| Возвращаемое значение | Тип | Описание |
| ------------ | ---- | ----------- |
| <*общие элементы*> | Массив или объект соответственно | Коллекция, которая содержит только общие элементы в указанных коллекциях |
||||

*Пример*

В этом примере находятся общие элементы в этих массивах:

```
intersection(createArray(1, 2, 3), createArray(101, 2, 1, 10), createArray(6, 8, 1, 2))
```

И возвращается массив *только* с этими элементами: `[1, 2]`

<a name="join"></a>

### <a name="join"></a>join

Возвращает строку, содержащую все элементы из массива, в которой каждый символ отделен *разделителем*.

```
join([<collection>], '<delimiter>')
```

| Параметр | Обязательно | Тип | Описание |
| --------- | -------- | ---- | ----------- |
| <*Коллекции*> | Да | Array | Массив, который содержит элементы для объединения |
| <*Разделитель*> | Да | Строка | Разделитель, содержащийся между каждым символом в результирующей строке |
|||||

| Возвращаемое значение | Тип | Описание |
| ------------ | ---- | ----------- |
| <*char1*><*delimiter*><*char2*><*delimiter*>... | Строка | Полученная строка, созданная из всех элементов в указанном массиве |
||||

*Пример*

В этом примере создается строка из всех элементов этого массива с указанным символом в качестве разделителя:

```
join(createArray('a', 'b', 'c'), '.')
```

Возвращается такой результат: `"a.b.c"`

<a name="last"></a>

### <a name="last"></a>last

Возвращает последний элемент из коллекции.

```
last('<collection>')
last([<collection>])
```

| Параметр | Обязательно | Тип | Описание |
| --------- | -------- | ---- | ----------- |
| <*Коллекции*> | Да | Строка или массив | Коллекция, в которой нужно найти последний элемент |
|||||

| Возвращаемое значение | Тип | Описание |
| ------------ | ---- | ----------- |
| <*последний сбор-пункт*> | Строка или массив соответственно | Последний элемент в коллекции |
||||

*Пример*

Эти примеру ищут последний элемент в этих коллекциях:

```
last('abcd')
last(createArray(0, 1, 2, 3))
```

И возвращаются следующие результаты:

* Первый пример: `"d"`.
* Второй пример: `3`.

<a name="lastindexof"></a>

### <a name="lastindexof"></a>lastIndexOf

Возвращает начальную позицию или значение индекса последнего вхождения подстроки.
Эта функция не учитывает регистр, и индексы начинаются с числа 0.

```
lastIndexOf('<text>', '<searchText>')
```

| Параметр | Обязательно | Тип | Описание |
| --------- | -------- | ---- | ----------- |
| <*Текст*> | Да | Строка | Строки, которая содержит подстроку для поиска |
| <*searchText*> | Да | Строка | Подстрока для поиска |
|||||

| Возвращаемое значение | Тип | Описание |
| ------------ | ---- | ----------- |
| <*окончание индекса-значение*> | Целое число | Начальная позиция или значение индекса последнего вхождения указанной подстроки. <p>Если строка не найдена, возвращается -1. |
||||

*Пример*

Этот пример находит начальное значение индекса для последнего вхождения подстроки "world" в строке "hello world":

```
lastIndexOf('hello world', 'world')
```

Возвращается такой результат: `6`

<a name="length"></a>

### <a name="length"></a>length

Возвращает число элементов в коллекции.

```
length('<collection>')
length([<collection>])
```

| Параметр | Обязательно | Тип | Описание |
| --------- | -------- | ---- | ----------- |
| <*Коллекции*> | Да | Строка или массив | Коллекция с элементами для подсчета |
|||||

| Возвращаемое значение | Тип | Описание |
| ------------ | ---- | ----------- |
| <*длина или подсчет*> | Целое число | Число элементов в данной коллекции |
||||

*Пример*

В этих примерах подсчитывается количество элементов в этих коллекциях:

```
length('abcd')
length(createArray(0, 1, 2, 3))
```

Возвращается результат: `4`

<a name="less"></a>

### <a name="less"></a>less

Проверяет, является ли первое значение меньшим, чем второе.
Возвращает true, если первое значение меньше, или значение false, если первое значение больше.

```
less(<value>, <compareTo>)
less('<value>', '<compareTo>')
```

| Параметр | Обязательно | Тип | Описание |
| --------- | -------- | ---- | ----------- |
| <*Значение*> | Да | Целое число, число с плавающей запятой или строка | Первое значение, для которого выполняется проверка того, является ли оно меньшим, чем второе. |
| <*Compareto*> | Да | Целое число, число с плавающей запятой или строка соответственно | Элемент для сравнения |
|||||

| Возвращаемое значение | Тип | Описание |
| ------------ | ---- | ----------- |
| true или false | Логическое | Возвращает значение true, если первое значение меньше, чем второе. Возвращает значение false, если первое значение больше или равно второму. |
||||

*Пример*

В этих примерах проверяется, является ли первое значение меньшим, чем второе.

```
less(5, 10)
less('banana', 'apple')
```

И возвращаются следующие результаты:

* Первый пример: `true`.
* Второй пример: `false`.

<a name="lessOrEquals"></a>

### <a name="lessorequals"></a>lessOrEquals

Проверяет, является ли первое значение меньшим, чем второе, или равным ему.
Возвращает true, если первое значение меньше или равно, или значение false, если первое значение больше.

```
lessOrEquals(<value>, <compareTo>)
lessOrEquals('<value>', '<compareTo>')
```

| Параметр | Обязательно | Тип | Описание |
| --------- | -------- | ---- | ----------- |
| <*Значение*> | Да | Целое число, число с плавающей запятой или строка | Проверяет, является ли первое значение меньшим, чем второе, или равным ему |
| <*Compareto*> | Да | Целое число, число с плавающей запятой или строка соответственно | Элемент для сравнения |
|||||

| Возвращаемое значение | Тип | Описание |
| ------------ | ---- | ----------- |
| true или false  | Логическое | Возвращает значение true, если первое значение меньше или равно второму. Возвращает значение false, если первое значение больше, чем второе. |
||||

*Пример*

В этих примерах проверяется, является ли первое значение меньшим или равным второму.

```
lessOrEquals(10, 10)
lessOrEquals('apply', 'apple')
```

И возвращаются следующие результаты:

* Первый пример: `true`.
* Второй пример: `false`.

<a name="max"></a>

### <a name="max"></a>max

Возвращает наибольшее значение из списка или массива, охватывающих последние и начальные значения.

```
max(<number1>, <number2>, ...)
max([<number1>, <number2>, ...])
```

| Параметр | Обязательно | Тип | Описание |
| --------- | -------- | ---- | ----------- |
| <*number1*>, <*number2*>, ... | Да | Целое число, число с плавающей запятой или оба типа | Набор чисел, из которого требуется получить наибольшее значение |
| «<*> номер1,* <*номер2*>, ...» | Да | Массив — целое число, число с плавающей запятой или оба типа | Массив чисел, из которого требуется получить наибольшее значение |
|||||

| Возвращаемое значение | Тип | Описание |
| ------------ | ---- | ----------- |
| <*макс-стоимость*> | Целое число и число с плавающей запятой | Максимальное значение в указанном массиве или набор чисел |
||||

*Пример*

В этих примерах извлекается наибольшее значение из набора чисел и массива:

```
max(1, 2, 3)
max(createArray(1, 2, 3))
```

Возвращается результат: `3`

<a name="min"></a>

### <a name="min"></a>Min

Возвращает наименьшее значение из набора чисел или массива.

```
min(<number1>, <number2>, ...)
min([<number1>, <number2>, ...])
```

| Параметр | Обязательно | Тип | Описание |
| --------- | -------- | ---- | ----------- |
| <*number1*>, <*number2*>, ... | Да | Целое число, число с плавающей запятой или оба типа | Набор чисел, из которого требуется получить наименьшее значение |
| «<*> номер1,* <*номер2*>, ...» | Да | Массив — целое число, число с плавающей запятой или оба типа | Массив чисел, из которого требуется получить наименьшее значение |
|||||

| Возвращаемое значение | Тип | Описание |
| ------------ | ---- | ----------- |
| <*мин-значение*> | Целое число и число с плавающей запятой | Наименьшее значение в указанном наборе чисел или массиве |
||||

*Пример*

В этих примерах извлекается наименьшее значение из набора чисел и массива:

```
min(1, 2, 3)
min(createArray(1, 2, 3))
```

Возвращается результат: `1`

<a name="mod"></a>

### <a name="mod"></a>mod

Возвращает остаток результата деления двух чисел.
Чтобы получить целочисленный результат, см. раздел [div()](#div).

```
mod(<dividend>, <divisor>)
```

| Параметр | Обязательно | Тип | Описание |
| --------- | -------- | ---- | ----------- |
| <*Дивидендов*> | Да | Целое число и число с плавающей запятой | Число, которое нужно поделить на *делитель* |
| <*Делитель*> | Да | Целое число и число с плавающей запятой | Число, на которое делится *делимое* (не может быть равно 0). |
|||||

| Возвращаемое значение | Тип | Описание |
| ------------ | ---- | ----------- |
| <*модуло-результат*> | Целое число и число с плавающей запятой | Остаток результата деления первого числа на второе |
||||

*Пример*

В этом примере первое число делится на второе:

```
mod(3, 2)
```

Возвращается результат: `1`

<a name="mul"></a>

### <a name="mul"></a>mul

Возвращает результат умножения двух чисел.

```
mul(<multiplicand1>, <multiplicand2>)
```

| Параметр | Обязательно | Тип | Описание |
| --------- | -------- | ---- | ----------- |
| <*multiplicand1*> | Да | Целое число и число с плавающей запятой | Число для умножения на *multiplicand2* |
| <*мультиканд2*> | Да | Целое число и число с плавающей запятой | Число, на которое умножается *multiplicand1* |
|||||

| Возвращаемое значение | Тип | Описание |
| ------------ | ---- | ----------- |
| <*продукт-результат*> | Целое число и число с плавающей запятой | Произведение от умножения первого числа на второе |
||||

*Пример*

В этом примере первое число умножается на второе:

```
mul(1, 2)
mul(1.5, 2)
```

И возвращаются следующие результаты:

* Первый пример: `2`.
* Второй пример: `3`.

<a name="not"></a>

### <a name="not"></a>not

Проверяет, имеет ли выражение значение false.
Возвращает значение true, если выражение ложно, или значение false, если значение истинно.

```json
not(<expression>)
```

| Параметр | Обязательно | Тип | Описание |
| --------- | -------- | ---- | ----------- |
| <*Выражение*> | Да | Логическое | Выражение для вычисления |
|||||

| Возвращаемое значение | Тип | Описание |
| ------------ | ---- | ----------- |
| true или false | Логическое | Возвращает значение true, если выражение ложно. Возвращает значение false, если выражение истинно. |
||||

*Пример 1*

В этих примерах проверяется, все ли указанные выражения ложны:

```json
not(false)
not(true)
```

И возвращаются следующие результаты:

* В первом примере выражение имеет значение false, поэтому функция возвращает `true`.
* Во втором примере выражение имеет значение true, поэтому функция возвращает `false`.

*Пример 2*

В этих примерах проверяется, все ли указанные выражения ложны:

```json
not(equals(1, 2))
not(equals(1, 1))
```

И возвращаются следующие результаты:

* В первом примере выражение имеет значение false, поэтому функция возвращает `true`.
* Во втором примере выражение имеет значение true, поэтому функция возвращает `false`.

<a name="or"></a>

### <a name="or"></a>или диспетчер конфигурации служб

Проверяет, является ли хотя бы одно выражение истинным.
Возвращает значение true, если хотя бы одно выражение истинно, или значение false, когда все являются ложными.

```
or(<expression1>, <expression2>, ...)
```

| Параметр | Обязательно | Тип | Описание |
| --------- | -------- | ---- | ----------- |
| <*expression1*>, <*expression2*>, ... | Да | Логическое | Выражения, которые следует проверить |
|||||

| Возвращаемое значение | Тип | Описание |
| ------------ | ---- | ----------- |
| true или false | Логическое | Возвращает значение true, если хотя бы одно выражение истинно. Возвращает значение false, если все выражения ложны. |
||||

*Пример 1*

В этих примерах проверяется, является ли хотя бы одно выражение истинным:

```json
or(true, false)
or(false, false)
```

И возвращаются следующие результаты:

* В первом примере по крайней мере одно выражение имеет значение true, поэтому функция возвращает `true`.
* Во втором примере оба выражения имеют значения false, поэтому функция возвращает `false`.

*Пример 2*

В этих примерах проверяется, является ли хотя бы одно выражение истинным:

```json
or(equals(1, 1), equals(1, 2))
or(equals(1, 2), equals(1, 3))
```

И возвращаются следующие результаты:

* В первом примере по крайней мере одно выражение имеет значение true, поэтому функция возвращает `true`.
* Во втором примере оба выражения имеют значения false, поэтому функция возвращает `false`.

<a name="rand"></a>

### <a name="rand"></a>rand

Возвращает случайное целое число из указанного диапазона с включительным значением только в начале.

```
rand(<minValue>, <maxValue>)
```

| Параметр | Обязательно | Тип | Описание |
| --------- | -------- | ---- | ----------- |
| <*Minvalue*> | Да | Целое число | Наименьшее целое число в диапазоне |
| <*Maxvalue*> | Да | Целое число | Целое число, следующее за наибольшим целым числом в диапазоне, которое функция может вернуть |
|||||

| Возвращаемое значение | Тип | Описание |
| ------------ | ---- | ----------- |
| <*случайный результат*> | Целое число | Случайное целое число, возвращаемое из указанного диапазона |
||||

*Пример*

Этот пример получает случайное целое число из указанного диапазона, исключая максимальное значение:

```
rand(1, 5)
```

И возвращает в виде результата одно из этих чисел: `1`, `2`, `3` или `4`.

<a name="range"></a>

### <a name="range"></a>range

Возвращает массив целых чисел, который начинается с заданного целого числа.

```
range(<startIndex>, <count>)
```

| Параметр | Обязательно | Тип | Описание |
| --------- | -------- | ---- | ----------- |
| <*Startindex*> | Да | Целое число | Целое значение, которое запускает массив в качестве первого элемента |
| <*Рассчитывать*> | Да | Целое число | Количество целых чисел в массиве |
|||||

| Возвращаемое значение | Тип | Описание |
| ------------ | ---- | ----------- |
| [<*результат диапазона*>] | Array | Массив с целыми числами, который начинается с указанного индекса |
||||

*Пример*

В этом примере создается целочисленный массив, который начинается с указанного индекса и имеет указанное число целых чисел:

```
range(1, 4)
```

Возвращается такой результат: `[1, 2, 3, 4]`

<a name="replace"></a>

### <a name="replace"></a>replace

Заменяет подстроку указанной строкой и возвращает полученную строку. Эта функция учитывает регистр.

```
replace('<text>', '<oldText>', '<newText>')
```

| Параметр | Обязательно | Тип | Описание |
| --------- | -------- | ---- | ----------- |
| <*Текст*> | Да | Строка | Строка, которая содержит заменяемую подстроку |
| <*oldText*> | Да | Строка | Заменяемая подстрока |
| <*newText*> | Да | Строка | Строка для замены |
|||||

| Возвращаемое значение | Тип | Описание |
| ------------ | ---- | ----------- |
| <*обновленный текст*> | Строка | Обновленная строка после замены подстроки <p>Если подстрока не найдена, возвращает исходную строку. |
||||

*Пример*

Этот пример выполняет поиск подстроки "old" в строке "the old string" и заменяет "old" на "new":

```
replace('the old string', 'old', 'new')
```

Возвращается такой результат: `"the new string"`

<a name="skip"></a>

### <a name="skip"></a>skip

Удаляет элементы из начала коллекции и возвращает *все другие элементы*.

```
skip([<collection>], <count>)
```

| Параметр | Обязательно | Тип | Описание |
| --------- | -------- | ---- | ----------- |
| <*Коллекции*> | Да | Array | Коллекция, элементы которой требуется удалить |
| <*Рассчитывать*> | Да | Целое число | Положительное целое число для количества элементов в начале коллекции, которые требуется удалить |
|||||

| Возвращаемое значение | Тип | Описание |
| ------------ | ---- | ----------- |
| [<*обновленная коллекция*>] | Array | Обновленная коллекция после удаления указанных элементов. |
||||

*Пример*

В этом примере удаляется один элемент, число 0, в начале указанного массива:

```
skip(createArray(0, 1, 2, 3), 1)
```

И возвращается исходный массив и оставшиеся элементы: `[1,2,3]`

<a name="split"></a>

### <a name="split"></a>split

Верните массив, содержащий подстроки, разделенный запятыми, на основе указанного символа делимитедора в исходной строке.

```
split('<text>', '<delimiter>')
```

| Параметр | Обязательно | Тип | Описание |
| --------- | -------- | ---- | ----------- |
| <*Текст*> | Да | Строка | Строка для разделения на подстроки в зависимости от указанного разделителя в исходной строке |
| <*Разделитель*> | Да | Строка | Символ в исходной строке для использования в качестве разделителя |
|||||

| Возвращаемое значение | Тип | Описание |
| ------------ | ---- | ----------- |
| [<*substring1*>,<*substring2*>,...] | Array | Массив, содержащий подстроки из исходной строки, разделенные запятыми |
||||

*Пример*

В этом примере создается массив подстрок из указанной строки с использованием заданного символа в качестве разделителя.

```
split('a_b_c', '_')
```

В результате возвращается такой массив: `["a","b","c"]`.

<a name="startOfDay"></a>

### <a name="startofday"></a>startOfDay

Возвращает начало дня для метки времени.

```
startOfDay('<timestamp>', '<format>'?)
```

| Параметр | Обязательно | Тип | Описание |
| --------- | -------- | ---- | ----------- |
| <*Timestamp*> | Да | Строка | Строка, содержащая метку времени |
| <*Формат*> | нет | Строка | Либо [один описатель формата](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings), либо [пользовательский шаблон формата](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings). Формат по умолчанию для метки времени — ["o"](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (ГГГГ-ММ-ДДTЧЧ:ММ:fffffffK), который соответствует [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) и сохраняет информацию о часовом поясе. |
|||||

| Возвращаемое значение | Тип | Описание |
| ------------ | ---- | ----------- |
| <*обновленная штамповка времени*> | Строка | Указанная метка времени, которая начинается с нулевого часа дня |
||||

*Пример*

Этот пример находит начало дня для этой метки времени:

```
startOfDay('2018-03-15T13:30:30Z')
```

Возвращается такой результат: `"2018-03-15T00:00:00.0000000Z"`

<a name="startOfHour"></a>

### <a name="startofhour"></a>startOfHour

Возвращает начало часа для метки времени.

```
startOfHour('<timestamp>', '<format>'?)
```

| Параметр | Обязательно | Тип | Описание |
| --------- | -------- | ---- | ----------- |
| <*Timestamp*> | Да | Строка | Строка, содержащая метку времени |
| <*Формат*> | нет | Строка | Либо [один описатель формата](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings), либо [пользовательский шаблон формата](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings). Формат по умолчанию для метки времени — ["o"](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (ГГГГ-ММ-ДДTЧЧ:ММ:fffffffK), который соответствует [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) и сохраняет информацию о часовом поясе. |
|||||

| Возвращаемое значение | Тип | Описание |
| ------------ | ---- | ----------- |
| <*обновленная штамповка времени*> | Строка | Указанная метка времени, которая начинается с нулевой минуты часа |
||||

*Пример*

Этот пример находит начало часа для этой метки времени:

```
startOfHour('2018-03-15T13:30:30Z')
```

Возвращается такой результат: `"2018-03-15T13:00:00.0000000Z"`

<a name="startOfMonth"></a>

### <a name="startofmonth"></a>startOfMonth

Возвращает начало месяца для метки времени.

```
startOfMonth('<timestamp>', '<format>'?)
```

| Параметр | Обязательно | Тип | Описание |
| --------- | -------- | ---- | ----------- |
| <*Timestamp*> | Да | Строка | Строка, содержащая метку времени |
| <*Формат*> | нет | Строка | Либо [один описатель формата](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings), либо [пользовательский шаблон формата](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings). Формат по умолчанию для метки времени — ["o"](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (ГГГГ-ММ-ДДTЧЧ:ММ:fffffffK), который соответствует [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) и сохраняет информацию о часовом поясе. |
|||||

| Возвращаемое значение | Тип | Описание |
| ------------ | ---- | ----------- |
| <*обновленная штамповка времени*> | Строка | Указанная метка времени, которая начинается с нулевого часа первого дня месяца |
||||

*Пример*

Этот пример находит начало месяца для этой метки времени:

```
startOfMonth('2018-03-15T13:30:30Z')
```

Возвращается такой результат: `"2018-03-01T00:00:00.0000000Z"`

<a name="startswith"></a>

### <a name="startswith"></a>startsWith

Проверяет, начинается ли строка с определенной подстроки.
Возвращает true, если подстрока найдена, или возвращает false, если нет.
Эта функция не учитывает регистр.

```
startsWith('<text>', '<searchText>')
```

| Параметр | Обязательно | Тип | Описание |
| --------- | -------- | ---- | ----------- |
| <*Текст*> | Да | Строка | Проверяемая строка |
| <*searchText*> | Да | Строка | Начальная строка для поиска |
|||||

| Возвращаемое значение | Тип | Описание |
| ------------ | ---- | ----------- |
| true или false  | Логическое | Возвращает значение true при обнаружении начальной подстроки. В противном случае возвращает значение false. |
||||

*Пример 1*

В этом примере проверяется, начинается ли строка "hello world" подстрокой "hello":

```
startsWith('hello world', 'hello')
```

Возвращается такой результат: `true`

*Пример 2*

В этом примере проверяется, начинается ли строка "hello world" подстрокой "greetings":

```
startsWith('hello world', 'greetings')
```

Возвращается такой результат: `false`

<a name="string"></a>

### <a name="string"></a>строка

Возвращает строковую версию значения.

```
string(<value>)
```

| Параметр | Обязательно | Тип | Описание |
| --------- | -------- | ---- | ----------- |
| <*Значение*> | Да | Любой | Значение, которое необходимо преобразовать |
|||||

| Возвращаемое значение | Тип | Описание |
| ------------ | ---- | ----------- |
| <*строка-значение*> | Строка | Строковая версия указанного значения |
||||

*Пример 1*

В этом примере создается строковая версия для этого числа:

```
string(10)
```

Возвращается такой результат: `"10"`

*Пример 2*

В этом примере создается строка для указанного объекта JSON и используется символ обратной косой черты (\\) в качестве escape-символа для двойных кавычек (").

```
string( { "name": "Sophie Owen" } )
```

Возвращается такой результат: `"{ \\"name\\": \\"Sophie Owen\\" }"`

<a name="sub"></a>

### <a name="sub"></a>sub

Вычитает второе число из первого числа и возвращает результат.

```
sub(<minuend>, <subtrahend>)
```

| Параметр | Обязательно | Тип | Описание |
| --------- | -------- | ---- | ----------- |
| <*minuend*> | Да | Целое число и число с плавающей запятой | Число, из которого вычитается *вычитаемое* |
| <*субтрахенд*> | Да | Целое число и число с плавающей запятой | Число, которое вычитается из *уменьшаемого* |
|||||

| Возвращаемое значение | Тип | Описание |
| ------------ | ---- | ----------- |
| <*Результат*> | Целое число и число с плавающей запятой | Вычитается второе число из первого числа и возвращается результат |
||||

*Пример*

В этом примере из первого числа вычитается второе число:

```
sub(10.3, .3)
```

Возвращается такой результат: `10`

<a name="substring"></a>

### <a name="substring"></a>substring

Возвращает символы из строки, начиная с указанной позиции или индекса.
Значения индекса начинаются с числа 0.

```
substring('<text>', <startIndex>, <length>)
```

| Параметр | Обязательно | Тип | Описание |
| --------- | -------- | ---- | ----------- |
| <*Текст*> | Да | Строка | Строка, символы которой требуется получить |
| <*Startindex*> | Да | Целое число | Положительное число, равное или превышающее 0, которое вы хотите использовать в качестве исходного положения или значения индекса |
| <*Длина*> | Да | Целое число | Положительное число символов для подстроки |
|||||

| Возвращаемое значение | Тип | Описание |
| ------------ | ---- | ----------- |
| <*подстрока-результат*> | Строка | Подстрока с указанным количеством символов, начиная с указанной позиции индекса в исходной строке |
||||

*Пример*

В этом примере создается пятисимвольная подстрока из указанной строки, начиная со значения индекса 6:

```
substring('hello world', 6, 5)
```

Возвращается такой результат: `"world"`

<a name="subtractFromTime"></a>

### <a name="subtractfromtime"></a>subtractFromTime

Вычитает количество единиц времени из метки времени.
См. раздел [getPastTime](#getPastTime).

```
subtractFromTime('<timestamp>', <interval>, '<timeUnit>', '<format>'?)
```

| Параметр | Обязательно | Тип | Описание |
| --------- | -------- | ---- | ----------- |
| <*Timestamp*> | Да | Строка | Строка, содержащая метку времени |
| <*Интервал*> | Да | Целое число | Число единиц времени для вычитания |
| <*timeUnit*> | Да | Строка | Единицы времени для использования с *интервалом*: "секунда", "минута", "час", "день", "неделя", "месяц", "год" |
| <*Формат*> | нет | Строка | Либо [один описатель формата](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings), либо [пользовательский шаблон формата](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings). Формат по умолчанию для метки времени — ["o"](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (ГГГГ-ММ-ДДTЧЧ:ММ:fffffffK), который соответствует [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) и сохраняет информацию о часовом поясе. |
|||||

| Возвращаемое значение | Тип | Описание |
| ------------ | ---- | ----------- |
| <*обновленная штамповка времени*> | Строка | Метка времени, от которой отнято указанное число единиц времени |
||||

*Пример 1*

В этом примере из этой метки времени вычитается один день:

```
subtractFromTime('2018-01-02T00:00:00Z', 1, 'Day')
```

Возвращается такой результат: `"2018-01-01T00:00:00:0000000Z"`

*Пример 2*

В этом примере из этой метки времени вычитается один день:

```
subtractFromTime('2018-01-02T00:00:00Z', 1, 'Day', 'D')
```

И возвращается результат с использованием необязательного формата "D": `"Monday, January, 1, 2018"`

<a name="take"></a>

### <a name="take"></a>take

Возвращает элементы, расположенные в начале коллекции.

```
take('<collection>', <count>)
take([<collection>], <count>)
```

| Параметр | Обязательно | Тип | Описание |
| --------- | -------- | ---- | ----------- |
| <*Коллекции*> | Да | Строка или массив | Коллекция, элементы которой требуется получить |
| <*Рассчитывать*> | Да | Целое число | Положительное целое число для количества элементов в начале коллекции, которые требуется получить |
|||||

| Возвращаемое значение | Тип | Описание |
| ------------ | ---- | ----------- |
| <*подмножество*> или [<*подмножество*>] | Строка или массив соответственно | Строка или массив, который содержит заданное число элементов, взятых из первой части исходной коллекции |
||||

*Пример*

Эти примеры получают указанное количество элементов из первой части этих коллекций:

```
take('abcde', 3)
take(createArray(0, 1, 2, 3, 4), 3)
```

И возвращаются следующие результаты:

* Первый пример: `"abc"`.
* Второй пример: `[0, 1, 2]`.

<a name="ticks"></a>

### <a name="ticks"></a>ticks

Возвращает значение свойства `ticks` для указанной метки времени.
*Интервал* составляет 100 наносекунд.

```
ticks('<timestamp>')
```

| Параметр | Обязательно | Тип | Описание |
| --------- | -------- | ---- | ----------- |
| <*Timestamp*> | Да | Строка | Строка для метки времени |
|||||

| Возвращаемое значение | Тип | Описание |
| ------------ | ---- | ----------- |
| <*тиков-число*> | Целое число | Число тактов с момента в указанной метке времени |
||||

<a name="toLower"></a>

### <a name="tolower"></a>toLower

Возвращает строку символов в нижнем регистре. Если символ в строке не имеет версии в нижнем регистре, он добавляется в возвращаемую строку без изменений.

```
toLower('<text>')
```

| Параметр | Обязательно | Тип | Описание |
| --------- | -------- | ---- | ----------- |
| <*Текст*> | Да | Строка | Строка, возвращаемая в нижнем регистре |
|||||

| Возвращаемое значение | Тип | Описание |
| ------------ | ---- | ----------- |
| <*текст нижнего регистра*> | Строка | Исходная строка в нижнем регистре |
||||

*Пример*

В этом примере эта строка преобразуется в нижний регистр:

```
toLower('Hello World')
```

Возвращается такой результат: `"hello world"`

<a name="toUpper"></a>

### <a name="toupper"></a>toUpper

Возвращает строку символов в верхнем регистре. Если символ в строке не имеет версии в верхнем регистре, он добавляется в возвращаемую строку без изменений.

```
toUpper('<text>')
```

| Параметр | Обязательно | Тип | Описание |
| --------- | -------- | ---- | ----------- |
| <*Текст*> | Да | Строка | Строка, возвращаемая в верхнем регистре |
|||||

| Возвращаемое значение | Тип | Описание |
| ------------ | ---- | ----------- |
| <*верхний текст*> | Строка | Исходная строка в верхнем регистре |
||||

*Пример*

В этом примере эта строка преобразуется в верхний регистр:

```
toUpper('Hello World')
```

Возвращается такой результат: `"HELLO WORLD"`

<a name="trim"></a>

### <a name="trim"></a>trim

Удаляет все начальные и конечные пробелы и возвращает обновленную строку.

```
trim('<text>')
```

| Параметр | Обязательно | Тип | Описание |
| --------- | -------- | ---- | ----------- |
| <*Текст*> | Да | Строка | Строка, которая содержит начальные и конечные пробелы для удаления |
|||||

| Возвращаемое значение | Тип | Описание |
| ------------ | ---- | ----------- |
| <*обновленныйТекст*> | Строка | Обновленная версия исходной строки без начальных и конечных пробелов |
||||

*Пример*

В этом примере удаляются начальные и конечные пробелы из строки " Hello World  ":

```
trim(' Hello World  ')
```

Возвращается такой результат: `"Hello World"`

<a name="union"></a>

### <a name="union"></a>union

Возвращает коллекцию, которая содержит *все* элементы из указанных коллекций.
Чтобы появиться в результатах, элемент должен содержаться в любой коллекции, переданной этой функции. Если один или несколько элементов имеют одинаковое имя, в результатах появляется последний элемент с таким именем.

```
union('<collection1>', '<collection2>', ...)
union([<collection1>], [<collection2>], ...)
```

| Параметр | Обязательно | Тип | Описание |
| --------- | -------- | ---- | ----------- |
| <*collection1*>, <*collection2*>, ...  | Да | Массив или объект, но не оба типа | Коллекции, из которых вы хотите получить *все* элементы |
|||||

| Возвращаемое значение | Тип | Описание |
| ------------ | ---- | ----------- |
| <*обновленнаяКоллекция*> | Массив или объект соответственно | Коллекция, которая содержит все элементы из указанных коллекций, без повторений |
||||

*Пример*

В этом примере извлекаются *все* элементы из коллекций:

```
union(createArray(1, 2, 3), createArray(1, 2, 10, 101))
```

Возвращается такой результат: `[1, 2, 3, 10, 101]`

<a name="uriComponent"></a>

### <a name="uricomponent"></a>uriComponent

Возвращает кодированную версию URI для строки, заменив символы, опасные для URL-адреса, на escape-символы.
Используйте эту функцию вместо [encodeUriComponent()](#encodeUriComponent).
Хотя обе функции работают одинаково, `uriComponent()` является предпочтительной.

```
uriComponent('<value>')
```

| Параметр | Обязательно | Тип | Описание |
| --------- | -------- | ---- | ----------- |
| <*Значение*> | Да | Строка | Строка для преобразования в формат закодированного URI |
|||||

| Возвращаемое значение | Тип | Описание |
| ------------ | ---- | ----------- |
| <*закодированный-ури*> | Строка | Строка с закодированным URI, содержащая escape-символы |
||||

*Пример*

В этом примере создается версия с закодированным URI для этой строки:

```
uriComponent('https://contoso.com')
```

Возвращается такой результат: `"http%3A%2F%2Fcontoso.com"`

<a name="uriComponentToBinary"></a>

### <a name="uricomponenttobinary"></a>uriComponentToBinary

Возвращает двоичную версию компонента URI.

```
uriComponentToBinary('<value>')
```

| Параметр | Обязательно | Тип | Описание |
| --------- | -------- | ---- | ----------- |
| <*Значение*> | Да | Строка | Преобразуемая строка с закодированным URI |
|||||

| Возвращаемое значение | Тип | Описание |
| ------------ | ---- | ----------- |
| <*бинарный для кодирования-ури*> | Строка | Двоичная версия строки с закодированным URI. Двоичное содержимое в формате Base64, представленное с помощью `$content`. |
||||

*Пример*

В этом примере создается двоичная версия для этой строки с закодированным URI:

```
uriComponentToBinary('http%3A%2F%2Fcontoso.com')
```

Возвращается такой результат:

`"001000100110100001110100011101000111000000100101001100
11010000010010010100110010010001100010010100110010010001
10011000110110111101101110011101000110111101110011011011
110010111001100011011011110110110100100010"`

<a name="uriComponentToString"></a>

### <a name="uricomponenttostring"></a>uriComponentToString

Возвращает декодированную версию строки с закодированным URI.

```
uriComponentToString('<value>')
```

| Параметр | Обязательно | Тип | Описание |
| --------- | -------- | ---- | ----------- |
| <*Значение*> | Да | Строка | Декодируемая строка с закодированным URI |
|||||

| Возвращаемое значение | Тип | Описание |
| ------------ | ---- | ----------- |
| <*расшифровано-ури*> | Строка | Декодированная версия строки с закодированным URI |
||||

*Пример*

В этом примере создается декодированная версия строки с закодированным URI:

```
uriComponentToString('http%3A%2F%2Fcontoso.com')
```

Возвращается такой результат: `"https://contoso.com"`

<a name="utcNow"></a>

### <a name="utcnow"></a>utcnow

Возвращает текущую метку времени.

```
utcNow('<format>')
```

Кроме того, можно указать другой формат с помощью параметра <*format*>.

| Параметр | Обязательно | Тип | Описание |
| --------- | -------- | ---- | ----------- |
| <*Формат*> | нет | Строка | Либо [один описатель формата](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings), либо [пользовательский шаблон формата](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings). Формат по умолчанию для метки времени — ["o"](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (ГГГГ-ММ-ДДTЧЧ:ММ:fffffffK), который соответствует [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) и сохраняет информацию о часовом поясе. |
|||||

| Возвращаемое значение | Тип | Описание |
| ------------ | ---- | ----------- |
| <*текущая штамповка времени*> | Строка | Текущая дата и время |
||||

*Пример 1*

Предположим, сейчас 13:00:00 15 апреля 2018 года.
В этом примере возвращается текущая метка времени:

```
utcNow()
```

Возвращается такой результат: `"2018-04-15T13:00:00.0000000Z"`

*Пример 2*

Предположим, сейчас 13:00:00 15 апреля 2018 года.
В этом примере возвращается текущая метка времени с использованием необязательного формата "D":

```
utcNow('D')
```

Возвращается такой результат: `"Sunday, April 15, 2018"`

<a name="xml"></a>

### <a name="xml"></a>Xml

Возвращает версию XML строки, которая содержит объект JSON.

```
xml('<value>')
```

| Параметр | Обязательно | Тип | Описание |
| --------- | -------- | ---- | ----------- |
| <*Значение*> | Да | Строка | Строка с объектом JSON для преобразования <p>Объект JSON должен содержать только одно корневое свойство, которое не может быть массивом. <br>Используйте обратную косую черту (\\) как escape-символ для двойных кавычек ("). |
|||||

| Возвращаемое значение | Тип | Описание |
| ------------ | ---- | ----------- |
| <*xml-версия*> | Объект | Закодированный XML для заданной строки или объекта JSON |
||||

*Пример 1*

В этом примере создается версия XML для этой строки, которая содержит объект JSON:

`xml(json('{ \"name\": \"Sophia Owen\" }'))`

Возвращается следующий XML:

```xml
<name>Sophia Owen</name>
```

*Пример 2*

Предположим, что у вас есть этот объект JSON:

```json
{
  "person": {
    "name": "Sophia Owen",
    "city": "Seattle"
  }
}
```

В этом примере создается XML для строки, содержащей этот объект JSON:

`xml(json('{\"person\": {\"name\": \"Sophia Owen\", \"city\": \"Seattle\"}}'))`

Возвращается следующий XML:

```xml
<person>
  <name>Sophia Owen</name>
  <city>Seattle</city>
<person>
```

<a name="xpath"></a>

### <a name="xpath"></a>xpath

Проверяет XML на наличие узлов или значений, которые соответствуют выражению XPath, и возвращает соответствующие узлы или значения. Выражение XPath (или просто "XPath") помогает перемещаться по структуре документа XML, чтобы вы могли выбирать узлы или вычислять значения в содержимом XML.

```
xpath('<xml>', '<xpath>')
```

| Параметр | Обязательно | Тип | Описание |
| --------- | -------- | ---- | ----------- |
| <*Xml*> | Да | Любой | Строка XML для поиска узлов и значений, которые соответствуют значению выражения XPath |
| <*Xpath*> | Да | Любой | Выражение XPath, используемое для поиска соответствующих узлов или значений XML |
|||||

| Возвращаемое значение | Тип | Описание |
| ------------ | ---- | ----------- |
| <*xml-узла*> | XML | Узел XML, где только один узел соответствует указанному выражению XPath |
| <*Значение*> | Любой | Значение из узла XML, где только одно значение соответствует указанному выражению XPath |
| [<*узел XML1*>, <*узел XML2*>, ...] </br>-или- </br>[<*значение1*>, <*значение2*>, ...] | Array | Массив с узлами XML или значениями, которые соответствуют указанному выражению XPath |
||||

*Пример 1*

Следуя примеру 1, этот пример находит узлы, которые соответствуют узлу `<count></count>`, и добавляет эти значения с помощью функции `sum()`:

`xpath(xml(parameters('items')), 'sum(/produce/item/count)')`

Возвращается такой результат: `30`

*Пример 2*

В этом примере оба выражения находят узлы, которые соответствуют узлу `<location></location>` в указанных аргументах, которые содержат XML с пространством имен. Выражения используют обратную косую черту (\\) как escape-символ для двойных кавычек (").

* *Выражение 1*

  `xpath(xml(body('Http')), '/*[name()=\"file\"]/*[name()=\"location\"]')`

* *Выражение 2*

  `xpath(xml(body('Http')), '/*[local-name()=\"file\" and namespace-uri()=\"http://contoso.com\"]/*[local-name()=\"location\"]')`

Ниже приведены аргументы.

* Это код XML, который включает пространство имен документа XML `xmlns="http://contoso.com"`:

  ```xml
  <?xml version="1.0"?> <file xmlns="http://contoso.com"> <location>Paris</location> </file>
  ```

* Можно использовать любое выражение XPath:

  * `/*[name()=\"file\"]/*[name()=\"location\"]`

  * `/*[local-name()=\"file\" and namespace-uri()=\"http://contoso.com\"]/*[local-name()=\"location\"]`

Ниже приведен узел, соответствующий узлу `<location></location>`:

```xml
<location xmlns="https://contoso.com">Paris</location>
```

*Пример 3*

Следуя примеру 3, этот пример находит значение в узле `<location></location>`:

`xpath(xml(body('Http')), 'string(/*[name()=\"file\"]/*[name()=\"location\"])')`

Возвращается такой результат: `"Paris"`

## <a name="next-steps"></a>Дальнейшие действия
Список системных переменных, которые можно использовать в выражениях, см. в статье [System variables supported by Azure Data Factory](control-flow-system-variables.md) (Системные переменные, поддерживаемые фабрикой данных Azure).
