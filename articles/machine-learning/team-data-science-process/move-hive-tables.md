---
title: Создание таблиц Hive и загрузка данных из хранилища BLOB-объектов Azure — процесс обработки и анализа данных группы
description: Использование Hive для создания таблиц Hive и загрузки данных из хранилища BLOB-объектов Azure. Секционирование таблиц Hive и использование формата Optimized Row Columnar (ORC) для улучшения производительности запросов.
services: machine-learning
author: marktab
manager: cgronlun
editor: cgronlun
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 11/04/2017
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 404335ce0cd05085c79cbeea29ad95f79008289c
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2019
ms.locfileid: "64681946"
---
# <a name="create-hive-tables-and-load-data-from-azure-blob-storage"></a>Создание таблиц Hive и загрузка данных из хранилища BLOB-объектов Azure

В этой статье рассматриваются общие запросы Hive, которые создают таблицы Hive и загружают данные из хранилищ BLOB-объектов Azure. Здесь также приведены некоторые указания по секционированию таблиц Hive и использованию формата Optimized Row Columnar (ORC) для улучшения производительности запросов.

## <a name="prerequisites"></a>Технические условия
В этой статье предполагается, что вы:

* Создали учетную запись хранения Azure. Инструкции см. в статье [Об учетных записях хранения Azure](../../storage/common/storage-introduction.md).
* Подготовили настраиваемый кластер Hadoop с помощью службы HDInsight.  Инструкции можно найти в статье об [установке кластеров HDInsight](../../hdinsight/hdinsight-hadoop-provision-linux-clusters.md).
* Включили удаленный доступ к кластеру, вошли в систему и открыли консоль командной строки Hadoop. Инструкции можно найти в статье об [управлении кластерами Apache Hadoop](../../hdinsight/hdinsight-administer-use-portal-linux.md).

## <a name="upload-data-to-azure-blob-storage"></a>Отправка данных в хранилище больших двоичных объектов Azure
Если вы создали виртуальную машину Azure в соответствии с инструкциями в разделе [Настройка виртуальной машины Azure для расширенной аналитики](../../machine-learning/data-science-virtual-machine/overview.md), этот файл сценария должен быть загружен в каталог *C:\\Users\\\<имя пользователя\>\\Documents\\Data Science Scripts* на виртуальной машине. Чтобы запросы Hive были готовы к отправке, достаточно подключить свою схему данных и применить конфигурацию хранилища больших двоичных объектов Azure в соответствующих полях.

Предполагается, что формат данных для таблиц Hive — табличный формат **без сжатия** и что данные отправлены в контейнер по умолчанию (или дополнительный контейнер) учетной записи хранения, которую использует кластер Hadoop.

Если вы хотите попрактиковаться на **наборе данных "Поездки такси Нью-Йорка"**, то необходимо выполнить следующие действия:

* **скачать** 24 файла [набора данных "Поездки такси Нью-Йорка"](https://www.andresmh.com/nyctaxitrips) (12 файлов поездок и 12 файлов тарифов);
* **распаковать** все CSV-файлы; а затем
* **передать**  их в контейнер по умолчанию (или соответствующий контейнер) в учетной записи хранения Azure. Параметры такой учетной записи описываются в статье [Использование службы хранилища Azure с кластерами Azure HDInsight](../../hdinsight/hdinsight-hadoop-use-blob-storage.md). Процесс отправки CSV-файла в контейнер по умолчанию в учетной записи хранения описан на этой [странице](hive-walkthrough.md#upload).

## <a name="submit"></a>Отправка запросов Hive
Инструменты для отправки запросов Hive:

1. [Отправка запросов Hive через командную строку Hadoop в головной узел кластера Hadoop](#headnode)
2. [Отправка запросов Hive с помощью редактора Hive](#hive-editor)
3. [Отправка запросов Hive с помощью команд PowerShell Azure](#ps)

Запросы Hive похожи на SQL. Если вы знакомы с SQL, то вам может оказаться полезной [памятка по Hive для пользователей SQL](https://hortonworks.com/wp-content/uploads/2013/05/hql_cheat_sheet.pdf) .

Отправляя запрос Hive, вы можете указать также место назначения, в которое будут отправлены результаты обработки запроса. Местом назначения может быть экран, локальный файл в головном узле или большой двоичный объект Azure.

### <a name="headnode"></a> 1. Отправка запросов Hive через командную строку Hadoop в головной узел кластера Hadoop
Отправка сложных запросов Hive непосредственно в головном узле кластера Hadoop обычно позволяет получить ответ быстрее, чем при использовании редактора Hive или сценариев Azure PowerShell.

Войдите в головной узел кластера Hadoop, откройте командную строку Hadoop на рабочем столе головного узла и введите команду `cd %hive_home%\bin`.

Отправить запрос Hive из командной строки Hadoop можно тремя способами:

* напрямую;
* с помощью HQL-файлов;
* из командной консоли Hive.

#### <a name="submit-hive-queries-directly-in-hadoop-command-line"></a>Отправка запросов Hive непосредственно из командной строки Hadoop
Отправить простой запрос Hive непосредственно из командной строки Hadoop можно с помощью такой команды, как `hive -e "<your hive query>;` . Ниже приведен пример, в котором красной рамкой обведена команда, которая отправляет запрос Hive, а зеленой обведены выходные данные обработки этого запроса.

![Команда для отправки запроса Hive с использованием выходных данных запроса Hive](./media/move-hive-tables/run-hive-queries-1.png)

#### <a name="submit-hive-queries-in-hql-files"></a>Отправка запросов Hive в HQL-файлах
Если запрос Hive сложный и содержит несколько строк, редактировать запросы в командной строке или командной консоли Hive нецелесообразно. Вместо этого можно использовать текстовый редактор в головном узле кластера Hadoop для сохранения запросов Hive в HQL-файле в локальном каталоге головного узла. Затем можно отправить запрос Hive в HQL-файле, воспользовавшись аргументом `-f` :

    hive -f "<path to the .hql file>"

![Запрос Hive в файле с расширением .hql](./media/move-hive-tables/run-hive-queries-3.png)

**Отменить вывод состояния хода выполнения запросов Hive на экран**

По умолчанию после отправки запроса Hive в командной строке Hadoop на экране отображается состояние хода выполнения задания Map/Reduce. Чтобы ход выполнения задания Map/Reduce не выводился на экран, можно указать в командной строке аргумент `-S` (S в верхнем регистре) как показано ниже:

    hive -S -f "<path to the .hql file>"
    hive -S -e "<Hive queries>"

#### <a name="submit-hive-queries-in-hive-command-console"></a>Отправка запросов Hive в командной консоли Hive
Также можно сначала войти в консоль команд Hive, выполнив команду `hive` в командной строке Hadoop, и затем отправить запросы Hive в командной консоли Hive. Ниже приведен пример. В этом примере красными рамками обведены команды, с помощью которых вы вошли в командную консоль Hive, и запрос Hive, отправленный в командную консоль Hive. Зеленой рамкой выделены выходные данные запроса Hive.

![Открытие командной консоли Hive, ввод команды и просмотр выходных данных запроса Hive](./media/move-hive-tables/run-hive-queries-2.png)

В предыдущих примерах данные обработки запроса Hive выведены прямо на экране. Выходные данные также можно записать в локальный файл на головном узле или в большой двоичный объект Azure. Затем можно использовать другие инструменты для дальнейшего анализа выходных данных запросов Hive.

**Вывод результатов запроса Hive в локальный файл**
Чтобы вывести результаты запроса Hive в локальный каталог на головном узле, следует отправить запрос Hive из командной строки Hadoop, как показано ниже:

    hive -e "<hive query>" > <local path in the head node>

В следующем примере выходные данные запроса Hive записываются в файл `hivequeryoutput.txt` в каталоге `C:\apps\temp`.

![Выходные данные запроса Hive](./media/move-hive-tables/output-hive-results-1.png)

**Вывод результатов запроса Hive в большой двоичный объект Azure**

Результаты запроса Hive также можно выводить в большой двоичный объект Azure в используемом по умолчанию контейнере кластера Hadoop. Для этого используется следующий запрос Hive:

    insert overwrite directory wasb:///<directory within the default container> <select clause from ...>

В следующем примере выходные данные запроса Hive записываются в каталог большого двоичного объекта `queryoutputdir` в используемом по умолчанию контейнере кластера Hadoop. Здесь нужно указать только имя каталога, не указывая имя большого двоичного объекта. Если указать имя каталога и имя большого двоичного объекта, появится следующее сообщение об ошибке: `wasb:///queryoutputdir/queryoutput.txt`.

![Выходные данные запроса Hive](./media/move-hive-tables/output-hive-results-2.png)

При открытии используемого по умолчанию контейнера кластера Hadoop с помощью Azure Storage Explorer выходные данные запроса Hive могут выглядеть так, как показано на следующем рисунке. Чтобы извлечь большие двоичные объекты только с указанными буквами в именах, можно применить фильтр (выделенный красной рамкой).

![Обозреватель службы хранилища Azure с выходными данными запроса Hive](./media/move-hive-tables/output-hive-results-3.png)

### <a name="hive-editor"></a> 2. Отправка запросов Hive с помощью редактора Hive
Можно также использовать консоль запросов (редактор Hive), введя URL-адрес в форме *https:\//\<имя кластера Hadoop >.azurehdinsight.net/Home/HiveEditor* в веб-браузере. Чтобы увидеть эту консоль, необходимо выполнить вход, а для этого вам потребуются ваши учетные данные для кластера Hadoop.

### <a name="ps"></a> 3. Отправка запросов Hive с помощью команд PowerShell Azure
Для отправки запросов Hive также можно использовать PowerShell. Инструкции см. в статье [Отправка заданий Hadoop в HDInsight](../../hdinsight/hadoop/apache-hadoop-use-hive-powershell.md).

## <a name="create-tables"></a>Создание базы данных и таблиц Hive
Запросы Hive доступны для общего использования в [репозитории GitHub](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_create_db_tbls_load_data_generic.hql), откуда их можно скачать.

Ниже приведен запрос Hive, который создает таблицу Hive.

    create database if not exists <database name>;
    CREATE EXTERNAL TABLE if not exists <database name>.<table name>
    (
        field1 string,
        field2 int,
        field3 float,
        field4 double,
        ...,
        fieldN string
    )
    ROW FORMAT DELIMITED FIELDS TERMINATED BY '<field separator>' lines terminated by '<line separator>'
    STORED AS TEXTFILE LOCATION '<storage location>' TBLPROPERTIES("skip.header.line.count"="1");

Здесь приведены описания полей, необходимых для подключения, и прочие конфигурации.

* **\<database name\>**  — имя создаваемой базы данных. Если вы предпочитаете использовать базу данных по умолчанию, то запрос *create database...* можно пропустить.
* **\<table name\>**  — имя таблицы, создаваемой в указанной базе данных. Если вы хотите использовать базу данных по умолчанию, то для обращения к таблице можно указать только *\<имя таблицы\>* без \<имени базы данных\>.
* **\<field separator\>**  — разделитель полей в файле данных, который следует загрузить в таблицу Hive.
* **\<line separator\>**  — разделитель строк в файле данных.
* **\<storage location\>**  — расположение хранилища Azure для сохранения данных таблиц Hive. Если не указывать параметр *LOCATION \<storage location\>*, база данных и таблицы сохраняются в каталоге *hive/warehouse/* в контейнере по умолчанию для кластера Hive по умолчанию. Если вы хотите указать место хранения, то оно должно находиться в пределах контейнера по умолчанию для базы данных и таблиц. Это расположение необходимо указать относительно контейнера кластера по умолчанию в формате *"wasb:///<каталог 1>/"* или *"wasb:///<каталог 1>/<каталог 2>/"* и т. д. После выполнения запроса в контейнере по умолчанию создаются соответствующие каталоги.
* **TBLPROPERTIES("skip.header.line.count"="1")**. Если файл данных содержит строку заголовка, необходимо добавить это свойство **в конец** запроса *create table*. В противном случае строка заголовка загружается в таблицу в качестве записи. Если в файле данных нет строки заголовка, в запросе эту конфигурацию можно пропустить.

## <a name="load-data"></a>Загрузка данных в таблицы Hive
Ниже приведен запрос Hive, который загружает данные в таблицу Hive.

    LOAD DATA INPATH '<path to blob data>' INTO TABLE <database name>.<table name>;

* **\<путь к данным BLOB-объекта\>**  — Если файл BLOB-объектов для передачи в таблицу Hive в контейнере по умолчанию кластера HDInsight Hadoop, *\<путь_к_данным_большого_двоичного_объекта\>* должно быть в формате *"wasb: / /\< каталог, в этом контейнере > /\<имя файла большого двоичного объекта > "*. В дополнительном контейнере кластера Hadoop под управлением службы HDInsight также может быть файл большого двоичного объекта. В этом случае *\<путь_к_данным_большого_двоичного_объекта\>* должно быть в формате *"wasb: / /\<имя контейнера >\<имя учетной записи хранения >.blob.core.windows.net/\<имя файла большого двоичного объекта > "*.

  > [!NOTE]
  > Данные большого двоичного объекта, которые необходимо отправить в таблицу Hive, должны находиться в контейнере по умолчанию или в дополнительном контейнере учетной записи хранения для кластера Hadoop. В противном случае запрос *LOAD DATA* завершается ошибкой доступа к данным.
  >
  >

## <a name="partition-orc"></a>Дополнительные разделы: секционированные таблицы и хранение данных Hive в формате ORC
При большом объеме данных секционирование таблицы полезно для запросов, в рамках которых требуется сканировать только несколько секций таблицы. Например, целесообразно секционировать данные журнала веб-сайта по датам.

Помимо секционирования таблиц Hive данные Hive также удобно хранить в формате ORC. Дополнительную информацию об использовании формата ORC см. в <a href="https://cwiki.apache.org/confluence/display/Hive/LanguageManual+ORC#LanguageManualORC-ORCFiles" target="_blank">разделе о том, как использование ORC-файлов повышает производительность при чтении, записи и обработке данных Hive</a>.

### <a name="partitioned-table"></a>Секционированная таблица
Ниже приведен запрос Hive, который создает секционированную таблицу и загружает в нее данные.

    CREATE EXTERNAL TABLE IF NOT EXISTS <database name>.<table name>
    (field1 string,
    ...
    fieldN string
    )
    PARTITIONED BY (<partitionfieldname> vartype) ROW FORMAT DELIMITED FIELDS TERMINATED BY '<field separator>'
         lines terminated by '<line separator>' TBLPROPERTIES("skip.header.line.count"="1");
    LOAD DATA INPATH '<path to the source file>' INTO TABLE <database name>.<partitioned table name>
        PARTITION (<partitionfieldname>=<partitionfieldvalue>);

При запросах к секционированным таблицам желательно указывать условие секционирования **в начале** предложения `where`. Этот прием позволяет значительно повысить эффективность поиска.

    select
        field1, field2, ..., fieldN
    from <database name>.<partitioned table name>
    where <partitionfieldname>=<partitionfieldvalue> and ...;

### <a name="orc"></a>Хранение данных Hive в формате ORC
Вы не можете загружать данные непосредственно из хранилища BLOB-объектов в таблицы Hive в формате ORC. Ниже приведены действия, которые необходимо выполнить, чтобы загрузить данные из больших двоичных объектов Azure в таблицы Hive в формате ORC.

Создайте внешнюю таблицу **В ВИДЕ ТЕКСТОВОГО ФАЙЛА** и загрузите в нее данные из хранилища больших двоичных объектов.

        CREATE EXTERNAL TABLE IF NOT EXISTS <database name>.<external textfile table name>
        (
            field1 string,
            field2 int,
            ...
            fieldN date
        )
        ROW FORMAT DELIMITED FIELDS TERMINATED BY '<field separator>'
            lines terminated by '<line separator>' STORED AS TEXTFILE
            LOCATION 'wasb:///<directory in Azure blob>' TBLPROPERTIES("skip.header.line.count"="1");

        LOAD DATA INPATH '<path to the source file>' INTO TABLE <database name>.<table name>;

Создайте внутреннюю таблицу, используя ту же схему и тот же разделитель полей, что и во внешней таблице на шаге 1, и сохраните данные Hive в формате ORC.

        CREATE TABLE IF NOT EXISTS <database name>.<ORC table name>
        (
            field1 string,
            field2 int,
            ...
            fieldN date
        )
        ROW FORMAT DELIMITED FIELDS TERMINATED BY '<field separator>' STORED AS ORC;

Выберите данные во внешней таблице на шаге 1 и вставьте их в таблицу в формате ORC.

        INSERT OVERWRITE TABLE <database name>.<ORC table name>
            SELECT * FROM <database name>.<external textfile table name>;

> [!NOTE]
> Если в таблице TEXTFILE *\<database name\>.\<external textfile table name\>* есть секции, команда `SELECT * FROM <database name>.<external textfile table name>`, выполняемая на шаге 3, выбирает переменную секции в качестве отдельного поля в возвращаемом наборе данных. При вставке этих данных в таблицу *\<database name\>.\<ORC table name\>* возникает ошибка, поскольку в *\<database name\>.\<ORC table name\>* не существует переменной секции в качестве поля в схеме таблицы. В этом случае необходимо указать поля, которые требуется вставить в таблицу *\<database name\>.\<ORC table name\>*, как указанно ниже.
>
>

        INSERT OVERWRITE TABLE <database name>.<ORC table name> PARTITION (<partition variable>=<partition value>)
           SELECT field1, field2, ..., fieldN
           FROM <database name>.<external textfile table name>
           WHERE <partition variable>=<partition value>;

Когда все данные будут вставлены в таблицу *\<database name\>.\<ORC table name\>*, можно безопасно удалить *\<external textfile table name\>*, используя следующий запрос.

        DROP TABLE IF EXISTS <database name>.<external textfile table name>;

После выполнения этой процедуры у вас должна быть готовая к использованию таблица с данными в формате ORC.  
