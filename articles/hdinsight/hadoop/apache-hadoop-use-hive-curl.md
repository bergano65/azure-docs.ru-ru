---
title: Использование Apache Hadoop Hive с Curl в HDInsight — Azure
description: Узнайте, как отправлять задания Apache Pig в HDInsight с помощью Curl.
services: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 04/23/2018
ms.author: hrasheed
ms.openlocfilehash: c5bf69a2ac6a70410339a5696da53169ca87170f
ms.sourcegitcommit: c37122644eab1cc739d735077cf971edb6d428fe
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/14/2018
ms.locfileid: "53407224"
---
# <a name="run-apache-hive-queries-with-apache-hadoop-in-hdinsight-using-rest"></a>Выполнение запросов Apache Hive в Apache Hadoop в HDInsight с использованием REST

[!INCLUDE [hive-selector](../../../includes/hdinsight-selector-use-hive.md)]

Узнайте, как с помощью REST API WebHCat выполнять запросы Apache Hive с Apache Hadoop в кластере Azure HDInsight.

## <a name="prerequisites"></a>Предварительные требования

* Hadoop в кластере HDInsight версии 3.4 или выше на платформе Linux.

  > [!IMPORTANT]  
  > Linux — это единственная операционная система, используемая для работы с HDInsight 3.4 или более поздних версий. Дополнительные сведения см. в разделе [Приближается дата прекращения сопровождения HDI версии 3.3](../hdinsight-component-versioning.md#hdinsight-windows-retirement).

* Клиент REST. В этом документе используются примеры для Windows PowerShell и [Curl](https://curl.haxx.se/).

    > [!NOTE]  
    > Azure PowerShell предоставляет специальные командлеты для работы с Hive в HDInsight. Дополнительные сведения см. в статье [Выполнение запросов Apache Hive с помощью PowerShell](apache-hadoop-use-hive-powershell.md).

В этом документе также используются Windows PowerShell и [Jq](https://stedolan.github.io/jq/) для обработки данных JSON, возвращаемых запросами REST.

## <a id="curl"></a>Выполнение запроса Hive

> [!NOTE]  
> При использовании Curl или любых других средств связи REST с WebHCat нужно выполнять аутентификацию запросов с помощью пароля и имени пользователя администратора кластера HDInsight.
>
> REST API защищен с помощью [обычной проверки подлинности](https://en.wikipedia.org/wiki/Basic_access_authentication). Чтобы обеспечить безопасную отправку учетных данных на сервер, все запросы следует отправлять с помощью протокола HTTPS.

1. Чтобы задать имя для входа в кластер, которое используют скрипты в этом документе, используйте одну из следующих команд:

    ```bash
    read -p "Enter your cluster login account name: " LOGIN
    ```

    ```powershell
    $creds = Get-Credential -UserName admin -Message "Enter the cluster login name and password"
    ```

2. Чтобы задать имя кластера, используйте одну из следующих команд:

    ```bash
    read -p "Enter the HDInsight cluster name: " CLUSTERNAME
    ```

    ```powershell
    $clusterName = Read-Host -Prompt "Enter the HDInsight cluster name"
    ```

3. Чтобы убедиться, что можно подключиться к кластеру HDInsight, используйте одну из следующих команд:

    ```bash
    curl -u $LOGIN -G https://$CLUSTERNAME.azurehdinsight.net/templeton/v1/status)
    ```
    
    ```powershell
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/templeton/v1/status" `
       -Credential $creds `
       -UseBasicParsing
    $resp.Content
    ```

    Вы должны получить ответ, аналогичный показанному ниже тексту.

    ```json
    {"status":"ok","version":"v1"}
    ```

    Ниже приведены параметры, используемые в этой команде:

    * `-u` — имя пользователя и пароль, используемый для проверки подлинности запроса.
    * `-G` — указывает, что этот запрос является операцией GET.

   Начало URL-адреса `https://$CLUSTERNAME.azurehdinsight.net/templeton/v1` одинаковое для всех запросов. Путь `/status` указывает, что по запросу серверу должно быть возвращено состояние WebHCat (другое название — Templeton). Используя следующую команду, можно также запросить версию Hive:

    ```bash
    curl -u $LOGIN -G https://$CLUSTERNAME.azurehdinsight.net/templeton/v1/version/hive
    ```

    ```powershell
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/templeton/v1/version/hive" `
       -Credential $creds `
       -UseBasicParsing
    $resp.Content
    ```

    Этот запрос возвращает ответ, аналогичный показанному ниже тексту.

    ```json
        {"module":"hive","version":"0.13.0.2.1.6.0-2103"}
    ```

4. Используйте следующую команду, чтобы создать таблицу **log4jLogs**.

    ```bash
    JOBID=`curl -s -u $LOGIN -d user.name=$LOGIN -d execute="set+hive.execution.engine=tez;DROP+TABLE+log4jLogs;CREATE+EXTERNAL+TABLE+log4jLogs(t1+string,t2+string,t3+string,t4+string,t5+string,t6+string,t7+string)+ROW+FORMAT+DELIMITED+FIELDS+TERMINATED+BY+' '+STORED+AS+TEXTFILE+LOCATION+'/example/data/';SELECT+t4+AS+sev,COUNT(*)+AS+count+FROM+log4jLogs+WHERE+t4+=+'[ERROR]'+AND+INPUT__FILE__NAME+LIKE+'%25.log'+GROUP+BY+t4;" -d statusdir="/example/rest" https://$CLUSTERNAME.azurehdinsight.net/templeton/v1/hive | jq .id`
    echo $JOBID
    ```

    ```powershell
    $reqParams = @{"user.name"="admin";"execute"="set hive.execution.engine=tez;DROP TABLE log4jLogs;CREATE EXTERNAL TABLE log4jLogs(t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string) ROW FORMAT DELIMITED BY ' ' STORED AS TEXTFILE LOCATION '/example/data/;SELECT t4 AS sev,COUNT(*) AS count FROM log4jLogs WHERE t4 = '[ERROR]' GROUP BY t4;";"statusdir"="/example/rest"}
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/templeton/v1/hive" `
       -Credential $creds `
       -Body $reqParams `
       -Method POST `
       -UseBasicParsing
    $jobID = (ConvertFrom-Json $resp.Content).id
    $jobID
    ```

    Этот запрос использует метод POST, который отправляет данные как часть запроса в REST API. Следующие значения данных отправляются с запросом:

     * `user.name` — пользователь, выполняющий команду.
     * `execute` — операторы HiveQL, которые необходимо выполнить.
     * `statusdir` — каталог, в который будет записано состояние этого задания.

   Эти операторы выполняют следующие действия:
   
   * `DROP TABLE` — если таблица уже существует, она будет удалена.
   * `CREATE EXTERNAL TABLE` — создает "внешнюю" таблицу в Hive. Внешние таблицы хранят только определение таблицы в Hive. Данные остаются в исходном расположении.

     > [!NOTE]  
     > Внешние таблицы следует использовать, если исходные данные должны обновляться с использованием внешних источников. Например, процессом автоматизированной передачи данных или другой операцией MapReduce.
     >
     > Удаление внешней таблицы **не** приводит к удалению данных, будет удалено только определение таблицы.

   * `ROW FORMAT` — настройка форматирования данных. Поля всех журналов разделены пробелами.
   * `STORED AS TEXTFILE LOCATION` — указывает Hive расположение хранения данных (каталог example/data) и их формат (текст).
   * `SELECT`. Подсчитывает количество строк, в которых столбец **t4** содержит значение **[ERROR]**. Эта инструкция возвращает значение **3**, так как данное значение содержат три строки.

     > [!NOTE]  
     > Обратите внимание, что при использовании Curl пробелы между операторами HiveQL заменяются знаком `+`. Заключенные в кавычки значения, содержащие пробелы в качестве разделителя, заменять на `+`не нужно.

      Эта команда возвращает идентификатор задания, который может использоваться для проверки состояния задания.

5. Чтобы проверить состояние задания, используйте следующую команду.

    ```bash
    curl -G -u $LOGIN -d user.name=$LOGIN https://$CLUSTERNAME.azurehdinsight.net/templeton/v1/jobs/$JOBID | jq .status.state
    ```

    ```powershell
    $reqParams=@{"user.name"="admin"}
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/templeton/v1/jobs/$jobID" `
       -Credential $creds `
       -Body $reqParams `
       -UseBasicParsing
    # ConvertFrom-JSON can't handle duplicate names with different case
    # So change one to prevent the error
    $fixDup=$resp.Content.Replace("jobID","job_ID")
    (ConvertFrom-Json $fixDup).status.state
    ```

    Если задание завершено, оно будет в состоянии **SUCCEEDED** (Успешно).

6. После изменения состояния задания на **SUCCEEDED** результаты задания можно получить из хранилища больших двоичных объектов Azure. Параметр `statusdir`, передаваемый с помощью запроса, содержит расположение выходного файла. В данном случае это `/example/rest`. Этот адрес задает каталог `example/curl` для сохранения выходных данных, который размещен в хранилище по умолчанию для кластера.

    Вы можете вывести список этих файлов и скачать их с помощью [интерфейса командной строки Azure](https://docs.microsoft.com/cli/azure/install-azure-cli). Дополнительные сведения об использовании Azure CLI со службой хранилища Azure см. в документе [Использование Azure CLI со службой хранилища Azure](https://docs.microsoft.com/azure/storage/storage-azure-cli#create-and-manage-blobs).

## <a id="nextsteps"></a>Дальнейшие действия

Общая информация об использовании Hive в HDInsight:

* [Использование Apache Hive с Apache Hadoop в HDInsight](hdinsight-use-hive.md)

Дополнительная информация о других способах работы с Hadoop в HDInsight.

* [Использование Apache Pig с Apache Hadoop в HDInsight](hdinsight-use-pig.md)
* [Использование MapReduce в Apache Hadoop в HDInsight](hdinsight-use-mapreduce.md)

Если вы используете Tez с Hive, обратитесь к следующим документам для сведений об отладке:

* [Отладка заданий Apache Tez в HDInsight с помощью представлений Apache Ambari](../hdinsight-debug-ambari-tez-view.md)

Дополнительную информацию о REST API, используемом в этом документе, см. в [справочнике по WebHCat](https://cwiki.apache.org/confluence/display/Hive/WebHCat+Reference).

[azure-purchase-options]: https://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: https://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: https://azure.microsoft.com/pricing/free-trial/

[apache-tez]: https://tez.apache.org
[apache-hive]: https://hive.apache.org/
[apache-log4j]: https://en.wikipedia.org/wiki/Log4j
[hive-on-tez-wiki]: https://cwiki.apache.org/confluence/display/Hive/Hive+on+Tez
[import-to-excel]: https://azure.microsoft.com/documentation/articles/hdinsight-connect-excel-power-query/


[hdinsight-use-oozie]: hdinsight-use-oozie.md
[hdinsight-analyze-flight-data]: hdinsight-analyze-flight-delay-data.md




[hdinsight-provision]: hdinsight-hadoop-provision-linux-clusters.md
[hdinsight-submit-jobs]:submit-apache-hadoop-jobs-programmatically.md
[hdinsight-upload-data]: hdinsight-upload-data.md

[powershell-here-strings]: https://technet.microsoft.com/library/ee692792.aspx


