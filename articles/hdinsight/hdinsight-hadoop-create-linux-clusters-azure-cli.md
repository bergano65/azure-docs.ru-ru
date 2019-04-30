---
title: Создание кластеров Apache Hadoop с помощью классического Azure CLI в Azure HDInsight
description: Узнайте, как создавать кластеры HDInsight с помощью кроссплатформенного классического Azure CLI.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 02/27/2018
ms.author: hrasheed
ms.openlocfilehash: 21985b009694dc5a21c65d4c9dc9536cf6c01a0e
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/23/2019
ms.locfileid: "62128725"
---
# <a name="create-hdinsight-clusters-using-the-azure-classic-cli"></a>Создание кластеров HDInsight с помощью классического интерфейса командной строки Azure

[!INCLUDE [selector](../../includes/hdinsight-create-linux-cluster-selector.md)]

Это пошаговое руководство содержит инструкции по созданию кластера HDInsight 3.5 с помощью классического интерфейса командной строки Azure.

[!INCLUDE [classic-cli-warning](../../includes/requires-classic-cli.md)]

## <a name="prerequisites"></a>Технические условия

[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

* **Подписка Azure**. См. страницу [бесплатной пробной версии Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

* **Классический Azure CLI**. Действия, описанные в этом документе, проверены с помощью классического интерфейса командной строки Azure версии 0.10.14.

## <a name="log-in-to-your-azure-subscription"></a>Вход в подписку Azure

Выполните действия, описанные в статье [Вход с помощью Azure CLI](/cli/azure/authenticate-azure-cli), и подключитесь к подписке с помощью метода **login**.

## <a name="create-a-cluster"></a>Создание кластера

Описанные ниже действия следует выполнять в командной строке, например PowerShell или Bash.

1. Выполните следующую команду для аутентификации в подписке Azure.

        azure login

    Вам будет предложено указать имя пользователя и пароль. Если подписок Azure несколько, укажите, какую подписку должны использовать команды классического Azure CLI, с помощью метода `azure account set <subscriptionname>`.

2. Переключитесь в режим диспетчера ресурсов Azure с помощью следующей команды:

        azure config mode arm

3. Создайте группу ресурсов. Эта группа ресурсов будет содержать кластер HDInsight и соответствующую учетную запись хранения.

        azure group create groupname location

    * Замените `groupname` на уникальное имя группы.

    * Замените `location` на географический регион, в котором нужно создать группу.

       Для получения списка допустимых расположений выполните команду `azure location list`, а затем воспользуйтесь одним из расположений из столбца `Name`.

4. Создайте учетную запись хранения. Эта учетная запись хранения будет использоваться как хранилище по умолчанию для кластера HDInsight.

        azure storage account create -g groupname --sku-name RAGRS -l location --kind Storage storagename

    * Замените `groupname` на имя группы, созданной на предыдущем этапе.

    * Замените `location` на расположение, которое использовалось на предыдущем этапе.

    * Замените `storagename` на уникальное имя учетной записи хранения.

        > [!NOTE]  
        > Дополнительные сведения о параметрах, используемых в этой команде, используйте `azure storage account create -h`, чтобы открыть справку по этой команде.

5. Извлеките ключ для доступа к учетной записи хранения.

        azure storage account keys list -g groupname storagename

    * Замените `groupname` на имя группы ресурсов.
    * Замените `storagename` на имя учетной записи хранения.

      Из полученных данных сохраните значение `key` параметра `key1`.

6. Создание кластера HDInsight.

        azure hdinsight cluster create -g groupname -l location -y Linux --clusterType Hadoop --defaultStorageAccountName storagename.blob.core.windows.net --defaultStorageAccountKey storagekey --defaultStorageContainer clustername --workerNodeCount 3 --userName admin --password httppassword --sshUserName sshuser --sshPassword sshuserpassword clustername

    * Замените `groupname` на имя группы ресурсов.

    * Замените `Hadoop` типом кластера, который хотите создать. Примеры: `Hadoop`, `HBase`, `Kafka`, `Spark` или `Storm`.

      > [!IMPORTANT]  
      > Кластеры HDInsight бывают разных типов, которые соответствуют рабочей нагрузке или технологии, для которой предназначен кластер. Создать кластер, в котором бы объединились несколько типов, например Storm и HBase, нельзя.

    * Замените `location` на расположение, которое использовалось на предыдущих этапах.

    * Замените `storagename` на имя учетной записи хранения.

    * Замените `storagekey` на ключ, полученный на предыдущем этапе.

    * Для параметра `--defaultStorageContainer` используйте то же имя, что и для кластера.

    * Замените `admin` и `httppassword` именем и паролем, которые нужно использовать для доступа к кластеру по протоколу HTTPS.

    * Замените `sshuser` и `sshuserpassword` именем пользователя и паролем, которые нужно использовать для доступа к кластеру по протоколу SSH.

      > [!IMPORTANT]  
      > Этот пример создает кластер с двумя рабочими узлами. После создания кластера можно изменить число рабочих узлов, выполнив операцию масштабирования. Если вы планируете использовать более 32 рабочих узлов, для головного узла необходимо выбрать по крайней мере 8-ядерный процессор и 14 ГБ ОЗУ. Настроить размер головного узла можно с помощью параметра `--headNodeSize` во время создания кластера.
      >
      > Дополнительные сведения о размерах узлов и их стоимости см. на странице с [ценами на HDInsight](https://azure.microsoft.com/pricing/details/hdinsight/).
      
      Создание кластера требует времени, обычно около 15 минут.

## <a name="troubleshoot"></a>Устранение неполадок

Если при создании кластеров HDInsight возникли проблемы, см. раздел [Создание кластеров](hdinsight-hadoop-create-linux-clusters-portal.md).

## <a name="next-steps"></a>Дальнейшие действия

Теперь, когда вы успешно создали кластер HDInsight с помощью классического интерфейса командной строки Azure, обратитесь к следующим статьям, чтобы научиться работать с кластером:

### <a name="apache-hadoop-clusters"></a>Кластеры Apache Hadoop

* [Использование Hive и HiveQL с Hadoop в HDInsight для анализа примера файла Apache log4j](hadoop/hdinsight-use-hive.md)
* [Использование Pig с Hadoop в HDInsight](hadoop/hdinsight-use-pig.md)
* [Использование MapReduce с HDInsight](hadoop/hdinsight-use-mapreduce.md)

### <a name="apache-hbase-clusters"></a>Кластеры Apache HBase

* [Начало работы с Apache HBase в HDInsight](hbase/apache-hbase-tutorial-get-started-linux.md)
* [Разработка приложений Java для Apache HBase в HDInsight](hbase/apache-hbase-build-java-maven-linux.md)

### <a name="apache-storm-clusters"></a>Кластеры Apache Storm

* [Создание топологий для Apache Storm в HDInsight на языке Java](storm/apache-storm-develop-java-topology.md)
* [Использование компонентов Python в Apache Storm в HDInsight](storm/apache-storm-develop-python-topology.md)
* [Развертывание и мониторинг топологии с помощью Apache Storm в HDInsight.](storm/apache-storm-deploy-monitor-topology-linux.md)
