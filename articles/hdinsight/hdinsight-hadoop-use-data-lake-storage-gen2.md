---
title: Использование Azure Data Lake Storage 2-го поколения с кластерами Azure HDInsight
description: Узнайте, как использовать Azure Data Lake Storage 2-го поколения с кластерами Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 89b86124d6da0d0d659ed0673585eadbf1008aa3
ms.sourcegitcommit: 16c5374d7bcb086e417802b72d9383f8e65b24a7
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/08/2019
ms.locfileid: "73847293"
---
# <a name="use-azure-data-lake-storage-gen2-with-azure-hdinsight-clusters"></a>Использование Azure Data Lake Storage 2-го поколения с кластерами Azure HDInsight

Azure Data Lake Storage 2-го поколения — это служба облачного хранилища, выделенная для аналитики больших данных, созданная на основе хранилища BLOB-объектов Azure. Data Lake Storage 2-го поколения сочетает возможности хранилища BLOB-объектов Azure и Azure Data Lake Storage 1-го поколения. Итоговая служба предлагает функции из Azure Data Lake Storage 1-го поколения, такие как семантика файловой системы, безопасность на уровне каталога и безопасности на уровне файлов и масштабируемость, а также недорогие, многоуровневые хранилища, высокая доступность и возможности аварийного восстановления. из хранилища BLOB-объектов Azure.

## <a name="data-lake-storage-gen2-availability"></a>Доступность Azure Data Lake Storage 2-го поколения

Data Lake Storage 2-го поколения доступен в качестве хранилища практически для всех типов кластеров Azure HDInsight как по умолчанию, так и в дополнительной учетной записи хранения. Однако HBase может иметь только одну учетную запись Data Lake Storage 2-го поколения.

Полное сравнение параметров создания кластера с помощью Data Lake Storage 2-го поколения см. в статье [Сравнение вариантов хранения для использования с кластерами Azure HDInsight](hdinsight-hadoop-compare-storage-options.md).

> [!Note]  
> Выбрав Data Lake Storage 2-го поколения в качестве **основного типа хранилища**, вы не сможете выбрать учетную запись Data Lake Storage 1-го поколения как дополнительное хранилище.

## <a name="create-a-cluster-with-data-lake-storage-gen2-through-the-azure-portal"></a>Создание кластера с Data Lake Storage 2-го поколения через портал Azure

Чтобы создать кластер HDInsight, который использует Data Lake Storage 2-го поколения для хранилища, выполните следующие действия, чтобы настроить учетную запись Data Lake Storage 2-го поколения.

### <a name="create-a-user-assigned-managed-identity"></a>Создание управляемого удостоверения, назначаемого пользователем

Создайте управляемое удостоверение, назначаемое пользователем, если у вас его еще нет.

1. Войдите на [портал Azure](https://portal.azure.com).
1. В левом верхнем углу щелкните **создать ресурс**.
1. В поле поиска введите " **пользователь назначен** " и щелкните " **управляемое удостоверение, назначенное пользователем**".
1. Щелкните **Создать**.
1. Введите имя управляемого удостоверения, выберите подходящую подписку, группу ресурсов и расположение.
1. Щелкните **Создать**.

Дополнительные сведения о работе управляемых удостоверений в Azure HDInsight см. [в статье управляемые удостоверения в Azure hdinsight](hdinsight-managed-identities.md).

![Создание управляемого удостоверения, назначаемого пользователем](./media/hdinsight-hadoop-use-data-lake-storage-gen2/create-user-assigned-managed-identity-portal.png)

### <a name="create-a-data-lake-storage-gen2-account"></a>Создание учетной записи Azure Data Lake Storage 2-го поколения

Создайте учетную запись хранения Azure Data Lake Storage 2-го поколения.

1. Войдите на [портал Azure](https://portal.azure.com).
1. В левом верхнем углу щелкните **создать ресурс**.
1. В поле поиска введите **Storage** и щелкните **учетная запись хранения**.
1. Щелкните **Создать**.
1. На экране **Создание учетной записи хранения** выполните следующие действия.
    1. Выберите подходящую подписку и группу ресурсов.
    1. Введите имя учетной записи Data Lake Storage 2-го поколения. Дополнительные сведения о соглашениях об именовании учетных записей хранения см. в разделе [соглашения об именовании для ресурсов Azure](/azure/architecture/best-practices/resource-naming#storage).
    1. Перейдите на вкладку **Дополнительно** .
    1. Щелкните **включено** рядом с **иерархическим пространством имен** в разделе **Data Lake Storage 2-го поколения**.
    1. Щелкните **Review + create** (Просмотреть и создать).
    1. Нажмите кнопку **Создать**

Дополнительные сведения о других параметрах во время создания учетной записи хранения см. в разделе [Краткое руководство. Создание учетной записи хранения Azure Data Lake Storage 2-го поколения](../storage/blobs/data-lake-storage-quickstart-create-account.md).

![Снимок экрана, на котором показано создание учетной записи хранения на портале Azure](./media/hdinsight-hadoop-use-data-lake-storage-gen2/azure-data-lake-storage-account-create-advanced.png)

### <a name="set-up-permissions-for-the-managed-identity-on-the-data-lake-storage-gen2-account"></a>Настройка разрешений для управляемого удостоверения в учетной записи Data Lake Storage 2-го поколения

Назначьте управляемое удостоверение роли **владельца данных BLOB-объекта хранилища** в учетной записи хранения.

1. Войдите в свою учетную запись хранения на [портале Azure](https://portal.azure.com).
1. Выберите учетную запись хранения, а затем щелкните **Управление доступом (IAM)** , чтобы отобразить параметры управления доступом для учетной записи. Выберите вкладку **Назначения ролей**, чтобы просмотреть список назначений ролей.

    ![Снимок экрана с параметрами управления доступом к хранилищу](./media/hdinsight-hadoop-use-data-lake-storage-gen2/portal-access-control.png)

1. Нажмите кнопку **+ добавить назначение роли** , чтобы добавить новую роль.
1. В окне **Добавление назначения ролей** выберите роль **владельца данных BLOB-объекта хранилища** . Затем выберите подписку с управляемым удостоверением и учетной записью хранения. Затем найдите управляемое удостоверение, назначаемое пользователем, которое было создано ранее. Наконец, выберите управляемое удостоверение, которое будет отображаться в разделе **Выбранные элементы**.

    ![Снимок экрана, на котором показано, как назначить роль RBAC](./media/hdinsight-hadoop-use-data-lake-storage-gen2/add-rbac-role3-window.png)

1. Щелкните **Сохранить**. Выбранное пользователем удостоверение теперь отображается под выбранной ролью.
1. После завершения первоначальной настройки вы можете создать кластер на портале. Кластер должен быть расположен в том же регионе Azure, что и учетная запись хранения. В разделе меню создания кластера **Хранилище** выберите следующие параметры:

    * В качестве **основного типа хранилища**выберите **Azure Data Lake Storage 2-го поколения**.
    * В разделе **выберите учетную запись хранения**найдите и выберите только что созданную учетную запись хранения data Lake Storage 2-го поколения.

        ![Параметры хранилища для Azure Data Lake Storage 2-го поколения с Azure HDInsight](./media/hdinsight-hadoop-use-data-lake-storage-gen2/primary-storage-type-adls-gen2.png)

    * В разделе **удостоверение**выберите правильную подписку и вновь созданное назначенное пользователем управляемое удостоверение.

        ![Параметры идентификации для использования Data Lake Storage 2-го поколения с HDInsight](./media/hdinsight-hadoop-use-data-lake-storage-gen2/managed-identity-cluster-creation.png)

> [!Note]
> Чтобы добавить вторичную учетную запись Data Lake Storage 2-го поколения, на уровне учетной записи хранения просто назначьте управляемое удостоверение, созданное ранее, в новую учетную запись хранения Data Lake Storage 2-го поколения, которую вы хотите добавить. Рекомендуем добавить дополнительную учетную запись Data Lake Storage 2-го поколения с помощью колонки "дополнительные учетные записи хранения" в HDInsight не поддерживается.

## <a name="create-a-cluster-with-data-lake-storage-gen2-through-the-azure-cli"></a>Создание кластера с Data Lake Storage 2-го поколения через Azure CLI

Вы можете [скачать пример файла шаблона](https://github.com/Azure-Samples/hdinsight-data-lake-storage-gen2-templates/blob/master/hdinsight-adls-gen2-template.json) и [скачать пример файла параметров](https://github.com/Azure-Samples/hdinsight-data-lake-storage-gen2-templates/blob/master/parameters.json). Перед использованием шаблона и приведенного ниже фрагмента кода Azure CLI Замените следующие заполнители их правильными значениями:

| Placeholder | Description (Описание) |
|---|---|
| `<SUBSCRIPTION_ID>` | Идентификатор подписки Azure |
| `<RESOURCEGROUPNAME>` | Группа ресурсов, в которой вы хотите создать новый кластер и учетную запись хранения. |
| `<MANAGEDIDENTITYNAME>` | Имя управляемого удостоверения, которому будут предоставлены разрешения на учетную запись Azure Data Lake Storage 2-го поколения. |
| `<STORAGEACCOUNTNAME>` | Новая учетная запись Azure Data Lake Storage 2-го поколения, которая будет создана. |
| `<CLUSTERNAME>` | Это имя вашего кластера HDInsight. |
| `<PASSWORD>` | Выбранный пароль для входа в кластер с помощью SSH, а также панели мониторинга Ambari. |

Приведенный ниже фрагмент кода выполняет следующие начальные действия.

1. Выполняет вход в учетную запись Azure.
1. Задает активную подписку, в которой будут выполняться операции создания.
1. Создает новую группу ресурсов для новых действий развертывания.
1. Создание управляемого удостоверения, назначенного пользователем.
1. Добавляет расширение в Azure CLI для использования функций Data Lake Storage 2-го поколения.
1. Создает новую учетную запись Data Lake Storage 2-го поколения с помощью флага `--hierarchical-namespace true`.

```azurecli
az login
az account set --subscription <SUBSCRIPTION_ID>

# Create resource group
az group create --name <RESOURCEGROUPNAME> --location eastus

# Create managed identity
az identity create -g <RESOURCEGROUPNAME> -n <MANAGEDIDENTITYNAME>

az extension add --name storage-preview

az storage account create --name <STORAGEACCOUNTNAME> \
    --resource-group <RESOURCEGROUPNAME> \
    --location eastus --sku Standard_LRS \
    --kind StorageV2 --hierarchical-namespace true
```

Затем войдите на портал. Добавьте новое назначенное пользователем управляемое удостоверение в роль **участник данных BLOB-объекта хранилища** в учетной записи хранения, как описано в шаге 3 в разделе [Использование портал Azure](hdinsight-hadoop-use-data-lake-storage-gen2.md).

После назначения роли для назначаемого пользователем управляемого удостоверения разверните шаблон с помощью следующего фрагмента кода.

```azurecli
az group deployment create --name HDInsightADLSGen2Deployment \
    --resource-group <RESOURCEGROUPNAME> \
    --template-file hdinsight-adls-gen2-template.json \
    --parameters parameters.json
```

## <a name="access-control-for-data-lake-storage-gen2-in-hdinsight"></a>Контроль доступа для Data Lake Storage 2-го поколения в HDInsight

### <a name="what-kinds-of-permissions-does-data-lake-storage-gen2-support"></a>Какие типы разрешений поддерживаются в Azure Data Lake Storage 2-го поколения?

Data Lake Storage 2-го поколения использует модель управления доступом, которая поддерживает как управление доступом на основе ролей (RBAC), так и POSIX-подобные списки управления доступом (ACL). Data Lake Storage 1-го поколения поддерживает списки управления доступом только для управления доступом к данным.

RBAC использует назначения ролей для эффективного применения наборов разрешений для пользователей, групп и субъектов-служб для ресурсов Azure. Как правило, ресурсы Azure ограничены ресурсами верхнего уровня (например, учетными записями хранения Azure). Для службы хранилища Azure, а также Data Lake Storage 2-го поколения этот механизм был расширен до ресурса файловой системы.

 Дополнительные сведения о разрешениях для файлов с помощью RBAC см. в статье [Управление доступом на основе ролей (RBAC) в Azure](../storage/blobs/data-lake-storage-access-control.md#azure-role-based-access-control-rbac).

Дополнительные сведения о разрешениях для файлов с помощью ACL см. [в разделе списки управления доступом к файлам и каталогам](../storage/blobs/data-lake-storage-access-control.md#access-control-lists-on-files-and-directories).

### <a name="how-do-i-control-access-to-my-data-in-data-lake-storage-gen2"></a>Разделы справки управлять доступом к моим данным в Data Lake Storage 2-го поколения?

Доступ к файлам в Data Lake Storage 2-го поколения управляется кластером HDInsight с помощью управляемых удостоверений. Управляемое удостоверение — это удостоверение, зарегистрированное в Azure Active Directory (Azure AD), учетные данные которых управляются Azure. При использовании управляемых удостоверений вам не нужно регистрировать субъекты-службы в Azure AD или хранить учетные данные, например сертификаты.

Службы Azure имеют два типа управляемых удостоверений: назначенные системой и назначенные пользователем. HDInsight использует назначенные пользователем управляемые удостоверения для доступа к Data Lake Storage 2-го поколения. Назначаемое пользователем управляемое удостоверение создается как изолированный ресурс Azure. При этом Azure создает удостоверение в доверенном клиенте Azure AD используемой подписки. После создания удостоверения оно может быть назначено одному или нескольким экземплярам служб Azure.

Управление жизненным циклом назначаемого пользователем удостоверения осуществляется отдельно от жизненного цикла экземпляров служб Azure, для которых оно назначено. Дополнительные сведения об управляемых удостоверениях см. [в статье как работают управляемые удостоверения для ресурсов Azure](../active-directory/managed-identities-azure-resources/overview.md#how-does-the-managed-identities-for-azure-resources-work).

### <a name="how-do-i-set-permissions-for-azure-ad-users-to-query-data-in-data-lake-storage-gen2-by-using-hive-or-other-services"></a>Разделы справки задать разрешения для пользователей Azure AD на запрос данных в Data Lake Storage 2-го поколения с помощью Hive или других служб?

Чтобы задать разрешения для запроса данных для пользователей, используйте группы безопасности Azure AD в качестве назначенного участника в списке ACL. Не присваивайте напрямую разрешения на доступ к файлам отдельным пользователям или субъектам-службам. При использовании групп безопасности Azure AD для управления потоком разрешений можно добавлять и удалять пользователей или субъектов-служб без повторного применения списков ACL ко всей структуре каталогов. Нужно просто добавить пользователей в соответствующую группу безопасности Azure AD или удалить их из нее. Списки управления доступом не наследуются, поэтому для повторного применения ACL требуется обновление списка управления доступом для каждого файла и подкаталога.

## <a name="access-files-from-the-cluster"></a>Доступ к файлам из кластера

Существует несколько способов доступа к файлам в Data Lake Storage 2-го поколения из кластера HDInsight.

* **С помощью полного доменного имени**. При таком подходе необходимо указать полный путь к файлу, к которому требуется доступ.

    ```
    abfs://<containername>@<accountname>.dfs.core.windows.net/<file.path>/
    ```

* **Используя сокращенный формат пути**. При таком подходе путь к корню кластера заменяется на:

    ```
    abfs:///<file.path>/
    ```

* **С помощью относительного пути**. При таком подходе указывается только относительный путь к файлу, к которому требуется доступ.

    ```
    /<file.path>/
    ```

### <a name="data-access-examples"></a>Примеры доступа к данным

Примеры основаны на [SSH-подключении](./hdinsight-hadoop-linux-use-ssh-unix.md) к головному узлу кластера. В примерах используются все три схемы URI. Замените `CONTAINERNAME` и `STORAGEACCOUNT` соответствующими значениями.

#### <a name="a-few-hdfs-commands"></a>Несколько команд HDFS

1. Создание простого файла в локальном хранилище.

    ```bash
    touch testFile.txt
    ```

1. Создание каталогов в хранилище кластера.

    ```bash
    hdfs dfs -mkdir abfs://CONTAINERNAME@STORAGEACCOUNT.dfs.core.windows.net/sampledata1/
    hdfs dfs -mkdir abfs:///sampledata2/
    hdfs dfs -mkdir /sampledata3/
    ```

1. Копирование данных из локального хранилища в хранилище кластера.

    ```bash
    hdfs dfs -copyFromLocal testFile.txt  abfs://CONTAINERNAME@STORAGEACCOUNT.dfs.core.windows.net/sampledata1/
    hdfs dfs -copyFromLocal testFile.txt  abfs:///sampledata2/
    hdfs dfs -copyFromLocal testFile.txt  /sampledata3/
    ```

1. Список содержимого каталога в хранилище кластера.

    ```bash
    hdfs dfs -ls abfs://CONTAINERNAME@STORAGEACCOUNT.dfs.core.windows.net/sampledata1/
    hdfs dfs -ls abfs:///sampledata2/
    hdfs dfs -ls /sampledata3/
    ```

#### <a name="creating-a-hive-table"></a>Создание таблицы Hive

Для наглядности показаны три расположения файлов. Для фактического выполнения используйте только одну из записей `LOCATION`.

```hql
DROP TABLE myTable;
CREATE EXTERNAL TABLE myTable (
    t1 string,
    t2 string,
    t3 string,
    t4 string,
    t5 string,
    t6 string,
    t7 string)
ROW FORMAT DELIMITED FIELDS TERMINATED BY ' '
STORED AS TEXTFILE
LOCATION 'abfs://CONTAINERNAME@STORAGEACCOUNT.dfs.core.windows.net/example/data/';
LOCATION 'abfs:///example/data/';
LOCATION '/example/data/';
```

## <a name="next-steps"></a>Дальнейшие действия

* [Azure HDInsight integration with Data Lake Storage Gen2 preview - ACL and security update](https://azure.microsoft.com/blog/azure-hdinsight-integration-with-data-lake-storage-gen-2-preview-acl-and-security-update/) (Интеграция Azure HDInsight с Data Lake Storage 2-го поколения (предварительная версия) — обновление списка управления доступом и системы безопасности)
* [Общие сведения о хранилище Azure Data Lake Storage Gen2 (предварительная версия)](../storage/blobs/data-lake-storage-introduction.md)
* [Руководство. Извлечение, преобразование и загрузка данных с помощью интерактивного запроса в Azure HDInsight](./interactive-query/interactive-query-tutorial-analyze-flight-data.md)
