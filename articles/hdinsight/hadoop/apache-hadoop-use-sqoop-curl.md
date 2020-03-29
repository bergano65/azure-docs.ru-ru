---
title: Используйте Curl для экспорта данных с Apache Sqoop в Azure HDInsight
description: Узнайте, как удаленно отправлять задания Apache Sqoop в Azure HDInsight с помощью Curl.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 01/06/2020
ms.openlocfilehash: da29785547d1b6eb4b38d07f020ba885dc5137ea
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75767592"
---
# <a name="run-apache-sqoop-jobs-in-hdinsight-with-curl"></a>Выполнить Apache Sqoop рабочих мест в HDInsight с Curl

[!INCLUDE [sqoop-selector](../../../includes/hdinsight-selector-use-sqoop.md)]

Узнайте, как использовать Curl для запуска заданий Apache Sqoop в кластере Apache Hadoop в HDInsight. В этой статье показано, как экспортировать данные из Хранилища Azure и импортировать их в базу данных сервера с помощью Curl. Эта статья является продолжением [использования Apache Sqoop с Hadoop в HDInsight](./hdinsight-use-sqoop.md).

Curl используется для демонстрации возможностей взаимодействия с HDInsight с помощью необработанных HTTP-запросов для выполнения и мониторинга заданий Sqoop, а также получения их результатов. Для этого используется REST API для WebHCat (прежнее название — Templeton), предоставляемый кластером HDInsight.

## <a name="prerequisites"></a>Предварительные требования

* Завершение [настройки тестовой среды](./hdinsight-use-sqoop.md#create-cluster-and-sql-database) от [Use Apache Sqoop с Hadoop в HDInsight](./hdinsight-use-sqoop.md).

* Клиент, задающего запрос в базу данных Azure S'L. Рассмотрите возможность использования [студии управления серверами s'L](../../sql-database/sql-database-connect-query-ssms.md) или [Visual Studio Code.](../../sql-database/sql-database-connect-query-vscode.md)

* [Curl](https://curl.haxx.se/). Curl — это средство для передачи данных в кластер HDInsight или из него.

* [jq](https://stedolan.github.io/jq/). Служебная программа jq используется для обработки данных JSON, возвращаемых запросами REST.

* Знакомство с Sqoop. Для получения дополнительной информации [см.](https://sqoop.apache.org/docs/1.4.7/SqoopUserGuide.html)

## <a name="submit-apache-sqoop-jobs-by-using-curl"></a>Отправка заданий Sqoop с помощью cURL

Используйте Curl для экспорта данных с помощью заданий Apache Sqoop от Azure Storage до S'L Server.

> [!NOTE]  
> При использовании Curl или любых других средств связи REST с WebHCat нужно выполнять аутентификацию запросов с помощью пароля и имени пользователя администратора кластера HDInsight. Имя кластера необходимо также использовать в составе универсального кода ресурса (URI), используемого для отправки запросов на сервер.

Для команд в этом разделе `USERNAME` замените пользователя для проверки подлинности `PASSWORD` кластера и замените пароль для учетной записи пользователя. Замените `CLUSTERNAME` именем кластера.

REST API защищен с помощью [обычной проверки подлинности](https://en.wikipedia.org/wiki/Basic_access_authentication). Чтобы обеспечить безопасную отправку учетных данных на сервер, все запросы следует отправлять с помощью протокола HTTPS.

1. Для удобства использования установите переменные ниже. Этот пример основан на среде Windows, пересматривайте по мере необходимости для вашей среды.

    ```cmd
    set CLUSTERNAME=
    set USERNAME=admin
    set PASSWORD=
    set SQLDATABASESERVERNAME=
    set SQLDATABASENAME=
    set SQLPASSWORD=
    set SQLUSER=sqluser
    ```

1. Используйте следующую команду в командной строке, чтобы проверить возможность подключения к кластеру HDInsight:

    ```cmd
    curl -u %USERNAME%:%PASSWORD% -G https://%CLUSTERNAME%.azurehdinsight.net/templeton/v1/status
    ```

    Вы должны получить ответ, аналогичный показанному ниже:

    ```json
    {"status":"ok","version":"v1"}
    ```

1. Чтобы отправить задание Sqoop, воспользуйтесь следующей командой:

    ```cmd
    curl -u %USERNAME%:%PASSWORD% -d user.name=%USERNAME% -d command="export --connect jdbc:sqlserver://%SQLDATABASESERVERNAME%.database.windows.net;user=%SQLUSER%@%SQLDATABASESERVERNAME%;password=%PASSWORD%;database=%SQLDATABASENAME% --table log4jlogs --export-dir /example/data/sample.log --input-fields-terminated-by \0x20 -m 1" -d statusdir="wasb:///example/data/sqoop/curl" https://%CLUSTERNAME%.azurehdinsight.net/templeton/v1/sqoop
    ```

    Ниже приведены параметры, используемые в этой команде:

   * **-d** - `-G` Поскольку запрос не используется, запрос по умолчанию по умолчанию к методу POST. `-d` задает значения данных, отправляемые в запросе.

       * **user.name** — пользователь, выполняющий команду.

       * **command** — выполняемая команда Sqoop.

       * **statusdir** — каталог, в который будет записано состояние этого задания.

     Эта команда вернет идентификатор задания, который может быть использован для проверки состояния задания.

       ```json
       {"id":"job_1415651640909_0026"}
       ```

1. Чтобы проверить состояние задания, используйте следующую команду. Замените `JOBID` значением, возвращенным на предыдущем шаге. Например, если значение `{"id":"job_1415651640909_0026"}`возврата `JOBID` было, `job_1415651640909_0026`то было бы . Пересмотрите расположение `jq` по мере необходимости.

    ```cmd
    set JOBID=job_1415651640909_0026

    curl -G -u %USERNAME%:%PASSWORD% -d user.name=%USERNAME% https://%CLUSTERNAME%.azurehdinsight.net/templeton/v1/jobs/%JOBID% | C:\HDI\jq-win64.exe .status.state
    ```

    Если задание завершено, у него будет состояние **SUCCEEDED**(Успешно).

   > [!NOTE]  
   > Этот запрос Curl возвращает документ нотации объектов JavaScript с информацией о задании. При этом jq используется только для получения значения состояния.

1. После изменения состояния задания на **SUCCEEDED** результаты задания можно получить из хранилища больших двоичных объектов Azure. Параметр `statusdir`, передаваемый с помощью запроса, содержит расположение выходного файла. В данном случае это `wasb:///example/data/sqoop/curl`. Этот адрес сохраняет выход задания `example/data/sqoop/curl` в каталоге контейнера для хранения данных по умолчанию, используемого кластером HDInsight.

    Портал Azure можно использовать для доступа к большим двоичным объектам stderr и stdout.

1. Чтобы проверить, что данные были экспортированы, используйте следующие запросы от вашего клиента S'L для просмотра экспортированных данных:

    ```sql
    SELECT COUNT(*) FROM [dbo].[log4jlogs] WITH (NOLOCK);
    SELECT TOP(25) * FROM [dbo].[log4jlogs] WITH (NOLOCK);
    ```

## <a name="limitations"></a>Ограничения

* Массовый экспорт - С linux-на основе HDInsight, разъем Sqoop, используемый для экспорта данных в Microsoft S'L Server или базу данных Azure S'L в настоящее время не поддерживает объемные вставки.
* Пакетная обработка: при использовании HDInsight на основе Linux, когда для выполнения вставок применяется переключатель `-batch` , Sqoop выполняет несколько вставок вместо пакетной обработки операций вставки.

## <a name="summary"></a>Сводка

Как показано в этом документе, для запуска, мониторинга и просмотра результатов выполнения заданий Sqoop в кластере HDInsight можно использовать необработанные HTTP-запросы.

Дополнительную информацию об интерфейсе REST, используемом в этой статье, см. в <a href="https://sqoop.apache.org/docs/1.99.3/RESTAPI.html" target="_blank">справочнике по REST API Sqoop</a>.

## <a name="next-steps"></a>Дальнейшие действия

[Использование Apache Sqoop с Hadoop в HDInsight](hdinsight-use-sqoop.md)

Другие статьи об HDInsight, в которых используется curl:

* [Создание кластеров Apache Hadoop с помощью REST API Azure](../hdinsight-hadoop-create-linux-clusters-curl-rest.md)
* [Выполнение запросов Apache Hive в Apache Hadoop в HDInsight с использованием REST](apache-hadoop-use-hive-curl.md)
* [Запуск заданий MapReduce в среде Apache Hadoop, размещенной в HDInsight, с помощью REST](apache-hadoop-use-mapreduce-curl.md)