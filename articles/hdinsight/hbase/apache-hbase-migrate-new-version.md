---
title: Перенос кластера HBase в новую версию в Azure HDInsight
description: Сведения о перемещении данных кластеров HBase в новую версию.
author: ashishthaps
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 05/06/2019
ms.author: ashishth
ms.openlocfilehash: a152b815daeefa4c199af9b159eee8e5783971e2
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/13/2019
ms.locfileid: "65143319"
---
# <a name="migrate-an-apache-hbase-cluster-to-a-new-version"></a>Перенос данных кластера Apache HBase в новую версию

В этой статье рассматриваются шаги, необходимые для обновления кластера Apache HBase в Azure HDInsight до более новой версии.

> [!NOTE]  
> Время простоя при обновлении должно быть минимальным, порядка нескольких минут. Простой связан с действиями по записи всех данных в памяти на диск, а также настройкой и перезапуском служб в новом кластере. Результаты будут различаться в зависимости от количества узлов, объема данных и других переменных.

## <a name="review-apache-hbase-compatibility"></a>Проверка совместимости Apache HBase

Перед обновлением Apache HBase убедитесь, что версии HBase в исходном и конечном кластерах совместимы. Дополнительные сведения см. в статье [Что представляют собой компоненты и версии Apache Hadoop, доступные в HDInsight?](../hdinsight-component-versioning.md)

> [!NOTE]  
> Настоятельно рекомендуем изучить матрицу совместимости версий в [руководстве по HBase](https://hbase.apache.org/book.html#upgrading).

Ниже приведен пример матрицы совместимости версий. Y — совместимость, N — возможная несовместимость:

| Тип совместимости | Основной номер версии| Дополнительный номер версии | Исправление |
| --- | --- | --- | --- |
| Совместимость клиента и сервера на уровне передачи данных | Нет | Да | Да |
| Совместимость серверов | Нет | Да | Да |
| Совместимость форматов файлов | Нет | Да | Да |
| Совместимость API клиента | Нет | Да | Да |
| Совместимость клиента на уровне двоичного кода | Нет | Нет | Да |
| **Ограниченная совместимость API на стороне сервера** |  |  |  |
| Stable | Нет | Да | Да |
| Развитие | Нет | Нет | Да |
| Работает неустойчиво | Нет | Нет | Нет |
| Совместимость зависимостей | Нет | Да | Да |
| Совместимость операций | Нет | Нет | Да |

> [!NOTE]  
> Все критические несовместимости должны быть описаны в заметках о выпуске версии HBase.

## <a name="upgrade-with-same-apache-hbase-major-version"></a>Обновление с тем же основным номером версии Apache HBase

Чтобы обновить кластер Apache HBase на HDInsight Azure, выполните следующие действия:

1. Убедитесь, что приложение совместимо с новой версией, обратившись к матрице совместимости HBase и заметкам о выпуске. Протестируйте приложение в кластере, запустив целевые версии HDInsight и HBase.

2. [Настройте новый кластер назначения HDInsight](../hdinsight-hadoop-provision-linux-clusters.md), используя ту же учетную запись хранения, но другое имя контейнера:

    ![Используйте ту же учетную запись хранения, но создайте другой контейнер](./media/apache-hbase-migrate-new-version/same-storage-different-container.png)

3. Очистите данные исходного кластера HBase, которая представляет кластер, который вы обновляете. HBase записывает входящие данные в хранилище в памяти, которое называется _memstore_. Когда memstore достигает определенного размера, HBase записать его на диск для долговременного хранения в учетной записи хранения кластера. При удалении старого кластера memstore очищается, что может привести к потере данных. Чтобы вручную перенести данные memstore для каждой таблицы на диск, выполните следующий скрипт. Последнюю версию этого скрипта можно найти на [GitHub](https://raw.githubusercontent.com/Azure/hbase-utils/master/scripts/flush_all_tables.sh) для Azure.

    ```bash
    #!/bin/bash
    
    #-------------------------------------------------------------------------------#
    # SCRIPT TO FLUSH ALL HBASE TABLES.
    #-------------------------------------------------------------------------------#
    
    LIST_OF_TABLES=/tmp/tables.txt
    HBASE_SCRIPT=/tmp/hbase_script.txt
    TARGET_HOST=$1
    
    usage ()
    {
        if [[ "$1" == "-h" ]] || [[ "$1" == "--help" ]]
        then
            cat << ...
    
    Usage: 
    
    $0 [hostname]
    
    Providing hostname is optional and not required when the script is executed within HDInsight cluster with access to 'hbase shell'.
    
    However hostname should be provided when executing the script as a script-action from HDInsight portal.
    
    For Example:
    
        1.  Executing script inside HDInsight cluster (where 'hbase shell' is 
            accessible):
    
            $0 
    
            [No need to provide hostname]
    
        2.  Executing script from HDinsight Azure portal:
    
            Provide Script URL.
    
            Provide hostname as a parameter (i.e. hn0, hn1 or wn2 etc.).
    ...
            exit
        fi
    }
    
    validate_machine ()
    {
        THIS_HOST=`hostname`
    
        if [[ ! -z "$TARGET_HOST" ]] && [[ $THIS_HOST  != $TARGET_HOST* ]]
        then
            echo "[INFO] This machine '$THIS_HOST' is not the right machine ($TARGET_HOST) to execute the script."
            exit 0
        fi
    }
    
    get_tables_list ()
    {
    hbase shell << ... > $LIST_OF_TABLES 2> /dev/null
        list
        exit
    ...
    }
    
    add_table_for_flush ()
    {
        TABLE_NAME=$1
        echo "[INFO] Adding table '$TABLE_NAME' to flush list..."
        cat << ... >> $HBASE_SCRIPT
            flush '$TABLE_NAME'
    ...
    }
    
    clean_up ()
    {
        rm -f $LIST_OF_TABLES
        rm -f $HBASE_SCRIPT
    }
    
    ########
    # MAIN #
    ########
    
    usage $1
    
    validate_machine
    
    clean_up
    
    get_tables_list
    
    START=false
    
    while read LINE 
    do 
        if [[ $LINE == TABLE ]] 
        then
            START=true
            continue
        elif [[ $LINE == *row*in*seconds ]]
        then
            break
        elif [[ $START == true ]]
        then
            add_table_for_flush $LINE
        fi
    
    done < $LIST_OF_TABLES
    
    cat $HBASE_SCRIPT
    
    hbase shell $HBASE_SCRIPT << ... 2> /dev/null
    exit
    ...
    
    ```
    
4. Остановите прием данных в старом кластере HBase.
5. Чтобы убедиться, что все последние данные в хранилище memstore записаны на диск, еще раз выполните предыдущий скрипт.
6. Войдите в [Apache Ambari](https://ambari.apache.org/) на старом кластере (https://OLDCLUSTERNAME.azurehdidnsight.net) и остановите службы HBase. Когда будет предложено подтвердить, что вы хотите остановить службы, установите этот флажок, чтобы включить режим обслуживания для HBase. Подробные сведения о подключении к платформе Ambari и ее использовании см. в статье [Управление кластерами HDInsight с помощью веб-интерфейса Ambari](../hdinsight-hadoop-manage-ambari.md).

    ![В Ambari, выберите службы > HBase > Остановить в области действий службы](./media/apache-hbase-migrate-new-version/stop-hbase-services.png)

    ![Проверьте, установлен ли флажок Turn On Maintenance Mode for HBase (Включить режим обслуживания HBase), а затем подтвердите остановку.](./media/apache-hbase-migrate-new-version/turn-on-maintenance-mode.png)

7. Войдите в Ambari на новом кластере HDInsight. Измените параметр HDFS `fs.defaultFS`, чтобы указать имя контейнера, используемого в исходном кластере. Этот параметр находится в разделе **HDFS > Configs (Конфигурации) > Advanced (Расширенные) > Advanced core-site (Расширенный основной сайт)** .

    ![В Ambari, выберите службы > HDFS > Configs > Дополнительно](./media/apache-hbase-migrate-new-version/hdfs-advanced-settings.png)

    ![Изменение имени контейнера в Ambari](./media/apache-hbase-migrate-new-version/change-container-name.png)

8. **Если вы не используете кластеров HBase с помощью функции расширенного записывает, пропустите этот шаг. Он необходим только для кластеров HBase с помощью функции расширенного записывает.**
   
   Изменение `hbase.rootdir` путь к контейнеру исходного кластера.

    ![В Ambari измените имя контейнера для HBase rootdir](./media/apache-hbase-migrate-new-version/change-container-name-for-hbase-rootdir.png)
1. При обновлении HDInsight 3.6 4.0, выполните следующие действия, в противном случае перейдите к шагу 10.
    1. Перезапустите все необходимые службы в Ambari, выбрав **служб** > **перезапустите все необходимые**.
    1. Остановите службы HBase.
    1. SSH на узел Zookeeper и выполните [zkCli](https://github.com/go-zkcli/zkcli) команда `rmr /hbase-unsecure` удаляемый znode корневой HBase из Zookeeper.
    1. Перезапустите HBase.
1. При обновлении с любой другой HDInsight помимо 4.0, выполните следующие действия.
    1. Сохраните изменения.
    1. Перезапустите все необходимые службы, как указано в Ambari.
1. Направьте приложение в новый кластер.

    > [!NOTE]  
    > Статическое DNS-имя вашего приложения изменится при обновлении. Вместо того чтобы жестко запрограммировать это DNS-имя, можно настроить запись CNAME в параметрах DNS доменного имени, которая указывает на имя кластера. Другой вариант — использовать файл конфигурации для приложения, которое можно обновить без повторного развертывания.

12. Запустите прием данных, чтобы убедиться, что все работает правильно.
13. Если новый кластер подходит, удалите исходный кластер.

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения о [Apache HBase](https://hbase.apache.org/) и обновлении кластеров HDInsight см. в следующих статьях:

* [Обновление кластера HDInsight до более новой версии](../hdinsight-upgrade-cluster.md)
* [Управление кластерами HDInsight с помощью веб-интерфейса Apache Ambari](../hdinsight-hadoop-manage-ambari.md)
* [Компоненты и версии Apache Hadoop](../hdinsight-component-versioning.md)
* [Оптимизация конфигураций с использованием Apache Ambari](../hdinsight-changing-configs-via-ambari.md#apache-hbase-optimization-with-the-ambari-web-ui)
