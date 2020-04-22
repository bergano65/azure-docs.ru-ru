---
title: Проанализируйте & процесс JSON с Apache Hive - Azure HDInsight
description: Узнайте, как использовать документы JSON и проанализировать их с помощью Apache Hive в Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 04/20/2020
ms.openlocfilehash: 8e0abf780589207b065b7262afb99de81e625fe8
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/21/2020
ms.locfileid: "81732212"
---
# <a name="process-and-analyze-json-documents-by-using-apache-hive-in-azure-hdinsight"></a>Обработка и анализ документов JSON с использованием Apache Hive в Azure HDInsight

Узнайте, как обрабатывать и анализировать файлы JavaScript Object Notation (JSON) с помощью Apache Hive в Azure HDInsight. В этой статье используется следующий документ JSON:

```json
{
  "StudentId": "trgfg-5454-fdfdg-4346",
  "Grade": 7,
  "StudentDetails": [
    {
      "FirstName": "Peggy",
      "LastName": "Williams",
      "YearJoined": 2012
    }
  ],
  "StudentClassCollection": [
    {
      "ClassId": "89084343",
      "ClassParticipation": "Satisfied",
      "ClassParticipationRank": "High",
      "Score": 93,
      "PerformedActivity": false
    },
    {
      "ClassId": "78547522",
      "ClassParticipation": "NotSatisfied",
      "ClassParticipationRank": "None",
      "Score": 74,
      "PerformedActivity": false
    },
    {
      "ClassId": "78675563",
      "ClassParticipation": "Satisfied",
      "ClassParticipationRank": "Low",
      "Score": 83,
      "PerformedActivity": true
    }
  ]
}
```

Файл можно найти в `wasb://processjson@hditutorialdata.blob.core.windows.net/`. Дополнительные сведения об использовании хранилища BLOB-объектов Azure с HDInsight приведены в разделе [Использование службы хранилища Azure с кластерами Azure HDInsight](../hdinsight-hadoop-use-blob-storage.md). Вы можете скопировать файл в контейнер по умолчанию кластера.

В этой статье используется консоль Apache Hive. Для получения инструкций о том, как открыть консоль Hive, [см.](apache-hadoop-use-hive-ambari-view.md)

> [!NOTE]  
> В HDInsight 4.0 больше не используется представление Hive.

## <a name="flatten-json-documents"></a>Документы JSON, преобразованные в плоскую структуру

Методы, перечисленные в следующем разделе, требуют, чтобы документ JSON состоял из одной строки. Поэтому необходимо преобразовать документ JSON в строку. Если документ JSON уже преобразован, можно пропустить этот шаг и сразу перейти к следующему разделу, посвященному анализу данных JSON. Чтобы преобразовать документ JSON в плоскую структуру, выполните следующий сценарий:

```sql
DROP TABLE IF EXISTS StudentsRaw;
CREATE EXTERNAL TABLE StudentsRaw (textcol string) STORED AS TEXTFILE LOCATION "wasb://processjson@hditutorialdata.blob.core.windows.net/";

DROP TABLE IF EXISTS StudentsOneLine;
CREATE EXTERNAL TABLE StudentsOneLine
(
  json_body string
)
STORED AS TEXTFILE LOCATION '/json/students';

INSERT OVERWRITE TABLE StudentsOneLine
SELECT CONCAT_WS(' ',COLLECT_LIST(textcol)) AS singlelineJSON
      FROM (SELECT INPUT__FILE__NAME,BLOCK__OFFSET__INSIDE__FILE, textcol FROM StudentsRaw DISTRIBUTE BY INPUT__FILE__NAME SORT BY BLOCK__OFFSET__INSIDE__FILE) x
      GROUP BY INPUT__FILE__NAME;

SELECT * FROM StudentsOneLine
```

Необработанный файл JSON находится в `wasb://processjson@hditutorialdata.blob.core.windows.net/`. Таблица **StudentsRaw** Hive указывает на необработанный документ JSON, который не сплющен.

Таблица Hive **StudentsOneLine** сохраняет данные в файловой системе по умолчанию HDInsight в каталоге **/json/students/**.

Инструкция **INSERT** заполняет таблицу **StudentOneLine** плоскими данными JSON.

Инструкция **SELECT** возвращает всего одну строку.

Вот результат выполнения инструкции **SELECT**:

![HDInsight уплощение документа JSON](./media/using-json-in-hive/hdinsight-flatten-json.png)

## <a name="analyze-json-documents-in-hive"></a>Анализ документов JSON в Hive

Hive предоставляет три различных механизма для выполнения запросов к документам JSON (вы также можете написать собственный):

* использование определяемой пользователем функции GET_JSON_OBJECT;
* использование определяемой пользователем функции JSON_TUPLE;
* использование пользовательского сериализатора/десериализатора (SerDe);
* написание собственной определяемой пользователем функции с использованием Python или других языков. Дополнительные сведения о том, как выполнять код Python с Hive, см. в статье [Использование пользовательских функций Python с Apache Hive и Apache Pig в Azure HDInsight](./python-udf-hdinsight.md).

### <a name="use-the-get_json_object-udf"></a>Использование определяемой пользователем функции GET_JSON_OBJECT

Hive предоставляет встроенный UDF под названием [get_json_object,](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+UDF#LanguageManualUDF-get_json_object) который запрашивает JSON во время выполнения. Этот метод использует два аргумента: имя таблицы и имя метода. Название метода имеет сплющенный документ JSON и поле JSON, которое необходимо разобрать. Давайте рассмотрим пример, чтобы увидеть, как работает этот UDF.

Следующий запрос возвращает имя и фамилию каждого студента:

```sql
SELECT
  GET_JSON_OBJECT(StudentsOneLine.json_body,'$.StudentDetails.FirstName'),
  GET_JSON_OBJECT(StudentsOneLine.json_body,'$.StudentDetails.LastName')
FROM StudentsOneLine;
```

Вот какие результаты дает выполнение этого запроса в окне консоли:

![Apache Hive получает объект Json UDF](./media/using-json-in-hive/hdinsight-get-json-object.png)

У определяемой пользователем функции GET_JSON_OBJECT есть некоторые ограничения.

* Так как каждое поле в запросе требует повторного синтаксического анализа запроса, это влияет на производительность.
* **GET\_JSON_OBJECT()** возвращает строковое представление массива. Для преобразования этого массива в массив Hive необходимо использовать регулярные выражения для замены квадратных скобок "[" и "]", а затем также выполнить разбиение для получения массива.

Это преобразование, почему Hive вики рекомендует вам использовать **json_tuple**.  

### <a name="use-the-json_tuple-udf"></a>Использование определяемой пользователем функции JSON_TUPLE

Другой UDF, предоставляемый Hive называется [json_tuple,](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+UDF#LanguageManualUDF-json_tuple)который делает лучше, чем [get_ json _object.](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+UDF#LanguageManualUDF-get_json_object) Этот метод принимает набор ключей и строку JSON. Затем возвращается tuple значений. Следующий запрос возвращает идентификатор студента и его оценку из документа JSON.

```sql
SELECT q1.StudentId, q1.Grade
FROM StudentsOneLine jt
LATERAL VIEW JSON_TUPLE(jt.json_body, 'StudentId', 'Grade') q1
  AS StudentId, Grade;
```

Выходные данные этого сценария в консоли Hive:

![Результаты запроса Apache Hive json](./media/using-json-in-hive/hdinsight-json-tuple.png)

UDF использует боковой синтаксис [представления](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+LateralView) в Hive, который позволяет json\_tuple создать виртуальную таблицу, применяя функцию UDT к каждой строке исходной таблицы. `json_tuple` Из-за многократного использования **LATERAL VIEW** синтаксис сложных документов JSON становится слишком громоздким. Кроме того, **JSON_TUPLE** не могут обрабатывать вложенные JSONs.

### <a name="use-a-custom-serde"></a>Использование пользовательского SerDe

SerDe отлично подходит для синтаксического анализа вложенных документов JSON. Он позволяет определить схему JSON, а затем использовать ее для синтаксического анализа документов. См. инструкции по [использованию пользовательского формата SerDe JSON с Microsoft Azure HDInsight](https://web.archive.org/web/20190217104719/https://blogs.msdn.microsoft.com/bigdatasupport/2014/06/18/how-to-use-a-custom-json-serde-with-microsoft-azure-hdinsight/).

## <a name="summary"></a>Сводка

Тип оператора JSON в Hive, который вы выбираете, зависит от вашего сценария. С помощью простого документа JSON и одного поля, чтобы посмотреть вверх, выберите Hive UDF **get_json_object.** Если у вас есть более чем один ключ, чтобы посмотреть на, то вы можете использовать **json_tuple**. Для вложенных документов используйте **JSON SerDe**.

## <a name="next-steps"></a>Следующие шаги

Другие статьи по этой теме см. в следующих источниках:

* [Использование Apache Hive и HiveQL с Apache Hadoop в HDInsight для анализа примера файла log4j Apache](../hdinsight-use-hive.md)
* [Анализ данных о задержке рейса с помощью интерактивного запроса в HDInsight](../interactive-query/interactive-query-tutorial-analyze-flight-data.md)
