---
title: Apache Hive & инструменты Data Lake для визуальной студии - Azure HDInsight
description: Узнайте, как использовать Средства Data Lake для Visual Studio с целью выполнения запросов Apache Hive с Apache Hadoop в Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 11/27/2019
ms.openlocfilehash: 27ab13481525819eb1435f4c9ac256a21acd21fb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74687802"
---
# <a name="run-apache-hive-queries-using-the-data-lake-tools-for-visual-studio"></a>Выполнение запросов Apache Hive с использованием средств Data Lake для Visual Studio

Узнайте, как использовать Средства Data Lake для Visual Studio с целью выполнения запросов Apache Hive. Средства Data Lake позволяют легко создавать, отправлять и отслеживать запросы Hive к Apache Hadoop в Azure HDInsight.

## <a name="prerequisites"></a>Предварительные требования

* Кластер Apache Hadoop в HDInsight. Для получения информации о создании этого элемента см. [Создать кластер Apache Hadoop в Azure HDInsight с помощью шаблона Resource Manager.](./apache-hadoop-linux-tutorial-get-started.md)

* [Визуальная студия](https://visualstudio.microsoft.com/vs/). Шаги в этой статье используют Visual Studio 2019.

* Средства HDInsight для Visual Studio или инструменты Azure Data Lake Tools для Visual Studio. Для получения информации об установке и настройке инструментов, [см.](apache-hadoop-visual-studio-tools-get-started.md#install-data-lake-tools-for-visual-studio)

## <a name="run-apache-hive-queries-using-the-visual-studio"></a> Выполнение запросов Apache Hive с помощью Visual Studio

Есть два способа создания и выполнения запросов Hive.

* Создание специальных запросов.
* Создайте приложение Hive.

### <a name="create-an-ad-hoc-hive-query"></a>Создание специального запроса Hive

Специальные запросы могут выполняться в **режиме пакета** или **Interactive.**

1. Запустите **Visual Studio** и выберите **Продолжить без кода**.

2. От **Server Explorer**, справа щелкните **Azure,** выберите **Подключитесь к подписке Microsoft Azure...**, и завершите регистрацию в процессе.

3. Расширьте **HDInsight,** нажмите правой кнопкой мыши кластера, где вы хотите запустить запрос, а затем выберите **Написать запрос Hive.**

4. Введите следующий запрос улья:

    ```hql
    SELECT * FROM hivesampletable;
    ```

5. Нажмите кнопку **Выполнить**. Режим выполнения по умолчанию для **Интерактивного**.

    ![Выполните интерактивный запрос Hive, Visual Studio](./media/apache-hadoop-use-hive-visual-studio/vs-execute-hive-query.png)

6. Чтобы запустить тот же запрос в режиме **пакета,** переключите список выпадающих из **интерактивного** в **пакет.** Кнопка выполнения изменяется от **выполнения** к **отправке**.

    ![Отправить пакет ный запрос Hive, Visual Studio](./media/apache-hadoop-use-hive-visual-studio/visual-studio-batch-query.png)

    Редактор Hive поддерживает технологию IntelliSense. Средства Data Lake для Visual Studio поддерживают загрузку удаленных метаданных при редактировании скрипта Hive. Например, при `SELECT * FROM`вводе IntelliSense перечислены все предложенные названия таблиц. Если указано имя таблицы, IntelliSense выведет список имен столбцов. Эти инструменты поддерживают почти все инструкции, подзапросы и встроенные определяемые пользователем функции Hive DML. IntelliSense предлагает только метаданные кластеров, выбранных на панели инструментов HDInsight.

7. В панели инструментов запроса (область ниже вкладки запроса и выше текста запроса) либо выберите **Отправить,** или выберите стрелку вытягивания рядом, чтобы **отправить** и выбрать **Расширенный** из списка выдвижной. Если вы выберете последний вариант,

8. Если вы выбрали расширенный вариант отправки, назначьте **имя вакансии,** **Аргументы,** **Дополнительные конфигурации**и **каталог состояния** в диалоговом поле **отправки сценария.** Затем нажмите кнопку **Отправить**.

    ![Отправить диалоговый ящик сценария, запрос HDInsight Hadoop Hive](./media/apache-hadoop-use-hive-visual-studio/vs-tools-submit-jobs-advanced.png)

### <a name="create-a-hive-application"></a>Создание приложения Hive

Чтобы запустить запрос Hive, создав приложение Hive, выполните следующие действия:

1. Открытая **визуальная студия**.

2. В окне **Начало работы** выберите **Создать проект**.

3. В **окне создания нового проекта,** в поле **поиска шаблонов,** введите *Hive*. Затем выберите **приложение Hive** и выберите **Следующую.**

4. В **новом** окне проекта введите **имя проекта,** выберите или создайте **место** для нового проекта, а затем выберите **Создать.**

5. Откройте файл **Script.hql** , созданный в этом проекте, и вставьте следующие операторы HiveQL:

    ```hql
    set hive.execution.engine=tez;
    DROP TABLE log4jLogs;
    CREATE EXTERNAL TABLE log4jLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string)
    ROW FORMAT DELIMITED FIELDS TERMINATED BY ' '
    STORED AS TEXTFILE LOCATION '/example/data/';
    SELECT t4 AS sev, COUNT(*) AS count FROM log4jLogs WHERE t4 = '[ERROR]' AND  INPUT__FILE__NAME LIKE '%.log' GROUP BY t4;
    ```

    Эти заявления делают следующие действия:

    * `DROP TABLE`: Удаляет таблицу, если она существует.

    * `CREATE EXTERNAL TABLE`: создает новую "внешнюю" таблицу в Hive. Внешние таблицы хранят только определения таблицы в Hive. (Данные остаются в исходном месте.)

        > [!NOTE]  
        > Внешние таблицы следует использовать, когда вы ожидаете, что базовые данные будут обновлены внешним источником, таким как задание MapReduce или служба Azure.
        >
        > Удаление внешней таблицы **не** приводит к удалению данных, будет удалено только определение таблицы.

    * `ROW FORMAT`: инструкции по форматированию данных для Hive. В данном случае поля всех журналов разделены пробелом.

    * `STORED AS TEXTFILE LOCATION`: Сообщает Hive, что данные хранятся в *каталоге примера/данных* и что они хранятся в виде текста.

    * `SELECT`: Выберите количество всех строк, `t4` где `[ERROR]`столбец содержит значение. Это утверждение возвращает `3`значение , потому что три строки содержат это значение.

    * `INPUT__FILE__NAME LIKE '%.log'`: Говорит Hive только вернуть данные из файлов, заканчивающихся в .log. Это положение ограничивает поиск *файлом sample.log,* содержащим данные.

6. Из панели инструментов файла запроса (которая имеет аналогичный внешний вид с панелью инструментов ad-hoc, выберите кластер HDInsight, который требуется использовать для этого запроса. Затем измените **Interactive** в **пакет** (при необходимости) и выберите **Отправить** для выполнения инструкций в качестве задания Hive.

   Отобразится **сводка по заданию Hive** и информация о его выполнении. Воспользуйтесь ссылкой **Обновить**, чтобы обновить информацию о задании. Обновляйте ее до тех пор, пока **состояние задания** не изменится на **Завершено**.

   ![Завершено Hive резюме работы, Hive приложение, Visual Studio](./media/apache-hadoop-use-hive-visual-studio/hdinsight-job-summary.png)

7. Выберите **выход задания** для просмотра вывода этого задания. Они содержат `[ERROR] 3`. Это значение, возвращенное данным запросом.

### <a name="additional-example"></a>Дополнительный пример

Следующий пример опирается `log4jLogs` на таблицу, созданную в предыдущей процедуре, [Создать приложение Hive](#create-a-hive-application).

1. С **Сервер Explorer**, правой кнопкой мыши кластера и выберите **Написать Запрос Hive**.

2. Введите следующий запрос улья:

    ```hql
    set hive.execution.engine=tez;
    CREATE TABLE IF NOT EXISTS errorLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string) STORED AS ORC;
    INSERT OVERWRITE TABLE errorLogs SELECT t1, t2, t3, t4, t5, t6, t7 FROM log4jLogs WHERE t4 = '[ERROR]' AND INPUT__FILE__NAME LIKE '%.log';
    ```

    Эти заявления делают следующие действия:

    * `CREATE TABLE IF NOT EXISTS`: Создает таблицу, если она еще не существует. Поскольку `EXTERNAL` ключевое слово не используется, это утверждение создает внутреннюю таблицу. Внутренние таблицы хранятся в хранилище данных Hive и управляются Hive.

        > [!NOTE]  
        > В отличие от таблиц `EXTERNAL`, удаление внутренней таблицы приводит к удалению ее базовых данных.

    * `STORED AS ORC`: Хранит данные в *оптимизированном формате столбцов строки* (ORC). Это высокооптимизированный и эффективный формат для хранения данных Hive.

    * `INSERT OVERWRITE ... SELECT`: выбирает строки из таблицы `log4jLogs`, которые содержат `[ERROR]`, а затем вставляет эти данные в таблицу `errorLogs`.

3. При необходимости измените **интерактивный** пакет в **пакет,** а затем выберите **Отправить.**

4. Чтобы убедиться в создании таблицы задания, перейдите на **Server Explorer** и расширьте **Azure** > **HDInsight.** Расширьте свой кластер HDInsight, а затем расширьте **базы данных** > Hive**по умолчанию.** Должны быть отображены таблицы **errorLogs** и **log4jLogs**.

## <a name="next-steps"></a>Дальнейшие действия

Как вы видите, средства HDInsight для Visual Studio упрощают работу с запросами Hive в HDInsight.

* Для получения общей информации о Hive в HDInsight, [см. Что такое Apache Hive и Hive'L на Azure HDInsight?](hdinsight-use-hive.md)

* Для получения информации о других способах работы с Hadoop на HDInsight, [см.](hdinsight-use-mapreduce.md)

* Для получения дополнительной информации об инструментах HDInsight для Visual Studio[см.](apache-hadoop-visual-studio-tools-get-started.md)
