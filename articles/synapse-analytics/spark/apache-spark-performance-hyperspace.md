---
title: Индексы гиперпространства для Apache Spark
description: Оптимизация производительности для Apache Spark с помощью индексов гиперпространств
services: synapse-analytics
author: euangMS
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: spark
ms.date: 08/12/2020
ms.author: euang
ms.reviewer: euang
zone_pivot_groups: programming-languages-spark-all-minus-sql
ms.openlocfilehash: f25aae64e117452cd689b68c5478e7431d1a21bf
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/25/2020
ms.locfileid: "91249371"
---
# <a name="hyperspace-an-indexing-subsystem-for-apache-spark"></a>Гиперпространства: подсистема индексирования для Apache Spark

В гиперпространства появилась возможность Apache Spark пользователям создавать индексы по их наборам данных, таким как CSV, JSON и Parquet, и использовать их для ускорения запросов и рабочих нагрузок.

В этой статье мы выделим основы гиперпространства, подчеркните простоту и покажем, как она может использоваться практически всеми.

Заявление об отказе: гиперпространства помогает ускорить рабочие нагрузки или запросы при двух обстоятельствах:

* Запросы содержат фильтры для предикатов с высокой избирательностью. Например, может потребоваться выбрать 100 совпадающих строк из миллиона потенциальных строк.
* Запросы содержат соединение, требующее большого случайного использования. Например, может потребоваться присоединить к набору данных 100 ГБ с набором данных размером 10 ГБ.

Может потребоваться тщательное отслеживание рабочих нагрузок и определение того, помогает ли индексирование потребоваться в каждом конкретном случае.

Этот документ также доступен в форме записной книжки для [Python](https://github.com/microsoft/hyperspace/blob/master/notebooks/python/Hitchhikers%20Guide%20to%20Hyperspace.ipynb), [C#](https://github.com/microsoft/hyperspace/blob/master/notebooks/csharp/Hitchhikers%20Guide%20to%20Hyperspace.ipynb)и [Scala](https://github.com/microsoft/hyperspace/blob/master/notebooks/scala/Hitchhikers%20Guide%20to%20Hyperspace.ipynb) .

## <a name="setup"></a>Установка

Чтобы начать работу, запустите новый сеанс Spark. Поскольку этот документ является учебником только для демонстрации того, что может предложить гиперпространства, вы вносите изменения в конфигурацию, позволяющие выделять, что делает гиперпространства в небольших наборах данных. 

По умолчанию Spark использует широковещательное соединение для оптимизации запросов JOIN, если размер данных одной стороны объединения невелик (что является вариантом для образцов данных, используемых в этом учебнике). Поэтому мы отключаем широковещательные подключения, чтобы позже при выполнении запросов присоединиться к работе, Spark использует соединение Sort-MERGE. В основном показано, как использовать индексы гиперпространства при масштабировании для ускорения запросов на соединение.

Результат выполнения следующей ячейки показывает ссылку на успешно созданный сеанс Spark и выводит "-1" в качестве значения для измененной конфигурации объединения, что означает, что широковещательное соединение успешно отключено.

:::zone pivot = "programming-language-scala"

```scala
// Start your Spark session
spark

// Disable BroadcastHashJoin, so Spark will use standard SortMergeJoin. Currently, Hyperspace indexes utilize SortMergeJoin to speed up query.
spark.conf.set("spark.sql.autoBroadcastJoinThreshold", -1)

// Verify that BroadcastHashJoin is set correctly
println(spark.conf.get("spark.sql.autoBroadcastJoinThreshold"))

```

::: zone-end

:::zone pivot = "programming-language-python"

```python
# Start your Spark session.
spark

# Disable BroadcastHashJoin, so Spark will use standard SortMergeJoin. Currently, Hyperspace indexes utilize SortMergeJoin to speed up query.
spark.conf.set("spark.sql.autoBroadcastJoinThreshold", -1)

# Verify that BroadcastHashJoin is set correctly 
print(spark.conf.get("spark.sql.autoBroadcastJoinThreshold"))
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
// Disable BroadcastHashJoin, so Spark will use standard SortMergeJoin. Currently, Hyperspace indexes utilize SortMergeJoin to speed up query.
spark.Conf().Set("spark.sql.autoBroadcastJoinThreshold", -1);

// Verify that BroadcastHashJoin is set correctly.
Console.WriteLine(spark.Conf().Get("spark.sql.autoBroadcastJoinThreshold"));
```

::: zone-end

Результат будет иметь такой вид:

```console
res3: org.apache.spark.sql.SparkSession = org.apache.spark.sql.SparkSession@297e957d
-1
```

## <a name="data-preparation"></a>Подготовка данных

Чтобы подготовить среду, вы создадите образцы записей данных и сохраните их как файлы данных Parquet. Parquet используется для иллюстрации, но можно также использовать другие форматы, например CSV. В последующих ячейках вы узнаете, как создать несколько индексов гиперпространства в этом образце набора данных и использовать их при выполнении запросов.

Пример записи соответствует двум наборам данных: Отделу и сотруднику. Необходимо настроить пути "Емплокатион" и "Дептлокатион" таким образом, чтобы учетная запись хранения указывала на нужное место для сохранения созданных файлов данных.

Выходные данные в следующей ячейке показывают содержимое наших наборов данных в виде списков триады, за которыми следуют ссылки на кадры данных, созданные для сохранения содержимого каждого из них в предпочтительном расположении.

:::zone pivot = "programming-language-scala"

```scala
import org.apache.spark.sql.DataFrame

// Sample department records
val departments = Seq(
      (10, "Accounting", "New York"),
      (20, "Research", "Dallas"),
      (30, "Sales", "Chicago"),
      (40, "Operations", "Boston"))

// Sample employee records
val employees = Seq(
      (7369, "SMITH", 20),
      (7499, "ALLEN", 30),
      (7521, "WARD", 30),
      (7566, "JONES", 20),
      (7698, "BLAKE", 30),
      (7782, "CLARK", 10),
      (7788, "SCOTT", 20),
      (7839, "KING", 10),
      (7844, "TURNER", 30),
      (7876, "ADAMS", 20),
      (7900, "JAMES", 30),
      (7934, "MILLER", 10),
      (7902, "FORD", 20),
      (7654, "MARTIN", 30))

// Save sample data in the Parquet format
import spark.implicits._
val empData: DataFrame = employees.toDF("empId", "empName", "deptId")
val deptData: DataFrame = departments.toDF("deptId", "deptName", "location")

val empLocation: String = "/<yourpath>/employees.parquet"       //TODO ** customize this location path **
val deptLocation: String = "/<yourpath>/departments.parquet"     //TODO ** customize this location path **
empData.write.mode("overwrite").parquet(empLocation)
deptData.write.mode("overwrite").parquet(deptLocation)
```

::: zone-end

:::zone pivot = "programming-language-python"

```python

from pyspark.sql.types import StructField, StructType, StringType, IntegerType

# Sample department records
departments = [(10, "Accounting", "New York"), (20, "Research", "Dallas"), (30, "Sales", "Chicago"), (40, "Operations", "Boston")]

# Sample employee records
employees = [(7369, "SMITH", 20), (7499, "ALLEN", 30), (7521, "WARD", 30), (7566, "JONES", 20), (7698, "BLAKE", 30)]

# Create a schema for the dataframe
dept_schema = StructType([StructField('deptId', IntegerType(), True), StructField('deptName', StringType(), True), StructField('location', StringType(), True)])
emp_schema = StructType([StructField('empId', IntegerType(), True), StructField('empName', StringType(), True), StructField('deptId', IntegerType(), True)])

departments_df = spark.createDataFrame(departments, dept_schema)
employees_df = spark.createDataFrame(employees, emp_schema)

#TODO ** customize this location path **
emp_Location = "/<yourpath>/employees.parquet"
dept_Location = "/<yourpath>/departments.parquet"

employees_df.write.mode("overwrite").parquet(emp_Location)
departments_df.write.mode("overwrite").parquet(dept_Location)

```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp

using Microsoft.Spark.Sql.Types;

// Sample department records
var departments = new List<GenericRow>()
{
    new GenericRow(new object[] {10, "Accounting", "New York"}),
    new GenericRow(new object[] {20, "Research", "Dallas"}),
    new GenericRow(new object[] {30, "Sales", "Chicago"}),
    new GenericRow(new object[] {40, "Operations", "Boston"})
};

// Sample employee records
var employees = new List<GenericRow>() {
      new GenericRow(new object[] {7369, "SMITH", 20}),
      new GenericRow(new object[] {7499, "ALLEN", 30}),
      new GenericRow(new object[] {7521, "WARD", 30}),
      new GenericRow(new object[] {7566, "JONES", 20}),
      new GenericRow(new object[] {7698, "BLAKE", 30}),
      new GenericRow(new object[] {7782, "CLARK", 10}),
      new GenericRow(new object[] {7788, "SCOTT", 20}),
      new GenericRow(new object[] {7839, "KING", 10}),
      new GenericRow(new object[] {7844, "TURNER", 30}),
      new GenericRow(new object[] {7876, "ADAMS", 20}),
      new GenericRow(new object[] {7900, "JAMES", 30}),
      new GenericRow(new object[] {7934, "MILLER", 10}),
      new GenericRow(new object[] {7902, "FORD", 20}),
      new GenericRow(new object[] {7654, "MARTIN", 30})
};

// Save sample data in the Parquet format
var departmentSchema = new StructType(new List<StructField>()
{
    new StructField("deptId", new IntegerType()),
    new StructField("deptName", new StringType()),
    new StructField("location", new StringType())
});
var employeeSchema = new StructType(new List<StructField>()
{
    new StructField("empId", new IntegerType()),
    new StructField("empName", new StringType()),
    new StructField("deptId", new IntegerType())
});

DataFrame empData = spark.CreateDataFrame(employees, employeeSchema); 
DataFrame deptData = spark.CreateDataFrame(departments, departmentSchema); 

string empLocation = "/<yourpath>/employees.parquet";       //TODO ** customize this location path **
string deptLocation = "/<yourpath>/departments.parquet";     //TODO ** customize this location path **
empData.Write().Mode("overwrite").Parquet(empLocation);
deptData.Write().Mode("overwrite").Parquet(deptLocation);

```

::: zone-end

Результат будет иметь такой вид:

```console
import org.apache.spark.sql.DataFrame  
departments: Seq[(Int, String, String)] = List((10,Accounting,New York), (20,Research,Dallas), (30,Sales,Chicago), (40,Operations,Boston))  
employees: Seq[(Int, String, Int)] = List((7369,SMITH,20), (7499,ALLEN,30), (7521,WARD,30), (7566,JONES,20), (7698,BLAKE,30), (7782,CLARK,10), (7788,SCOTT,20), (7839,KING,10), (7844,TURNER,30), (7876,ADAMS,20), (7900,JAMES,30), (7934,MILLER,10), (7902,FORD,20), (7654,MARTIN,30))  
import spark.implicits._  
empData: org.apache.spark.sql.DataFrame = [empId: int, empName: string ... 1 more field]  
deptData: org.apache.spark.sql.DataFrame = [deptId: int, deptName: string ... 1 more field]  
empLocation: String = /your-path/employees.parquet  
deptLocation: String = /your-path/departments.parquet  
```

Давайте проверим содержимое файлов Parquet, которые были созданы, чтобы убедиться в том, что они содержат ожидаемые записи в правильном формате. Позже мы будем использовать эти файлы данных для создания индексов гиперпространства и выполнения образцов запросов.

При выполнении следующей ячейки создаются и выводятся выходные данные, которые отображают строки в кадрах данных сотрудников и отделов в табличной форме. Должно быть 14 сотрудников и 4 отдела, каждое из которых соответствует одному из триады, созданному в предыдущей ячейке.

:::zone pivot = "programming-language-scala"

```scala
// empLocation and deptLocation are the user defined locations above to save parquet files
val empDF: DataFrame = spark.read.parquet(empLocation)
val deptDF: DataFrame = spark.read.parquet(deptLocation)

// Verify the data is available and correct
empDF.show()
deptDF.show()
```

::: zone-end

:::zone pivot = "programming-language-python"

```python

# emp_Location and dept_Location are the user-defined locations above to save parquet files
emp_DF = spark.read.parquet(emp_Location)
dept_DF = spark.read.parquet(dept_Location)

# Verify the data is available and correct
emp_DF.show()
dept_DF.show()

```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp

// empLocation and deptLocation are the user-defined locations above to save parquet files
DataFrame empDF = spark.Read().Parquet(empLocation);
DataFrame deptDF = spark.Read().Parquet(deptLocation);

// Verify the data is available and correct
empDF.Show();
deptDF.Show();

```

::: zone-end

Результат будет иметь такой вид:

```console
empDF: org.apache.spark.sql.DataFrame = [empId: int, empName: string ... 1 more field]  
deptDF: org.apache.spark.sql.DataFrame = [deptId: int, deptName: string ... 1 more field]
```

```console
|EmpId|EmpName|DeptId|
|-----|-------|------|
| 7499|  ALLEN|    30|
| 7521|   WARD|    30|
| 7369|  SMITH|    20|
| 7844| TURNER|    30|
| 7876|  ADAMS|    20|
| 7900|  JAMES|    30|
| 7934| MILLER|    10|
| 7839|   KING|    10|
| 7566|  JONES|    20|
| 7698|  BLAKE|    30|
| 7782|  CLARK|    10|
| 7788|  SCOTT|    20|
| 7902|   FORD|    20|
| 7654| MARTIN|    30|  
```

&nbsp; &nbsp;

```console
|DeptId|  DeptName|Location|
|------|----------|--------|
|    10|Accounting|New York|
|    40|Operations|  Boston|
|    20|  Research|  Dallas|
|    30|     Sales| Chicago|
```

## <a name="indexes"></a>Индексы

Гиперпространства позволяет создавать индексы для записей, просмотренных из сохраненных файлов данных. После успешного создания в метаданные гиперпространства добавляется запись, соответствующая индексу. Эти метаданные позже используются оптимизатором Apache Spark (с нашими расширениями) во время обработки запроса для поиска и использования правильных индексов.

После создания индексов можно выполнить несколько действий:

* **Обновить при изменении базовых данных.** Чтобы записать изменения, можно обновить существующий индекс.
* **Удалите, если индекс не требуется.** Вы можете выполнить обратимое удаление, т. е. индекс физически не удален, но помечен как "удалено", чтобы он больше не использовался в рабочих нагрузках.
* **Очистка, если индекс больше не требуется.** Можно выполнить операцию очистки индекса, что приводит к полному удалению содержимого индекса и связанных с ним метаданных из метаданных гиперпространства.

Обновить если базовые данные изменяются, можно обновить существующий индекс, чтобы захватить его.
Удалить если индекс не нужен, можно выполнить обратимое удаление, т. е. индекс физически удален, но помеченный как удаленный, чтобы он больше не использовался в рабочих нагрузках.

В следующих разделах показано, как можно выполнять операции управления индексами в гиперпространства.

Сначала необходимо импортировать необходимые библиотеки и создать экземпляр гиперпространства. Позже этот экземпляр будет использоваться для вызова различных интерфейсов API гиперпространства для создания индексов в демонстрационных данных и изменения этих индексов.

В выходных данных выполнения следующей ячейки показана ссылка на созданный экземпляр элемента гиперпространства.

:::zone pivot = "programming-language-scala"

```scala
// Create an instance of Hyperspace
import com.microsoft.hyperspace._

val hyperspace: Hyperspace = Hyperspace()
```

::: zone-end

:::zone pivot = "programming-language-python"

```python

# Create an instance of Hyperspace
hyperspace = Hyperspace(spark)

```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp

// Create an instance of Hyperspace
using Microsoft.Spark.Extensions.Hyperspace;

Hyperspace hyperspace = new Hyperspace(spark);

```

::: zone-end

Результат будет иметь такой вид:

```console
import com.microsoft.hyperspace._  
hyperspace: com.microsoft.hyperspace.Hyperspace = com.microsoft.hyperspace.Hyperspace@1432f740
```

## <a name="create-indexes"></a>Создать индексы

Чтобы создать индекс гиперпространства, необходимо предоставить два фрагмента информации:

* Кадр данных Spark, ссылающийся на индексируемые данные.
* Объект конфигурации индекса Индексконфиг, который указывает имя индекса и индексируемые и включаемые столбцы индекса.

Начните с создания трех индексов гиперпространства на нашем образце данных: два индекса в наборе данных «deptIndex1» и «deptIndex2» и один индекс в наборе данных «сотрудники» с именем «Емпиндекс». Для каждого индекса требуется соответствующая Индексконфиг для записи имени вместе со списками столбцов для индексированных и включаемых столбцов. При выполнении следующей ячейки создаются эти Индексконфигс, а их выходные данные перечисляются.

> [!Note]
> Столбец индекса — это столбец, который отображается в фильтрах или условиях объединения. Включенный столбец — это столбец, который отображается в выбранном проекте.

Например, в следующем запросе:

```sql
SELECT X
FROM T
WHERE Y = 2
```

X может быть столбцом индекса, а Y может быть включаемым столбцом.

:::zone pivot = "programming-language-scala"

```scala
// Create index configurations
import com.microsoft.hyperspace.index.IndexConfig

val empIndexConfig: IndexConfig = IndexConfig("empIndex", Seq("deptId"), Seq("empName"))
val deptIndexConfig1: IndexConfig = IndexConfig("deptIndex1", Seq("deptId"), Seq("deptName"))
val deptIndexConfig2: IndexConfig = IndexConfig("deptIndex2", Seq("location"), Seq("deptName"))
```

::: zone-end

:::zone pivot = "programming-language-python"

```python

# Create index configurations

emp_IndexConfig = IndexConfig("empIndex1", ["deptId"], ["empName"])
dept_IndexConfig1 = IndexConfig("deptIndex1", ["deptId"], ["deptName"])
dept_IndexConfig2 = IndexConfig("deptIndex2", ["location"], ["deptName"])

```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp

using Microsoft.Spark.Extensions.Hyperspace.Index;

var empIndexConfig = new IndexConfig("empIndex", new string[] {"deptId"}, new string[] {"empName"});
var deptIndexConfig1 = new IndexConfig("deptIndex1", new string[] {"deptId"}, new string[] {"deptName"});
var deptIndexConfig2 = new IndexConfig("deptIndex2", new string[] {"location"}, new string[] {"deptName"});

```

::: zone-end

Результат будет иметь такой вид:

```console
import com.microsoft.hyperspace.index.IndexConfig  
empIndexConfig: com.microsoft.hyperspace.index.IndexConfig = [indexName: empIndex; indexedColumns: deptid; includedColumns: empname]  
deptIndexConfig1: com.microsoft.hyperspace.index.IndexConfig = [indexName: deptIndex1; indexedColumns: deptid; includedColumns: deptname]  
deptIndexConfig2: com.microsoft.hyperspace.index.IndexConfig = [indexName: deptIndex2; indexedColumns: location; includedColumns: deptname]  
```
Теперь вы создадите три индекса с помощью ваших конфигураций индекса. Для этой цели вы вызываете команду "createIndex" для нашего экземпляра гиперпространства. Для этой команды требуется конфигурация индекса и фрейм данных, содержащий строки для индексирования. При выполнении следующей ячейки создаются три индекса.

:::zone pivot = "programming-language-scala"

```scala
// Create indexes from configurations
import com.microsoft.hyperspace.index.Index

hyperspace.createIndex(empDF, empIndexConfig)
hyperspace.createIndex(deptDF, deptIndexConfig1)
hyperspace.createIndex(deptDF, deptIndexConfig2)
```

::: zone-end

:::zone pivot = "programming-language-python"

```python

# Create indexes from configurations

hyperspace.createIndex(emp_DF, emp_IndexConfig)
hyperspace.createIndex(dept_DF, dept_IndexConfig1)
hyperspace.createIndex(dept_DF, dept_IndexConfig2)

```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp

// Create indexes from configurations
hyperspace.CreateIndex(empDF, empIndexConfig);
hyperspace.CreateIndex(deptDF, deptIndexConfig1);
hyperspace.CreateIndex(deptDF, deptIndexConfig2);

```

::: zone-end

Результат будет иметь такой вид:

```console
import com.microsoft.hyperspace.index.Index
```

## <a name="list-indexes"></a>Индексы списка

Приведенный ниже код показывает, как можно вывести список всех доступных индексов в экземпляре гиперпространства. Он использует API-интерфейс "индексы", который возвращает сведения о существующих индексах в виде таблицы данных Spark, чтобы можно было выполнять дополнительные операции. 

Например, можно вызывать допустимые операции с этим кадром данных для проверки содержимого или его анализа (например, фильтровать конкретные индексы или группировать их в соответствии с требуемым свойством).

В следующей ячейке для полной печати строк и отображения сведений о индексах в табличной форме используется действие "показывать" в кадре данных. Для каждого индекса можно увидеть всю информацию, сохраненную в метаданных. Сразу же обратите внимание на следующее:

* config. indexName, config. Индекседколумнс, config. Инклудедколумнс и Status. status — это поля, на которые пользователь обычно ссылается.
* Дфсигнатуре создается автоматически с помощью гиперпространства и является уникальным для каждого индекса. Гиперпространства использует эту подпись для внутренних целей, чтобы поддерживать индекс и использовать его во время выполнения запроса.


В следующих выходных данных все три индекса должны иметь состояние "активный", а их имя, индексированные столбцы и включаемые столбцы должны совпадать с тем, что мы определили в конфигурациях индекса выше.

:::zone pivot = "programming-language-scala"

```scala
hyperspace.indexes.show
```

::: zone-end

:::zone pivot = "programming-language-python"

```python

hyperspace.indexes().show()

```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp

hyperspace.Indexes().Show();

```

::: zone-end

Результат будет иметь такой вид:

```console
|Config.IndexName|Config.IndexedColumns|Config.IncludedColumns|        SchemaString|   SignatureProvider|         DfSignature|      SerializedPlan|NumBuckets|             DirPath|Status.Value|Stats.IndexSize|
|----------------|---------------------|----------------------|--------------------|--------------------|--------------------|--------------------|----------|--------------------|------------|---------------|
|      deptIndex1|             [deptId]|            [deptName]|`deptId` INT,`dep...|com.microsoft.cha...|0effc1610ae2e7c49...|Relation[deptId#3...|       200|abfss://datasets@...|      ACTIVE|              0|
|      deptIndex2|           [location]|            [deptName]|`location` STRING...|com.microsoft.cha...|0effc1610ae2e7c49...|Relation[deptId#3...|       200|abfss://datasets@...|      ACTIVE|              0|
|        empIndex|             [deptId]|             [empName]|`deptId` INT,`emp...|com.microsoft.cha...|30768c6c9b2533004...|Relation[empId#32...|       200|abfss://datasets@...|      ACTIVE|              0|
```

## <a name="delete-indexes"></a>Удаление индексов

Можно удалить существующий индекс с помощью API "Делетеиндекс" и указав имя индекса. Удаление индекса выполняет обратимое удаление: оно в основном обновляет состояние индекса в метаданных гиперпространства с "ACTIVE" на "DELETEd". Это исключит удаленный индекс из любой будущей оптимизации запроса, а гиперпространства больше не выберет этот индекс для любого запроса. 

Однако файлы индекса для удаленного индекса по-прежнему остаются доступными (так как это обратимое удаление), поэтому индекс может быть восстановлен, если пользователь запрашивает.

Следующая ячейка Удаляет индекс с именем "deptIndex2" и перечисляет метаданные гиперпространства после этого. Выходные данные должны быть похожи на ячейки "List indexes", за исключением "deptIndex2", состояние которого теперь должно быть изменено на "УДАЛЕНо".

:::zone pivot = "programming-language-scala"

```scala
hyperspace.deleteIndex("deptIndex2")

hyperspace.indexes.show
```

::: zone-end

:::zone pivot = "programming-language-python"

```python

hyperspace.deleteIndex("deptIndex2")
hyperspace.indexes().show()

```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
hyperspace.DeleteIndex("deptIndex2");

hyperspace.Indexes().Show();

```

::: zone-end

Результат будет иметь такой вид:

```console
|Config.IndexName|Config.IndexedColumns|Config.IncludedColumns|        SchemaString|   SignatureProvider|         DfSignature|      SerializedPlan|NumBuckets|             DirPath|Status.Value|Stats.IndexSize|
|----------------|---------------------|----------------------|--------------------|--------------------|--------------------|--------------------|----------|--------------------|------------|---------------|
|      deptIndex1|             [deptId]|            [deptName]|`deptId` INT,`dep...|com.microsoft.cha...|0effc1610ae2e7c49...|Relation[deptId#3...|       200|abfss://datasets@...|      ACTIVE|              0|
|      deptIndex2|           [location]|            [deptName]|`location` STRING...|com.microsoft.cha...|0effc1610ae2e7c49...|Relation[deptId#3...|       200|abfss://datasets@...|     DELETED|              0|
|        empIndex|             [deptId]|             [empName]|`deptId` INT,`emp...|com.microsoft.cha...|30768c6c9b2533004...|Relation[empId#32...|       200|abfss://datasets@...|      ACTIVE|              0|
```

## <a name="restore-indexes"></a>Восстановить индексы

Для восстановления удаленного индекса можно использовать API "Рестореиндекс". При этом последняя версия индекса будет возвращена в активное состояние и снова станет доступной для запросов. В следующей ячейке приведен пример использования "Рестореиндекс". Удалите "deptIndex1" и восстановите его. Выходные данные показывают, что "deptIndex1" сначала перешел в состояние "УДАЛЕНо" после вызова команды "Делетеиндекс" и возвращается в состояние "активно" после вызова "Рестореиндекс".

:::zone pivot = "programming-language-scala"

```scala
hyperspace.deleteIndex("deptIndex1")

hyperspace.indexes.show

hyperspace.restoreIndex("deptIndex1")

hyperspace.indexes.show
```

::: zone-end

:::zone pivot = "programming-language-python"

```python

hyperspace.deleteIndex("deptIndex1")
hyperspace.indexes().show()
hyperspace.restoreIndex("deptIndex1")
hyperspace.indexes().show()

```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp

hyperspace.DeleteIndex("deptIndex1");
hyperspace.Indexes().Show();
hyperspace.RestoreIndex("deptIndex1");
hyperspace.Indexes().Show();

```

::: zone-end

Результат будет иметь такой вид:

```console
|Config.IndexName|Config.IndexedColumns|Config.IncludedColumns|        SchemaString|   SignatureProvider|         DfSignature|      SerializedPlan|NumBuckets|             DirPath|Status.Value|Stats.indexSize|
|----------------|---------------------|----------------------|--------------------|--------------------|--------------------|--------------------|----------|--------------------|------------|---------------|
|      deptIndex1|             [deptId]|            [deptName]|`deptId` INT,`dep...|com.microsoft.cha...|0effc1610ae2e7c49...|Relation[deptId#3...|       200|abfss://datasets@...|     DELETED|              0|
|      deptIndex2|           [location]|            [deptName]|`location` STRING...|com.microsoft.cha...|0effc1610ae2e7c49...|Relation[deptId#3...|       200|abfss://datasets@...|     DELETED|              0|
|        empIndex|             [deptId]|             [empName]|`deptId` INT,`emp...|com.microsoft.cha...|30768c6c9b2533004...|Relation[empId#32...|       200|abfss://datasets@...|      ACTIVE|              0|
```

&nbsp; &nbsp;

```console
|Config.IndexName|Config.IndexedColumns|Config.IncludedColumns|        SchemaString|   SignatureProvider|         DfSignature|      SerializedPlan|NumBuckets|             DirPath|Status.value|Stats.IndexSize|
|----------------|---------------------|----------------------|--------------------|--------------------|--------------------|--------------------|----------|--------------------|------------|---------------|
|      deptIndex1|             [deptId]|            [deptName]|`deptId` INT,`dep...|com.microsoft.cha...|0effc1610ae2e7c49...|Relation[deptId#3...|       200|abfss://datasets@...|      ACTIVE|              0|
|      deptIndex2|           [location]|            [deptName]|`location` STRING...|com.microsoft.cha...|0effc1610ae2e7c49...|Relation[deptId#3...|       200|abfss://datasets@...|     DELETED|              0|
|        empIndex|             [deptId]|             [empName]|`deptId` INT,`emp...|com.microsoft.cha...|30768c6c9b2533004...|Relation[empId#32...|       200|abfss://datasets@...|      ACTIVE|              0|
```

## <a name="vacuum-indexes"></a>Индексы очистки

Можно выполнить жесткое удаление, то есть полностью удалить файлы и запись метаданных для удаленного индекса с помощью команды **вакууминдекс** . Это действие необратимо. Он физически удаляет все файлы индексов, поэтому это окончательное удаление.

Следующая ячейка очистки индекса "deptIndex2" и отображение метаданных гиперпространства после очистки. Вы должны увидеть записи метаданных для двух индексов "deptIndex1" и "Емпиндекс" с состоянием "ACTIVE" и без записи для "deptIndex2".

:::zone pivot = "programming-language-scala"

```scala
hyperspace.vacuumIndex("deptIndex2")

hyperspace.indexes.show
```

::: zone-end

:::zone pivot = "programming-language-python"

```python

hyperspace.vacuumIndex("deptIndex2")
hyperspace.indexes().show()

```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp

hyperspace.VacuumIndex("deptIndex2");
hyperspace.Indexes().Show();

```

::: zone-end

Результат будет иметь такой вид:

```console
|Config.IndexName|Config.IndexedColumns|Config.IncludedColumns|        SchemaString|   SignatureProvider|         DfSignature|      SerializedPlan|NumBuckets|             DirPath|Status.Value|Stats.IndexSize|
|----------------|---------------------|----------------------|--------------------|--------------------|--------------------|--------------------|----------|--------------------|------------|---------------|
|      deptIndex1|             [deptId]|            [deptName]|`deptId` INT,`dep...|com.microsoft.cha...|0effc1610ae2e7c49...|Relation[deptId#3...|       200|abfss://datasets@...|      ACTIVE|              0|
|        empIndex|             [deptId]|             [empName]|`deptId` INT,`emp...|com.microsoft.cha...|30768c6c9b2533004...|Relation[empId#32...|       200|abfss://datasets@...|      ACTIVE|              0|
```

## <a name="enable-or-disable-hyperspace"></a>Включение или отключение гиперпространства

Гиперпространства предоставляет API-интерфейсы для включения или отключения использования индексов в Spark.

* С помощью команды **енаблехиперспаце** правила оптимизации гиперпространства становятся видимыми для оптимизатора Spark и используют существующие индексы гиперпространства для оптимизации пользовательских запросов.
* При использовании команды **дисаблехиперспаце** правила гиперпространства больше не применяются во время оптимизации запросов. Отключение гиперпространства не влияет на созданные индексы, так как они остаются неизменными.

В следующей ячейке показано, как можно использовать эти команды для включения или отключения гиперпространства. В выходных данных отображается ссылка на существующий сеанс Spark, конфигурация которого обновлена.

:::zone pivot = "programming-language-scala"

```scala
// Enable Hyperspace
spark.enableHyperspace

// Disable Hyperspace
spark.disableHyperspace
```

::: zone-end

:::zone pivot = "programming-language-python"

```python

# Enable Hyperspace
Hyperspace.enable(spark)

# Disable Hyperspace
Hyperspace.disable(spark)

```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp

// Enable Hyperspace
spark.EnableHyperspace();

// Disable Hyperspace
spark.DisableHyperspace();

```

::: zone-end

Результат будет иметь такой вид:

```console
res48: org.apache.spark.sql.Spark™Session = org.apache.spark.sql.SparkSession@39fe1ddb  
res51: org.apache.spark.sql.Spark™Session = org.apache.spark.sql.SparkSession@39fe1ddb
```

## <a name="index-usage"></a>Использование индекса

Чтобы создать в Spark использование индексов гиперпространства во время обработки запроса, необходимо убедиться, что параметр гиперпространства включен.

Следующая ячейка включает гиперпространства и создает две таблицы данных, содержащие записи выборки, которые используются для выполнения примеров запросов. Для каждого кадра данных печатается несколько образцов строк.

:::zone pivot = "programming-language-scala"

```scala
// Enable Hyperspace
spark.enableHyperspace

val empDFrame: DataFrame = spark.read.parquet(empLocation)
val deptDFrame: DataFrame = spark.read.parquet(deptLocation)

empDFrame.show(5)
deptDFrame.show(5)
```

::: zone-end

:::zone pivot = "programming-language-python"

```python

# Enable Hyperspace
Hyperspace.enable(spark)

emp_DF = spark.read.parquet(emp_Location)
dept_DF = spark.read.parquet(dept_Location)

emp_DF.show(5)
dept_DF.show(5)

```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp

// Enable Hyperspace
spark.EnableHyperspace();

DataFrame empDFrame = spark.Read().Parquet(empLocation);
DataFrame deptDFrame = spark.Read().Parquet(deptLocation);

empDFrame.Show(5);
deptDFrame.Show(5);

```

::: zone-end

Результат будет иметь такой вид:

```console
res53: org.apache.spark.sql.Spark™Session = org.apache.spark.sql.Spark™Session@39fe1ddb  
empDFrame: org.apache.spark.sql.DataFrame = [empId: int, empName: string ... 1 more field]  
deptDFrame: org.apache.spark.sql.DataFrame = [deptId: int, deptName: string ... 1 more field]  
```

&nbsp; &nbsp;

```console
|empId|empName|deptId|
|-----|-------|------|
| 7499|  ALLEN|    30|
| 7521|   WARD|    30|
| 7369|  SMITH|    20|
| 7844| TURNER|    30|
| 7876|  ADAMS|    20|
```

&nbsp;&nbsp;Отображаются только первые 5 строк &nbsp;&nbsp;

```console
|deptId|  deptName|location|
|------|----------|--------|
|    10|Accounting|New York|
|    40|Operations|  Boston|
|    20|  Research|  Dallas|
|    30|     Sales| Chicago|
```

## <a name="index-types"></a>Типы индексов

В настоящее время в гиперпространства есть правила использования индексов для двух групп запросов:

* Запросы выбора с предикатами подстановки или фильтрации выбора диапазона.
* Объединение запросов с помощью предиката равенства Join (т. е. являющиеся эквисоединениями).

## <a name="indexes-for-accelerating-filters"></a>Индексы для фильтров сочетаний клавиш

В первом примере запроса выполняется поиск записей в отделе, как показано в следующей ячейке. В SQL этот запрос выглядит, как в следующем примере:

```sql
SELECT deptName
FROM departments
WHERE deptId = 20
```

В результате выполнения следующей ячейки отображается следующее:

* Результат запроса — одно название отдела.
* План запроса, который Spark использовал для выполнения запроса.

В плане запроса оператор **филескан** в нижней части плана показывает источник данных, из которого считываются записи. Расположение этого файла указывает путь к последней версии индекса "deptIndex1". Эта информация показывает, что в соответствии с запросом и использованием правил оптимизации гиперпространства, Spark решил использовать правильный индекс во время выполнения.

:::zone pivot = "programming-language-scala"

```scala
// Filter with equality predicate

val eqFilter: DataFrame = deptDFrame.filter("deptId = 20").select("deptName")
eqFilter.show()

eqFilter.explain(true)
```

::: zone-end

:::zone pivot = "programming-language-python"

```python

# Filter with equality predicate

eqFilter = dept_DF.filter("""deptId = 20""").select("""deptName""")
eqFilter.show()

eqFilter.explain(True)

```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp

DataFrame eqFilter = deptDFrame.Filter("deptId = 20").Select("deptName");
eqFilter.Show();

eqFilter.Explain(true);

```

::: zone-end

Результат будет иметь такой вид:

```console
eqFilter: org.apache.spark.sql.DataFrame = [deptName: string]
```

```console
|DeptName|
|--------|
|Research|
```

&nbsp; &nbsp;

```console
== Parsed Logical Plan ==
'Project [unresolvedalias('deptName, None)]
+- Filter (deptId#533 = 20)
   +- Relation[deptId#533,deptName#534,location#535] parquet

== Analyzed Logical Plan ==
deptName: string
Project [deptName#534]
+- Filter (deptId#533 = 20)
   +- Relation[deptId#533,deptName#534,location#535] parquet

== Optimized Logical Plan ==
Project [deptName#534]
+- Filter (isnotnull(deptId#533) && (deptId#533 = 20))
   +- Relation[deptId#533,deptName#534,location#535] parquet

== Physical Plan ==
*(1) Project [deptName#534]
+- *(1) Filter (isnotnull(deptId#533) && (deptId#533 = 20))
   +- *(1) FileScan parquet [deptId#533,deptName#534] Batched: true, Format: Parquet, Location: InMemoryFileIndex[abfss://datasets@hyperspacebenchmark.dfs.core.windows.net/hyperspaceon..., PartitionFilters: [], PushedFilters: [IsNotNull(deptId), EqualTo(deptId,20)], ReadSchema: struct<deptId:int,deptName:string>
```

Вторым примером является запрос на выбор диапазона для записей отдела. В SQL этот запрос выглядит, как в следующем примере:

```sql
SELECT deptName
FROM departments
WHERE deptId > 20
```

Как и в первом примере, выходные данные в следующей ячейке показывают результаты запроса (имена двух отделов) и план запроса. Расположение файла данных в операторе **филескан** показывает, что для выполнения запроса был использован "deptIndex1".

:::zone pivot = "programming-language-scala"

```scala
// Filter with range selection predicate

val rangeFilter: DataFrame = deptDFrame.filter("deptId > 20").select("deptName")
rangeFilter.show()

rangeFilter.explain(true)
```

::: zone-end

:::zone pivot = "programming-language-python"

```python

# Filter with range selection predicate

rangeFilter = dept_DF.filter("""deptId > 20""").select("deptName")
rangeFilter.show()

rangeFilter.explain(True)

```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp

// Filter with range selection predicate
DataFrame rangeFilter = deptDFrame.Filter("deptId > 20").Select("deptName");
rangeFilter.Show();

rangeFilter.Explain(true);

```

::: zone-end

Результат будет иметь такой вид:

```console
rangeFilter: org.apache.spark.sql.DataFrame = [deptName: string]
```

```console
|  DeptName|
|----------|
|Operations|
|     Sales|
```

```console
== Parsed Logical Plan ==
'Project [unresolvedalias('deptName, None)]
+- Filter (deptId#533 > 20)
   +- Relation[deptId#533,deptName#534,location#535] parquet

== Analyzed Logical Plan ==
deptName: string
Project [deptName#534]
+- Filter (deptId#533 > 20)
   +- Relation[deptId#533,deptName#534,location#535] parquet

== Optimized Logical Plan ==
Project [deptName#534]
+- Filter (isnotnull(deptId#533) && (deptId#533 > 20))
   +- Relation[deptId#533,deptName#534,location#535] parquet

== Physical Plan ==
*(1) Project [deptName#534]
+- *(1) Filter (isnotnull(deptId#533) && (deptId#533 > 20))
   +- *(1) FileScan parquet [deptId#533,deptName#534] Batched: true, Format: Parquet, Location: InMemoryFileIndex[abfss://datasets@hyperspacebenchmark.dfs.core.windows.net/hyperspaceon..., PartitionFilters: [], PushedFilters: [IsNotNull(deptId), GreaterThan(deptId,20)], ReadSchema: struct<deptId:int,deptName:string>
```
Третий пример — запрос, объединяющий отделы и записи сотрудников по ИДЕНТИФИКАТОРу отдела. Эквивалентная инструкция SQL показана следующим образом:

```sql
SELECT employees.deptId, empName, departments.deptId, deptName
FROM   employees, departments
WHERE  employees.deptId = departments.deptId
```
В результате выполнения следующей ячейки отображаются результаты запроса, представляющие собой имена 14 сотрудников и название отдела, в котором работает каждый сотрудник. План запроса также включается в выходные данные. Обратите внимание на то, как расположение файлов для двух операторов **филескан** показывает, что в Spark использовались индексы "емпиндекс" и "deptIndex1" для выполнения запроса.

:::zone pivot = "programming-language-scala"

```scala
// Join

val eqJoin: DataFrame =
      empDFrame.
      join(deptDFrame, empDFrame("deptId") === deptDFrame("deptId")).
      select(empDFrame("empName"), deptDFrame("deptName"))

eqJoin.show()

eqJoin.explain(true)
```

::: zone-end

:::zone pivot = "programming-language-python"

```python

# Join

eqJoin = emp_DF.join(dept_DF, emp_DF.deptId == dept_DF.deptId).select(emp_DF.empName, dept_DF.deptName)

eqJoin.show()

eqJoin.explain(True)

```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp

// Join
DataFrame eqJoin =
      empDFrame
      .Join(deptDFrame, empDFrame.Col("deptId") == deptDFrame.Col("deptId"))
      .Select(empDFrame.Col("empName"), deptDFrame.Col("deptName"));

eqJoin.Show();

eqJoin.Explain(true);

```

::: zone-end

Результат будет иметь такой вид:

```console
eqJoin: org.apache.spark.sql.DataFrame = [empName: string, deptName: string]
```

```console
|empName|  deptName|
|-------|----------|
|  SMITH|  Research|
|  JONES|  Research|
|   FORD|  Research|
|  ADAMS|  Research|
|  SCOTT|  Research|
|   KING|Accounting|
|  CLARK|Accounting|
| MILLER|Accounting|
|  JAMES|     Sales|
|  BLAKE|     Sales|
| MARTIN|     Sales|
|  ALLEN|     Sales|
|   WARD|     Sales|
| TURNER|     Sales|
```

```console
== Parsed Logical Plan ==
Project [empName#528, deptName#534]
+- Join Inner, (deptId#529 = deptId#533)
   :- Relation[empId#527,empName#528,deptId#529] parquet
   +- Relation[deptId#533,deptName#534,location#535] parquet

== Analyzed Logical Plan ==
empName: string, deptName: string
Project [empName#528, deptName#534]
+- Join Inner, (deptId#529 = deptId#533)
   :- Relation[empId#527,empName#528,deptId#529] parquet
   +- Relation[deptId#533,deptName#534,location#535] parquet

== Optimized Logical Plan ==
Project [empName#528, deptName#534]
+- Join Inner, (deptId#529 = deptId#533)
   :- Project [empName#528, deptId#529]
   :  +- Filter isnotnull(deptId#529)
   :     +- Relation[empName#528,deptId#529] parquet
   +- Project [deptId#533, deptName#534]
      +- Filter isnotnull(deptId#533)
         +- Relation[deptId#533,deptName#534] parquet

== Physical Plan ==
*(3) Project [empName#528, deptName#534]
+- *(3) SortMergeJoin [deptId#529], [deptId#533], Inner
   :- *(1) Project [empName#528, deptId#529]
   :  +- *(1) Filter isnotnull(deptId#529)
   :     +- *(1) FileScan parquet [deptId#529,empName#528] Batched: true, Format: Parquet, Location: InMemoryFileIndex[abfss://datasets@hyperspacebenchmark.dfs.core.windows.net/hyperspaceon..., PartitionFilters: [], PushedFilters: [IsNotNull(deptId)], ReadSchema: struct<deptId:int,empName:string>, SelectedBucketsCount: 200 out of 200
   +- *(2) Project [deptId#533, deptName#534]
      +- *(2) Filter isnotnull(deptId#533)
         +- *(2) FileScan parquet [deptId#533,deptName#534] Batched: true, Format: Parquet, Location: InMemoryFileIndex[abfss://datasets@hyperspacebenchmark.dfs.core.windows.net/hyperspaceon..., PartitionFilters: [], PushedFilters: [IsNotNull(deptId)], ReadSchema: struct<deptId:int,deptName:string>, SelectedBucketsCount: 200 out of 200
```

## <a name="support-for-sql-semantics"></a>Поддержка семантики SQL

Использование индекса прозрачно для использования API кадров данных или Spark SQL. В следующем примере показан тот же пример объединения, что и раньше, в форме SQL, в котором показано использование индексов, если применимо.

:::zone pivot = "programming-language-scala"

```scala
empDFrame.createOrReplaceTempView("EMP")
deptDFrame.createOrReplaceTempView("DEPT")

val joinQuery = spark.sql("SELECT EMP.empName, DEPT.deptName FROM EMP, DEPT WHERE EMP.deptId = DEPT.deptId")

joinQuery.show()
joinQuery.explain(true)
```

::: zone-end

:::zone pivot = "programming-language-python"

```python

from pyspark.sql import SparkSession

emp_DF.createOrReplaceTempView("EMP")
dept_DF.createOrReplaceTempView("DEPT")

joinQuery = spark.sql("SELECT EMP.empName, DEPT.deptName FROM EMP, DEPT WHERE EMP.deptId = DEPT.deptId")

joinQuery.show()
joinQuery.explain(True)

```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp

empDFrame.CreateOrReplaceTempView("EMP");
deptDFrame.CreateOrReplaceTempView("DEPT");

var joinQuery = spark.Sql("SELECT EMP.empName, DEPT.deptName FROM EMP, DEPT WHERE EMP.deptId = DEPT.deptId");

joinQuery.Show();
joinQuery.Explain(true);

```

::: zone-end

Результат будет иметь такой вид:

```console

joinQuery: org.apache.spark.sql.DataFrame = [empName: string, deptName: string]
```

```console
|empName|  deptName|
|-------|----------|
|  SMITH|  Research|
|  JONES|  Research|
|   FORD|  Research|
|  ADAMS|  Research|
|  SCOTT|  Research|
|   KING|Accounting|
|  CLARK|Accounting|
| MILLER|Accounting|
|  JAMES|     Sales|
|  BLAKE|     Sales|
| MARTIN|     Sales|
|  ALLEN|     Sales|
|   WARD|     Sales|
| TURNER|     Sales|
```

```console
== Parsed Logical Plan ==
'Project ['EMP.empName, 'DEPT.deptName]
+- 'Filter ('EMP.deptId = 'DEPT.deptId)
   +- 'Join Inner
      :- 'UnresolvedRelation `EMP`
      +- 'UnresolvedRelation `DEPT`

== Analyzed Logical Plan ==
empName: string, deptName: string
Project [empName#528, deptName#534]
+- Filter (deptId#529 = deptId#533)
   +- Join Inner
      :- SubqueryAlias `emp`
      :  +- Relation[empId#527,empName#528,deptId#529] parquet
      +- SubqueryAlias `dept`
         +- Relation[deptId#533,deptName#534,location#535] parquet

== Optimized Logical Plan ==
Project [empName#528, deptName#534]
+- Join Inner, (deptId#529 = deptId#533)
   :- Project [empName#528, deptId#529]
   :  +- Filter isnotnull(deptId#529)
   :     +- Relation[empId#527,empName#528,deptId#529] parquet
   +- Project [deptId#533, deptName#534]
      +- Filter isnotnull(deptId#533)
         +- Relation[deptId#533,deptName#534,location#535] parquet

== Physical Plan ==
*(5) Project [empName#528, deptName#534]
+- *(5) SortMergeJoin [deptId#529], [deptId#533], Inner
   :- *(2) Sort [deptId#529 ASC NULLS FIRST], false, 0
   :  +- Exchange hashpartitioning(deptId#529, 200)
   :     +- *(1) Project [empName#528, deptId#529]
   :        +- *(1) Filter isnotnull(deptId#529)
   :           +- *(1) FileScan parquet [deptId#529,empName#528] Batched: true, Format: Parquet, Location: InMemoryFileIndex[abfss://datasets@hyperspacebenchmark.dfs.core.windows.net/hyperspaceon..., PartitionFilters: [], PushedFilters: [IsNotNull(deptId)], ReadSchema: struct<deptId:int,empName:string>
   +- *(4) Sort [deptId#533 ASC NULLS FIRST], false, 0
      +- Exchange hashpartitioning(deptId#533, 200)
         +- *(3) Project [deptId#533, deptName#534]
            +- *(3) Filter isnotnull(deptId#533)
               +- *(3) FileScan parquet [deptId#533,deptName#534] Batched: true, Format: Parquet, Location: InMemoryFileIndex[abfss://datasets@hyperspacebenchmark.dfs.core.windows.net/your-path/departments.parquet], PartitionFilters: [], PushedFilters: [IsNotNull(deptId)], ReadSchema: struct<deptId:int,deptName:string>
```

## <a name="explain-api"></a>Объяснение API

Индексы отлично, но как узнать, используются ли они? Функция гиперпространства позволяет пользователям сравнить исходный план с обновленным планом, зависящим от индекса, перед выполнением запроса. Для вывода выходных данных команды можно выбрать формат HTML, обычный текст или режим консоли.

В следующей ячейке приведен пример с HTML. Выделенный раздел представляет разницу между исходным и обновленным планом вместе с используемыми индексами.

:::zone pivot = "programming-language-scala"

```scala
spark.conf.set("spark.hyperspace.explain.displayMode", "html")
hyperspace.explain(eqJoin) { displayHTML }
```

::: zone-end

:::zone pivot = "programming-language-python"

```python

eqJoin = emp_DF.join(dept_DF, emp_DF.deptId == dept_DF.deptId).select(emp_DF.empName, dept_DF.deptName)

spark.conf.set("spark.hyperspace.explain.displayMode", "html")
hyperspace.explain(eqJoin, True, displayHTML)

```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp

spark.Conf().Set("spark.hyperspace.explain.displayMode", "html");
spark.Conf().Set("spark.hyperspace.explain.displayMode.highlight.beginTag", "<b style=\"background:LightGreen\">");
spark.Conf().Set("spark.hyperspace.explain.displayMode.highlight.endTag", "</b>");

hyperspace.Explain(eqJoin, false, input => DisplayHTML(input));

```

::: zone-end

Результат будет иметь такой вид:

### <a name="plan-with-indexes"></a>Планирование с помощью индексов

```console
Project [empName#528, deptName#534]
+- SortMergeJoin [deptId#529], [deptId#533], Inner
   :- *(1) Project [empName#528, deptId#529]
   :  +- *(1) Filter isnotnull(deptId#529)
   :     +- *(1) FileScan parquet [deptId#529,empName#528] Batched: true, Format: Parquet, Location: InMemoryFileIndex[abfss://datasets@hyperspacebenchmark.dfs.core.windows.net/hyperspaceon..., PartitionFilters: [], PushedFilters: [IsNotNull(deptId)], ReadSchema: struct
   +- *(2) Project [deptId#533, deptName#534]
      +- *(2) Filter isnotnull(deptId#533)
         +- *(2) FileScan parquet [deptId#533,deptName#534] Batched: true, Format: Parquet, Location: InMemoryFileIndex[abfss://datasets@hyperspacebenchmark.dfs.core.windows.net/hyperspaceon..., PartitionFilters: [], PushedFilters: [IsNotNull(deptId)], ReadSchema: struct
```

### <a name="plan-without-indexes"></a>Планирование без индексов

```console
Project [empName#528, deptName#534]
+- SortMergeJoin [deptId#529], [deptId#533], Inner
   :- *(2) Sort [deptId#529 ASC NULLS FIRST], false, 0
   :  +- Exchange hashpartitioning(deptId#529, 200)
   :     +- *(1) Project [empName#528, deptId#529]
   :        +- *(1) Filter isnotnull(deptId#529)
   :           +- *(1) FileScan parquet [empName#528,deptId#529] Batched: true, Format: Parquet, Location: InMemoryFileIndex[abfss://datasets@hyperspacebenchmark.dfs.core.windows.net/your-path/employees.parquet], PartitionFilters: [], PushedFilters: [IsNotNull(deptId)], ReadSchema: struct
   +- *(4) Sort [deptId#533 ASC NULLS FIRST], false, 0
      +- Exchange hashpartitioning(deptId#533, 200)
         +- *(3) Project [deptId#533, deptName#534]
            +- *(3) Filter isnotnull(deptId#533)
               +- *(3) FileScan parquet [deptId#533,deptName#534] Batched: true, Format: Parquet, Location: InMemoryFileIndex[abfss://datasets@hyperspacebenchmark.dfs.core.windows.net/your-path/departments.parquet], PartitionFilters: [], PushedFilters: [IsNotNull(deptId)], ReadSchema: struct
```

### <a name="indexes-used"></a>Использованные индексы

```console
deptIndex1:abfss://datasets@hyperspacebenchmark.dfs.core.windows.net/<container>/indexes/public/deptIndex1/v__=0
empIndex:abfss://datasets@hyperspacebenchmark.dfs.core.windows.net/<container>/indexes/public/empIndex/v__=0
```

## <a name="refresh-indexes"></a>Обновить индексы

Если исходные данные, на которых был создан индекс, изменились, индекс больше не будет захватывать Последнее состояние данных. Вы можете обновить устаревший индекс с помощью команды **рефрешиндекс** . Эта команда приводит к полному перестроению индекса и его обновлению в соответствии с последними записями данных. Мы покажем, как постепенно обновить индекс в других записных книжках.

В следующих двух ячейках показан пример для этого сценария:

* Первая ячейка добавляет еще два подразделения в исходные данные о подразделениях. Он считывает и печатает список отделов для проверки правильности добавления новых подразделений. В выходных данных в итоге отображается шесть подразделений: четыре старых и два новых. Вызов **рефрешиндекс** Updates "deptIndex1", чтобы индекс захватывает новые отделы.
* Вторая ячейка выполняет пример запроса выбора диапазона. Теперь в результатах должны содержаться четыре подразделения: два — это те, которые встречаются до выполнения предыдущего запроса, а два — это новые добавленные отделы.

### <a name="specific-index-refresh"></a>Обновление конкретного индекса

:::zone pivot = "programming-language-scala"

```scala
val extraDepartments = Seq(
      (50, "Inovation", "Seattle"),
      (60, "Human Resources", "San Francisco"))

val extraDeptData: DataFrame = extraDepartments.toDF("deptId", "deptName", "location")
extraDeptData.write.mode("Append").parquet(deptLocation)

val deptDFrameUpdated: DataFrame = spark.read.parquet(deptLocation)

deptDFrameUpdated.show(10)

hyperspace.refreshIndex("deptIndex1")
```

::: zone-end

:::zone pivot = "programming-language-python"

```python

extra_Departments = [(50, "Inovation", "Seattle"), (60, "Human Resources", "San Francisco")]

extra_departments_df = spark.createDataFrame(extra_Departments, dept_schema)
extra_departments_df.write.mode("Append").parquet(dept_Location)


dept_DFrame_Updated = spark.read.parquet(dept_Location)

dept_DFrame_Updated.show(10)

```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp

var extraDepartments = new List<GenericRow>()
{
    new GenericRow(new object[] {50, "Inovation", "Seattle"}),
    new GenericRow(new object[] {60, "Human Resources", "San Francisco"})
};
    
DataFrame extraDeptData = spark.CreateDataFrame(extraDepartments, departmentSchema);
extraDeptData.Write().Mode("Append").Parquet(deptLocation);

DataFrame deptDFrameUpdated = spark.Read().Parquet(deptLocation);

deptDFrameUpdated.Show(10);

hyperspace.RefreshIndex("deptIndex1");

```

::: zone-end

Результат будет иметь такой вид:

```console
extraDepartments: Seq[(Int, String, String)] = List((50,Inovation,Seattle), (60,Human Resources,San Francisco))  
extraDeptData: org.apache.spark.sql.DataFrame = [deptId: int, deptName: string ... 1 more field]  
deptDFrameUpdated: org.apache.spark.sql.DataFrame = [deptId: int, deptName: string ... 1 more field]
```

&nbsp; &nbsp;

```console  
|deptId|       deptName|     location|
|------|---------------|-------------|
|    60|Human Resources|San Francisco|
|    10|     Accounting|     New York|
|    50|      Inovation|      Seattle|
|    40|     Operations|       Boston|
|    20|       Research|       Dallas|
|    30|          Sales|      Chicago|
```

### <a name="range-selection"></a>Выбор диапазона

:::zone pivot = "programming-language-scala"

```scala
val newRangeFilter: DataFrame = deptDFrameUpdated.filter("deptId > 20").select("deptName")
newRangeFilter.show()

newRangeFilter.explain(true)
```

::: zone-end

:::zone pivot = "programming-language-python"

```python

newRangeFilter = dept_DFrame_Updated.filter("deptId > 20").select("deptName")
newRangeFilter.show()

newRangeFilter.explain(True)

```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp

DataFrame newRangeFilter = deptDFrameUpdated.Filter("deptId > 20").Select("deptName");
newRangeFilter.Show();

newRangeFilter.Explain(true);

```

::: zone-end

Результат будет иметь такой вид:

```console
newRangeFilter: org.apache.spark.sql.DataFrame = [deptName: string]
```

```console
|       DeptName|
|---------------|
|Human Resources|
|      Inovation|
|     Operations|
|          Sales|
```

```console
== Parsed Logical Plan ==
'Project [unresolvedalias('deptName, None)]
+- Filter (deptId#674 > 20)
   +- Relation[deptId#674,deptName#675,location#676] parquet

== Analyzed Logical Plan ==
deptName: string
Project [deptName#675]
+- Filter (deptId#674 > 20)
   +- Relation[deptId#674,deptName#675,location#676] parquet

== Optimized Logical Plan ==
Project [deptName#675]
+- Filter (isnotnull(deptId#674) && (deptId#674 > 20))
   +- Relation[deptId#674,deptName#675,location#676] parquet

== Physical Plan ==
*(1) Project [deptName#675]
+- *(1) Filter (isnotnull(deptId#674) && (deptId#674 > 20))
   +- *(1) FileScan parquet [deptId#674,deptName#675] Batched: true, Format: Parquet, Location: InMemoryFileIndex[abfss://datasets@hyperspacebenchmark.dfs.core.windows.net/hyperspaceon..., PartitionFilters: [], PushedFilters: [IsNotNull(deptId), GreaterThan(deptId,20)], ReadSchema: struct<deptId:int,deptName:string>
```

## <a name="next-steps"></a>Дальнейшие действия

* [Проект гиперпространства](https://microsoft.github.io/hyperspace/)
* [Azure Synapse Analytics](https://docs.microsoft.com/azure/synapse-analytics)
