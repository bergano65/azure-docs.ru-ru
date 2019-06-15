---
title: Экспорт данных с помощью Apache Sqoop в HDInsight Azure с помощью Curl
description: Узнайте, как удаленно создавать задания Apache Sqoop в HDInsight с помощью Curl.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 04/15/2019
ms.openlocfilehash: 345f492c5b2c754cbbcfa150561ee06b5a4154a5
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/13/2019
ms.locfileid: "64718687"
---
# <a name="run-apache-sqoop-jobs-in-hdinsight-with-curl"></a>Выполнение заданий Apache Sqoop в HDInsight с помощью Curl
[!INCLUDE [sqoop-selector](../../../includes/hdinsight-selector-use-sqoop.md)]

Узнайте, как использовать Curl для запуска заданий Apache Sqoop в кластере Apache Hadoop в HDInsight. В этой статье показано, как экспортировать данные из службы хранилища Azure и импортировать его в базу данных SQL Server, с помощью Curl. Эта статья является продолжением [использование Apache Sqoop с Hadoop в HDInsight](./hdinsight-use-sqoop.md).

Curl используется для демонстрации возможностей взаимодействия с HDInsight с помощью необработанных HTTP-запросов для выполнения и мониторинга заданий Sqoop, а также получения их результатов. Для этого используется REST API для WebHCat (прежнее название — Templeton), предоставляемый кластером HDInsight.

## <a name="prerequisites"></a>Технические условия

* Завершение [Настройка тестовой среды](./hdinsight-use-sqoop.md#create-cluster-and-sql-database) из [использование Apache Sqoop с Hadoop в HDInsight](./hdinsight-use-sqoop.md).

* Клиент в базе данных Azure SQL. Рассмотрите возможность использования [SQL Server Management Studio](../../sql-database/sql-database-connect-query-ssms.md) или [Visual Studio Code](../../sql-database/sql-database-connect-query-vscode.md).

* [Curl](https://curl.haxx.se/). Curl — это средство для передачи данных в кластер HDInsight или из него.

* [jq](https://stedolan.github.io/jq/). Служебная программа jq используется для обработки данных JSON, возвращаемых запросами REST.

## <a name="submit-apache-sqoop-jobs-by-using-curl"></a>Отправка заданий Sqoop с помощью cURL

Используйте Curl, чтобы экспортировать данные с помощью заданий Apache Sqoop из службы хранилища Azure для SQL Server.

> [!NOTE]  
> При использовании Curl или любых других средств связи REST с WebHCat нужно выполнять аутентификацию запросов с помощью пароля и имени пользователя администратора кластера HDInsight. Имя кластера необходимо также использовать в составе универсального кода ресурса (URI), используемого для отправки запросов на сервер.

Команды в этом разделе, замените `USERNAME` с пользователем для аутентификации в кластере и замены `PASSWORD` с паролем учетной записи пользователя. Замените `CLUSTERNAME` именем кластера.
 
REST API защищен с помощью [обычной проверки подлинности](https://en.wikipedia.org/wiki/Basic_access_authentication). Чтобы обеспечить безопасную отправку учетных данных на сервер, все запросы следует отправлять с помощью протокола HTTPS.

1. Используйте следующую команду в командной строке, чтобы проверить возможность подключения к кластеру HDInsight:

    ```cmd
    curl -u USERNAME:PASSWORD -G https://CLUSTERNAME.azurehdinsight.net/templeton/v1/status
    ```

    Вы должны получить ответ, аналогичный показанному ниже:

    ```json
    {"status":"ok","version":"v1"}
    ```

2. Замените `SQLDATABASESERVERNAME`, `USERNAME@SQLDATABASESERVERNAME`, `PASSWORD`, `SQLDATABASENAME` с соответствующими значениями из предварительных требований. Чтобы отправить задание Sqoop, воспользуйтесь следующей командой:

    ```cmd
    curl -u USERNAME:PASSWORD -d user.name=USERNAME -d command="export --connect jdbc:sqlserver://SQLDATABASESERVERNAME.database.windows.net;user=USERNAME@SQLDATABASESERVERNAME;password=PASSWORD;database=SQLDATABASENAME --table log4jlogs --export-dir /example/data/sample.log --input-fields-terminated-by \0x20 -m 1" -d statusdir="wasb:///example/data/sqoop/curl" https://CLUSTERNAME.azurehdinsight.net/templeton/v1/sqoop
    ```

    Ниже приведены параметры, используемые в этой команде:

   * **-d** — так как `-G` не используется, в запросе по умолчанию используется метод POST. `-d` задает значения данных, отправляемые в запросе.

       * **user.name** — пользователь, выполняющий команду.

       * **command** — выполняемая команда Sqoop.

       * **statusdir** — каталог, в который будет записано состояние этого задания.

     Эта команда возвратит идентификатор задания, который может использоваться для проверки состояния задания.

       ```json
       {"id":"job_1415651640909_0026"}
       ```

3. Чтобы проверить состояние задания, используйте следующую команду. Замените `JOBID` значением, возвращенным на предыдущем шаге. Например, если возвращено значение `{"id":"job_1415651640909_0026"}`, затем `JOBID` бы `job_1415651640909_0026`.

    ```cmd
    curl -G -u USERNAME:PASSWORD -d user.name=USERNAME https://CLUSTERNAME.azurehdinsight.net/templeton/v1/jobs/JOBID | jq .status.state
    ```

    Если задание завершено, у него будет состояние **SUCCEEDED**(Успешно).
   
   > [!NOTE]  
   > Этот запрос Curl возвращает документ нотации объектов JavaScript с информацией о задании. При этом jq используется только для получения значения состояния.

4. После изменения состояния задания на **SUCCEEDED** результаты задания можно получить из хранилища больших двоичных объектов Azure. Параметр `statusdir`, передаваемый с помощью запроса, содержит расположение выходного файла. В данном случае это `wasb:///example/data/sqoop/curl`. Этот адрес сохраняет выходные данные задания в `example/data/sqoop/curl` каталог в контейнере хранилища по умолчанию, используемой вашим кластером HDInsight.

    Портал Azure можно использовать для доступа к большим двоичным объектам stderr и stdout.

5. Чтобы убедиться, что данные экспортированы, используйте следующие запросы из клиента SQL для просмотра экспортированных данных:

    ```sql
    SELECT COUNT(*) FROM [dbo].[log4jlogs] WITH (NOLOCK);
    SELECT TOP(25) * FROM [dbo].[log4jlogs] WITH (NOLOCK);
    ```

## <a name="limitations"></a>Ограничения
* Массовый экспорт: при использовании HDInsight на основе Linux соединитель Sqoop, применяемый для экспорта данных в Microsoft SQL Server или базу данных SQL Azure, пока не поддерживает операции массовой вставки.
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
* [Выполнение заданий Pig с помощью cURL с использованием Apache Hadoop в HDInsight](apache-hadoop-use-pig-curl.md)
