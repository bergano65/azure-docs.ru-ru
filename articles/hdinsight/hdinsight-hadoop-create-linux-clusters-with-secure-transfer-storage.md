---
title: Apache Hadoop & безопасное хранилище относительных данных в Azure HDInsight
description: Узнайте, как создать кластеры HDInsight с помощью учетных записей хранения Azure с включенной безопасной передачей.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 07/24/2018
ms.openlocfilehash: b8a5d48e977009cf592f271760232a3b0138c8c5
ms.sourcegitcommit: 38251963cf3b8c9373929e071b50fd9049942b37
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/29/2019
ms.locfileid: "73044947"
---
# <a name="create-apache-hadoop-cluster-with-secure-transfer-storage-accounts-in-azure-hdinsight"></a>Создание кластера Apache Hadoop с помощью учетных записей хранения с безопасной передачей в Azure HDInsight

Функция [Secure transfer required](../storage/common/storage-require-secure-transfer.md) (Необходима безопасная передача) усиливает безопасность учетной записи хранения Azure путем принудительной передачи всех запросов к вашей учетной записи через безопасное подключение. Эта функция и схема wasbs поддерживаются только кластером HDInsight версии 3.6 или более новым.

## <a name="prerequisites"></a>Технические условия

Прежде чем приступать к этой статье, необходимо:

* **Подписка Azure**: чтобы создать бесплатную пробную учетную запись (на один месяц), перейдите по адресу [azure.microsoft.com/free](https://azure.microsoft.com/free).
* **Учетная запись хранения Azure с включенной безопасной передачей**. Инструкции см. в статье [Об учетных записях хранения Azure](../storage/common/storage-quickstart-create-account.md) и [Требование безопасной передачи](../storage/common/storage-require-secure-transfer.md).
* **Контейнер больших двоичных объектов в учетной записи хранения**.

## <a name="create-cluster"></a>Создание кластера

[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

В этом разделе вы создадите в HDInsight кластер Hadoop, используя [шаблон Azure Resource Manager](../azure-resource-manager/resource-group-template-deploy.md). Шаблон можно найти на сайте [GitHub](https://azure.microsoft.com/resources/templates/101-hdinsight-linux-with-existing-default-storage-account/). Знакомство с шаблонами Resource Manager не является обязательным для работы с этой статьей. Сведения о других способах создания кластера и о свойствах, используемых в этой статье, см. в разделе [Создание кластеров HDInsight](hdinsight-hadoop-provision-linux-clusters.md).

1. Щелкните следующее изображение, чтобы войти в Azure и открыть шаблон Resource Manager на портале Azure.

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-hdinsight-linux-with-existing-default-storage-account%2Fazuredeploy.json" target="_blank"><img src="./media/hdinsight-hadoop-create-linux-clusters-with-secure-transfer-storage/hdi-deploy-to-azure1.png" alt="Deploy to Azure button for new cluster"></a>

2. Следуйте инструкциям, чтобы создать кластер со следующими характеристиками: 

    - Укажите HDInsight версии 3.6. Требуется версия 3.6 или более новая.
    - Укажите учетную запись хранения с включенной безопасной передачей.
    - Введите короткое имя для учетной записи хранения.
    - Учетную запись хранения и контейнер больших двоичных объектов необходимо создать заранее.

      Инструкции см. в [этой статье](hadoop/apache-hadoop-linux-tutorial-get-started.md#create-cluster).

При использовании действия скрипта для предоставления собственных файлов конфигурации необходимо использовать wasbs в следующих параметрах:

- fs.defaultFS (core-site)
- spark.eventLog.dir 
- spark.history.fs.logDirectory

## <a name="add-additional-storage-accounts"></a>Добавление дополнительных учетных записей хранения

Добавить дополнительные учетные записи хранения с включенной безопасной передачей можно несколькими способами:

- Измените шаблон Azure Resource Manager в предыдущем разделе.
- Создайте кластер с помощью [портала Azure](https://portal.azure.com) и укажите связанную учетную запись хранения.
- Используйте действия скриптов для добавления дополнительных учетных записей хранения с включенной безопасной передачей в имеющийся кластер HDInsight. Дополнительные сведения см. в статье [Добавление дополнительных учетных записей хранения в HDInsight](hdinsight-hadoop-add-storage.md).

## <a name="next-steps"></a>Дальнейшие действия
В этой статье вы узнали, как создать кластер HDInsight и включить безопасную пересылку учетных записей хранения.

Дополнительные сведения об анализе данных с помощью HDInsight см. в следующих статьях:

* Дополнительные сведения об использовании [Apache Hive](https://hive.apache.org/) с hdinsight, в том числе о выполнении запросов Hive из Visual Studio, см. в статье [использование Apache Hive с hdinsight][hdinsight-use-hive].
* Сведения о языке [Apache Pig](https://pig.apache.org/), который используется для преобразования данных, см. в статье [использование Apache Pig с HDInsight][hdinsight-use-pig].
* Дополнительные сведения о [Apache Hadoop MapReduce](https://hadoop.apache.org/docs/current/hadoop-mapreduce-client/hadoop-mapreduce-client-core/MapReduceTutorial.html)— способах написания программ, обрабатывающих данные в Hadoop, см. в статье [использование Apache Hadoop MapReduce с HDInsight][hdinsight-use-mapreduce].
* Дополнительные сведения об анализе данных в HDInsight с помощью средств HDInsight для Visual Studio см. в статье [Подключение к Azure HDInsight и выполнение запросов Apache Hive с помощью средств Data Lake для Visual Studio](hadoop/apache-hadoop-visual-studio-tools-get-started.md).

Чтобы узнать, как HDInsight хранит данные или получает их, ознакомьтесь со следующими статьями:

* Сведения о том, как HDInsight использует службу хранилища Azure, см. в статье [Использование службы хранилища Azure в HDInsight](hdinsight-hadoop-use-blob-storage.md).
* Сведения об отправке данных в HDInsight см. в статье [Отправка данных в HDInsight][hdinsight-upload-data].

Дополнительные сведения о создании кластера HDInsight и управлении этим кластером см. в следующих статьях:

* Сведения об управлении кластером HDInsight под управлением Linux см. в руководстве по [управлению кластерами HDInsight с помощью Apache Ambari](hdinsight-hadoop-manage-ambari.md).
* Дополнительные сведения о параметрах, которые можно выбрать при создании кластера HDInsight, см. в статье [Создание кластеров HDInsight в Linux с пользовательскими параметрами](hdinsight-hadoop-provision-linux-clusters.md).
* Если вы уже знакомы с Linux и Apache Hadoop, но хотите узнать об особенностях Hadoop в HDInsight, см. статью [Сведения об использовании HDInsight в Linux](hdinsight-hadoop-linux-information.md). Эта статья содержит следующую информацию:

  * URL-адреса для служб, размещенных в кластере, например [Apache Ambari](https://ambari.apache.org/) и [WebHCat](https://cwiki.apache.org/confluence/display/Hive/WebHCat)
  * расположение файлов [Apache Hadoop](https://hadoop.apache.org/) и примеры в локальной файловой системе;
  * использование службы хранилища Azure (WASB) вместо [Apache Hadoop HDFS](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsUserGuide.html) в качестве хранилища данных по умолчанию.

[1]: ../HDInsight/hadoop/apache-hadoop-visual-studio-tools-get-started.md

[hdinsight-provision]: hdinsight-provision-linux-clusters.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-use-mapreduce]:hadoop/hdinsight-use-mapreduce.md
[hdinsight-use-hive]:hadoop/hdinsight-use-hive.md
[hdinsight-use-pig]:hadoop/hdinsight-use-pig.md
