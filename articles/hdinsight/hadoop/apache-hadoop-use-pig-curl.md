---
title: Использование Apache Hadoop Pig с REST в HDInsight в Azure
description: Узнайте, как с помощью REST выполнять задания Pig Latin в кластере Apache Hadoop в Azure HDInsight.
services: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 04/10/2018
ms.author: hrasheed
ms.openlocfilehash: 653d3e357e3a02659a225b4e26c386ca54b6288f
ms.sourcegitcommit: 549070d281bb2b5bf282bc7d46f6feab337ef248
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/21/2018
ms.locfileid: "53715432"
---
# <a name="run-apache-pig-jobs-with-apache-hadoop-on-hdinsight-by-using-rest"></a>Выполнение заданий Apache Pig с использованием Apache Hadoop в HDInsight с помощью REST

[!INCLUDE [pig-selector](../../../includes/hdinsight-selector-use-pig.md)]

Узнайте, как выполнять задания Apache Pig Latin с помощью запросов REST к кластеру Azure HDInsight. Curl используется для демонстрации возможностей взаимодействия с HDInsight с помощью REST API WebHCat.

> [!NOTE]  
> Если вы уже знаете, как использовать серверы Apache Hadoop на платформе Linux, но не знакомы с HDInsight, ознакомьтесь со статьей [Сведения об использовании HDInsight в Linux](../hdinsight-hadoop-linux-information.md).

## <a id="prereq"></a>Предварительные требования

* Кластер Azure HDInsight (Hadoop в HDInsight) (на платформе Linux или Windows).

  > [!IMPORTANT]  
  > Linux — это единственная операционная система, используемая для работы с HDInsight 3.4 или более поздних версий. Дополнительные сведения см. в разделе [Приближается дата прекращения сопровождения HDI версии 3.3](../hdinsight-component-versioning.md#hdinsight-windows-retirement).

* [Curl](https://curl.haxx.se/)

* [jq](https://stedolan.github.io/jq/)

## <a id="curl"></a>Выполнение заданий Pig с помощью Curl


> [!NOTE]
> REST API защищается с помощью [обычной проверки подлинности](https://en.wikipedia.org/wiki/Basic_access_authentication). Чтобы обеспечить безопасную отправку учетных данных на сервер, запросы всегда следует отправлять с помощью протокола HTTPS.
>
> При использовании команд, описанных в этом разделе, замените `USERNAME` на имя пользователя для выполнения проверки подлинности в кластере, а `PASSWORD` — на пароль учетной записи пользователя. Замените `CLUSTERNAME` именем кластера.
>


1. Используйте следующую команду в командной строке, чтобы проверить возможность подключения к кластеру HDInsight:

    ```bash
    curl -u USERNAME:PASSWORD -G https://CLUSTERNAME.azurehdinsight.net/templeton/v1/status
    ```

    Вы получите следующий ответ JSON:

        {"status":"ok","version":"v1"}

    Ниже приведены параметры, используемые в этой команде:

    * **-u**. Имя пользователя и пароль, используемый для проверки подлинности запроса.
    * **-G**. Указывает, что этот запрос является запросом GET.

     Начало URL-адреса **https://CLUSTERNAME.azurehdinsight.net/templeton/v1** одинаковое для всех запросов. Путь **/status** указывает, что по запросу серверу должно быть возвращено состояние WebHCat (другое название — Templeton).

2. Чтобы отправить задание Pig Latin в кластер, используйте следующий код:

    ```bash
    curl -u USERNAME:PASSWORD -d user.name=USERNAME -d execute="LOGS=LOAD+'/example/data/sample.log';LEVELS=foreach+LOGS+generate+REGEX_EXTRACT($0,'(TRACE|DEBUG|INFO|WARN|ERROR|FATAL)',1)+as+LOGLEVEL;FILTEREDLEVELS=FILTER+LEVELS+by+LOGLEVEL+is+not+null;GROUPEDLEVELS=GROUP+FILTEREDLEVELS+by+LOGLEVEL;FREQUENCIES=foreach+GROUPEDLEVELS+generate+group+as+LOGLEVEL,COUNT(FILTEREDLEVELS.LOGLEVEL)+as+count;RESULT=order+FREQUENCIES+by+COUNT+desc;DUMP+RESULT;" -d statusdir="/example/pigcurl" https://CLUSTERNAME.azurehdinsight.net/templeton/v1/pig
    ```

    Ниже приведены параметры, используемые в этой команде:

    * **-d**. Так как `-G` не используется, в запросе по умолчанию используется метод POST. `-d` задает значения данных, отправляемые в запросе.

    * **user.name**. Пользователь, выполняющий команду.
    * **execute**. Оператор Pig Latin, который необходимо выполнить.
    * **statusdir**. Каталог, в который будет записано состояние этого задания.

    > [!NOTE]  
    > Обратите внимание, что при использовании Curl пробелы в операторах Pig Latin заменяются знаком `+`.

    Эта команда должна возвращать идентификатор задания, который может использоваться для проверки состояния задания. Пример:

        {"id":"job_1415651640909_0026"}

3. Чтобы проверить состояние задания, используйте следующую команду.

     ```bash
    curl -G -u USERNAME:PASSWORD -d user.name=USERNAME https://CLUSTERNAME.azurehdinsight.net/templeton/v1/jobs/JOBID | jq .status.state
    ```

     Замените `JOBID` значением, возвращенным на предыдущем шаге. Например, если возвращено значение `{"id":"job_1415651640909_0026"}`, то `JOBID` равно `job_1415651640909_0026`.

    Если задание завершено, оно будет в состоянии **SUCCEEDED** (Успешно).

    > [!NOTE]  
    > Этот запрос Curl возвращает документ JSON с информацией о задании. При этом jq используется только для получения значения состояния.

## <a id="results"></a>Просмотр результатов

Когда состояние задания изменится на **SUCCEEDED**, вы можете получить результаты задания. Параметр `statusdir`, передаваемый с помощью запроса, содержит расположение выходного файла. В данном случае это `/example/pigcurl`.

В качестве хранилища данных по умолчанию HDInsight может использовать хранилище Azure или Azure Data Lake Store. Существуют различные способы получения данных в зависимости от их используемого типа. Дополнительные сведения см. в разделе о хранилище в документе [Linux-based HDInsight information](../hdinsight-hadoop-linux-information.md#hdfs-azure-storage-and-data-lake-storage) (Сведения о HDInsight на базе Linux).

## <a id="summary"></a>Сводка

Как показано в этом документе, для запуска, мониторинга и просмотра результатов выполнения заданий Pig в кластере HDInsight можно использовать необработанные HTTP-запросы.

Дополнительные сведения об интерфейсе REST, используемом в этой статье, см. в [справочнике по WebHCat](https://cwiki.apache.org/confluence/display/Hive/WebHCat+Reference).

## <a id="nextsteps"></a>Дальнейшие действия

Общая информация о Pig в HDInsight:

* [Использование Apache Pig с Apache Hadoop в HDInsight](hdinsight-use-pig.md)

Дополнительная информация о других способах работы с Hadoop в HDInsight:

* [Использование Apache Hive с Apache Hadoop в HDInsight](hdinsight-use-hive.md)
* [Использование MapReduce в Apache Hadoop в HDInsight](hdinsight-use-mapreduce.md)
