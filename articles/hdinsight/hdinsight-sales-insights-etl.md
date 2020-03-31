---
title: Руководство по созданию сквозного конвейера извлечения, преобразования и загрузки для получения аналитических сведений о продажах в Azure HDInsight
description: Узнайте, как использовать создание конвейеров ETL с Azure HDInsight для получения аналитических сведений из данных о продажах с помощью кластеров по запросу Spark и Power BI.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: tutorial
ms.custom: hdinsightactive
ms.date: 03/24/2020
ms.openlocfilehash: a4df99c45b27ad662133010422cae2e30e36e584
ms.sourcegitcommit: 940e16ff194d5163f277f98d038833b1055a1a3e
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/25/2020
ms.locfileid: "80247271"
---
# <a name="tutorial-create-an-end-to-end-data-pipeline-to-derive-sales-insights-in-azure-hdinsight"></a>Руководство по созданию сквозного конвейера данных для получения аналитических сведений о продажах в Azure HDInsight

В этом учебнике вы создадите сквозной конвейер данных, который выполняет операции извлечения, преобразования и загрузки (ETL). Конвейер будет использовать кластеры [Apache Spark](./spark/apache-spark-overview.md) и Apache Hive, работающие в Azure HDInsight, для запроса данных и управления ими. Вы также будете использовать технологии, которые включают Data Lake Storage 2-го поколения для хранения данных и Power BI для визуализации.

Этот конвейер данных объединяет данные из различных магазинов, удаляет ненужные данные, добавляет новые данные и загружает их все обратно в хранилище для визуализации бизнес-аналитики. Дополнительные сведения о конвейерах ETL см. в статье [Extract, transform, and load (ETL) at scale](./hadoop/apache-hadoop-etl-at-scale.md) (Извлечение, преобразование и загрузка (ETL) в масштабе).

![Архитектура ETL](./media/hdinsight-sales-insights-etl/architecture.png)

Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), прежде чем начинать работу.

## <a name="prerequisites"></a>Предварительные требования

* Azure CLI. Подробнее см. статью [Установка Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli).

* Член [встроенной роли Azure — владелец](../role-based-access-control/built-in-roles.md).

* [Power BI Desktop](https://www.microsoft.com/download/details.aspx?id=45331) для визуализации бизнес-аналитики в конце этого руководства.

## <a name="create-resources"></a>Создание ресурсов

### <a name="clone-the-repository-with-scripts-and-data"></a>Клонирование репозитория со скриптами и данными

1. Войдите на [портал Azure](https://portal.azure.com).

1. Откройте Azure Cloud Shell в верхней строке меню. Выберите подписку для создания общей папки, если появится запрос Azure Cloud Shell.

   ![Открытие Azure Cloud Shell](./media/hdinsight-sales-insights-etl/hdinsight-sales-insights-etl-click-cloud-shell.png)

1. Выберите **Bash** в раскрывающемся меню **Выбор среды**.

1. Убедитесь, что вы являетесь членом роли Azure [Владелец](../role-based-access-control/built-in-roles.md). Замените `user@contoso.com` своей учетной записью, а затем введите команду:

    ```azurecli
    az role assignment list \
    --assignee "user@contoso.com" \
    --role "Owner"
    ```

    Если запись не возвращается, вы не являетесь членом и не сможете завершить работу с этим руководством.

1. Выведите список подписок, введя команду:

    ```azurecli
    az account list --output table
    ```

    Запишите идентификатор подписки, которая будет использоваться для этого проекта.

1. Задайте подписку, которая будет использоваться для этого проекта. Замените `SUBSCRIPTIONID` фактическим значением, а затем введите команду.

    ```azurecli
    subscriptionID="SUBSCRIPTIONID"
    az account set --subscription $subscriptionID
    ```

1. Создайте новую группу ресурсов для проекта. Замените `RESOURCEGROUP` требуемым именем, а затем введите команду.

    ```azurecli
    resourceGroup="RESOURCEGROUP"
    az group create --name $resourceGroup --location westus
    ```

1. Скачайте данные и скрипты для этого учебника из [репозитория ETL данных по продажам HDInsight](https://github.com/Azure-Samples/hdinsight-sales-insights-etl).  Введите следующую команду:

    ```bash
    git clone https://github.com/Azure-Samples/hdinsight-sales-insights-etl.git
    cd hdinsight-sales-insights-etl
    ```

1. Убедитесь, что `salesdata scripts templates` созданы. Выполните проверку с помощью следующей команды:

   ```bash
   ls
   ```

### <a name="deploy-azure-resources-needed-for-the-pipeline"></a>Развертывание ресурсов Azure, необходимых для конвейера

1. Добавьте разрешения на выполнение для всех скриптов, введя:

    ```bash
    chmod +x scripts/*.sh
    ````

1. Выполните скрипт. Замените `RESOURCE_GROUP_NAME` и `LOCATION` соответствующими значениями, а затем введите команду:

    ```bash
    ./scripts/resources.sh RESOURCE_GROUP_NAME LOCATION
    ```

    Команда развернет следующие ресурсы:

    * Учетной записи хранения BLOB-объектов Azure. Эта учетная запись будет содержать данные о продажах компании.
    * Учетной записи Azure Data Lake Storage 2-го поколения. Эта учетная запись будет использоваться в качестве учетной записи хранения для обоих кластеров HDInsight. Дополнительные сведения об HDInsight и Data Lake Storage 2-го поколения см. в статье [Azure HDInsight integration with Data Lake Storage Gen2 preview — ACL and security update](https://azure.microsoft.com/blog/azure-hdinsight-integration-with-data-lake-storage-gen-2-preview-acl-and-security-update/) (Интеграция Azure HDInsight с Data Lake Storage 2-го поколения (предварительная версия). Обновление ACL и системы безопасности).
    * Управляемого удостоверения, назначаемого пользователем. Эта учетная запись предоставляет кластерам HDInsight доступ к учетной записи Data Lake Storage 2-го поколения.
    * Кластер Apache Spark. Этот кластер будет использоваться для очистки и преобразования необработанных данных.
    * Кластер [Interactive Query](./interactive-query/apache-interactive-query-get-started.md) Apache Hive. Этот кластер позволяет отправлять запросы к данным о продажах, визуализируя их с помощью Power BI.
    * Виртуальная сеть Azure, поддерживаемая правилами группы безопасности сети (NSG). Эта виртуальная сеть позволяет кластерам обмениваться данными, а также защищать их связь.

Создание кластера может занять около 20 минут.

Скрипт `resources.sh` содержит следующие команды. Вы можете не выполнять эти команды, если уже выполнили сценарий на предыдущем шаге.

* `az group deployment create` — эта команда использует шаблон Azure Resource Manager (`resourcestemplate.json`) для создания указанных ресурсов с требуемой конфигурацией.

    ```azurecli
    az group deployment create --name ResourcesDeployment \
        --resource-group $resourceGroup \
        --template-file resourcestemplate.json \
        --parameters "@resourceparameters.json"
    ```

* `az storage blob upload-batch` — эта команда передает CSV-файлы данных о продажах в созданную учетную запись хранилища BLOB-объектов с помощью этой команды:

    ```azurecli
    az storage blob upload-batch -d rawdata \
        --account-name <BLOB STORAGE NAME> -s ./ --pattern *.csv
    ```

Пароль по умолчанию для доступа к кластерам по SSH — `Thisisapassword1`. Если вы хотите изменить пароль, перейдите к файлу `resourcesparameters.json` и измените пароль для параметров `sparksshPassword`, `sparkClusterLoginPassword`, `llapClusterLoginPassword` и `llapsshPassword`.

### <a name="verify-deployment-and-collect-resource-information"></a>Проверка развертывания и получение сведений о ресурсах

1. Если вы хотите проверить состояние развертывания, перейдите к группе ресурсов на портале Azure. Выберите **Развертывания** в разделе **Параметры**. Выберите имя развертывания `ResourcesDeployment`. Здесь можно просмотреть ресурсы, которые уже успешно развернуты и те ресурсы, которые еще выполняются.

1. Чтобы просмотреть имена кластеров, введите следующую команду:

    ```azurecli
    sparkCluster=$(az hdinsight list \
        --resource-group $resourceGroup \
        --query "[?contains(name,'spark')].{clusterName:name}" -o tsv)

    llapCluster=$(az hdinsight list \
        --resource-group $resourceGroup \
        --query "[?contains(name,'llap')].{clusterName:name}" -o tsv)

    echo $sparkCluster
    echo $llapCluster
    ```

1. Чтобы просмотреть учетную запись хранения Azure и ключ доступа, введите следующую команду:

    ```azurecli
    blobStorageName=$(cat resourcesoutputs.json | jq -r '.properties.outputs.blobStorageName.value')

    blobKey=$(az storage account keys list \
        --account-name $blobStorageName \
        --resource-group $resourceGroup \
        --query [0].value -o tsv)

    echo $blobStorageName
    echo $blobKey
    ```

1. Чтобы просмотреть учетную запись Data Lake Storage 2-го поколения и ключ доступа, введите следующую команду:

    ```azurecli
    ADLSGen2StorageName=$(cat resourcesoutputs.json | jq -r '.properties.outputs.adlsGen2StorageName.value')

    adlsKey=$(az storage account keys list \
        --account-name $ADLSGen2StorageName \
        --resource-group $resourceGroup \
        --query [0].value -o tsv)

    echo $ADLSGen2StorageName
    echo $adlsKey
    ```

### <a name="create-a-data-factory"></a>Создание фабрики данных

Фабрика данных Azure — это инструмент, помогающий автоматизировать Azure Pipelines. Это не единственный способ выполнить эти задачи, но он отлично подходит для их автоматизации. Дополнительные сведения о Фабрике данных Azure см. в [документации по Фабрике данных Azure](https://azure.microsoft.com/services/data-factory/).

Эта Фабрика данных будет иметь один конвейер с двумя действиями:

* Первое действие скопирует данные из хранилища BLOB-объектов Azure в учетную запись хранения Data Lake Storage 2-го поколения для имитации приема данных.
* Второе действие будет преобразовывать данные в кластере Spark. Скрипт выполняет преобразование данных, удаляя ненужные столбцы. Он также добавляет новый столбец, который вычисляет доход, создаваемый одной транзакцией.

Чтобы настроить конвейер Фабрики данных Azure, выполните следующую команду:

```bash
./scripts/adf.sh
```

Скрипт делает следующее:

1. Создает субъект-службу с разрешениями `Storage Blob Data Contributor` в учетной записи хранения Data Lake Storage 2-го поколения.
1. Получает токен проверки подлинности для авторизации запросов POST к [REST API файловой системы Data Lake Storage 2-го поколения](https://docs.microsoft.com/rest/api/storageservices/datalakestoragegen2/filesystem/create).
1. Заполняет фактическое имя учетной записи хранения Data Lake Storage 2-го поколения в файлах `sparktransform.py` и `query.hql`.
1. Получает ключи к хранилищу данных для учетной записи Data Lake Storage 2-го поколения и хранилища BLOB-объектов.
1. Создает еще одно развертывание ресурсов для создания конвейера Фабрики данных Azure со связанными службами и действиями. Он передает ключи хранилища в качестве параметров в файл шаблона, чтобы связанные службы могли правильно получить доступ к учетным записям хранения.

Конвейер Фабрики данных развертывается с помощью следующей команды:

```azurecli-interactive
az group deployment create --name ADFDeployment \
    --resource-group $resourceGroup \
    --template-file adftemplate.json \
    --parameters "@adfparameters.json"
```

## <a name="run-the-data-pipeline"></a>Запуск конвейера данных

### <a name="trigger-the-data-factory-activities"></a>Активация действий Фабрики данных

Первое действие в созданном конвейере Фабрики данных перемещает данные из хранилища BLOB-объектов в Data Lake Storage 2-го поколения. Второе действие применяет преобразования Spark к данным и сохраняет преобразованные CSV-файлы в новом расположении. Выполнение всего конвейера может занять несколько минут.

Чтобы активировать конвейеры, можно выполнить одно из следующих действий:

* Активируйте конвейеры Фабрики данных в PowerShell. Замените `DataFactoryName` фактическим именем Фабрики данных, а затем выполните следующие команды:

    ```powershell
    Invoke-AzDataFactoryV2Pipeline -DataFactory DataFactoryName -PipelineName "CopyPipeline_k8z"
    Invoke-AzDataFactoryV2Pipeline -DataFactory DataFactoryName -PipelineName "sparkTransformPipeline"
    ```

    либо

* Откройте фабрику данных и выберите **Author & Monitor** (Создание и мониторинг). Активируйте конвейер копирования, а затем конвейер Spark с портала. Сведения о запуске конвейеров на портале см. в разделе [Активация конвейера](hdinsight-hadoop-create-linux-clusters-adf.md#trigger-a-pipeline).

Чтобы убедиться, что конвейеры выполнены, можно выполнить одно из следующих действий.

* Перейдите к разделу **Мониторинг** в фабрике данных с помощью портала.
* В Обозревателе службы хранилища Azure перейдите к учетной записи хранения Data Lake Storage 2-го поколения. Перейдите в файловую систему `files`, а затем к папке `transformed` и проверьте ее содержимое, чтобы убедиться, что конвейер выполнен.

Другие способы преобразования данных с помощью HDInsight см. в [этой статье об использовании Jupyter Notebook](/azure/hdinsight/spark/apache-spark-load-data-run-query).

### <a name="create-a-table-on-the-interactive-query-cluster-to-view-data-on-power-bi"></a>Создание таблицы в кластере интерактивных запросов для просмотра данных в Power BI

1. Скопируйте файл `query.hql` в кластер LLAP с помощью SCP. Замените `LLAPCLUSTERNAME` фактическим именем, а затем введите команду:

    ```bash
    scp scripts/query.hql sshuser@LLAPCLUSTERNAME-ssh.azurehdinsight.net:/home/sshuser/
    ```

2. Используйте SSH для доступа к кластеру LLAP. Замените `LLAPCLUSTERNAME` фактическим именем, а затем введите команду. Если вы еще не изменили файл `resourcesparameters.json`, пароль — `Thisisapassword1`.

    ```bash
    ssh sshuser@LLAPCLUSTERNAME-ssh.azurehdinsight.net
    ```

3. Выполните следующую команду, чтобы запустить скрипт.

    ```bash
    beeline -u 'jdbc:hive2://localhost:10001/;transportMode=http' -f query.hql
    ```

Этот скрипт создаст управляемую таблицу в кластере интерактивных запросов, к которому можно получить доступ из Power BI.

### <a name="create-a-power-bi-dashboard-from-sales-data"></a>Создание информационной панели Power BI на основе данных о продажах

1. Откройте Power BI Desktop.
1. Выберите **Получение данных**.
1. Найдите **кластер интерактивных запросов HDInsight**.
1. Вставьте универсальный код ресурса (URI) для кластера. Ее необходимо указать в формате `https://LLAPCLUSTERNAME.azurehdinsight.net`.

   Введите `default` для базы данных.
1. Введите имя пользователя и пароль, используемые для доступа к кластеру.

После загрузки данных можно поэкспериментировать с информационной панелью, которую вы хотите создать. Дополнительные сведения о начале работы с информационными панелями Power BI см. по следующим ссылкам:

* [Общие сведения о панелях мониторинга для разработчиков Power BI](https://docs.microsoft.com/power-bi/service-dashboards)
* [Руководство. Приступая к работе со службой Power BI](https://docs.microsoft.com/power-bi/service-get-started)

## <a name="clean-up-resources"></a>Очистка ресурсов

Если вы не собираетесь использовать это приложение, удалите все ресурсы, выполнив следующую команду, чтобы избежать необходимости оплаты.

```azurecli-interactive
az group delete -n $resourceGroup
```

## <a name="next-steps"></a>Дальнейшие действия

> [!div class="nextstepaction"]
> [Extract, transform, and load (ETL) at scale](./hadoop/apache-hadoop-etl-at-scale.md) (Извлечение, преобразование и загрузка (ETL) в масштабе)
