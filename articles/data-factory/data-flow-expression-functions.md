---
title: Функции выражений в потоке данных сопоставления
description: Сведения о функциях выражений в сопоставлении потока данных.
author: kromerm
ms.author: makromer
manager: anandsub
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/15/2019
ms.openlocfilehash: f384c440dab06660c95f635dde02ced5b3e54d94
ms.sourcegitcommit: 8bd85510aee664d40614655d0ff714f61e6cd328
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/06/2019
ms.locfileid: "74896314"
---
# <a name="data-transformation-expressions-in-mapping-data-flow"></a>Выражения преобразования данных в потоке данных сопоставления 

## <a name="expression-functions"></a>Функции выражений

В фабрике данных используйте язык выражений функции потока данных сопоставления для настройки преобразований данных.

___
### <code>abs</code>
<code><b>abs(<i>&lt;value1&gt;</i> : number) => number</b></code><br/><br/>
Абсолютное значение числа.

* ``abs(-20) -> 20``

* ``abs(10) -> 10``

___
### <code>acos</code>
<code><b>acos(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Вычисляет арккосинус значения

* ``acos(1) -> 0.0``

___
### <code>add</code>
<code><b>add(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => any</b></code><br/><br/>
Добавляет пару строк или чисел. Добавляет дату к числу дней. Добавляет длительность в отметку времени. Добавляет один массив аналогичного типа к другому. То же, что и оператор +

* ``add(10, 20) -> 30``

* ``10 + 20 -> 30``

* ``add('ice', 'cream') -> 'icecream'``

* ``'ice' + 'cream' + ' cone' -> 'icecream cone'``

* ``add(toDate('2012-12-12'), 3) -> toDate('2012-12-15')``

* ``toDate('2012-12-12') + 3 -> toDate('2012-12-15')``

* ``[10, 20] + [30, 40] -> [10, 20, 30, 40]``

* ``toTimestamp('2019-02-03 05:19:28.871', 'yyyy-MM-dd HH:mm:ss.SSS') + (days(1) + hours(2) - seconds(10)) -> toTimestamp('2019-02-04 07:19:18.871', 'yyyy-MM-dd HH:mm:ss.SSS')``

___
### <code>addDays</code>
<code><b>addDays(<i>&lt;date/timestamp&gt;</i> : datetime, <i>&lt;days to add&gt;</i> : integral) => datetime</b></code><br/><br/>
Добавляет дни к дате или метке времени. То же, что и оператор + для даты

* ``addDays(toDate('2016-08-08'), 1) -> toDate('2016-08-09')``

___
### <code>addMonths</code>
<code><b>addMonths(<i>&lt;date/timestamp&gt;</i> : datetime, <i>&lt;months to add&gt;</i> : integral, [<i>&lt;value3&gt;</i> : string]) => datetime</b></code><br/><br/>
Добавление месяцев к дате или метке времени. При необходимости можно передать часовой пояс

* ``addMonths(toDate('2016-08-31'), 1) -> toDate('2016-09-30')``

* ``addMonths(toTimestamp('2016-09-30 10:10:10'), -1) -> toTimestamp('2016-08-31 10:10:10')``

___
### <code>and</code>
<code><b>and(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : boolean) => boolean</b></code><br/><br/>
Логический оператор AND. То же, что и & &

* ``and(true, false) -> false``

* ``true && false -> false``

___
### <code>asin</code>
<code><b>asin(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Вычисляет обратное значение синуса

* ``asin(0) -> 0.0``

___
### <code>atan</code>
<code><b>atan(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Вычисляет обратное значение тангенса

* ``atan(0) -> 0.0``

___
### <code>atan2</code>
<code><b>atan2(<i>&lt;value1&gt;</i> : number, <i>&lt;value2&gt;</i> : number) => double</b></code><br/><br/>
Возвращает угол в радианах между положительной осью x плоскости и точкой, заданной координатами

* ``atan2(0, 0) -> 0.0``

___
### <code>avg</code>
<code><b>avg(<i>&lt;value1&gt;</i> : number) => number</b></code><br/><br/>
Возвращает среднее значение столбца.

* ``avg(sales)``

___
### <code>avgIf</code>
<code><b>avgIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number) => number</b></code><br/><br/>
На основе критерия Возвращает среднее значение столбца

* ``avgIf(region == 'West', sales)``

___
### <code>byName</code>
<code><b>byName(<i>&lt;column name&gt;</i> : string, [<i>&lt;stream name&gt;</i> : string]) => any</b></code><br/><br/>
Выбирает значение столбца по имени в потоке. В качестве второго аргумента можно передать необязательное имя потока. При наличии нескольких совпадений возвращается первое совпадение. Если совпадений нет, возвращается значение NULL. Возвращаемое значение должно быть преобразовано с помощью одной из функций преобразования типа (TO_DATE, TO_STRING...).  Имена столбцов, известные во время разработки, должны быть адресованы только по имени. Вычисленные входные данные не поддерживаются, но можно использовать подстановки параметров

* ``toString(byName('parent'))``

* ``toLong(byName('income'))``

* ``toBoolean(byName('foster'))``

* ``toLong(byName($debtCol))``

* ``toString(byName('Bogus Column'))``

* ``toString(byName('Bogus Column', 'DeriveStream'))``

___
### <code>byPosition</code>
<code><b>byPosition(<i>&lt;position&gt;</i> : integer) => any</b></code><br/><br/>
Выбирает значение столбца по относительному положению (1 на основе) в потоке. Если расположение выходит за пределы допустимого диапазона, возвращается значение NULL. Возвращаемое значение должно быть преобразовано с помощью одной из функций преобразования типов (TO_DATE, TO_STRING...) Вычисленные входные данные не поддерживаются, но можно использовать подстановки параметров

* ``toString(byPosition(1))``

* ``toDecimal(byPosition(2), 10, 2)``

* ``toBoolean(byName(4))``

* ``toString(byName($colName))``

* ``toString(byPosition(1234))``

___
### <code>case</code>
<code><b>case(<i>&lt;condition&gt;</i> : boolean, <i>&lt;true_expression&gt;</i> : any, <i>&lt;false_expression&gt;</i> : any, ...) => any</b></code><br/><br/>
На основе чередующихся условий применяется одно или другое значение. Если число входов четное, другое значение по умолчанию равно NULL для последнего условия

* ``case(10 + 20 == 30, 'dumbo', 'gumbo') -> 'dumbo'``

* ``case(10 + 20 == 25, 'bojjus', 'do' < 'go', 'gunchus') -> 'gunchus'``

* ``isNull(case(10 + 20 == 25, 'bojjus', 'do' > 'go', 'gunchus')) -> true``

* ``case(10 + 20 == 25, 'bojjus', 'do' > 'go', 'gunchus', 'dumbo') -> 'dumbo'``

___
### <code>cbrt</code>
<code><b>cbrt(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Вычисление корня Куба числа

* ``cbrt(8) -> 2.0``

___
### <code>ceil</code>
<code><b>ceil(<i>&lt;value1&gt;</i> : number) => number</b></code><br/><br/>
Возвращает наименьшее целое число, не меньшее числа

* ``ceil(-0.1) -> 0``

___
### <code>coalesce</code>
<code><b>coalesce(<i>&lt;value1&gt;</i> : any, ...) => any</b></code><br/><br/>
Возвращает первое значение, не равное NULL, из набора входных данных. Все входные данные должны иметь один и тот же тип

* ``coalesce(10, 20) -> 10``

* ``coalesce(toString(null), toString(null), 'dumbo', 'bo', 'go') -> 'dumbo'``
___
### <code>compare</code>
<code><b>compare(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => integer</b></code><br/><br/>
Сравнивает два значения одного типа. Возвращает отрицательное целое число, если значение1 < value2, 0, если значение1 = = value2, положительное значение, если значение1 > value2

* ``(compare(12, 24) < 1) -> true``

* ``(compare('dumbo', 'dum') > 0) -> true``

___
### <code>concat</code>
<code><b>concat(<i>&lt;this&gt;</i> : string, <i>&lt;that&gt;</i> : string, ...) => string</b></code><br/><br/>
Объединяет переменное количество строк. То же, что и оператор + со строками

* ``concat('dataflow', 'is', 'awesome') -> 'dataflowisawesome'``

* ``'dataflow' + 'is' + 'awesome' -> 'dataflowisawesome'``

* ``isNull('sql' + null) -> true``

___
### <code>concatWS</code>
<code><b>concatWS(<i>&lt;separator&gt;</i> : string, <i>&lt;this&gt;</i> : string, <i>&lt;that&gt;</i> : string, ...) => string</b></code><br/><br/>
Объединяет переменное количество строк с использованием разделителя. Первый параметр — это разделитель.

* ``concatWS(' ', 'dataflow', 'is', 'awesome') -> 'dataflow is awesome'``

* ``isNull(concatWS(null, 'dataflow', 'is', 'awesome')) -> true``

* ``concatWS(' is ', 'dataflow', 'awesome') -> 'dataflow is awesome'``

___
### <code>contains</code>
<code><b>contains(<i>&lt;value1&gt;</i> : array, <i>&lt;value2&gt;</i> : unaryfunction) => boolean</b></code><br/><br/>
Возвращает значение true, если любой элемент в предоставленном массиве принимает значение true в указанном предикате. Contains принимает ссылку на один элемент в функции предиката, как #item

* ``contains([1, 2, 3, 4], #item == 3) -> true``

* ``contains([1, 2, 3, 4], #item > 5) -> false``

___
### <code>cos</code>
<code><b>cos(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Вычисляет значение косинуса

* ``cos(10) -> -0.8390715290764524``

___
### <code>cosh</code>
<code><b>cosh(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Вычисляет гиперболический косинус значения

* ``cosh(0) -> 1.0``

___
### <code>count</code>
<code><b>count([<i>&lt;value1&gt;</i> : any]) => long</b></code><br/><br/>
Возвращает совокупное количество значений. Если указаны необязательные столбцы, то значения NULL не учитываются в числе.

* ``count(custId)``

* ``count(custId, custName)``

* ``count()``

* ``count(iif(isNull(custId), 1, NULL))``

___
### <code>countDistinct</code>
<code><b>countDistinct(<i>&lt;value1&gt;</i> : any, [<i>&lt;value2&gt;</i> : any], ...) => long</b></code><br/><br/>
Возвращает общее число различных значений набора столбцов.

* ``countDistinct(custId, custName)``

___
### <code>countIf</code>
<code><b>countIf(<i>&lt;value1&gt;</i> : boolean, [<i>&lt;value2&gt;</i> : any]) => long</b></code><br/><br/>
Возвращает совокупное количество значений на основе критериев. Если указан необязательный столбец, он игнорирует значения NULL в числе.

* ``countIf(state == 'CA' && commission < 10000, name)``

___
### <code>covariancePopulation</code>
<code><b>covariancePopulation(<i>&lt;value1&gt;</i> : number, <i>&lt;value2&gt;</i> : number) => double</b></code><br/><br/>
Возвращает ковариацию заполнения между двумя столбцами

* ``covariancePopulation(sales, profit)``

___
### <code>covariancePopulationIf</code>
<code><b>covariancePopulationIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number, <i>&lt;value3&gt;</i> : number) => double</b></code><br/><br/>
В зависимости от критерия получает ковариацию Генеральной совокупности двух столбцов

* ``covariancePopulationIf(region == 'West', sales)``

___
### <code>covarianceSample</code>
<code><b>covarianceSample(<i>&lt;value1&gt;</i> : number, <i>&lt;value2&gt;</i> : number) => double</b></code><br/><br/>
Возвращает ковариацию выборки двух столбцов

* ``covarianceSample(sales, profit)``

___
### <code>covarianceSampleIf</code>
<code><b>covarianceSampleIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number, <i>&lt;value3&gt;</i> : number) => double</b></code><br/><br/>
В зависимости от критериев получает выборку ковариации двух столбцов

* ``covarianceSampleIf(region == 'West', sales, profit)``

___
### <code>crc32</code>
<code><b>crc32(<i>&lt;value1&gt;</i> : any, ...) => long</b></code><br/><br/>
Вычисляет хэш-код CRC32 набора столбцов различных примитивных типов данных с заданной разрядностью, которая может иметь только значения 0(256), 224, 256, 384, 512. Его можно использовать для вычисления отпечатка для строки

* ``crc32(256, 'gunchus', 8.2, 'bojjus', true, toDate('2010-4-4')) -> 3630253689L``

___
### <code>cumeDist</code>
<code><b>cumeDist() => integer</b></code><br/><br/>
Функция CumeDist вычисляет позицию значения относительно всех значений в разделе. Результатом является количество строк, предшествующих или равных текущей строке в упорядоченном наборе раздела, деленное на общее количество строк в разделе окна. Все привязку значений в упорядочении будут иметь ту же самую точку.

* ``cumeDist()``

___
### <code>currentDate</code>
<code><b>currentDate([<i>&lt;value1&gt;</i> : string]) => date</b></code><br/><br/>
Возвращает текущую дату начала выполнения этого задания. Вы можете передать дополнительный часовой пояс в формате GMT, PST, UTC, "Острова Кайман". По умолчанию используется локальный часовой пояс. Сведения о доступных форматах см. в разделе Симпледатеформат Java. https://docs.oracle.com/javase/8/docs/api/java/text/SimpleDateFormat.html

* ``currentDate() == toDate('2250-12-31') -> false``

* ``currentDate('PST')  == toDate('2250-12-31') -> false``

* ``currentDate('America/New_York')  == toDate('2250-12-31') -> false``

___
### <code>currentTimestamp</code>
<code><b>currentTimestamp() => timestamp</b></code><br/><br/>
Возвращает текущую метку времени, когда задание начинает выполняться с местным часовым поясом.

* ``currentTimestamp() == toTimestamp('2250-12-31 12:12:12') -> false``

___
### <code>currentUTC</code>
<code><b>currentUTC([<i>&lt;value1&gt;</i> : string]) => timestamp</b></code><br/><br/>

Возвращает текущую метку времени в формате UTC. Если вы хотите, чтобы текущее время было интерпретировано на другом часовом поясе, отличном от часового пояса кластера, можно передать дополнительный часовой пояс в формате "GMT", "PST", "UTC", "America/Кайман". По умолчанию используется текущий часовой пояс. Сведения о доступных форматах см. в разделе Симпледатеформат Java. Используйте [симпледатеформат](https://docs.oracle.com/javase/8/docs/api/java/text/SimpleDateFormat.html) для преобразования времени в формате UTC в другой часовой пояс, используйте фромутк ().

* ``currentUTC() == toTimestamp('2050-12-12 19:18:12') -> false``

* ``currentUTC() != toTimestamp('2050-12-12 19:18:12') -> true``

* ``fromUTC(currentUTC(), 'Asia/Seoul') != toTimestamp('2050-12-12 19:18:12') -> true``

___
### <code>dayOfMonth</code>
<code><b>dayOfMonth(<i>&lt;value1&gt;</i> : datetime) => integer</b></code><br/><br/>
Возвращает день месяца, заданный в качестве даты

* ``dayOfMonth(toDate('2018-06-08')) -> 8``

___
### <code>dayOfWeek</code>
<code><b>dayOfWeek(<i>&lt;value1&gt;</i> : datetime) => integer</b></code><br/><br/>
Возвращает день недели для заданной даты. 1 – воскресенье, 2 – понедельник..., 7 — Суббота

* ``dayOfWeek(toDate('2018-06-08')) -> 6``

___
### <code>dayOfYear</code>
<code><b>dayOfYear(<i>&lt;value1&gt;</i> : datetime) => integer</b></code><br/><br/>
Возвращает день года, заданный в качестве даты

* ``dayOfYear(toDate('2016-04-09')) -> 100``

___
### <code>days</code>
<code><b>days(<i>&lt;value1&gt;</i> : integer) => long</b></code><br/><br/>
Длительность в миллисекундах для количества дней

* ``days(2) -> 172800000L``

___
### <code>degrees</code>
<code><b>degrees(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Преобразует радианы в градусы

* ``degrees(3.141592653589793) -> 180``

___
### <code>denseRank</code>
<code><b>denseRank(<i>&lt;value1&gt;</i> : any, ...) => integer</b></code><br/><br/>
Вычисляет ранг значения в группе значений. Результатом является единица плюс количество строк, предшествующих или равных текущей строке в упорядоченном наборе раздела. Значения не будут создавать промежутки в последовательности. Сжатый ранг работает даже в том случае, если данные не отсортированы и выполняется поиск изменений в значениях

* ``denseRank(salesQtr, salesAmt)``

___
### <code>divide</code>
<code><b>divide(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => any</b></code><br/><br/>
Делит пару чисел. То же, что и оператор/

* ``divide(20, 10) -> 2``

* ``20 / 10 -> 2``

___
### <code>endsWith</code>
<code><b>endsWith(<i>&lt;string&gt;</i> : string, <i>&lt;substring to check&gt;</i> : string) => boolean</b></code><br/><br/>
Проверяет, заканчивается ли строка указанной строкой

* ``endsWith('dumbo', 'mbo') -> true``

___
### <code>equals</code>
<code><b>equals(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => boolean</b></code><br/><br/>
Оператор сравнения на равенство. Аналогично оператору = =

* ``equals(12, 24) -> false``

* ``12 == 24 -> false``

* ``'bad' == 'bad' -> true``

* ``isNull('good' == toString(null)) -> true``

* ``isNull(null == null) -> true``

___
### <code>equalsIgnoreCase</code>
<code><b>equalsIgnoreCase(<i>&lt;value1&gt;</i> : string, <i>&lt;value2&gt;</i> : string) => boolean</b></code><br/><br/>
Оператор сравнения на равенство без учета регистра. Совпадает с оператором < = >

* ``'abc'<=>'Abc' -> true``

* ``equalsIgnoreCase('abc', 'Abc') -> true``

___
### <code>factorial</code>
<code><b>factorial(<i>&lt;value1&gt;</i> : number) => long</b></code><br/><br/>
Вычисление факториала числа

* ``factorial(5) -> 120``

___
### <code>false</code>
<code><b>false() => boolean</b></code><br/><br/>
Всегда возвращает значение false. Используйте синтаксис функции (false ()), если имеется столбец с именем "false"

* ``(10 + 20 > 30) -> false``

* ``(10 + 20 > 30) -> false()``

___
### <code>filter</code>
<code><b>filter(<i>&lt;value1&gt;</i> : array, <i>&lt;value2&gt;</i> : unaryfunction) => array</b></code><br/><br/>
Фильтрует элементы из массива, который не соответствует указанному предикату. Фильтр принимает ссылку на один элемент в функции предиката как #item

* ``filter([1, 2, 3, 4], #item > 2) -> [3, 4]``

* ``filter(['a', 'b', 'c', 'd'], #item == 'a' || #item == 'b') -> ['a', 'b']``

___
### <code>first</code>
<code><b>first(<i>&lt;value1&gt;</i> : any, [<i>&lt;value2&gt;</i> : boolean]) => any</b></code><br/><br/>
Возвращает первое значение группы столбцов. Если второй параметр Игноренуллс опущен, он принимает значение false.

* ``first(sales)``

* ``first(sales, false)``

___
### <code>floor</code>
<code><b>floor(<i>&lt;value1&gt;</i> : number) => number</b></code><br/><br/>
Возвращает максимальное целое число, не большее числа

* ``floor(-0.1) -> -1``

___
### <code>fromUTC</code>
<code><b>fromUTC(<i>&lt;value1&gt;</i> : timestamp, [<i>&lt;value2&gt;</i> : string]) => timestamp</b></code><br/><br/>
Преобразует в отметку времени из времени в формате UTC. При необходимости можно передать часовой пояс в формате "GMT", "PST", "UTC", "America/Кайман". По умолчанию используется текущий Симпледатеформат Java Тимезонерефер для доступных форматов. https://docs.oracle.com/javase/8/docs/api/java/text/SimpleDateFormat.html

* ``fromUTC(currentTimeStamp()) == toTimestamp('2050-12-12 19:18:12') -> false``

* ``fromUTC(currentTimeStamp(), 'Asia/Seoul') != toTimestamp('2050-12-12 19:18:12') -> true``

___
### <code>greater</code>
<code><b>greater(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => boolean</b></code><br/><br/>
Оператор сравнения "больше". Аналогично оператору >

* ``greater(12, 24) -> false``

* ``('dumbo' > 'dum') -> true``

* ``(toTimestamp('2019-02-05 08:21:34.890', 'yyyy-MM-dd HH:mm:ss.SSS') > toTimestamp('2019-02-03 05:19:28.871', 'yyyy-MM-dd 
HH:mm:ss.SSS')) -> true``

___
### <code>greaterOrEqual</code>
<code><b>greaterOrEqual(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => boolean</b></code><br/><br/>
Оператор сравнения "больше чем или равно". Совпадает с оператором > =

* ``greaterOrEqual(12, 12) -> true``

* ``('dumbo' >= 'dum') -> true``

___
### <code>greatest</code>
<code><b>greatest(<i>&lt;value1&gt;</i> : any, ...) => any</b></code><br/><br/>
Возвращает наибольшее значение из списка значений, в котором пропускаются значения NULL. Возвращает значение null, если все входные данные имеют значение null

* ``greatest(10, 30, 15, 20) -> 30``

* ``greatest(10, toInteger(null), 20) -> 20``

* ``greatest(toDate('2010-12-12'), toDate('2011-12-12'), toDate('2000-12-12')) -> toDate('2011-12-12')``

* ``greatest(toTimestamp('2019-02-03 05:19:28.871', 'yyyy-MM-dd HH:mm:ss.SSS'), toTimestamp('2019-02-05 08:21:34.890', 'yyyy-MM-dd HH:mm:ss.SSS')) -> toTimestamp('2019-02-05 08:21:34.890', 'yyyy-MM-dd HH:mm:ss.SSS')``

___
### <code>hasColumn</code>
<code><b>hasColumn(<i>&lt;column name&gt;</i> : string, [<i>&lt;stream name&gt;</i> : string]) => boolean</b></code><br/><br/>
Проверяет значение столбца по имени в потоке. В качестве второго аргумента можно передать необязательное имя потока.  Имена столбцов, известные во время разработки, должны быть адресованы только по имени. Вычисленные входные данные не поддерживаются, но можно использовать подстановки параметров

* ``hasColumn('parent')``

___
### <code>hour</code>
<code><b>hour(<i>&lt;value1&gt;</i> : timestamp, [<i>&lt;value2&gt;</i> : string]) => integer</b></code><br/><br/>
Возвращает значение часа метки времени. Вы можете передать дополнительный часовой пояс в формате GMT, PST, UTC, "Острова Кайман". По умолчанию используется локальный часовой пояс. Сведения о доступных форматах см. в разделе Симпледатеформат Java. https://docs.oracle.com/javase/8/docs/api/java/text/SimpleDateFormat.html

* ``hour(toTimestamp('2009-07-30 12:58:59')) -> 12``

* ``hour(toTimestamp('2009-07-30 12:58:59'), 'PST') -> 12``

___
### <code>hours</code>
<code><b>hours(<i>&lt;value1&gt;</i> : integer) => long</b></code><br/><br/>
Длительность в миллисекундах для количества часов

* ``hours(2) -> 7200000L``

___
### <code>iMap</code>
<code><b>iMap(<i>&lt;value1&gt;</i> : array, <i>&lt;value2&gt;</i> : binaryfunction) => any</b></code><br/><br/>
Сопоставляет каждый элемент массива с новым элементом, используя предоставленное выражение. Функция map принимает ссылку на один элемент в функции выражения как #item и ссылку на индекс элемента как #index

* ``iMap([1, 2, 3, 4], #item + 2 + #index) -> [4, 6, 8, 10]``

___
### <code>iif</code>
<code><b>iif(<i>&lt;condition&gt;</i> : boolean, <i>&lt;true_expression&gt;</i> : any, [<i>&lt;false_expression&gt;</i> : any]) => any</b></code><br/><br/>
В зависимости от условия применяется одно или другое значение. Если другое значение не указано, оно считается нулевым. Оба значения должны быть совместимыми (числовыми, строковыми...).

* ``iif(10 + 20 == 30, 'dumbo', 'gumbo') -> 'dumbo'``

* ``iif(10 > 30, 'dumbo', 'gumbo') -> 'gumbo'``

* ``iif(month(toDate('2018-12-01')) == 12, 345.12, 102.67) -> 345.12``

___
### <code>iifNull</code>
<code><b>iifNull(<i>&lt;value1&gt;</i> : any, [<i>&lt;value2&gt;</i> : any], ...) => any</b></code><br/><br/>
Проверяет, что значение не равно NULL, и возвращает его, иначе возвращает альтернативный. Он проверяет все входные данные до тех пор, пока не найдет первое значение, отличное от NULL.

* ``iifNull(10, 20) -> 10``

* ``iifNull(null, 20, 40) -> 20``

* ``iifNull('bojjus', 'bo', 'dumbo') -> 'dumbo'``

___
### <code>in</code>
<code><b>in(<i>&lt;array of items&gt;</i> : array, <i>&lt;item to find&gt;</i> : any) => boolean</b></code><br/><br/>
Проверяет, находится ли элемент в массиве

* ``in([10, 20, 30], 10) -> true``

* ``in(['good', 'kid'], 'bad') -> false``

___
### <code>initCap</code>
<code><b>initCap(<i>&lt;value1&gt;</i> : string) => string</b></code><br/><br/>
Преобразует первую букву каждого слова в верхний регистр. Слова обозначаются символами пробела

* ``initCap('cool iceCREAM') -> 'Cool Icecream'``

___
### <code>instr</code>
<code><b>instr(<i>&lt;string&gt;</i> : string, <i>&lt;substring to find&gt;</i> : string) => integer</b></code><br/><br/>
Находит позицию (на основе 1) подстроки в строке. 0 возвращается, если не найден

* ``instr('dumbo', 'mbo') -> 3``

* ``instr('microsoft', 'o') -> 5``

* ``instr('good', 'bad') -> 0``

___
### <code>isDelete</code>
<code><b>isDelete([<i>&lt;value1&gt;</i> : integer]) => boolean</b></code><br/><br/>
Проверяет, помечена ли строка для удаления. Для преобразований, использующих более одного входного потока, можно передать индекс потока (с 1). Значение по умолчанию для индекса потока — 1

* ``isDelete()``

* ``isDelete(1)``

___
### <code>isError</code>
<code><b>isError([<i>&lt;value1&gt;</i> : integer]) => boolean</b></code><br/><br/>
Проверяет, помечена ли строка как ошибка. Для преобразований, использующих более одного входного потока, можно передать индекс потока (с 1). Значение по умолчанию для индекса потока — 1

* ``isError()``

* ``isError(1)``

___
### <code>isIgnore</code>
<code><b>isIgnore([<i>&lt;value1&gt;</i> : integer]) => boolean</b></code><br/><br/>
Проверяет, помечена ли строка для игнорирования. Для преобразований, использующих более одного входного потока, можно передать индекс потока (с 1). Значение по умолчанию для индекса потока — 1

* ``isIgnore()``

* ``isIgnore(1)``

___
### <code>isInsert</code>
<code><b>isInsert([<i>&lt;value1&gt;</i> : integer]) => boolean</b></code><br/><br/>
Проверяет, помечена ли строка для вставки. Для преобразований, использующих более одного входного потока, можно передать индекс потока (с 1). Значение по умолчанию для индекса потока — 1

* ``isInsert()``

* ``isInsert(1)``

___
### <code>isMatch</code>
<code><b>isMatch([<i>&lt;value1&gt;</i> : integer]) => boolean</b></code><br/><br/>
Проверяет соответствие строки при поиске. Для преобразований, использующих более одного входного потока, можно передать индекс потока (с 1). Значение по умолчанию для индекса потока — 1

* ``isMatch()``

* ``isMatch(1)``

___
### <code>isNull</code>
<code><b>isNull(<i>&lt;value1&gt;</i> : any) => boolean</b></code><br/><br/>
Проверяет, является ли значение значением NULL

* ``isNull(NULL()) -> true``

* ``isNull('') -> false``

___
### <code>isUpdate</code>
<code><b>isUpdate([<i>&lt;value1&gt;</i> : integer]) => boolean</b></code><br/><br/>
Проверяет, помечена ли строка для обновления. Для преобразований, использующих более одного входного потока, можно передать индекс потока (с 1). Значение по умолчанию для индекса потока — 1

* ``isUpdate()``

* ``isUpdate(1)``

___
### <code>isUpsert</code>
<code><b>isUpsert([<i>&lt;value1&gt;</i> : integer]) => boolean</b></code><br/><br/>
Проверяет, помечена ли строка для вставки. Для преобразований, использующих более одного входного потока, можно передать индекс потока (с 1). Значение по умолчанию для индекса потока — 1

* ``isUpsert()``

* ``isUpsert(1)``

___
### <code>kurtosis</code>
<code><b>kurtosis(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Возвращает эксцесс столбца

* ``kurtosis(sales)``

___
### <code>kurtosisIf</code>
<code><b>kurtosisIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number) => double</b></code><br/><br/>
В зависимости от критерия получает эксцесс столбца

* ``kurtosisIf(region == 'West', sales)``

___
### <code>lag</code>
<code><b>lag(<i>&lt;value&gt;</i> : any, [<i>&lt;number of rows to look before&gt;</i> : number], [<i>&lt;default value&gt;</i> : any]) => any</b></code><br/><br/>
Возвращает значение первого параметра, вычислившего n строк перед текущей строкой. Второй параметр — число строк, которые необходимо просмотреть, а значение по умолчанию — 1. Если число строк не превышает допустимого значения, возвращается значение null, если не указано значение по умолчанию.

* ``lag(amount, 2)``

* ``lag(amount, 2000, 100)``

___
### <code>last</code>
<code><b>last(<i>&lt;value1&gt;</i> : any, [<i>&lt;value2&gt;</i> : boolean]) => any</b></code><br/><br/>
Возвращает последнее значение группы столбцов. Если второй параметр Игноренуллс опущен, он принимает значение false.

* ``last(sales)``

* ``last(sales, false)``

___
### <code>lastDayOfMonth</code>
<code><b>lastDayOfMonth(<i>&lt;value1&gt;</i> : datetime) => date</b></code><br/><br/>
Возвращает последнюю дату месяца в заданной дате

* ``lastDayOfMonth(toDate('2009-01-12')) -> toDate('2009-01-31')``

___
### <code>lead</code>
<code><b>lead(<i>&lt;value&gt;</i> : any, [<i>&lt;number of rows to look after&gt;</i> : number], [<i>&lt;default value&gt;</i> : any]) => any</b></code><br/><br/>
Возвращает значение первого параметра, вычислившего n строк после текущей строки. Вторым параметром является число строк, которые нужно найти вперед, а значение по умолчанию — 1. Если число строк не превышает допустимого значения, возвращается значение null, если не указано значение по умолчанию.

* ``lead(amount, 2)``

* ``lead(amount, 2000, 100)``

___
### <code>least</code>
<code><b>least(<i>&lt;value1&gt;</i> : any, ...) => any</b></code><br/><br/>
Оператор сравнения "меньше чем или равно". То же, что <= operator

* ``least(10, 30, 15, 20) -> 10``

* ``least(toDate('2010-12-12'), toDate('2011-12-12'), toDate('2000-12-12')) -> toDate('2000-12-12')``

___
### <code>left</code>
<code><b>left(<i>&lt;string to subset&gt;</i> : string, <i>&lt;number of characters&gt;</i> : integral) => string</b></code><br/><br/>
Извлекает подстроку, начиная с индекса 1, с необходимым количеством знаков. То же, что и substring (STR, 1, n)

* ``left('bojjus', 2) -> 'bo'``

* ``left('bojjus', 20) -> 'bojjus'``

___
### <code>length</code>
<code><b>length(<i>&lt;value1&gt;</i> : string) => integer</b></code><br/><br/>
Возвращает длину строки

* ``length('dumbo') -> 5``

___
### <code>lesser</code>
<code><b>lesser(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => boolean</b></code><br/><br/>
Оператор сравнения "меньше". Аналогично оператору <

* ``lesser(12, 24) -> true``

* ``('abcd' < 'abc') -> false``

* ``(toTimestamp('2019-02-03 05:19:28.871', 'yyyy-MM-dd HH:mm:ss.SSS') < toTimestamp('2019-02-05 08:21:34.890', 'yyyy-MM-dd HH:mm:ss.SSS')) -> true``

___
### <code>lesserOrEqual</code>
<code><b>lesserOrEqual(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => boolean</b></code><br/><br/>
Оператор сравнения "меньше чем или равно". То же, что <= operator

* ``lesserOrEqual(12, 12) -> true``

* ``('dumbo' <= 'dum') -> false``

___
### <code>levenshtein</code>
<code><b>levenshtein(<i>&lt;from string&gt;</i> : string, <i>&lt;to string&gt;</i> : string) => integer</b></code><br/><br/>
Возвращает Левенштейна расстояние между двумя строками

* ``levenshtein('boys', 'girls') -> 4``

___
### <code>like</code>
<code><b>like(<i>&lt;string&gt;</i> : string, <i>&lt;pattern match&gt;</i> : string) => boolean</b></code><br/><br/>
Шаблон — это строка, которая сопоставляется буквально. Исключения являются следующими специальными символами: _ соответствует любому символу во входных данных (аналогично. в регулярных выражениях posix), % соответствует нулю или большему количеству знаков во входной строке (аналогично * в регулярных выражениях posix).
Escape-символом является ''. Если escape-символ предшествует специальному символу или другому escape-символу, следующий знак сопоставляется буквально. Недопустимо экранировать любые другие знаки.

* ``like('icecream', 'ice%') -> true``

___
### <code>locate</code>
<code><b>locate(<i>&lt;substring to find&gt;</i> : string, <i>&lt;string&gt;</i> : string, [<i>&lt;from index - 1-based&gt;</i> : integral]) => integer</b></code><br/><br/>
Находит позицию (на основе 1) подстроки в строке, начиная определение с определенной позиции. Если позиция опущена, то она отсчитывается с начала строки. 0 возвращается, если не найден

* ``locate('mbo', 'dumbo') -> 3``

* ``locate('o', 'microsoft', 6) -> 7``

* ``locate('bad', 'good') -> 0``

___
### <code>log</code>
<code><b>log(<i>&lt;value1&gt;</i> : number, [<i>&lt;value2&gt;</i> : number]) => double</b></code><br/><br/>
Вычисляет значение логарифма. При использовании можно указать дополнительный базовый номер Эйлера.

* ``log(100, 10) -> 2``

___
### <code>log10</code>
<code><b>log10(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Вычисляет значение журнала на основе 10 базовых

* ``log10(100) -> 2``

___
### <code>lower</code>
<code><b>lower(<i>&lt;value1&gt;</i> : string) => string</b></code><br/><br/>
Строка в нижнем регистре

* ``lower('GunChus') -> 'gunchus'``

___
### <code>lpad</code>
<code><b>lpad(<i>&lt;string to pad&gt;</i> : string, <i>&lt;final padded length&gt;</i> : integral, <i>&lt;padding&gt;</i> : string) => string</b></code><br/><br/>
Слева дополняет строку прилагаемым отступом определенной длины. Если строка больше или равна длине, то она усекается до длины.

* ``lpad('dumbo', 10, '-') -> '-----dumbo'``

* ``lpad('dumbo', 4, '-') -> 'dumb'``

* ``lpad('dumbo', 8, '<>') -> '<><dumbo'``

___
### <code>ltrim</code>
<code><b>ltrim(<i>&lt;string to trim&gt;</i> : string, [<i>&lt;trim characters&gt;</i> : string]) => string</b></code><br/><br/>
Обрезает строку начальных знаков слева. Если второй параметр не указан, удаляются пробелы. В противном случае все символы, указанные во втором параметре, обрезаются.

* ``ltrim('  dumbo  ') -> 'dumbo  '``

* ``ltrim('!--!du!mbo!', '-!') -> 'du!mbo!'``

___
### <code>map</code>
<code><b>map(<i>&lt;value1&gt;</i> : array, <i>&lt;value2&gt;</i> : unaryfunction) => any</b></code><br/><br/>
Сопоставляет каждый элемент массива с новым элементом, используя предоставленное выражение. Map ждет ссылку на один элемент в функции выражения, как #item

* ``map([1, 2, 3, 4], #item + 2) -> [3, 4, 5, 6]``

* ``map(['a', 'b', 'c', 'd'], #item + '_processed') -> ['a_processed', 'b_processed', 'c_processed', 'd_processed']``

___
### <code>max</code>
<code><b>max(<i>&lt;value1&gt;</i> : any) => any</b></code><br/><br/>
Возвращает максимальное значение столбца

* ``max(sales)``

___
### <code>maxIf</code>
<code><b>maxIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : any) => any</b></code><br/><br/>
В зависимости от критерия возвращает максимальное значение столбца

* ``maxIf(region == 'West', sales)``

___
### <code>md5</code>
<code><b>md5(<i>&lt;value1&gt;</i> : any, ...) => string</b></code><br/><br/>
Вычисляет хэш-код MD5 набора столбцов с различными типами данных и возвращает шестнадцатеричную строку из 32 знаков. Его можно использовать для вычисления отпечатка для строки

* ``md5(5, 'gunchus', 8.2, 'bojjus', true, toDate('2010-4-4')) -> '4ce8a880bd621a1ffad0bca905e1bc5a'``

___
### <code>mean</code>
<code><b>mean(<i>&lt;value1&gt;</i> : number) => number</b></code><br/><br/>
Возвращает среднее значение столбца. То же, что и AVG

* ``mean(sales)``

___
### <code>meanIf</code>
<code><b>meanIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number) => number</b></code><br/><br/>
Возвращает среднее значение столбца на основе критериев. То же, что и Авгиф

* ``meanIf(region == 'West', sales)``

___
### <code>millisecond</code>
<code><b>millisecond(<i>&lt;value1&gt;</i> : timestamp, [<i>&lt;value2&gt;</i> : string]) => integer</b></code><br/><br/>
Возвращает значение миллисекунды для даты. Вы можете передать дополнительный часовой пояс в формате GMT, PST, UTC, "Острова Кайман". По умолчанию используется локальный часовой пояс. Сведения о доступных форматах см. в разделе Симпледатеформат Java. https://docs.oracle.com/javase/8/docs/api/java/text/SimpleDateFormat.html

* ``millisecond(toTimestamp('2009-07-30 12:58:59.871', 'yyyy-MM-dd HH:mm:ss.SSS')) -> 871``

___
### <code>milliseconds</code>
<code><b>milliseconds(<i>&lt;value1&gt;</i> : integer) => long</b></code><br/><br/>
Длительность в миллисекундах для числа миллисекунд

* ``seconds(2) -> 2L``

___
### <code>min</code>
<code><b>min(<i>&lt;value1&gt;</i> : any) => any</b></code><br/><br/>
Возвращает минимальное значение столбца

* ``min(sales)``

___
### <code>minIf</code>
<code><b>minIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : any) => any</b></code><br/><br/>
В зависимости от критерия возвращает минимальное значение столбца

* ``minIf(region == 'West', sales)``

___
### <code>minus</code>
<code><b>minus(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => any</b></code><br/><br/>
Вычитает числа. Вычитает количество дней из даты. Вычитание длительность из метки времени. Вычитание две метки времени, чтобы получить разницу в миллисекундах. То же, что и оператор-

* ``minus(20, 10) -> 10``

* ``20 - 10 -> 10``

* ``minus(toDate('2012-12-15'), 3) -> toDate('2012-12-12')``

* ``toDate('2012-12-15') - 3 -> toDate('2012-12-12')``

* ``toTimestamp('2019-02-03 05:19:28.871', 'yyyy-MM-dd HH:mm:ss.SSS') + (days(1) + hours(2) - seconds(10)) -> toTimestamp('2019-02-04 07:19:18.871', 'yyyy-MM-dd HH:mm:ss.SSS')``

* ``toTimestamp('2019-02-03 05:21:34.851', 'yyyy-MM-dd HH:mm:ss.SSS') - toTimestamp('2019-02-03 05:21:36.923', 'yyyy-MM-dd HH:mm:ss.SSS') -> -2072``

___
### <code>minute</code>
<code><b>minute(<i>&lt;value1&gt;</i> : timestamp, [<i>&lt;value2&gt;</i> : string]) => integer</b></code><br/><br/>
Возвращает значение минуты метки времени. Вы можете передать дополнительный часовой пояс в формате GMT, PST, UTC, "Острова Кайман". По умолчанию используется локальный часовой пояс. Сведения о доступных форматах см. в разделе Симпледатеформат Java. https://docs.oracle.com/javase/8/docs/api/java/text/SimpleDateFormat.html

* ``minute(toTimestamp('2009-07-30 12:58:59')) -> 58``

* ``minute(toTimestamp('2009-07-30 12:58:59'), 'PST') -> 58``

___
### <code>minutes</code>
<code><b>minutes(<i>&lt;value1&gt;</i> : integer) => long</b></code><br/><br/>
Длительность в миллисекундах для количества минут

* ``minutes(2) -> 120000L``

___
### <code>mod</code>
<code><b>mod(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => any</b></code><br/><br/>
Модуль пары чисел. Аналогичен оператору%

* ``mod(20, 8) -> 4``

* ``20 % 8 -> 4``

___
### <code>month</code>
<code><b>month(<i>&lt;value1&gt;</i> : datetime) => integer</b></code><br/><br/>
Возвращает значение месяца для даты или метки времени

* ``month(toDate('2012-8-8')) -> 8``

___
### <code>monthsBetween</code>
<code><b>monthsBetween(<i>&lt;from date/timestamp&gt;</i> : datetime, <i>&lt;to date/timestamp&gt;</i> : datetime, [<i>&lt;roundoff&gt;</i> : boolean], [<i>&lt;time zone&gt;</i> : string]) => double</b></code><br/><br/>
Возвращает число месяцев между двумя датами. Вычисление можно округлить. Можно передать дополнительный часовой пояс в формате "GMT", "PST", "UTC", "America/Кайман". По умолчанию используется локальный часовой пояс. Сведения о доступных форматах см. в разделе Симпледатеформат Java. https://docs.oracle.com/javase/8/docs/api/java/text/SimpleDateFormat.html

* ``monthsBetween(toTimestamp('1997-02-28 10:30:00'), toDate('1996-10-30')) -> 3.94959677``

___
### <code>multiply</code>
<code><b>multiply(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => any</b></code><br/><br/>
Умножает пару чисел. То же, что и оператор *

* ``multiply(20, 10) -> 200``

* ``20 * 10 -> 200``

___
### <code>nTile</code>
<code><b>nTile([<i>&lt;value1&gt;</i> : integer]) => integer</b></code><br/><br/>
Функция NTile делит строки для каждого раздела окна на `n` сегментов от 1 до `n`. Значения сегментов будут отличаться максимум на 1. Если количество строк в разделе не делится поровну на количество сегментов, то остальные значения распределяются по одному на сегмент, начиная с первого. Функция NTile полезна для вычисления тертилес, квартилей, децили и других общих сводных статистических данных. Функция вычисляет две переменные во время инициализации: размер обычного контейнера будет добавлен к одной дополнительной строке. Обе переменные основаны на размере текущей секции. В процессе вычисления функция отслеживает текущее число строк, номер текущего сегмента и номер строки, на которой изменяется сегмент (bucketThreshold). Когда текущий номер строки достигает порога сегмента, значение сегмента увеличивается на единицу, а порог увеличивается на размер сегмента (плюс единица, если текущий сегмент заполняется).

* ``nTile()``

* ``nTile(numOfBuckets)``

___
### <code>negate</code>
<code><b>negate(<i>&lt;value1&gt;</i> : number) => number</b></code><br/><br/>
Инвертирует число. Превращает положительные числа в отрицательные и наоборот

* ``negate(13) -> -13``

___
### <code>nextSequence</code>
<code><b>nextSequence() => long</b></code><br/><br/>
Возвращает следующую уникальную последовательность. Это число является последовательным только в пределах секции и имеет префикс partitionId

* ``nextSequence() == 12313112 -> false``

___
### <code>normalize</code>
<code><b>normalize(<i>&lt;String to normalize&gt;</i> : string) => string</b></code><br/><br/>
Нормализация строкового значения для разделения символов Юникода с диакритическими знаками

* ``regexReplace(normalize('bo²s'), `\p{M}`, '') -> 'boys'``

___
### <code>not</code>
<code><b>not(<i>&lt;value1&gt;</i> : boolean) => boolean</b></code><br/><br/>
Оператор логического отрицания:

* ``not(true) -> false``

* ``not(10 == 20) -> true``

___
### <code>notEquals</code>
<code><b>notEquals(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => boolean</b></code><br/><br/>
Оператор сравнения на неравенство. Аналогично оператору! =

* ``12 != 24 -> true``

* ``'bojjus' != 'bo' + 'jjus' -> false``

___
### <code>null</code>
<code><b>null() => null</b></code><br/><br/>
Возвращает значение NULL. Используйте функцию syntax(null()), если есть столбец с именем null. Любая операция, которая использует, приведет к значению NULL

* ``isNull('dumbo' + null) -> true``

* ``isNull(10 * null) -> true``

* ``isNull('') -> false``

* ``isNull(10 + 20) -> false``

* ``isNull(10/0) -> true``

___
### <code>or</code>
<code><b>or(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : boolean) => boolean</b></code><br/><br/>
Логический оператор OR. То же, что и | |

* ``or(true, false) -> true``

* ``true || false -> true``

___
### <code>pMod</code>
<code><b>pMod(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => any</b></code><br/><br/>
Положительный модуль пары чисел.

* ``pmod(-20, 8) -> 4``

___
### <code>partitionId</code>
<code><b>partitionId() => integer</b></code><br/><br/>
Возвращает идентификатор текущей секции, в которой находится входная строка

* ``partitionId()``

___
### <code>power</code>
<code><b>power(<i>&lt;value1&gt;</i> : number, <i>&lt;value2&gt;</i> : number) => double</b></code><br/><br/>
Порождает одно число в степень другого

* ``power(10, 2) -> 100``

___
### <code>rank</code>
<code><b>rank(<i>&lt;value1&gt;</i> : any, ...) => integer</b></code><br/><br/>
Вычисляет ранг значения в группе значений. Результатом является единица плюс количество строк, предшествующих или равных текущей строке в упорядоченном наборе раздела. Значения будут создавать промежутки в последовательности. Ранжирование работает, даже если данные не отсортированы и выполняется поиск изменений в значениях

* ``rank(salesQtr, salesAmt)``

___
### <code>reduce</code>
<code><b>reduce(<i>&lt;value1&gt;</i> : array, <i>&lt;value2&gt;</i> : any, <i>&lt;value3&gt;</i> : binaryfunction, <i>&lt;value4&gt;</i> : unaryfunction) => any</b></code><br/><br/>
Накапливает элементы в массиве. Функция Reduce принимает ссылку на накапливаемое и один элемент в первой функции expression как #acc и #item и ждет, что результирующее значение #resultся для использования во второй функции выражения.

* ``reduce([1, 2, 3, 4], 0, #acc + #item, #result) -> 10``

* ``reduce(['1', '2', '3', '4'], '0', #acc + #item, #result) -> '01234'``

* ``reduce([1, 2, 3, 4], 0, #acc + #item, #result + 15) -> 25``

___
### <code>regexExtract</code>
<code><b>regexExtract(<i>&lt;string&gt;</i> : string, <i>&lt;regex to find&gt;</i> : string, [<i>&lt;match group 1-based index&gt;</i> : integral]) => string</b></code><br/><br/>
Извлекает соответствующую подстроку для данного шаблона регулярного выражения. Последний параметр идентифицирует группу для сопоставления и, если не указан, по умолчанию равен 1. Используйте "<regex>" (Обратная кавычка) для сопоставления строки без экранирования

* ``regexExtract('Cost is between 600 and 800 dollars', '(\\d+) and (\\d+)', 2) -> '800'``

* ``regexExtract('Cost is between 600 and 800 dollars', `(\d+) and (\d+)`, 2) -> '800'``

___
### <code>regexMatch</code>
<code><b>regexMatch(<i>&lt;string&gt;</i> : string, <i>&lt;regex to match&gt;</i> : string) => boolean</b></code><br/><br/>
Проверяет, соответствует ли строка заданному шаблону регулярного выражения. Используйте "<regex>" (Обратная кавычка) для сопоставления строки без экранирования

* ``regexMatch('200.50', '(\\d+).(\\d+)') -> true``

* ``regexMatch('200.50', `(\d+).(\d+)`) -> true``

___
### <code>regexReplace</code>
<code><b>regexReplace(<i>&lt;string&gt;</i> : string, <i>&lt;regex to find&gt;</i> : string, <i>&lt;substring to replace&gt;</i> : string) => string</b></code><br/><br/>
Заменить все вхождения шаблона регулярного выражения другой подстрокой в заданной строке использовать "<regex>" (Обратная кавычка) для сопоставления строки без экранирования

* ``regexReplace('100 and 200', '(\\d+)', 'bojjus') -> 'bojjus and bojjus'``

* ``regexReplace('100 and 200', `(\d+)`, 'gunchus') -> 'gunchus and gunchus'``

___
### <code>regexSplit</code>
<code><b>regexSplit(<i>&lt;string to split&gt;</i> : string, <i>&lt;regex expression&gt;</i> : string) => array</b></code><br/><br/>
Разделяет строку на основе разделителя на основе регулярного выражения и возвращает массив строк.

* ``regexSplit('bojjusAgunchusBdumbo', `[CAB]`) -> ['bojjus', 'gunchus', 'dumbo']``

* ``regexSplit('bojjusAgunchusBdumboC', `[CAB]`) -> ['bojjus', 'gunchus', 'dumbo', '']``

* ``(regexSplit('bojjusAgunchusBdumboC', `[CAB]`)[1]) -> 'bojjus'``

* ``isNull(regexSplit('bojjusAgunchusBdumboC', `[CAB]`)[20]) -> true``

___
### <code>replace</code>
<code><b>replace(<i>&lt;string&gt;</i> : string, <i>&lt;substring to find&gt;</i> : string, [<i>&lt;substring to replace&gt;</i> : string]) => string</b></code><br/><br/>
Замена всех вхождений подстроки другой подстрокой в заданной строке. Если последний параметр пропущен, по умолчанию используется пустая строка.

* ``replace('doggie dog', 'dog', 'cat') -> 'catgie cat'``

* ``replace('doggie dog', 'dog', '') -> 'gie '``

* ``replace('doggie dog', 'dog') -> 'gie '``

___
### <code>reverse</code>
<code><b>reverse(<i>&lt;value1&gt;</i> : string) => string</b></code><br/><br/>
Изменяет направление строки на противоположную

* ``reverse('gunchus') -> 'suhcnug'``

___
### <code>right</code>
<code><b>right(<i>&lt;string to subset&gt;</i> : string, <i>&lt;number of characters&gt;</i> : integral) => string</b></code><br/><br/>
Извлекает подстроку с количеством знаков справа. То же, что и substring (STR, LENGTH (STR)-n, n)

* ``right('bojjus', 2) -> 'us'``

* ``right('bojjus', 20) -> 'bojjus'``

___
### <code>rlike</code>
<code><b>rlike(<i>&lt;string&gt;</i> : string, <i>&lt;pattern match&gt;</i> : string) => boolean</b></code><br/><br/>
Проверяет, совпадает ли строка с заданным шаблоном регулярного выражения

* ``rlike('200.50', `(\d+).(\d+)`) -> true``

* ``rlike('bogus', `M[0-9]+.*`) -> false``

___
### <code>round</code>
<code><b>round(<i>&lt;number&gt;</i> : number, [<i>&lt;scale to round&gt;</i> : number], [<i>&lt;rounding option&gt;</i> : integral]) => double</b></code><br/><br/>
Округляет число, заданное необязательным масштабом, и необязательным режимом округления. Если масштаб не указан, по умолчанию используется значение 0.  Если режим опущен, по умолчанию используется значение ROUND_HALF_UP (5). Округление значений включает 1-ROUND_UP 2-ROUND_DOWN 3-ROUND_CEILING 4-ROUND_FLOOR 5-ROUND_HALF_UP 6-ROUND_HALF_DOWN 7 — ROUND_HALF_EVEN 8 — ROUND_UNNECESSARY

* ``round(100.123) -> 100.0``

* ``round(2.5, 0) -> 3.0``

* ``round(5.3999999999999995, 2, 7) -> 5.40``

___
### <code>rowNumber</code>
<code><b>rowNumber() => integer</b></code><br/><br/>
Назначает последовательную нумерацию строк для строк в окне, начиная с 1

* ``rowNumber()``

___
### <code>rpad</code>
<code><b>rpad(<i>&lt;string to pad&gt;</i> : string, <i>&lt;final padded length&gt;</i> : integral, <i>&lt;padding&gt;</i> : string) => string</b></code><br/><br/>
Дополняет строку прилагаемым отступом справа до достижения определенной длины. Если строка больше или равна длине, то она усекается до длины.

* ``rpad('dumbo', 10, '-') -> 'dumbo-----'``

* ``rpad('dumbo', 4, '-') -> 'dumb'``

* ``rpad('dumbo', 8, '<>') -> 'dumbo<><'``

___
### <code>rtrim</code>
<code><b>rtrim(<i>&lt;string to trim&gt;</i> : string, [<i>&lt;trim characters&gt;</i> : string]) => string</b></code><br/><br/>
Обрезает строку начальных знаков справа. Если второй параметр не указан, удаляются пробелы. В противном случае все символы, указанные во втором параметре, обрезаются.

* ``rtrim('  dumbo  ') -> '  dumbo'``

* ``rtrim('!--!du!mbo!', '-!') -> '!--!du!mbo'``

___
### <code>second</code>
<code><b>second(<i>&lt;value1&gt;</i> : timestamp, [<i>&lt;value2&gt;</i> : string]) => integer</b></code><br/><br/>
Возвращает второе значение даты. Вы можете передать дополнительный часовой пояс в формате GMT, PST, UTC, "Острова Кайман". По умолчанию используется локальный часовой пояс. Сведения о доступных форматах см. в разделе Симпледатеформат Java. https://docs.oracle.com/javase/8/docs/api/java/text/SimpleDateFormat.html

* ``second(toTimestamp('2009-07-30 12:58:59')) -> 59``

___
### <code>seconds</code>
<code><b>seconds(<i>&lt;value1&gt;</i> : integer) => long</b></code><br/><br/>
Длительность в миллисекундах для количества секунд

* ``seconds(2) -> 2000L``

___
### <code>sha1</code>
<code><b>sha1(<i>&lt;value1&gt;</i> : any, ...) => string</b></code><br/><br/>
Вычисляет хэш-код SHA-1 набора столбцов с различными типами данных и возвращает шестнадцатеричную строку из 40 знаков. Его можно использовать для вычисления отпечатка для строки

* ``sha1(5, 'gunchus', 8.2, 'bojjus', true, toDate('2010-4-4')) -> '46d3b478e8ec4e1f3b453ac3d8e59d5854e282bb'``

___
### <code>sha2</code>
<code><b>sha2(<i>&lt;value1&gt;</i> : integer, <i>&lt;value2&gt;</i> : any, ...) => string</b></code><br/><br/>
Вычисляет дайджест SHA-2 для набора столбцов различных примитивных типов данных с битовой длиной, которая может иметь только значения 0 (256), 224, 256, 384, 512. Его можно использовать для вычисления отпечатка для строки

* ``sha2(256, 'gunchus', 8.2, 'bojjus', true, toDate('2010-4-4')) -> 'afe8a553b1761c67d76f8c31ceef7f71b66a1ee6f4e6d3b5478bf68b47d06bd3'``

___
### <code>sin</code>
<code><b>sin(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Вычисляет значение синуса

* ``sin(2) -> 0.9092974268256817``

___
### <code>sinh</code>
<code><b>sinh(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Вычисляет гиперболический синус значения

* ``sinh(0) -> 0.0``

___
### <code>skewness</code>
<code><b>skewness(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Возвращает асимметрию столбца

* ``skewness(sales)``

___
### <code>skewnessIf</code>
<code><b>skewnessIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number) => double</b></code><br/><br/>
В зависимости от критерия получает асимметрию столбца

* ``skewnessIf(region == 'West', sales)``

___
### <code>slice</code>
<code><b>slice(<i>&lt;array to slice&gt;</i> : array, <i>&lt;from 1-based index&gt;</i> : integral, [<i>&lt;number of items&gt;</i> : integral]) => array</b></code><br/><br/>
Извлекает подмножество массива из позиции. Позиция отсчитывается от единицы. Если длина опущена, по умолчанию используется конец строки.

* ``slice([10, 20, 30, 40], 1, 2) -> [10, 20]``

* ``slice([10, 20, 30, 40], 2) -> [20, 30, 40]``

* ``slice([10, 20, 30, 40], 2)[1] -> 20``

* ``isNull(slice([10, 20, 30, 40], 2)[0]) -> true``

* ``isNull(slice([10, 20, 30, 40], 2)[20]) -> true``

* ``slice(['a', 'b', 'c', 'd'], 8) -> []``

___
### <code>sort</code>
<code><b>sort(<i>&lt;value1&gt;</i> : array, <i>&lt;value2&gt;</i> : binaryfunction) => array</b></code><br/><br/>
Сортирует массив с помощью предоставленной функции предиката. Функция Sort принимает ссылку на два последовательных элемента в функции выражения как #item1 и #item2

* ``sort([4, 8, 2, 3], compare(#item1, #item2)) -> [2, 3, 4, 8]``

* ``sort(['a3', 'b2', 'c1'], iif(right(#item1, 1) >= right(#item2, 1), 1, -1)) -> ['c1', 'b2', 'a3']``

___
### <code>soundex</code>
<code><b>soundex(<i>&lt;value1&gt;</i> : string) => string</b></code><br/><br/>
Возвращает код SOUNDEX для строки.

* ``soundex('genius') -> 'G520'``

___
### <code>split</code>
<code><b>split(<i>&lt;string to split&gt;</i> : string, <i>&lt;split characters&gt;</i> : string) => array</b></code><br/><br/>
Разделяет строку на основе разделителя и возвращает массив строк.

* ``split('bojjus,guchus,dumbo', ',') -> ['bojjus', 'guchus', 'dumbo']``

* ``split('bojjus,guchus,dumbo', '|') -> ['bojjus,guchus,dumbo']``

* ``split('bojjus, guchus, dumbo', ', ') -> ['bojjus', 'guchus', 'dumbo']``

* ``split('bojjus, guchus, dumbo', ', ')[1] -> 'bojjus'``

* ``isNull(split('bojjus, guchus, dumbo', ', ')[0]) -> true``

* ``isNull(split('bojjus, guchus, dumbo', ', ')[20]) -> true``

* ``split('bojjusguchusdumbo', ',') -> ['bojjusguchusdumbo']``

___
### <code>sqrt</code>
<code><b>sqrt(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Вычисляет квадратный корень числа

* ``sqrt(9) -> 3``

___
### <code>startsWith</code>
<code><b>startsWith(<i>&lt;string&gt;</i> : string, <i>&lt;substring to check&gt;</i> : string) => boolean</b></code><br/><br/>
Проверяет, начинается ли строка с указанной строки

* ``startsWith('dumbo', 'du') -> true``

___
### <code>stddev</code>
<code><b>stddev(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Возвращает стандартное отклонение столбца

* ``stdDev(sales)``

___
### <code>stddevIf</code>
<code><b>stddevIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number) => double</b></code><br/><br/>
В зависимости от критерия возвращает стандартное отклонение столбца

* ``stddevIf(region == 'West', sales)``

___
### <code>stddevPopulation</code>
<code><b>stddevPopulation(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Возвращает среднеквадратичное отклонение генеральной совокупности для столбца

* ``stddevPopulation(sales)``

___
### <code>stddevPopulationIf</code>
<code><b>stddevPopulationIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number) => double</b></code><br/><br/>
В зависимости от критерия возвращает стандартное отклонение совокупности для столбца

* ``stddevPopulationIf(region == 'West', sales)``

___
### <code>stddevSample</code>
<code><b>stddevSample(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Возвращает стандартное отклонение выборки для столбца

* ``stddevSample(sales)``

___
### <code>stddevSampleIf</code>
<code><b>stddevSampleIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number) => double</b></code><br/><br/>
В зависимости от критерия получает стандартное отклонение выборки для столбца

* ``stddevSampleIf(region == 'West', sales)``

___
### <code>subDays</code>
<code><b>subDays(<i>&lt;date/timestamp&gt;</i> : datetime, <i>&lt;days to subtract&gt;</i> : integral) => datetime</b></code><br/><br/>
Вычтите месяцы из даты или метки времени. То же, что и оператор-operator для Date

* ``subDays(toDate('2016-08-08'), 1) -> toDate('2016-08-07')``

___
### <code>subMonths</code>
<code><b>subMonths(<i>&lt;date/timestamp&gt;</i> : datetime, <i>&lt;months to subtract&gt;</i> : integral) => datetime</b></code><br/><br/>
Вычитание месяцев из даты или метки времени

* ``subMonths(toDate('2016-09-30'), 1) -> toDate('2016-08-31')``

___
### <code>substring</code>
<code><b>substring(<i>&lt;string to subset&gt;</i> : string, <i>&lt;from 1-based index&gt;</i> : integral, [<i>&lt;number of characters&gt;</i> : integral]) => string</b></code><br/><br/>
Извлекает подстроку определенной длины из заданной позиции. Позиция отсчитывается от единицы. Если длина опущена, по умолчанию используется конец строки.

* ``substring('Cat in the hat', 5, 2) -> 'in'``

* ``substring('Cat in the hat', 5, 100) -> 'in the hat'``

* ``substring('Cat in the hat', 5) -> 'in the hat'``

* ``substring('Cat in the hat', 100, 100) -> ''``

___
### <code>sum</code>
<code><b>sum(<i>&lt;value1&gt;</i> : number) => number</b></code><br/><br/>
Возвращает статистическую сумму числового столбца

* ``sum(col)``

___
### <code>sumDistinct</code>
<code><b>sumDistinct(<i>&lt;value1&gt;</i> : number) => number</b></code><br/><br/>
Возвращает статистическую сумму различных значений числового столбца

* ``sumDistinct(col)``

___
### <code>sumDistinctIf</code>
<code><b>sumDistinctIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number) => number</b></code><br/><br/>
Возвращает общую сумму числового столбца на основе критериев. Условие может основываться на любом столбце

* ``sumDistinctIf(state == 'CA' && commission < 10000, sales)``

* ``sumDistinctIf(true, sales)``

___
### <code>sumIf</code>
<code><b>sumIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number) => number</b></code><br/><br/>
Возвращает общую сумму числового столбца на основе критериев. Условие может основываться на любом столбце

* ``sumIf(state == 'CA' && commission < 10000, sales)``

* ``sumIf(true, sales)``

___
### <code>tan</code>
<code><b>tan(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Вычисляет значение тангенса

* ``tan(0) -> 0.0``

___
### <code>tanh</code>
<code><b>tanh(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Вычисляет гиперболический тангенс значения

* ``tanh(0) -> 0.0``

___
### <code>toBinary</code>
<code><b>toBinary(<i>&lt;value1&gt;</i> : any) => binary</b></code><br/><br/>
Преобразует все числовые значения типа Date, timestamp и String в двоичное представление

* ``toBinary(3) -> [0x11]``

___
### <code>toBoolean</code>
<code><b>toBoolean(<i>&lt;value1&gt;</i> : string) => boolean</b></code><br/><br/>
Преобразует значения ('t ', ' true ', ' y ', ' YES ', ' 1 ') в true и (' f ', ' false ', ' n ', ' No ', ' 0 ') в значение false и NULL для любого другого значения

* ``toBoolean('true') -> true``

* ``toBoolean('n') -> false``

* ``isNull(toBoolean('truthy')) -> true``

___
### <code>toDate</code>
<code><b>toDate(<i>&lt;string&gt;</i> : any, [<i>&lt;date format&gt;</i> : string]) => date</b></code><br/><br/>
Преобразует входную строку даты в дату, используя необязательный входной формат даты. Сведения о доступных форматах см. в разделе Симпледатеформат Java. Если входной формат даты опущен, по умолчанию используется формат YYYY-[M] M-[d] d. Допустимые форматы: [гггг, гггг-[M] M, гггг-[M] M-[d] d, гггг-[M] M-[d] dT *]

* ``toDate('2012-8-18') -> toDate('2012-08-18')``

* ``toDate('12/18/2012', 'MM/dd/yyyy') -> toDate('2012-12-18')``

___
### <code>toDecimal</code>
<code><b>toDecimal(<i>&lt;value&gt;</i> : any, [<i>&lt;precision&gt;</i> : integral], [<i>&lt;scale&gt;</i> : integral], [<i>&lt;format&gt;</i> : string], [<i>&lt;locale&gt;</i> : string]) => decimal(10,0)</b></code><br/><br/>
Преобразует любое числовое значение или строку в десятичное значение. Если точность и масштаб не указаны, по умолчанию используется значение (10,2). Для преобразования можно использовать необязательный десятичный формат Java. Необязательный формат языкового стандарта в формате BCP47, например en-US, de, zh-CN

* ``toDecimal(123.45) -> 123.45``

* ``toDecimal('123.45', 8, 4) -> 123.4500``

* ``toDecimal('$123.45', 8, 4,'$###.00') -> 123.4500``

* ``toDecimal('Ç123,45', 10, 2, 'Ç###,##', 'de') -> 123.45``

___
### <code>toDouble</code>
<code><b>toDouble(<i>&lt;value&gt;</i> : any, [<i>&lt;format&gt;</i> : string], [<i>&lt;locale&gt;</i> : string]) => double</b></code><br/><br/>
Преобразует любое числовое значение или строку в двойное значение. Для преобразования может использоваться дополнительный десятичный формат Java. Необязательный формат языкового стандарта в формате BCP47, например en-US, de, zh-CN

* ``toDouble(123.45) -> 123.45``

* ``toDouble('123.45') -> 123.45``

* ``toDouble('$123.45', '$###.00') -> 123.45``

* ``toDouble('Ç123,45', 'Ç###,##', 'de') -> 123.45``

___
### <code>toFloat</code>
<code><b>toFloat(<i>&lt;value&gt;</i> : any, [<i>&lt;format&gt;</i> : string], [<i>&lt;locale&gt;</i> : string]) => float</b></code><br/><br/>
Преобразует любое числовое или строковое значение в плавающее. Для преобразования может использоваться дополнительный десятичный формат Java. Усекает любое двойное

* ``toFloat(123.45) -> 123.45f``

* ``toFloat('123.45') -> 123.45f``

* ``toFloat('$123.45', '$###.00') -> 123.45f``

___
### <code>toInteger</code>
<code><b>toInteger(<i>&lt;value&gt;</i> : any, [<i>&lt;format&gt;</i> : string], [<i>&lt;locale&gt;</i> : string]) => integer</b></code><br/><br/>
Преобразует любое числовое или строковое значение в целое число. Для преобразования может использоваться дополнительный десятичный формат Java. Усекает любое длинное, float, Double

* ``toInteger(123) -> 123``

* ``toInteger('123') -> 123``

* ``toInteger('$123', '$###') -> 123``

___
### <code>toLong</code>
<code><b>toLong(<i>&lt;value&gt;</i> : any, [<i>&lt;format&gt;</i> : string], [<i>&lt;locale&gt;</i> : string]) => long</b></code><br/><br/>
Преобразует любое числовое или строковое значение в длинное. Для преобразования может использоваться дополнительный десятичный формат Java. Усекает любое число с плавающей запятой, Double

* ``toLong(123) -> 123``

* ``toLong('123') -> 123``

* ``toLong('$123', '$###') -> 123``

___
### <code>toShort</code>
<code><b>toShort(<i>&lt;value&gt;</i> : any, [<i>&lt;format&gt;</i> : string], [<i>&lt;locale&gt;</i> : string]) => short</b></code><br/><br/>
Преобразует любое числовое или строковое значение в короткое. Для преобразования может использоваться дополнительный десятичный формат Java. Усекает любое целое число, Long, float, Double

* ``toShort(123) -> 123``

* ``toShort('123') -> 123``

* ``toShort('$123', '$###') -> 123``

___
### <code>toString</code>
<code><b>toString(<i>&lt;value&gt;</i> : any, [<i>&lt;number format/date format&gt;</i> : string]) => string</b></code><br/><br/>
Преобразует примитивный тип данных в строку. Для чисел и даты можно указать формат. Если формат не задан, выбирается значение по умолчанию. Для чисел используется десятичный формат Java, Все возможные форматы даты см. в разделе Java Симпледатеформат. Формат по умолчанию — гггг-мм-дд.

* ``toString(10) -> '10'``

* ``toString('engineer') -> 'engineer'``

* ``toString(123456.789, '##,###.##') -> '123,456.79'``

* ``toString(123.78, '000000.000') -> '000123.780'``

* ``toString(12345, '##0.#####E0') -> '12.345E3'``

* ``toString(toDate('2018-12-31')) -> '2018-12-31'``

* ``isNull(toString(toDate('2018-12-31', 'MM/dd/yy'))) -> true``

* ``toString(4 == 20) -> 'false'``

___
### <code>toTimestamp</code>
<code><b>toTimestamp(<i>&lt;string&gt;</i> : any, [<i>&lt;timestamp format&gt;</i> : string], [<i>&lt;time zone&gt;</i> : string]) => timestamp</b></code><br/><br/>
Преобразует строку в метку времени по указанному необязательному формату метки времени. Для всех возможных форматов используйте Java Симпледатеформат. Если метка времени пропущена, используется шаблон по умолчанию. гггг-[M] M-[d] d чч: мм: СС [. f...]. Можно передать дополнительный часовой пояс в формате "GMT", "PST", "UTC", "America/Кайман". Метка времени поддерживает точность в миллисекундах со значением 999Refer Java Симпледатеформат для доступных форматов. https://docs.oracle.com/javase/8/docs/api/java/text/SimpleDateFormat.html

* ``toTimestamp('2016-12-31 00:12:00') -> toTimestamp('2016-12-31 00:12:00')``

* ``toTimestamp('2016-12-31T00:12:00', 'yyyy-MM-dd\'T\'HH:mm:ss', 'PST') -> toTimestamp('2016-12-31 00:12:00')``

* ``toTimestamp('12/31/2016T00:12:00', 'MM/dd/yyyy\'T\'HH:mm:ss') -> toTimestamp('2016-12-31 00:12:00')``

* ``millisecond(toTimestamp('2019-02-03 05:19:28.871', 'yyyy-MM-dd HH:mm:ss.SSS')) -> 871``

___
### <code>toUTC</code>
<code><b>toUTC(<i>&lt;value1&gt;</i> : timestamp, [<i>&lt;value2&gt;</i> : string]) => timestamp</b></code><br/><br/>
Преобразует метку времени в формат UTC. Вы можете передать дополнительный часовой пояс в формате GMT, PST, UTC, "Острова Кайман". По умолчанию используется текущий Симпледатеформат Java Тимезонерефер для доступных форматов. https://docs.oracle.com/javase/8/docs/api/java/text/SimpleDateFormat.html

* ``toUTC(currentTimeStamp()) == toTimestamp('2050-12-12 19:18:12') -> false``

* ``toUTC(currentTimeStamp(), 'Asia/Seoul') != toTimestamp('2050-12-12 19:18:12') -> true``

___
### <code>translate</code>
<code><b>translate(<i>&lt;string to translate&gt;</i> : string, <i>&lt;lookup characters&gt;</i> : string, <i>&lt;replace characters&gt;</i> : string) => string</b></code><br/><br/>
Заменяет один набор знаков другим набором знаков в строке. Символы имеют от 1 до 1 замены

* ``translate('(bojjus)', '()', '[]') -> '[bojjus]'``

* ``translate('(gunchus)', '()', '[') -> '[gunchus'``

___
### <code>trim</code>
<code><b>trim(<i>&lt;string to trim&gt;</i> : string, [<i>&lt;trim characters&gt;</i> : string]) => string</b></code><br/><br/>
Обрезает строку начальных и конечных знаков. Если второй параметр не указан, удаляются пробелы. В противном случае все символы, указанные во втором параметре, обрезаются.

* ``trim('  dumbo  ') -> 'dumbo'``

* ``trim('!--!du!mbo!', '-!') -> 'du!mbo'``

___
### <code>true</code>
<code><b>true() => boolean</b></code><br/><br/>
Всегда возвращает истинное значение. Использовать синтаксис функции (true ()), если имеется столбец с именем "true"

* ``(10 + 20 == 30) -> true``

* ``(10 + 20 == 30) -> true()``

___
### <code>typeMatch</code>
<code><b>typeMatch(<i>&lt;type&gt;</i> : string, <i>&lt;base type&gt;</i> : string) => boolean</b></code><br/><br/>
Сопоставляет тип столбца. Может использоваться только в выражениях шаблонов. Number соответствует Short, Integer, Long, Double, float или Decimal, целочисленные совпадения Short, Integer, Long, дробные числа соответствуют значениям типа Double, float, Decimal и DateTime, соответствующие типу Date или timestamp

* ``typeMatch(type, 'number')``

* ``typeMatch('date', 'datetime')``

___
### <code>upper</code>
<code><b>upper(<i>&lt;value1&gt;</i> : string) => string</b></code><br/><br/>
Строка в верхнем регистре

* ``upper('bojjus') -> 'BOJJUS'``

___
### <code>uuid</code>
<code><b>uuid() => string</b></code><br/><br/>
Возвращает созданный UUID

* ``uuid()``

___
### <code>variance</code>
<code><b>variance(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Возвращает дисперсию столбца

* ``variance(sales)``

___
### <code>varianceIf</code>
<code><b>varianceIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number) => double</b></code><br/><br/>
В зависимости от критерия Получает дисперсию столбца

* ``varianceIf(region == 'West', sales)``

___
### <code>variancePopulation</code>
<code><b>variancePopulation(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Возвращает дисперсию генеральной совокупности для столбца

* ``variancePopulation(sales)``

___
### <code>variancePopulationIf</code>
<code><b>variancePopulationIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number) => double</b></code><br/><br/>
В зависимости от критерия Возвращает дисперсию совокупности столбца

* ``variancePopulationIf(region == 'West', sales)``

___
### <code>varianceSample</code>
<code><b>varianceSample(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Возвращает несмещенную дисперсию столбца

* ``varianceSample(sales)``

___
### <code>varianceSampleIf</code>
<code><b>varianceSampleIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number) => double</b></code><br/><br/>
В зависимости от критерия получает несмещенную дисперсию столбца

* ``varianceSampleIf(region == 'West', sales)``

___
### <code>weekOfYear</code>
<code><b>weekOfYear(<i>&lt;value1&gt;</i> : datetime) => integer</b></code><br/><br/>
Возвращает неделю года с учетом даты

* ``weekOfYear(toDate('2008-02-20')) -> 8``

___
### <code>weeks</code>
<code><b>weeks(<i>&lt;value1&gt;</i> : integer) => long</b></code><br/><br/>
Длительность в миллисекундах для количества недель

* ``weeks(2) -> 1209600000L``

___
### <code>xor</code>
<code><b>xor(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : boolean) => boolean</b></code><br/><br/>
Логический оператор XOR. Аналогично оператору ^

* ``xor(true, false) -> true``

* ``xor(true, true) -> false``

* ``true ^ false -> true``

___
### <code>year</code>
<code><b>year(<i>&lt;value1&gt;</i> : datetime) => integer</b></code><br/><br/>
Возвращает значение года для даты

* ``year(toDate('2012-8-8')) -> 2012``


## <a name="next-steps"></a>Дальнейшие действия

[Узнайте, как использовать построитель выражений](concepts-data-flow-expression-builder.md).
