---
title: Перенос рабочих нагрузок Hive Azure HDInsight 3,6 в HDInsight 4,0
description: Узнайте, как перенести рабочие нагрузки Apache Hive в HDInsight 3,6 в HDInsight 4,0.
author: msft-tacox
ms.author: tacox
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 11/13/2019
ms.openlocfilehash: 14849dd1f68f281009808d1bd1dc1cae62927ab4
ms.sourcegitcommit: 3abadafcff7f28a83a3462b7630ee3d1e3189a0e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/30/2020
ms.locfileid: "82594242"
---
# <a name="migrate-azure-hdinsight-36-hive-workloads-to-hdinsight-40"></a>Перенос рабочих нагрузок Hive Azure HDInsight 3,6 в HDInsight 4,0

В этом документе показано, как перенести рабочие нагрузки Apache Hive и LLAP в HDInsight 3,6 в HDInsight 4,0. HDInsight 4,0 предоставляет более новые функции Hive и LLAP, такие как материализованные представления и кэширование результатов запросов. При переносе рабочих нагрузок в HDInsight 4,0 можно использовать множество новых функций Hive 3, которые недоступны в HDInsight 3,6.

В этой статье рассматриваются следующие темы:

* Миграция метаданных Hive в HDInsight 4,0
* Безопасность переноса таблиц ACID и не ACID
* Сохранение политик безопасности Hive в версиях HDInsight
* Выполнение запросов и отладка из HDInsight 3,6 в HDInsight 4,0

Одним из преимуществ Hive является возможность экспорта метаданных во внешнюю базу данных (хранилище метаданных Hive). **Хранилище метаданных Hive** отвечает за хранение статистики таблицы, включая расположение хранилища таблиц, имена столбцов и сведения об индексах таблиц. Для HDInsight 3,6 и HDInsight 4,0 требуются разные схемы хранилище метаданных и не могут совместно использовать один хранилище метаданных. Для безопасного обновления хранилище метаданных Hive рекомендуется обновить копию вместо исходной в текущей рабочей среде. Для работы с этим документом требуется, чтобы исходные и новые кластеры имели доступ к одной и той же учетной записи хранения. Поэтому он не охватывает перенос данных в другой регион.

## <a name="migrate-from-external-metastore"></a>Миграция с внешних хранилище метаданных

### <a name="1-run-major-compaction-on-acid-tables-in-hdinsight-36"></a>1. выполнение основного сжатия таблиц ACID в HDInsight 3,6

Таблицы HDInsight 3,6 и HDInsight 4,0 ACID по-разному понимают различия ACID. Единственное действие, необходимое перед миграцией, — выполнение "основных" сжатия для каждой таблицы ACID в кластере 3,6. Дополнительные сведения о сжатии см. в [руководстве по языку Hive](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+DDL#LanguageManualDDL-AlterTable/Partition/Compact) .

### <a name="2-copy-sql-database"></a>2. копирование базы данных SQL
Создайте новую копию внешнего хранилище метаданных. Если вы используете внешний хранилище метаданных, один из надежных и простых способов создания копии хранилище метаданных — [Восстановление базы данных](../../sql-database/sql-database-recovery-using-backups.md#point-in-time-restore) с другим именем с помощью функции восстановления базы данных SQL.  Дополнительные сведения о подключении внешних хранилище метаданных к кластеру HDInsight см. в статье [использование внешних хранилищ метаданных в Azure hdinsight](../hdinsight-use-external-metadata-stores.md) .

### <a name="3-upgrade-metastore-schema"></a>3. обновление схемы хранилище метаданных
После завершения **копирования** хранилище метаданных запустите сценарий обновления схемы в [действии сценария](../hdinsight-hadoop-customize-cluster-linux.md) в существующем кластере HDInsight 3,6, чтобы обновить схему хранилище метаданных до куста 3. (Этот шаг не требует подключения нового хранилище метаданных к кластеру.) Это позволяет подключить базу данных как HDInsight 4,0 хранилище метаданных.

Используйте значения в таблице ниже. Замените `SQLSERVERNAME DATABASENAME USERNAME PASSWORD` на соответствующие значения для хранилище метаданных Hive **копии**, разделенные пробелами. При указании имени SQL Server не включайте ". database.windows.net".

|Свойство | Применение |
|---|---|
|Тип скрипта|- Custom|
|Название|Обновление Hive|
|URI bash-скрипта|`https://hdiconfigactions.blob.core.windows.net/hivemetastoreschemaupgrade/launch-schema-upgrade.sh`|
|Типы узлов|Head|
|Параметры|SQLSERVERNAME ИМЯ ПОЛЬЗОВАТЕЛЯ, ПАРОЛЬ|

> [!Warning]  
> Обновление, преобразующее схему метаданных HDInsight 3,6 в схему HDInsight 4,0, не может быть отменено.

Вы можете проверить обновление, выполнив следующий запрос SQL к базе данных:

```sql
select * from dbo.version
```

### <a name="4-deploy-a-new-hdinsight-40-cluster"></a>4. Развертывание нового кластера HDInsight 4,0

1. Укажите обновленный хранилище метаданных в качестве хранилище метаданных Hive нового кластера.

1. Однако фактические данные из таблиц недоступны, пока кластер не будет иметь доступ к необходимым учетным записям хранения.
Убедитесь, что учетные записи хранения таблиц Hive в кластере HDInsight 3,6 указаны как первичные или вторичные учетные записи хранения для нового кластера HDInsight 4,0.
Дополнительные сведения о добавлении учетных записей хранения в кластеры HDInsight см. [в статье Добавление дополнительных учетных записей хранения в hdinsight](../hdinsight-hadoop-add-storage.md).

### <a name="5-complete-migration-with-a-post-upgrade-tool-in-hdinsight-40"></a>5. завершите миграцию с помощью средства после обновления в HDInsight 4,0

По умолчанию управляемые таблицы должны быть совместимы с ACID в HDInsight 4,0. После завершения миграции хранилище метаданных запустите средство после обновления, чтобы сделать ранее не совместимые с кластером HDInsight 4,0 таблицы, отличные от ACID. Это средство будет применять следующее преобразование:

|3.6 |4,0 |
|---|---|
|Внешние таблицы|Внешние таблицы|
|Таблицы, не являющиеся управляемыми ACID|Внешние таблицы со свойством "External. Table. unочисткой" = "true"|
|Управляемые таблицы ACID|Управляемые таблицы ACID|

Выполните средство после обновления Hive из кластера HDInsight 4,0 с помощью оболочки SSH:

1. Подключитесь к кластеру головного узла с помощью SSH. Инструкции см. [в статье подключение к HDInsight с помощью SSH](../hdinsight-hadoop-linux-use-ssh-unix.md) .
1. Откройте оболочку входа от имени пользователя Hive, выполнив`sudo su - hive`
1. Выполните следующую команду из оболочки.

    ```bash
    STACK_VERSION=$(hdp-select status hive-server2 | awk '{ print $3; }')
    /usr/hdp/$STACK_VERSION/hive/bin/hive --config /etc/hive/conf --service  strictmanagedmigration --hiveconf hive.strict.managed.tables=true -m automatic --modifyManagedTables
    ```

После завершения работы средства хранилище Hive будет готово к работе с HDInsight 4,0.

## <a name="migrate-from-internal-metastore"></a>Миграция из внутренних хранилище метаданных

Если в кластере HDInsight 3,6 используется внутренний хранилище метаданных Hive, выполните следующие действия, чтобы выполнить сценарий, который создает запросы Hive для экспорта определений объектов из хранилище метаданных.

Кластеры HDInsight 3,6 и 4,0 должны использовать одну и ту же учетную запись хранения.

> [!NOTE]
>
> * В случае таблиц ACID будет создана новая копия данных, расположенных под таблицей.
>
> * Этот сценарий поддерживает перенос только баз данных Hive, таблиц и секций. Другие объекты метаданных, такие как представления, UDF и табличные ограничения, должны быть скопированы вручную.
>
> * После завершения этого сценария предполагается, что старый кластер больше не будет использоваться для доступа к любой из таблиц или баз данных, на которые ссылается скрипт.
>
> * Все управляемые таблицы станут транзакционными в HDInsight 4,0. При необходимости обеспечьте нетранзакционную таблицу, экспортируя данные во внешнюю таблицу со свойством external. Table. очисткой = ' true '. Например,
>
>    ```SQL
>    create table tablename_backup like tablename;
>    insert overwrite table tablename_backup select * from tablename;
>    create external table tablename_tmp like tablename;
>    insert overwrite table tablename_tmp select * from tablename;
>    alter table tablename_tmp set tblproperties('external.table.purge'='true');
>    drop table tablename;
>    alter table tablename_tmp rename to tablename;
>    ```

1. Подключитесь к кластеру HDInsight 3,6 с помощью [клиента Secure Shell (SSH)](../hdinsight-hadoop-linux-use-ssh-unix.md).

1. В открытом сеансе SSH Скачайте следующий файл скрипта, чтобы создать файл с именем **аллтаблес. HQL**.

    ```bash
    wget https://hdiconfigactions.blob.core.windows.net/hivemetastoreschemaupgrade/exporthive_hdi_3_6.sh
    chmod 755 exporthive_hdi_3_6.sh
    ```

    * Для обычного кластера HDInsight без ESP просто выполните `exporthive_hdi_3_6.sh`.

    * Для кластера с ESP kinit и измените аргументы на Beeline: выполните следующую команду, определив пользователя и домен для пользователя Azure AD с полными разрешениями Hive.

        ```bash
        USER="USER"  # replace USER
        DOMAIN="DOMAIN"  # replace DOMAIN
        DOMAIN_UPPER=$(printf "%s" "$DOMAIN" | awk '{ print toupper($0) }')
        kinit "$USER@$DOMAIN_UPPER"
        ```

        ```bash
        hn0=$(grep hn0- /etc/hosts | xargs | cut -d' ' -f4)
        BEE_CMD="beeline -u 'jdbc:hive2://$hn0:10001/default;principal=hive/_HOST@$DOMAIN_UPPER;auth-kerberos;transportMode=http' -n "$USER@$DOMAIN" --showHeader=false --silent=true --outputformat=tsv2 -e"
        ./exporthive_hdi_3_6.sh "$BEE_CMD"
        ```

1. Закройте сеанс SSH. Затем введите команду scp, чтобы загрузить **аллтаблес. HQL** локально.

    ```bash
    scp sshuser@CLUSTERNAME-ssh.azurehdinsight.net:alltables.hql c:/hdi
    ```

1. Отправьте **аллтаблес. HQL** в *Новый* кластер HDInsight.

    ```bash
    scp c:/hdi/alltables.hql sshuser@CLUSTERNAME-ssh.azurehdinsight.net:/home/sshuser/
    ```

1. Затем с помощью SSH подключитесь к *новому* кластеру HDInsight 4,0. Выполните следующий код из сеанса SSH в этом кластере:

    Без ESP:

    ```bash
    beeline -u "jdbc:hive2://localhost:10001/;transportMode=http" -f alltables.hql
    ```

    С помощью ESP:

    ```bash
    USER="USER"  # replace USER
    DOMAIN="DOMAIN"  # replace DOMAIN
    DOMAIN_UPPER=$(printf "%s" "$DOMAIN" | awk '{ print toupper($0) }')
    kinit "$USER@$DOMAIN_UPPER"
    ```

    ```bash
    hn0=$(grep hn0- /etc/hosts | xargs | cut -d' ' -f4)
    beeline -u "jdbc:hive2://$hn0:10001/default;principal=hive/_HOST@$DOMAIN_UPPER;auth-kerberos;transportMode=http" -n "$USER@$DOMAIN" -f alltables.hql
    ```

Средство после обновления для внешнего хранилище метаданных миграции не применяется здесь, так как таблицы, не являющиеся неacid из HDInsight 3,6, преобразуются в управляемые таблицы в HDInsight 4,0.

> [!Important]  
> Управляемые таблицы в HDInsight 4,0 (включая таблицы, перенесенные из 3,6) не должны быть доступны другим службам или приложениям, включая кластеры HDInsight 3,6.

## <a name="secure-hive-across-hdinsight-versions"></a>Защита Hive в версиях HDInsight

Начиная с HDInsight 3,6, HDInsight интегрируется с Azure Active Directory с помощью HDInsight Корпоративный пакет безопасности (ESP). ESP использует Kerberos и Apache Ranger для управления разрешениями конкретных ресурсов в кластере. Политики Ranger, развернутые с помощью Hive в HDInsight 3,6, можно перенести в HDInsight 4,0, выполнив следующие действия:

1. Перейдите на панель Service Manager Ranger в кластере HDInsight 3,6.
2. Перейдите к политике с именем **Hive** и экспортируйте политику в JSON-файл.
3. Убедитесь, что все пользователи, которые ссылаются на экспортированную политику JSON, существуют в новом кластере. Если пользователь упоминается в политике JSON, но не существует в новом кластере, добавьте пользователя в новый кластер или удалите ссылку из политики.
4. Перейдите на панель **Service Manager Ranger** в кластере HDInsight 4,0.
5. Перейдите к политике с именем **Hive** и импортируйте Ranger политику JSON из шага 2.

## <a name="check-compatibility-and-modify-codes-as-needed-in-test-app"></a>Проверка совместимости и изменение кодов при необходимости в тестовом приложении

При переносе рабочих нагрузок, таких как существующие программы и запросы, просмотрите заметки о выпуске и документацию, чтобы внести изменения и применить изменения по мере необходимости. Если кластер HDInsight 3,6 использует общий ресурс Spark и хранилище метаданных Hive, требуется [Дополнительная настройка с использованием соединителя хранилища Hive](./apache-hive-warehouse-connector.md) .

## <a name="deploy-new-app-for-production"></a>Развертывание нового приложения для рабочей среды

Чтобы переключиться на новый кластер, например, можно установить новое клиентское приложение и использовать его в качестве новой рабочей среды, или обновить существующее клиентское приложение и переключиться на HDInsight 4,0.

## <a name="switch-hdinsight-40-to-the-production"></a>Переключение HDInsight 4,0 в рабочую среду

Если во время тестирования были созданы различия в хранилище метаданных, необходимо обновить изменения непосредственно перед переключением. В этом случае можно экспортировать & импортировать хранилище метаданных, а затем снова выполнить обновление.

## <a name="remove-the-old-production"></a>Удаление старого рабочего

Убедившись, что выпуск завершен и полностью работоспособ, можно удалить версию 3,6 и предыдущую хранилище метаданных. Убедитесь, что все перенесено перед удалением среды.

## <a name="query-execution-across-hdinsight-versions"></a>Выполнение запросов в версиях HDInsight

Существует два способа выполнения и отладки запросов Hive/LLAP в кластере HDInsight 3,6. Хивекли предоставляет возможности командной строки, а представление представления/Hive Tez предоставляет рабочий процесс на основе графического пользовательского интерфейса.

В HDInsight 4,0 Хивекли был заменен на Beeline. Хивекли — это Thrift клиент для Hiveserver 1, а Beeline — клиент JDBC, предоставляющий доступ к Hiveserver 2. Beeline также можно использовать для подключения к любой другой конечной точке базы данных, совместимой с JDBC. Beeline доступен в HDInsight 4,0 без необходимости установки.

В HDInsight 3,6 клиент GUI для взаимодействия с сервером Hive является представлением Hive Ambari. HDInsight 4,0 не поставляется с Ambari представлением. Мы предоставили нашим клиентам способ использования Data Analytics Studio (DAS), который не является основной службой HDInsight. DAS не поставляется с кластерами HDInsight и не является официально поддерживаемым пакетом. Однако DAS можно установить в кластере с помощью [действия сценария](../hdinsight-hadoop-customize-cluster-linux.md) следующим образом:

|Свойство | Применение |
|---|---|
|Тип скрипта|- Custom|
|Название|DAS|
|URI bash-скрипта|`https://hdiconfigactions.blob.core.windows.net/dasinstaller/LaunchDASInstaller.sh`|
|Типы узлов|Head|

Подождите 10 – 15 минут, а затем запустите Data Analytics Studio с помощью этого URL `https://CLUSTERNAME.azurehdinsight.net/das/`-адреса:.

Перед доступом к DAS может потребоваться обновить пользовательский интерфейс Ambari и (или) перезапустить все компоненты Ambari.

После установки DAS, если вы не видите запросы, которые вы выполняли в средстве просмотра запросов, выполните следующие действия.

1. Задайте конфигурации для Hive, TEZ и DAS, как описано в [этом разделе руководства по устранению неполадок установки Das](https://docs.hortonworks.com/HDPDocuments/DAS/DAS-1.2.0/troubleshooting/content/das_queries_not_appearing.html).
2. Убедитесь, что следующие конфигурации каталога службы хранилища Azure являются страничными BLOB-объектами и перечислены в разделе `fs.azure.page.blob.dirs`:
    * `hive.hook.proto.base-directory`
    * `tez.history.logging.proto-base-dir`
3. Перезапустите HDFS, Hive, TEZ и DAS в обоих головных узлах.

## <a name="next-steps"></a>Дальнейшие действия

* [Объявление о HDInsight 4,0](../hdinsight-version-release.md)
* [HDInsight 4,0. подробное углубление](https://azure.microsoft.com/blog/deep-dive-into-azure-hdinsight-4-0/)
* [Таблицы ACID Hive 3](https://docs.hortonworks.com/HDPDocuments/HDP3/HDP-3.1.0/using-hiveql/content/hive_3_internals.html)
