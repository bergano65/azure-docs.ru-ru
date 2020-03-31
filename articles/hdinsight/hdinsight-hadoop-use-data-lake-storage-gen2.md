---
title: Использование Azure Data Lake Storage 2-го поколения с кластерами Azure HDInsight
description: Узнайте, как использовать данные Azure Data Lake Storage Gen2 с кластерами Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 02/20/2020
ms.openlocfilehash: d711cc7e58fb055eda62cfc364a5552a7d10f7bd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79272296"
---
# <a name="use-azure-data-lake-storage-gen2-with-azure-hdinsight-clusters"></a>Использование Azure Data Lake Storage 2-го поколения с кластерами Azure HDInsight

Azure Data Lake Storage Gen2 — облачный сервис хранения данных, предназначенный для анализа больших данных, построенный на хранилище Azure Blob. Data Lake Storage Gen2 сочетает в себе возможности хранения Azure Blob и хранилища azure Data Lake Data Gen1. Полученная служба предлагает функции из Azure Data Lake Storage Gen1, такие как семантика файловой системы, безопасность на уровне каталогов и безопасность файлов, а также низкозатратная многоуровневая система хранения, высокая доступность и возможности аварийного восстановления из хранилища Azure Blob.

## <a name="data-lake-storage-gen2-availability"></a>Доступность Azure Data Lake Storage 2-го поколения

Data Lake Storage Gen2 доступен в качестве опции хранения практически для всех типов кластеров Azure HDInsight как по умолчанию, так и для дополнительной учетной записи хранения. HBase, однако, может иметь только одну учетную запись Data Lake Storage Gen2.

Для полного сравнения вариантов создания кластеров с использованием Data Lake Storage Gen2 [см. Сравнить параметры хранения для использования с кластерами Azure HDInsight.](hdinsight-hadoop-compare-storage-options.md)

> [!Note]  
> После выбора Data Lake Storage Gen2 в качестве **основного типа хранилища**вы не можете выбрать учетную запись Data Lake Storage Gen1 в качестве дополнительного хранилища.

## <a name="create-a-cluster-with-data-lake-storage-gen2-through-the-azure-portal"></a>Создание кластера с хранением данных озера Gen2 через портал Azure

Чтобы создать кластер HDInsight, который использует Data Lake Storage Gen2 для хранения, выполните следующие действия, чтобы настроить учетную запись Data Lake Storage Gen2.

### <a name="create-a-user-assigned-managed-identity"></a>Создание управляемого удостоверения, назначаемого пользователем

Создайте управляемое удостоверение, назначаемое пользователем, если у вас его еще нет.

1. Войдите на [портал Azure](https://portal.azure.com).
1. В верхнем левом щелчке **Создайте ресурс.**
1. В поле поиска введите **назначенный пользователем** и нажмите **кнопку «Управляемая идентификация пользователя».**
1. Нажмите кнопку **Создать**.
1. Введите имя для управляемой идентификации, выберите правильную подписку, группу ресурсов и местоположение.
1. Нажмите кнопку **Создать**.

Более подробную информацию о том, как работают управляемые идентификаторы в Azure HDInsight, можно узнать [в Azure HDInsight.](hdinsight-managed-identities.md)

![Создание управляемого удостоверения, назначаемого пользователем](./media/hdinsight-hadoop-use-data-lake-storage-gen2/create-user-assigned-managed-identity-portal.png)

### <a name="create-a-data-lake-storage-gen2-account"></a>Создание учетной записи Azure Data Lake Storage 2-го поколения

Создайте учетную запись хранения Azure Data Lake Storage 2-го поколения.

1. Войдите на [портал Azure](https://portal.azure.com).
1. В верхнем левом щелчке **Создайте ресурс.**
1. В поле поиска введите **учетную запись хранения** и **нажмите**кнопку.
1. Нажмите кнопку **Создать**.
1. На экране **учетной записи «Создать»:**
    1. Выберите правильную группу подписки и ресурсов.
    1. Введите имя для учетной записи Data Lake Storage Gen2. Для получения дополнительной информации о [Naming conventions for Azure resources](/azure/azure-resource-manager/management/resource-name-rules#microsoftstorage)соглашениях о именованиях учетных записей хранилища см.
    1. Нажмите на вкладку **Advanced.**
    1. Нажмите **Кнопка Включено** рядом с **иерархическим пространством имен** под **Data Lake Storage Gen2**.
    1. Нажмите **Обзор и создайте**.
    1. Нажмите **Создать**

Для получения дополнительной информации о других [Quickstart: Create an Azure Data Lake Storage Gen2 storage account](../storage/blobs/data-lake-storage-quickstart-create-account.md)вариантах при создании учетной записи хранилища см.

![Снимок экрана, на котором показано создание учетной записи хранения на портале Azure](./media/hdinsight-hadoop-use-data-lake-storage-gen2/azure-data-lake-storage-account-create-advanced.png)

### <a name="set-up-permissions-for-the-managed-identity-on-the-data-lake-storage-gen2-account"></a>Настройка разрешений для управляемого удостоверения личности в учетной записи Data Lake Storage Gen2

Назначаем управляемое удостоверение роли **владельца данных Storage Blob** в учетной записи хранилища.

1. Войдите в свою учетную запись хранения на [портале Azure](https://portal.azure.com).
1. Выберите учетную запись хранилища, а затем выберите **элемент управления доступом (IAM)** для отображения параметров управления доступом для учетной записи. Выберите вкладку **Назначения ролей**, чтобы просмотреть список назначений ролей.

    ![Снимок экрана с параметрами управления доступом к хранилищу](./media/hdinsight-hadoop-use-data-lake-storage-gen2/portal-access-control.png)

1. Выберите кнопку **назначения ролей,** чтобы добавить новую роль.
1. В окне **назначения роли Добавить** выберите роль **владельца данных Storage Blob.** Затем выберите подписку с управляемым удостоверением и учетной записью хранения. Затем найдите управляемое удостоверение, назначаемое пользователем, которое было создано ранее. Наконец, выберите управляемую идентификацию, и она будет перечислена под **выбранными членами.**

    ![Снимок экрана, на котором показано, как назначить роль RBAC](./media/hdinsight-hadoop-use-data-lake-storage-gen2/add-rbac-role3-window.png)

1. Нажмите кнопку **Сохранить**. Выбранное пользователем удостоверение теперь перечислено в выбранной роли.
1. После завершения первоначальной настройки вы можете создать кластер на портале. Кластер должен быть расположен в том же регионе Azure, что и учетная запись хранения. Во вкладке **«Хранение»** меню создания кластера выберите следующие варианты:

    * Для **типа первичного хранения**выберите **Azure Data Lake Storage Gen2.**
    * В соответствии с **учетной записью Primary Storage,** поиск и выбор недавно созданной учетной записи хранения Data Lake Storage Gen2.

    * Под **identity**- выберите недавно созданную пользовательскую управляемую идентификацию.

        ![Параметры хранилища для Azure Data Lake Storage 2-го поколения с Azure HDInsight](./media/hdinsight-hadoop-use-data-lake-storage-gen2/azure-portal-cluster-storage-gentwo.png)

    > [!NOTE]
    > * Чтобы добавить вторичную учетную запись Data Lake Storage Gen2 на уровне учетной записи хранилища, просто присвоите управляемую идентификацию, созданную ранее, новой учетной записи хранения data Lake Storage Gen2, которую вы хотите добавить. Пожалуйста, имейте в виду, что добавление вторичной учетной записи Data Lake Storage Gen2 через лезвие "Дополнительные учетные записи хранения" на HDInsight не поддерживается.
    > * Вы можете включить RA-GRS или RA-RS на учетную запись хранения Azure, которую использует HDInsight. Однако создание кластера против второй конечной точки RA-GRS или RA-'RS не поддерживается.


## <a name="create-a-cluster-with-data-lake-storage-gen2-through-the-azure-cli"></a>Создание кластера с хранением данных озера Gen2 через Azure CLI

Вы можете [скачать пример файла шаблона](https://github.com/Azure-Samples/hdinsight-data-lake-storage-gen2-templates/blob/master/hdinsight-adls-gen2-template.json) и [скачать файл параметров образца](https://github.com/Azure-Samples/hdinsight-data-lake-storage-gen2-templates/blob/master/parameters.json). Прежде чем использовать шаблон и фрагмент кода Azure CLI ниже, замените следующие заполнители их правильными значениями:

| Заполнитель | Описание |
|---|---|
| `<SUBSCRIPTION_ID>` | Идентификатор подписки Azure |
| `<RESOURCEGROUPNAME>` | Группа ресурсов, в которой требуется создать новую учетную запись кластера и хранения данных. |
| `<MANAGEDIDENTITYNAME>` | Имя управляемого удостоверения, которому будут даны разрешения в учетной записи Azure Data Lake Storage Gen2. |
| `<STORAGEACCOUNTNAME>` | Новая учетная запись хранения azure Data Lake Gen2 будет создана. |
| `<CLUSTERNAME>` | Это имя вашего кластера HDInsight. |
| `<PASSWORD>` | Выбранный вами пароль для вхастыва в кластер с помощью SSH, а также панели мониторинга Ambari. |

Фрагмент кода ниже делает следующие начальные шаги:

1. Входите в учетную запись Azure.
1. Устанавливает активную подписку, в которой будут выполнены операции создания.
1. Создает новую группу ресурсов для новых действий развертывания.
1. Создает назначенную пользователем управляемую идентификацию.
1. Добавляет расширение в Azure CLI для использования функций для хранения данных Озера Gen2.
1. Создает новую учетную запись Data `--hierarchical-namespace true` Lake Storage Gen2 с помощью флага.

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

Далее, вопием на портале. Добавьте новую управляемую идентификацию пользователя в роль **вкладчика хранилища данных Хранилища** в учетной записи хранилища, описанную в шаге 3 при [использовании портала Azure.](hdinsight-hadoop-use-data-lake-storage-gen2.md)

После того, как вы назначили роль для назначенного пользователем управляемого удостоверения, разместите шаблон, используя следующий фрагмент кода.

```azurecli
az group deployment create --name HDInsightADLSGen2Deployment \
    --resource-group <RESOURCEGROUPNAME> \
    --template-file hdinsight-adls-gen2-template.json \
    --parameters parameters.json
```

## <a name="create-a-cluster-with-data-lake-storage-gen2-through-azure-powershell"></a>Создание кластера с хранением данных озера Gen2 через Azure PowerShell

Использование PowerShell для создания кластера HDInsight с Azure Data Lake Storage Gen2 в настоящее время не поддерживается.

## <a name="access-control-for-data-lake-storage-gen2-in-hdinsight"></a>Контроль доступа для хранения данных озера Gen2 в HDInsight

### <a name="what-kinds-of-permissions-does-data-lake-storage-gen2-support"></a>Какие типы разрешений поддерживаются в Azure Data Lake Storage 2-го поколения?

Data Lake Storage Gen2 использует модель управления доступом, которая поддерживает как ролевые элементы управления доступом (RBAC), так и POSIX-подобные списки управления доступом (ACLs). Data Lake Storage Gen1 поддерживает списки контроля доступа только для контроля доступа к данным.

RBAC использует ролевые назначения для эффективного применения наборов разрешений для пользователей, групп и основ служб для ресурсов Azure. Как правило, эти ресурсы Azure ограничиваются ресурсами верхнего уровня (например, учетными записями хранения Azure). Для хранилища Azure, а также Data Lake Storage Gen2 этот механизм был распространен на ресурс файловой системы.

 Для получения дополнительной информации о разрешениях файлов с RBAC [см.](../storage/blobs/data-lake-storage-access-control.md#azure-role-based-access-control-rbac)

Для получения дополнительной информации о разрешениях файлов с ACL смотрите [списки управления доступом в файлах и каталогах.](../storage/blobs/data-lake-storage-access-control.md#access-control-lists-on-files-and-directories)

### <a name="how-do-i-control-access-to-my-data-in-data-lake-storage-gen2"></a>Как контролировать доступ к своим данным в Data Lake Storage Gen2?

Способность кластера HDInsight получать доступ к файлам в Data Lake Storage Gen2 контролируется с помощью управляемых идентификаторов. Управляемая идентификация — это идентификация, зарегистрированная в Active Directory Azure (Azure AD), учетные данные которого управляются Azure. С управляемыми учетными данными не нужно регистрировать принципы служб в Azure AD или сохранять учетные данные, такие как сертификаты.

Службы Azure имеют два типа управляемых идентификаторов: назначенные системой и назначенные пользователем. HDInsight использует назначенные пользователем управляемые идентификаторы для доступа к Data Lake Storage Gen2. Назначаемое пользователем управляемое удостоверение создается как изолированный ресурс Azure. При этом Azure создает удостоверение в доверенном клиенте Azure AD используемой подписки. После создания удостоверения оно может быть назначено одному или нескольким экземплярам служб Azure.

Управление жизненным циклом назначаемого пользователем удостоверения осуществляется отдельно от жизненного цикла экземпляров служб Azure, для которых оно назначено. Более подробную информацию об управляемых идентификаторах можно узнать о том, [как работают управляемые идентификаторы для ресурсов Azure?.](../active-directory/managed-identities-azure-resources/overview.md#how-does-the-managed-identities-for-azure-resources-work)

### <a name="how-do-i-set-permissions-for-azure-ad-users-to-query-data-in-data-lake-storage-gen2-by-using-hive-or-other-services"></a>Как установить разрешения для пользователей Azure AD на запрос данных в Data Lake Storage Gen2 с помощью Hive или других служб?

Чтобы установить разрешения для пользователей на запрос данных, используйте группы безопасности Azure AD в качестве назначенного принципала в АКЛ. Не напрямую назначайте разрешения на доступ к файлам отдельным пользователям или директорам служб. При использовании групп безопасности Azure AD для управления потоком разрешений можно добавлять и удалять принципы пользователей или служб без повторного применения ACLs ко всей структуре каталогов. Нужно просто добавить пользователей в соответствующую группу безопасности Azure AD или удалить их из нее. ACLs не наследуются, поэтому повторное применение ACL требует обновления ACL на каждом файле и субдиректории.

## <a name="access-files-from-the-cluster"></a>Доступ к файлам из кластера

Существует несколько способов доступа к файлам в Data Lake Storage Gen2 из кластера HDInsight.

* **С помощью полного доменного имени**. При таком подходе необходимо указать полный путь к файлу, к которому требуется доступ.

    ```
    abfs://<containername>@<accountname>.dfs.core.windows.net/<file.path>/
    ```

* **Используя сокращенный формат пути**. При таком подходе вы заменяете путь до корня кластера:

    ```
    abfs:///<file.path>/
    ```

* **С помощью относительного пути**. При таком подходе указывается только относительный путь к файлу, к которому требуется доступ.

    ```
    /<file.path>/
    ```

### <a name="data-access-examples"></a>Примеры доступа к данным

Примеры основаны на [съеме Ssh](./hdinsight-hadoop-linux-use-ssh-unix.md) к головному узлам кластера. В примерах используются все три схемы URI. `CONTAINERNAME` Заменить `STORAGEACCOUNT` и с соответствующими значениями

#### <a name="a-few-hdfs-commands"></a>Несколько команд hdfs

1. Создайте файл локального хранилища.

    ```bash
    touch testFile.txt
    ```

1. Создание каталогов по кластерной памяти.

    ```bash
    hdfs dfs -mkdir abfs://CONTAINERNAME@STORAGEACCOUNT.dfs.core.windows.net/sampledata1/
    hdfs dfs -mkdir abfs:///sampledata2/
    hdfs dfs -mkdir /sampledata3/
    ```

1. Копирование данных из локального хранилища в кластерное хранилище.

    ```bash
    hdfs dfs -copyFromLocal testFile.txt  abfs://CONTAINERNAME@STORAGEACCOUNT.dfs.core.windows.net/sampledata1/
    hdfs dfs -copyFromLocal testFile.txt  abfs:///sampledata2/
    hdfs dfs -copyFromLocal testFile.txt  /sampledata3/
    ```

1. Список содержимого каталога на кластерных хранилищах.

    ```bash
    hdfs dfs -ls abfs://CONTAINERNAME@STORAGEACCOUNT.dfs.core.windows.net/sampledata1/
    hdfs dfs -ls abfs:///sampledata2/
    hdfs dfs -ls /sampledata3/
    ```

#### <a name="creating-a-hive-table"></a>Создание таблицы Hive

Три местоположения файлов отображаются для иллюстративных целей. Для фактического выполнения используйте `LOCATION` только одну из записей.

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
* [Учебник: Извлекайте, преобразовывали и загружали данные с помощью интерактивного запроса в Azure HDInsight](./interactive-query/interactive-query-tutorial-analyze-flight-data.md)
