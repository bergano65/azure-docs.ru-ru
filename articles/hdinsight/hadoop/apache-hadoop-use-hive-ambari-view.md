---
title: Использование представлений Apache Ambari для работы с Hive в HDInsight (Apache Hadoop) — Azure
description: Узнайте, как использовать представление Hive в веб-браузере для отправки запросов Hive. Представление Hive — это компонент веб-интерфейса Ambari, поставляемого с кластером HDInsight на основе Linux.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 03/21/2019
ms.author: hrasheed
ms.openlocfilehash: 55f8f453faf35d52c5c292e6b309194443980466
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/13/2019
ms.locfileid: "64719564"
---
# <a name="use-apache-ambari-hive-view-with-apache-hadoop-in-hdinsight"></a>Использование представления Hive Apache Ambari с Apache Hadoop в HDInsight

[!INCLUDE [hive-selector](../../../includes/hdinsight-selector-use-hive.md)]

Узнайте, как выполнять запросы Hive с использованием представления Hive Apache Ambari. Представление Hive позволяет создавать, оптимизировать и выполнять запросы Hive из веб-браузера.

## <a name="prerequisites"></a>Технические условия

* Кластер Hadoop в HDInsight. Ознакомьтесь со статьей [Краткое руководство. Использование Apache Hadoop и Apache Hive в Azure HDInsight с шаблоном Resource Manager](./apache-hadoop-linux-tutorial-get-started.md).
* веб-браузера;

## <a name="run-a-hive-query"></a>Выполнение запроса Hive

1. Из [портала Azure](https://portal.azure.com/), выберите свой кластер.  См. в разделе [отображение кластеров](../hdinsight-administer-use-portal-linux.md#showClusters) инструкции. Кластер откроется в новой колонке портала.

2. Из **панели мониторинга кластера**выберите **представлений Ambari**. Если запрашивается проверка подлинности, используйте имя пользователя и пароль учетной записи входа в кластер (по умолчанию — `admin`), указанные при создании кластера.

3. В списке представлений выберите __Представление Hive__.

    ![Выбрано представление Hive](./media/apache-hadoop-use-hive-ambari-view/select-hive-view.png)

    Страница представления Hive выглядит следующим образом:

    ![Изображение листа запроса для представления Hive](./media/apache-hadoop-use-hive-ambari-view/ambari-hive-view.png)

4. На вкладке __Запрос__ вставьте в лист следующие инструкции HiveQL:

    ```hiveql
    DROP TABLE log4jLogs;
    CREATE EXTERNAL TABLE log4jLogs(
        t1 string,
        t2 string,
        t3 string,
        t4 string,
        t5 string,
        t6 string,
        t7 string)
    ROW FORMAT DELIMITED FIELDS TERMINATED BY ' '
    STORED AS TEXTFILE LOCATION '/example/data/';
    SELECT t4 AS loglevel, COUNT(*) AS count FROM log4jLogs 
        WHERE t4 = '[ERROR]' 
        GROUP BY t4;
    ```

    Эти операторы выполняют следующие действия:

   * `DROP TABLE`: удаляет таблицу и файл данных, если таблица уже существует.

   * `CREATE EXTERNAL TABLE`: создает "внешнюю" таблицу в Hive.
     Внешние таблицы хранят только определение таблицы в Hive. Данные остаются в исходном расположении.

   * `ROW FORMAT`: показывает настройку форматирования данных. В данном случае поля всех журналов разделены пробелом.

   * `STORED AS TEXTFILE LOCATION`: показывает место хранения данных и их формат (текст).

   * `SELECT`: выбирает подсчет количества строк, в которых столбец t4 содержит значение [ERROR].

   > [!IMPORTANT]  
   > Оставьте для параметра __База данных__ значение __по умолчанию__. В примерах в этом документе используется база данных по умолчанию, входящая в состав HDInsight.

5. Чтобы выполнить запрос, выберите **Execute** под листом. Кнопка станет оранжевой, а текст изменится на **Остановить**.

6. Когда запрос будет выполнен, на вкладке **Результаты** появятся результаты этой операции. Вот пример результата запроса:

        loglevel       count
        [ERROR]        3

    Можно использовать **ЖУРНАЛА** вкладку для просмотра сведений, создания задания.

   > [!TIP]  
   > Скачать или сохранить результаты из **действия** раскрывающемся диалоговом окне **результаты** вкладки.

### <a name="visual-explain"></a>Визуальное объяснение

Чтобы отобразить визуализацию плана запроса, выберите под листом вкладку **Visual Explain** (Визуальное пояснение).

Представление запроса **Visual Explain** (Визуальное объяснение) помогает разобраться в потоке сложных запросов.

### <a name="tez-ui"></a>Пользовательский интерфейс Tez

Чтобы отобразить пользовательский Интерфейс Tez для запроса, выберите **пользовательский Интерфейс Tez** под листом вкладку.

> [!IMPORTANT]  
> Tez используется не для всех запросов. Многие запросы можно разрешить и без применения Tez. 

## <a name="view-job-history"></a>Просмотр журнала заданий

На вкладке __Задания__ отображается журнал запросов Hive.

![Изображение журнала заданий](./media/apache-hadoop-use-hive-ambari-view/job-history.png)

## <a name="database-tables"></a>Таблицы базы данных

Вкладку __Таблицы__ можно использовать для работы с таблицами в базе данных Hive.

![Изображение вкладки "Таблицы"](./media/apache-hadoop-use-hive-ambari-view/tables.png)

## <a name="saved-queries"></a>Сохраненные запросы

На вкладке **Запрос** можно при желании сохранять запросы. После сохранения запроса можно повторно использовать его из вкладки __Saved Queries__ (Сохраненные запросы).

![Изображение вкладки "Сохраненные запросы"](./media/apache-hadoop-use-hive-ambari-view/saved-queries.png)

> [!TIP]  
> Запросы сохраняются в системе хранения данных кластера по умолчанию. Сохраненные запросы можно найти в следующем расположении: `/user/<username>/hive/scripts`. Они хранятся в виде обычных текстовых файлов `.hql`.
>
> Если вы удалите кластер, но сохраните хранилище, для извлечения запросов можно использовать такую служебную программу, как [Обозреватель службы хранилища Azure](https://azure.microsoft.com/features/storage-explorer/) или обозреватель хранилища Data Lake (на [портале Azure](https://portal.azure.com)).

## <a name="user-defined-functions"></a>Определяемые пользователем функции

Инфраструктуру Hive можно расширить с помощью определяемых пользователем функций (UDF). Они позволяют реализовать функции или логику, сложно моделируемые в HiveQL.

Объявлять и сохранять наборы определяемых пользователем функций можно с помощью вкладки **UDF** вверху представления Hive. Эти функции могут использоваться в **редакторе запросов**.

![Изображение вкладки "Определяемая пользователем функция"](./media/apache-hadoop-use-hive-ambari-view/user-defined-functions.png)

После добавления в представление Hive UDF в нижней части **редактора запросов** появляется кнопка **Insert udfs** (Вставить определяемые пользователем функции). Нажав эту кнопку, вы увидите раскрывающийся список функций, определенных в представлении Hive. Выбирая определяемую пользователем функцию, вы добавляете в запрос соответствующие инструкции HiveQL.

Например, вы определили функцию со следующими свойствами:

* имя ресурса — myudfs;

* путь к ресурсу — /myudfs.jar;

* имя определяемой пользователем функции — myawesomeudf;

* имя класса определяемой пользователем функции — com.myudfs.Awesome.

Нажав кнопку **Insert udfs** (Вставить определяемые пользователем функции), вы увидите запись с именем **myudfs**, содержащую раскрывающийся список для каждой функции, определяемой для этого ресурса. В данном случае это **myawesomeudf**. Если вы выберете эту запись, в начало запроса будет добавлен следующий код:

```hiveql
add jar /myudfs.jar;
create temporary function myawesomeudf as 'com.myudfs.Awesome';
```

Затем вы можете использовать эту функцию в своем запросе. Например, `SELECT myawesomeudf(name) FROM people;`.

Дополнительные сведения об использовании определяемых пользователем функций с Hive в HDInsight см. в следующих статьях:

* [Использование определяемых пользователем функций Python с Apache Hive и Apache Pig в HDInsight](python-udf-hdinsight.md)
* [How to add custom Hive UDFs to HDInsight](https://blogs.msdn.com/b/bigdatasupport/archive/2014/01/14/how-to-add-custom-hive-udfs-to-hdinsight.aspx) (Как добавить пользовательские UDF Hive в HDInsight)

## <a name="hive-settings"></a>Параметры Hive

Вы можете изменять различные настройки Hive. Например, для изменения механизма выполнения для Hive с Tez (значение по умолчанию) на MapReduce.

## <a id="nextsteps"></a>Дальнейшие действия

Общая информация о Hive в HDInsight:

* [Использование Apache Hive с Apache Hadoop в HDInsight](hdinsight-use-hive.md)

Дополнительная информация о других способах работы с Hadoop в HDInsight.

* [Использование Apache Pig с Apache Hadoop в HDInsight](hdinsight-use-pig.md)
* [Использование MapReduce в Apache Hadoop в HDInsight](hdinsight-use-mapreduce.md)
