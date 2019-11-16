---
title: Руководство по созданию сквозного конвейера извлечения, преобразования и загрузки для получения аналитических сведений о продажах
description: Узнайте, как использовать создание конвейеров ETL с Azure HDInsight для получения аналитических сведений из данных о продажах с помощью кластеров по запросу Spark и Power BI.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: tutorial
ms.date: 09/30/2019
ms.author: hrasheed
ms.openlocfilehash: d662ad59722658ed888aa732c1f45afdf48f850c
ms.sourcegitcommit: cf36df8406d94c7b7b78a3aabc8c0b163226e1bc
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/09/2019
ms.locfileid: "73889210"
---
# <a name="tutorial-create-an-end-to-end-data-pipeline-to-derive-sales-insights"></a>Руководство по созданию сквозного конвейера данных для получения аналитических сведений о продажах

В этом учебнике вы создадите сквозной конвейер данных, который выполняет операции извлечения, преобразования и загрузки (ETL). Конвейер будет использовать кластеры Apache Spark и Apache Hive, работающие в Azure HDInsight, для запроса данных и управления ими. Вы также будете использовать технологии, которые включают Data Lake Storage 2-го поколения для хранения данных и Power BI для визуализации.

Этот конвейер данных объединяет данные из различных магазинов, удаляет ненужные данные, добавляет новые данные и загружает их все обратно в хранилище для визуализации бизнес-аналитики. Дополнительные сведения о конвейерах ETL см. в статье [Extract, transform, and load (ETL) at scale](./hadoop/apache-hadoop-etl-at-scale.md) (Извлечение, преобразование и загрузка (ETL) в масштабе).

![Архитектура ETL](./media/hdinsight-sales-insights-etl/architecture.png)

## <a name="prerequisites"></a>Предварительные требования

Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/), прежде чем начинать работу.

Скачайте [Power BI Desktop](https://www.microsoft.com/download/details.aspx?id=45331), чтобы визуализировать бизнес-аналитику в конце этого руководства.

## <a name="create-resources"></a>Создание ресурсов

### <a name="clone-the-repository-with-scripts-and-data"></a>Клонирование репозитория со скриптами и данными

1. Войдите на [портале Azure](https://portal.azure.com).
1. Откройте Azure Cloud Shell в верхней строке меню. Выберите подписку для создания общей папки, если появится запрос Azure Cloud Shell.

   ![Открытие Azure Cloud Shell](./media/hdinsight-sales-insights-etl/hdinsight-sales-insights-etl-click-cloud-shell.png)
1. Выберите **Bash** в раскрывающемся меню **Выбор среды**.
1. Войдите в учетную запись Azure и задайте подписку. 
1. Настройте группу ресурсов для проекта.
   1. Выберите уникальное имя для группы ресурсов.
   1. Выполните приведенный ниже фрагмент кода в Cloud Shell, чтобы задать переменные, которые будут использоваться в последующих шагах.

       ```azurecli-interactive 
       resourceGroup="<RESOURCE GROUP NAME>"
       subscriptionID="<SUBSCRIPTION ID>"
        
       az account set --subscription $subscriptionID
       az group create --name $resourceGroup --location westus
       ```

1. Скачайте данные и скрипты для этого учебника из [репозитория ETL данных по продажам HDInsight](https://github.com/Azure-Samples/hdinsight-sales-insights-etl), введя следующие команды в Cloud Shell:

    ```azurecli-interactive 
    git clone https://github.com/Azure-Samples/hdinsight-sales-insights-etl.git
    cd hdinsight-sales-insights-etl
    ```

1. Введите `ls` в командной строке оболочки, чтобы увидеть, что были созданы следующие файлы и каталоги:

   ```output
   /salesdata/
   /scripts/
   /templates/
   ```

### <a name="deploy-azure-resources-needed-for-the-pipeline"></a>Развертывание ресурсов Azure, необходимых для конвейера 

1. Добавьте разрешения на выполнение для скрипта `chmod +x scripts/*.sh`.
1. Используйте команду `./scripts/resources.sh <RESOURCE_GROUP_NAME> <LOCATION>`, чтобы выполнить скрипт для развертывания следующих ресурсов в Azure:

   1. Учетной записи хранения BLOB-объектов Azure. Эта учетная запись будет содержать данные о продажах компании.
   2. Учетной записи Azure Data Lake Storage 2-го поколения. Эта учетная запись будет использоваться в качестве учетной записи хранения для обоих кластеров HDInsight. Дополнительные сведения об HDInsight и Data Lake Storage 2-го поколения см. в статье [Azure HDInsight integration with Data Lake Storage Gen2 preview — ACL and security update](https://azure.microsoft.com/blog/azure-hdinsight-integration-with-data-lake-storage-gen-2-preview-acl-and-security-update/) (Интеграция Azure HDInsight с Data Lake Storage 2-го поколения (предварительная версия). Обновление ACL и системы безопасности).
   3. Управляемого удостоверения, назначаемого пользователем. Эта учетная запись предоставляет кластерам HDInsight доступ к учетной записи Data Lake Storage 2-го поколения.
   4. Кластер Apache Spark. Этот кластер будет использоваться для очистки и преобразования необработанных данных.
   5. Кластер Interactive Query Apache Hive. Этот кластер позволяет отправлять запросы к данным о продажах, визуализируя их с помощью Power BI.
   6. Виртуальная сеть Azure, поддерживаемая правилами группы безопасности сети (NSG). Эта виртуальная сеть позволяет кластерам обмениваться данными, а также защищать их связь. 

Создание кластера может занять около 20 минут.

Скрипт `resources.sh` содержит следующую команду. Эта команда использует шаблон Azure Resource Manager (`resourcestemplate.json`) для создания указанных ресурсов с требуемой конфигурацией.

```azurecli-interactive 
az group deployment create --name ResourcesDeployment \
    --resource-group $resourceGroup \
    --template-file resourcestemplate.json \
    --parameters "@resourceparameters.json"
```

Скрипт `resources.sh` также передает CSV-файлы данных о продажах в созданную учетную запись хранилища BLOB-объектов с помощью этой команды:

```
az storage blob upload-batch -d rawdata \
    --account-name <BLOB STORAGE NAME> -s ./ --pattern *.csv
```

Пароль по умолчанию для доступа к кластерам по SSH — `Thisisapassword1`. Если вы хотите изменить пароль, перейдите к файлу `resourcesparameters.json` и измените пароль для параметров `sparksshPassword`, `sparkClusterLoginPassword`, `llapClusterLoginPassword` и `llapsshPassword`.

### <a name="verify-deployment-and-collect-resource-information"></a>Проверка развертывания и получение сведений о ресурсах

1. Если вы хотите проверить состояние развертывания, перейдите к группе ресурсов на портале Azure. Выберите **Развертывания** в разделе **Параметры**. Выберите имя развертывания `ResourcesDeployment`. Здесь можно просмотреть ресурсы, которые уже успешно развернуты и те ресурсы, которые еще выполняются.
1. После завершения развертывания перейдите на портал Azure и выберите **Группы ресурсов** > <RESOURCE_GROUP_NAME>.
1. Найдите новую учетную запись хранения Azure, созданную для хранения файлов продаж. Имя учетной записи хранения начинается с `blob` и содержит случайную строку. Выполните следующее:
   1. Запишите имя учетной записи хранения для последующего использования.
   1. Выберите имя учетной записи хранилища BLOB-объектов.
   1. В левой части портала в разделе **Параметры** выберите **Ключи доступа**.
   1. Скопируйте строку в поле **Key1** и сохраните ее для последующего использования.
1. Найдите учетную запись Data Lake Storage 2-го поколения, созданную как хранилище для кластеров HDInsight. Эта учетная запись находится в той же группе ресурсов, что и учетная запись хранилища BLOB-объектов, но начинается с `adlsgen2`. Выполните следующее:
   1. Запишите имя учетной записи Data Lake Storage 2-го поколения.
   1. Выберите имя учетной записи Data Lake Storage 2-го поколения.
   1. В левой части портала в разделе **Параметры** выберите **Ключи доступа**.
   1. Скопируйте строку в поле **Key1** и сохраните ее для последующего использования.

> [!Note]
> Зная имена учетных записей хранения, можно получить их ключи, выполнив следующую команду в командной строке Azure Cloud Shell:
> ```azurecli-interactive
> az storage account keys list \
>    --account-name <STORAGE NAME> \
>    --resource-group $rg \
>    --output table
> ```

### <a name="create-a-data-factory"></a>Создание фабрики данных

Фабрика данных Azure — это инструмент, помогающий автоматизировать Azure Pipelines. Это не единственный способ выполнить эти задачи, но он отлично подходит для их автоматизации. Дополнительные сведения о Фабрике данных Azure см. в [документации по Фабрике данных Azure](https://azure.microsoft.com/services/data-factory/). 

Эта Фабрика данных будет иметь один конвейер с двумя действиями: 

- Первое действие скопирует данные из хранилища BLOB-объектов Azure в учетную запись хранения Data Lake Storage 2-го поколения для имитации приема данных.
- Второе действие будет преобразовывать данные в кластере Spark. Скрипт выполняет преобразование данных, удаляя ненужные столбцы. Он также добавляет новый столбец, который вычисляет доход, создаваемый одной транзакцией.

Чтобы настроить конвейер Фабрики данных Azure, выполните скрипт `adf.sh`:

1. Добавьте разрешения на выполнение для файла с помощью команды `chmod +x adf.sh`.
1. Выполните скрипт с помощью команды `./adf.sh`. 

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

- Выполните следующие команды, чтобы активировать конвейеры Фабрики данных в PowerShell: 

    ```powershell
    Invoke-AzDataFactoryV2Pipeline -DataFactory $df -PipelineName "CopyPipeline_k8z" 
    Invoke-AzDataFactoryV2Pipeline -DataFactory $df -PipelineName "sparkTransformPipeline"
    ```

- Откройте фабрику данных и выберите **Author & Monitor** (Создание и мониторинг). Активируйте конвейер копирования, а затем конвейер Spark с портала. Сведения о запуске конвейеров на портале см. в разделе [Активация конвейера](hdinsight-hadoop-create-linux-clusters-adf.md#trigger-a-pipeline).

Чтобы убедиться, что конвейеры выполнены, можно выполнить одно из следующих действий.

- Перейдите к разделу **Мониторинг** в фабрике данных с помощью портала.
- В Обозревателе службы хранилища Azure перейдите к учетной записи хранения Data Lake Storage 2-го поколения. Перейдите в файловую систему `files`, а затем к папке `transformed` и проверьте ее содержимое, чтобы убедиться, что конвейер выполнен.

Другие способы преобразования данных с помощью HDInsight см. в [этой статье об использовании Jupyter Notebook](/azure/hdinsight/spark/apache-spark-load-data-run-query).

### <a name="create-a-table-on-the-interactive-query-cluster-to-view-data-on-power-bi"></a>Создание таблицы в кластере интерактивных запросов для просмотра данных в Power BI

1. Скопируйте файл `query.hql` в кластер LLAP с помощью SCP:

    ```
    scp scripts/query.hql sshuser@<clustername>-ssh.azurehdinsight.net:/home/sshuser/
    ```

2. Подключитесь к кластеру LLAP по протоколу SSH, используя следующую команду, а затем введите пароль. Если вы еще не изменили файл `resourcesparameters.json`, пароль — `Thisisapassword1`.

    ```
    ssh sshuser@<clustername>-ssh.azurehdinsight.net
    ```

3. Выполните следующую команду, чтобы запустить скрипт.

    ```
    beeline -u 'jdbc:hive2://localhost:10001/;transportMode=http' -f query.hql
    ```

Этот скрипт создаст управляемую таблицу в кластере интерактивных запросов, к которому можно получить доступ из Power BI. 

### <a name="create-a-power-bi-dashboard-from-sales-data"></a>Создание информационной панели Power BI на основе данных о продажах

1. Откройте Power BI Desktop.
1. Выберите **Получение данных**.
1. Найдите **кластер интерактивных запросов HDInsight**.
1. Вставьте универсальный код ресурса (URI) для кластера. Ее необходимо указать в формате `https://<LLAP CLUSTER NAME>.azurehdinsight.net`.

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

## <a name="next-steps"></a>Дополнительная информация

> [!div class="nextstepaction"]
> [Extract, transform, and load (ETL) at scale](./hadoop/apache-hadoop-etl-at-scale.md) (Извлечение, преобразование и загрузка (ETL) в масштабе)
