---
title: Мигрируйте в рабочие нагрузки Azure HDInsight 3.6 Hive в HDInsight 4.0
description: Узнайте, как перенести рабочие нагрузки Apache Hive на HDInsight 3.6 в HDInsight 4.0.
author: msft-tacox
ms.author: tacox
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 11/13/2019
ms.openlocfilehash: ec96189185a06c1fcbd95eed6216ade47f3089c3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79214644"
---
# <a name="migrate-azure-hdinsight-36-hive-workloads-to-hdinsight-40"></a>Мигрируйте в рабочие нагрузки Azure HDInsight 3.6 Hive в HDInsight 4.0

В этом документе показано, как мигрировать рабочие нагрузки Apache Hive и LLAP на HDInsight 3.6 в HDInsight 4.0. HDInsight 4.0 предоставляет новые функции Hive и LLAP, такие как материализованные представления и кэширование результатов запросов. При переносе рабочих нагрузок в HDInsight 4.0 можно использовать множество новых функций Hive 3, которые недоступны на HDInsight 3.6.

Эта статья охватывает следующие темы:

* Миграция метаданных Hive в HDInsight 4.0
* Безопасная миграция таблиц ACID и неacidе
* Сохранение политик безопасности Hive в версиях HDInsight
* Выполнение запросов и отладка с HDInsight 3.6 до HDInsight 4.0

Одним из преимуществ Hive является возможность экспорта метаданных во внешнюю базу данных (именуемую Hive Metastore). **Hive Metastore** отвечает за хранение статистики таблиц, включая место хранения таблиц, названия столбцов и информацию об индексах таблицы. Схема базы данных metastore отличается между версиями Hive. Рекомендуемый способ обновления метамагазина Hive — это создание копии и обновление копии вместо текущей производственной среды.

## <a name="copy-metastore"></a>Копировать метамагазин

HDInsight 3.6 и HDInsight 4.0 требуют различных схем метамагазина и не могут поделиться одним метамагазином.

### <a name="external-metastore"></a>Внешний метамагазин

Создайте новую копию внешнего метамагазина. Если вы используете внешний метамагазин, одним из безопасных и простых способов сделать копию метамагазина является [восстановление базы данных](../../sql-database/sql-database-recovery-using-backups.md#point-in-time-restore) с другим именем с помощью функции восстановления базы данных S'L.  [См. Воспользуйтесь внешними хранилищами метаданных в Azure HDInsight,](../hdinsight-use-external-metadata-stores.md) чтобы узнать больше о подключении внешнего метамагазина к кластеру HDInsight.

### <a name="internal-metastore"></a>Внутренний метамагазин

Если вы используете внутренний метамагазин, можно использовать запросы для экспорта определений объектов в метамагазине Hive и импортировать их в новую базу данных.

Как только этот скрипт будет завершен, предполагается, что старый кластер больше не будет использоваться для доступа к любым таблицам или базам данных, упомянутым в скрипте.

> [!NOTE]
> В случае таблиц ACID будет создана новая копия данных под таблицей.

1. Подключитесь к кластеру HDInsight с помощью [клиента Secure Shell (SSH).](../hdinsight-hadoop-linux-use-ssh-unix.md)

1. Подключитесь к HiveServer2 с [вашим клиентом Beeline](../hadoop/apache-hadoop-use-hive-beeline.md) из открытой сессии SSH, введя следующую команду:

    ```hiveql
    for d in `beeline -u "jdbc:hive2://localhost:10001/;transportMode=http" --showHeader=false --silent=true --outputformat=tsv2 -e "show databases;"`; 
    do
        echo "Scanning Database: $d"
        echo "create database if not exists $d; use $d;" >> alltables.hql; 
        for t in `beeline -u "jdbc:hive2://localhost:10001/$d;transportMode=http" --showHeader=false --silent=true --outputformat=tsv2 -e "show tables;"`;
        do
            echo "Copying Table: $t"
            ddl=`beeline -u "jdbc:hive2://localhost:10001/$d;transportMode=http" --showHeader=false --silent=true --outputformat=tsv2 -e "show create table $t;"`;

            echo "$ddl;" >> alltables.hql;
            lowerddl=$(echo $ddl | awk '{print tolower($0)}')
            if [[ $lowerddl == *"'transactional'='true'"* ]]; then
                if [[ $lowerddl == *"partitioned by"* ]]; then
                    # partitioned
                    raw_cols=$(beeline -u "jdbc:hive2://localhost:10001/$d;transportMode=http" --showHeader=false --silent=true --outputformat=tsv2 -e "show create table $t;" | tr '\n' ' ' | grep -io "CREATE TABLE .*" | cut -d"(" -f2- | cut -f1 -d")" | sed 's/`//g');
                    ptn_cols=$(beeline -u "jdbc:hive2://localhost:10001/$d;transportMode=http" --showHeader=false --silent=true --outputformat=tsv2 -e "show create table $t;" | tr '\n' ' ' | grep -io "PARTITIONED BY .*" | cut -f1 -d")" | cut -d"(" -f2- | sed 's/`//g');
                    final_cols=$(echo "(" $raw_cols "," $ptn_cols ")")

                    beeline -u "jdbc:hive2://localhost:10001/$d;transportMode=http" --showHeader=false --silent=true --outputformat=tsv2 -e "create external table ext_$t $final_cols TBLPROPERTIES ('transactional'='false');";
                    beeline -u "jdbc:hive2://localhost:10001/$d;transportMode=http" --showHeader=false --silent=true --outputformat=tsv2 -e "insert into ext_$t select * from $t;";
                    staging_ddl=`beeline -u "jdbc:hive2://localhost:10001/$d;transportMode=http" --showHeader=false --silent=true --outputformat=tsv2 -e "show create table ext_$t;"`;
                    dir=$(echo $staging_ddl | grep -io " LOCATION .*" | grep -m1 -o "'.*" | sed "s/'[^-]*//2g" | cut -c2-);

                    parsed_ptn_cols=$(echo $ptn_cols| sed 's/ [a-z]*,/,/g' | sed '$s/\w*$//g');
                    echo "create table flattened_$t $final_cols;" >> alltables.hql;
                    echo "load data inpath '$dir' into table flattened_$t;" >> alltables.hql;
                    echo "insert into $t partition($parsed_ptn_cols) select * from flattened_$t;" >> alltables.hql;
                    echo "drop table flattened_$t;" >> alltables.hql;
                    beeline -u "jdbc:hive2://localhost:10001/$d;transportMode=http" --showHeader=false --silent=true --outputformat=tsv2 -e "drop table ext_$t";
                else
                    # not partitioned
                    beeline -u "jdbc:hive2://localhost:10001/$d;transportMode=http" --showHeader=false --silent=true --outputformat=tsv2 -e "create external table ext_$t like $t TBLPROPERTIES ('transactional'='false');";
                    staging_ddl=`beeline -u "jdbc:hive2://localhost:10001/$d;transportMode=http" --showHeader=false --silent=true --outputformat=tsv2 -e "show create table ext_$t;"`;
                    dir=$(echo $staging_ddl | grep -io " LOCATION .*" | grep -m1 -o "'.*" | sed "s/'[^-]*//2g" | cut -c2-);

                    beeline -u "jdbc:hive2://localhost:10001/$d;transportMode=http" --showHeader=false --silent=true --outputformat=tsv2 -e "insert into ext_$t select * from $t;";
                    echo "load data inpath '$dir' into table $t;" >> alltables.hql;
                    beeline -u "jdbc:hive2://localhost:10001/$d;transportMode=http" --showHeader=false --silent=true --outputformat=tsv2 -e "drop table ext_$t";
                fi
            fi
            echo "$ddl" | grep -q "PARTITIONED\s*BY" && echo "MSCK REPAIR TABLE $t;" >> alltables.hql;
        done;
    done
    ```

    Эта команда генерирует файл под названием **alltables.hql.**

1. Закройте сеанс SSH. Затем введите команду scp, чтобы загрузить **alltables.hql** локально.

    ```bash
    scp sshuser@CLUSTERNAME-ssh.azurehdinsight.net:alltables.hql c:/hdi
    ```

1. Загрузите **alltables.hql** в *новый* кластер HDInsight.

    ```bash
    scp c:/hdi/alltables.hql sshuser@CLUSTERNAME-ssh.azurehdinsight.net:/home/sshuser/
    ```

1. Затем используйте SSH для подключения к *новому* кластеру HDInsight. Выполнить следующий код из сеанса SSH:

    ```bash
    beeline -u "jdbc:hive2://localhost:10001/;transportMode=http" -i alltables.hql
    ```


## <a name="upgrade-metastore"></a>Обновление метамагазин

После завершения **копирования** metastore запустите сценарий обновления схемы в [Сценарий действий](../hdinsight-hadoop-customize-cluster-linux.md) на существующем кластере HDInsight 3.6 для обновления нового метамагазина до схемы Hive 3. Это позволяет прикрепить базу данных как метастор HDInsight 4.0.

Используйте значения в таблице ниже. Замените `SQLSERVERNAME DATABASENAME USERNAME PASSWORD` соответствующие значения для **скопированных** метастора Hive, разделенного пробелами. При указании имени сервера S'L не указано ".database.windows.net".

|Свойство | Значение |
|---|---|
|Тип скрипта|- Custom|
|name|Обновление Hive|
|URI bash-скрипта|`https://hdiconfigactions.blob.core.windows.net/hivemetastoreschemaupgrade/launch-schema-upgrade.sh`|
|Типы узлов|Head|
|Параметры|СЗЛСЕРВЕРНАМЕ БАЗИСНАНАМА УЗЕРНАМЕ PASSWORD|

> [!Warning]  
> Обновление, которое преобразует схему метаданных HDInsight 3.6 в схему HDInsight 4.0, не может быть отменено.

Вы можете проверить обновление, запустив следующий запрос sql против базы данных:

```sql
select * from dbo.version
```

## <a name="migrate-hive-tables-to-hdinsight-40"></a>Перенести таблицы Hive в HDInsight 4.0

После завершения предыдущего набора шагов по переносу Hive Metastore в HDInsight 4.0, таблицы и базы данных, записанные в метасторе, будут видны из кластера HDInsight 4.0 путем выполнения `show tables` или `show databases` внутри кластера. Просчитайте [выполнение запросов в версиях HDInsight](#query-execution-across-hdinsight-versions) для получения информации о выполнении запросов в кластерах HDInsight 4.0.

Однако фактические данные из таблиц недоступны до тех пор, пока кластер не получит доступ к необходимым учетным записям хранения. Чтобы убедиться, что кластер HDInsight 4.0 может получить доступ к тем же данным, что и старый кластер HDInsight 3.6, выполните следующие шаги:

1. Определите учетную запись хранения данных таблицы или базы данных Azure.

1. Если кластер HDInsight 4.0 уже работает, прикрепите учетную запись хранения Azure к кластеру через Ambari. Если вы еще не создали кластер HDInsight 4.0, убедитесь, что учетная запись хранения Azure указана как основная или второстепенная учетная запись хранения кластеров. Для получения дополнительной информации о добавлении учетных записей хранилища в кластеры HDInsight [см.](../hdinsight-hadoop-add-storage.md)

## <a name="deploy-new-hdinsight-40-and-connect-to-the-new-metastore"></a>Развертывание нового HDInsight 4.0 и подключение к новому метамагазину

После завершения обновления схемы развернуть новый кластер HDInsight 4.0 и подключить обновленный метамагазин. Если вы уже развернули 4.0, установите его так, чтобы вы могли подключиться к метамагазину из Ambari.

## <a name="run-schema-migration-script-from-hdinsight-40"></a>Выполнить сценарий миграции схемы с HDInsight 4.0

Таблицы рассматриваются по-разному в HDInsight 3.6 и HDInsight 4.0. По этой причине нельзя обмениваться одинаковыми таблицами для кластеров различных версий. Если вы хотите использовать HDInsight 3.6 одновременно с HDInsight 4.0, вы должны иметь отдельные копии данных для каждой версии.

Рабочая нагрузка Hive может включать в себя сочетание таблиц ACID и неACID. Одним из ключевых различий между Hive на HDInsight 3.6 (Hive 2) и Hive на HDInsight 4.0 (Hive 3) является ACID-соответствие таблиц. В HDInsight 3.6, что позволяет Hive ACID-соответствие требует дополнительной конфигурации, но в таблицах HDInsight 4.0 acid-совместимы по умолчанию. Единственное действие, необходимое перед миграцией, это запустить крупное уплотнение против таблицы ACID в кластере 3.6. С видом Hive или от Билайн, запустите следующий запрос:

```sql
alter table myacidtable compact 'major';
```

Это уплотнение необходимо, потому что ТАБЛИЦы HDInsight 3.6 и HDInsight 4.0 ACID понимают дельты ACID по-разному. Compaction обеспечивает чистый лист, который гарантирует последовательность. Раздел 4 [миграционной документации Hive](https://docs.hortonworks.com/HDPDocuments/Ambari-2.7.3.0/bk_ambari-upgrade-major/content/prepare_hive_for_upgrade.html) содержит рекомендации по массовому уплотнению таблиц HDInsight 3.6 ACID.

После завершения шагов миграции и уплотнения метамагазина можно перенести фактический склад. После завершения миграции склада Hive склад HDInsight 4.0 будет иметь следующие свойства:

|3.6 |4,0 |
|---|---|
|Внешние таблицы|Внешние таблицы|
|Таблицы управления нетранзакционными операциями|Внешние таблицы|
|Таблицы транзакционных управляемых|Управляемые таблицы|

Возможно, вам придется настроить свойства вашего склада перед выполнением миграции. Например, если вы ожидаете, что к некоторой таблице будет доступна третья сторона (например, кластер HDInsight 3.6), эта таблица должна быть внешней после завершения миграции. В HDInsight 4.0 все управляемые таблицы транзакционны. Таким образом, управляемые таблицы в HDInsight 4.0 должны быть доступны только кластерами HDInsight 4.0.

После правильного настройки свойств таблицы выполните инструмент миграции склада Hive из одного из блоковых головных узлов с помощью оболочки SSH:

1. Подключитесь к головному ноду кластера с помощью SSH. Для получения инструкций [см. Подключение к HDInsight с помощью SSH](../hdinsight-hadoop-linux-use-ssh-unix.md)
1. Откройте оболочку входа в качестве пользователя Hive, запустив`sudo su - hive`
1. Определите версию стека платформы `ls /usr/hdp`данных путем выполнения. Это будет отображать строку версии, которую следует использовать в следующей команде.
1. Выполните следующую команду из оболочки. Заменить `STACK_VERSION` строку версии из предыдущего шага:

```bash
/usr/hdp/STACK_VERSION/hive/bin/hive --config /etc/hive/conf --service  strictmanagedmigration --hiveconf hive.strict.managed.tables=true -m automatic --modifyManagedTables
```

После завершения инструмента миграции ваш склад Hive будет готов к HDInsight 4.0.

> [!Important]  
> Управляемые таблицы в HDInsight 4.0 (включая таблицы, перенесенные из 3.6) не должны быть доступны другимслужбам или приложениям, включая кластеры HDInsight 3.6.

## <a name="secure-hive-across-hdinsight-versions"></a>Безопасный улей в версиях HDInsight

Начиная с HDInsight 3.6, HDInsight интегрируется с Active Directory Azure с помощью пакета безопасности для предприятий HDInsight (ESP). ESP использует Kerberos и Apache Ranger для управления разрешениями конкретных ресурсов в кластере. Политики рейнджеров, развернутые против Hive в HDInsight 3.6, могут быть перенесены в HDInsight 4.0 со следующими шагами:

1. Перейдите к панели менеджера службы Ranger в кластере HDInsight 3.6.
2. Перейдите к политике под названием **HIVE** и экспортите политику в файл json.
3. Убедитесь, что все пользователи, упомянутые в экспортируемой политике json, существуют в новом кластере. Если пользователь упоминается в json политики, но не существует в новом кластере, либо добавьте пользователя в новый кластер, либо удалите ссылку из политики.
4. Перейдите к панели **менеджера службы Ranger** в кластере HDInsight 4.0.
5. Перейдите к политике под названием **HIVE** и импортируйте политику рейнджера json со шага 2.

## <a name="check-compatibility-and-modify-codes-as-needed-in-test-app"></a>Проверьте совместимость и изменяйте коды по мере необходимости в тестовом приложении

При миграции рабочих нагрузок, таких как существующие программы и запросы, пожалуйста, проверьте примечания к выпуску и документацию на наличие изменений и при меняв изменения по мере необходимости. Если кластер HDInsight 3.6 использует общий метамагазин Spark и Hive, требуется [дополнительная конфигурация с помощью разъема Склада Hive.](./apache-hive-warehouse-connector.md)

## <a name="deploy-new-app-for-production"></a>Развертывание нового приложения для производства

Чтобы переключиться на новый кластер, например, можно установить новое клиентское приложение и использовать его в качестве новой производственной среды, или обновить существующее клиентское приложение и переключиться на HDInsight 4.0.

## <a name="switch-hdinsight-40-to-the-production"></a>Переключить HDInsight 4.0 на производство

Если во время тестирования были созданы различия в метамагазине, необходимо обновить изменения непосредственно перед переключением. В этом случае можно экспортировать & импортировать метамагазин, а затем обновить снова.

## <a name="remove-the-old-production"></a>Удалить старое производство

После того как вы подтвердили, что релиз завершен и полностью функционирует, вы можете удалить версию 3.6 и предыдущий метамагазин. Пожалуйста, убедитесь, что все мигрирует перед удалянием среды.

## <a name="query-execution-across-hdinsight-versions"></a>Выполнение запросов в версиях HDInsight

Существует два способа выполнения и отладки запросов Hive/LLAP в кластере HDInsight 3.6. HiveCLI обеспечивает командную строку, а представление Tez/Hive обеспечивает рабочий процесс на основе графического интерфейса.

В HDInsight 4.0 HiveCLI был заменен на Билайн. HiveCLI является бережливым клиентом Hiveserver 1, а Билайн является клиентом JDBC, который предоставляет доступ к Hiveserver 2. Билайн также может быть использован для подключения к любой другой конечной точке базы данных, совместимой с JDBC. Билайн доступен вне коробки на HDInsight 4.0 без какой-либо установки необходимо.

В HDInsight 3.6 клиент GUI для взаимодействия с сервером Hive — это Ambari Hive View. HDInsight 4.0 не подает с Ambari View. Мы предоставили нашим клиентам возможность использовать студию data Analytics Studio (DAS), которая не является основным сервисом HDInsight. DAS не подает с кластерами HDInsight вне коробки и не является официально поддерживаемым пакетом. Тем не менее, DAS может быть установлен на кластере с помощью [действия скрипта](../hdinsight-hadoop-customize-cluster-linux.md) следующим образом:

|Свойство | Значение |
|---|---|
|Тип скрипта|- Custom|
|name|Das|
|URI bash-скрипта|`https://hdiconfigactions.blob.core.windows.net/dasinstaller/LaunchDASInstaller.sh`|
|Типы узлов|Head|

Подождите от 10 до 15 минут, а `https://CLUSTERNAME.azurehdinsight.net/das/`затем запустите Data Analytics Studio с помощью этого URL: .

Перед доступом к DAS может потребоваться обновление uI Ambari и/или перезапуск всех компонентов Ambari.

После установки DAS, если вы не видите запросы, которые вы задали в просмотре запросов, сделайте следующие шаги:

1. Установите конфигурации для Hive, Tez и DAS, как описано в [этом руководстве для устранения неполадок установки DAS.](https://docs.hortonworks.com/HDPDocuments/DAS/DAS-1.2.0/troubleshooting/content/das_queries_not_appearing.html)
2. Убедитесь, что следующие конфигурации каталога хранения Azure являются Каплями, и что они перечислены в соответствии `fs.azure.page.blob.dirs`с:
    * `hive.hook.proto.base-directory`
    * `tez.history.logging.proto-base-dir`
3. Перезапустите HDFS, Hive, Tez и DAS на обоих головных узлах.

## <a name="next-steps"></a>Дальнейшие действия

* [Объявление HDInsight 4.0](../hdinsight-version-release.md)
* [Глубокое погружение HDInsight 4.0](https://azure.microsoft.com/blog/deep-dive-into-azure-hdinsight-4-0/)
* [Улей 3 ACID Столы](https://docs.hortonworks.com/HDPDocuments/HDP3/HDP-3.1.0/using-hiveql/content/hive_3_internals.html)
