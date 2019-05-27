---
title: Apache Hive со средствами Data Lake (Apache Hadoop) для Visual Studio — Azure HDInsight
description: Узнайте, как использовать Средства Data Lake для Visual Studio с целью выполнения запросов Apache Hive с Apache Hadoop в Azure HDInsight.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 05/14/2019
ms.author: hrasheed
ms.openlocfilehash: 7480dafe435e555bfba81ebd9242bb5724c0bf3f
ms.sourcegitcommit: 4c2b9bc9cc704652cc77f33a870c4ec2d0579451
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/17/2019
ms.locfileid: "65861600"
---
# <a name="run-apache-hive-queries-using-the-data-lake-tools-for-visual-studio"></a>Выполнение запросов Apache Hive с использованием средств Data Lake для Visual Studio

Узнайте, как использовать Средства Data Lake для Visual Studio с целью выполнения запросов Apache Hive. Средства Data Lake позволяют легко создавать, отправлять и отслеживать запросы Hive к Apache Hadoop в Azure HDInsight.

## <a id="prereq"></a>Предварительные требования

* Кластер Apache Hadoop в HDInsight. См. в разделе [начало работы с HDInsight на платформе Linux](./apache-hadoop-linux-tutorial-get-started.md).

* Одна из следующих версий Visual Studio:

    * Visual Studio 2015, 2017 (любой выпуск)
    * Visual Studio 2013 Community, Professional, Premium или Ultimate с обновлением 4

* Средства HDInsight для Visual Studio или инструменты Azure Data Lake Tools для Visual Studio. Пошаговые указания по установке и настройке инструментов Visual Studio Hadoop см. в статье [Приступая к работе с инструментами Azure Data Lake (в HDInsight) для Visual Studio для выполнения запроса Hive](apache-hadoop-visual-studio-tools-get-started.md).

## <a id="run"></a> Выполнение запросов Apache Hive с помощью Visual Studio

Есть два способа создания и выполнения запросов Hive.

* Создание специальных запросов
* Создание приложения Hive

### <a name="ad-hoc"></a>Ad hoc

Нерегламентированные запросы могут выполняться в любом **пакета** или **интерактивные** режим.

1. Откройте **Visual Studio**.

2. Из **обозревателя серверов**, перейдите к **Azure** > **HDInsight**.

3. Разверните **HDInsight**и щелкните правой кнопкой мыши кластер, где вы хотите выполнить запрос, а затем выберите **написать запрос Hive**.

4. Введите следующий запрос hive:

    ```hql
    SELECT * FROM hivesampletable;
    ```

5. Нажмите кнопку **Выполнить**. Обратите внимание, что режим выполнения по умолчанию становится **интерактивные**.

    ![Снимок экрана "Выполнения интерактивных запросов Hive"](./media/apache-hadoop-use-hive-visual-studio/vs-execute-hive-query.png)

6. Для выполнения того же запроса в **пакета** режим, переключателя в раскрывающемся списке из **интерактивные** для **пакета**. Обратите внимание, что кнопка выполнения меняется от **Execute** для **отправить**.

    ![Снимок экрана "Отправка запроса Hive"](./media/apache-hadoop-use-hive-visual-studio/vs-batch-query.png)

    Редактор Hive поддерживает технологию IntelliSense. Средства Data Lake для Visual Studio поддерживают загрузку удаленных метаданных при редактировании скрипта Hive. Например, если ввести `SELECT * FROM`, IntelliSense отобразит все предлагаемые имена таблиц. Если указано имя таблицы, IntelliSense выведет список имен столбцов. Эти инструменты поддерживают почти все инструкции, подзапросы и встроенные определяемые пользователем функции Hive DML. IntelliSense предлагает только метаданные кластеров, выбранных на панели инструментов HDInsight.

    ![Снимок экрана примера 1 IntelliSense в средствах HDInsight для Visual Studio](./media/apache-hadoop-use-hive-visual-studio/vs-intellisense-table-name.png "U-SQL IntelliSense")
   
    ![Снимок экрана примера 2 IntelliSense в средствах HDInsight для Visual Studio](./media/apache-hadoop-use-hive-visual-studio/vs-intellisense-column-name.png "U-SQL IntelliSense")

7. Выберите **Отправить** или выполните **расширенную отправку**.

   С помощью расширенной отправки можно настроить **имя задания**, **аргументы**, **дополнительные конфигурации** и **каталог состояния** скрипта.

    ![Снимок экрана запроса Hive в HDInsight Hadoop](./media/apache-hadoop-use-hive-visual-studio/hdinsight.visual.studio.tools.submit.jobs.advanced.png "Отправка запросов")

### <a name="hive-application"></a>Приложения Hive

1. Откройте **Visual Studio**.

2. В строке меню перейдите к **файл** > **New** > **проекта**.

3. Из **новый проект** окно, перейдите к **шаблоны** > **Azure Data Lake** > **HIVE (HDInsight)**  >  **Hive приложения**. 

4. Укажите имя для этого проекта, а затем выберите **ОК**.

5. Откройте файл **Script.hql** , созданный в этом проекте, и вставьте следующие операторы HiveQL:

    ```hiveql
    set hive.execution.engine=tez;
    DROP TABLE log4jLogs;
    CREATE EXTERNAL TABLE log4jLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string)
    ROW FORMAT DELIMITED FIELDS TERMINATED BY ' '
    STORED AS TEXTFILE LOCATION '/example/data/';
    SELECT t4 AS sev, COUNT(*) AS count FROM log4jLogs WHERE t4 = '[ERROR]' AND  INPUT__FILE__NAME LIKE '%.log' GROUP BY t4;
    ```

    Эти операторы выполняют следующие действия:

   * `DROP TABLE`: если таблица существует, эта инструкция удаляет ее.

   * `CREATE EXTERNAL TABLE`: создает "внешнюю" таблицу в Hive. Внешние таблицы хранят только определение самой таблицы в Hive, в то время как данные остаются в исходном расположении.

     > [!NOTE]  
     > Внешние таблицы следует использовать, если исходные данные должны обновляться с использованием внешних источников. Например, с помощью задания MapReduce или службы Azure.
     >
     > Удаление внешней таблицы **не** приводит к удалению данных, будет удалено только определение таблицы.

   * `ROW FORMAT`: инструкции по форматированию данных для Hive. В данном случае поля всех журналов разделены пробелом.

   * `STORED AS TEXTFILE LOCATION`: указывает Hive, что данные хранятся в каталоге example/data и их формат — "Текст".

   * `SELECT`: подсчитывает количество строк, в которых столбец `t4` содержит значение `[ERROR]`. Эта инструкция должна вернуть значение `3`, так как данное значение содержат три строки.

   * `INPUT__FILE__NAME LIKE '%.log'`: указывает Hive, что вернуть нужно только данные из файлов с расширением LOG. Это предложение ограничивает поиск до файла sample.log, который содержит данные.

6. На панели инструментов выберите **Кластер HDInsight**, который вы хотите использовать для этого запроса. Щелкните **Отправить**, чтобы выполнить инструкции как задание Hive.

   ![Панель отправки](./media/apache-hadoop-use-hive-visual-studio/toolbar.png)

7. Отобразится **сводка по заданию Hive** и информация о его выполнении. Воспользуйтесь ссылкой **Обновить**, чтобы обновить информацию о задании. Обновляйте ее до тех пор, пока **состояние задания** не изменится на **Завершено**.

   ![Сводка по завершенному заданию](./media/apache-hadoop-use-hive-visual-studio/jobsummary.png)

8. Воспользуйтесь ссылкой **Выходные данные задания** , чтобы просмотреть выходные данные этого задания. Они содержат `[ERROR] 3`. Это значение, возвращенное данным запросом.

### <a name="additional-example"></a>Дополнительный пример

Этот пример основан на `log4jLogs` таблицу, созданную на предыдущем шаге.

1. Из **обозревателя серверов**, щелкните правой кнопкой мыши кластер и выберите **написать запрос Hive**.

2. Введите следующий запрос hive:

    ```hql
    set hive.execution.engine=tez;
    CREATE TABLE IF NOT EXISTS errorLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string) STORED AS ORC;
    INSERT OVERWRITE TABLE errorLogs SELECT t1, t2, t3, t4, t5, t6, t7 FROM log4jLogs WHERE t4 = '[ERROR]' AND INPUT__FILE__NAME LIKE '%.log';
    ```

    Эти операторы выполняют следующие действия:

    * `CREATE TABLE IF NOT EXISTS`: создает таблицу, если ее нет. Так как не используется ключевое слово `EXTERNAL`, эта инструкция создает внутреннюю таблицу. Внутренние таблицы хранятся в хранилище данных Hive и управляются Hive.
    
    > [!NOTE]  
    > В отличие от таблиц `EXTERNAL`, удаление внутренней таблицы приводит к удалению ее базовых данных.

    * `STORED AS ORC`: позволяет сохранить данные в формате ORC. Это высокооптимизированный и эффективный формат для хранения данных Hive.
    
    * `INSERT OVERWRITE ... SELECT`: выбирает строки из таблицы `log4jLogs`, которые содержат `[ERROR]`, а затем вставляет эти данные в таблицу `errorLogs`.

3. Выполнение запроса в среде **пакета** режим.

4. Убедитесь в том, что задание создало таблицу. Для этого в **обозревателе сервера** разверните **Azure** > **HDInsight** > ваш кластер HDInsight > **Базы данных Hive** > **По умолчанию**. Должны быть отображены таблицы **errorLogs** и **log4jLogs**.

## <a id="nextsteps"></a>Дальнейшие действия

Как вы видите, средства HDInsight для Visual Studio упрощают работу с запросами Hive в HDInsight.

Общая информация о Hive в HDInsight:

* [Использование Apache Hive с Apache Hadoop в HDInsight](hdinsight-use-hive.md)

Дополнительная информация о других способах работы с Hadoop в HDInsight:

* [Использование Apache Pig с Apache Hadoop в HDInsight](hdinsight-use-pig.md)

* [Использование MapReduce в Apache Hadoop в HDInsight](hdinsight-use-mapreduce.md)

Дополнительная информация об инструментах HDInsight для Visual Studio:

* [Приступая к работе со средствами HDInsight для Visual Studio](apache-hadoop-visual-studio-tools-get-started.md)