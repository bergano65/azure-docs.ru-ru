---
title: Руководство по Создание кластеров в Azure HDInsight по запросу с помощью Фабрики данных
description: 'Учебник: узнайте, как создавать кластеры Apache Hadoop в HDInsight по запросу с помощью Фабрики данных Azure.'
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: tutorial
ms.date: 10/09/2019
ms.openlocfilehash: 1d1ddb84c000efaf58356ffdd15382e0b74aa744
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/04/2019
ms.locfileid: "73494876"
---
# <a name="tutorial-create-on-demand-apache-hadoop-clusters-in-hdinsight-using-azure-data-factory"></a>Руководство по Создание кластеров Apache Hadoop в HDInsight по запросу с помощью Фабрики данных Azure

[!INCLUDE [selector](../../includes/hdinsight-create-linux-cluster-selector.md)]

Из этого учебника вы узнаете, как создать кластер [Apache Hadoop](https://hadoop.apache.org/) по запросу в Azure HDInsight с помощью Фабрики данных Azure. Затем как использовать конвейеры данных в фабрике данных Azure для выполнения заданий Hive и удалить кластер. Изучив это руководство вы узнаете, как реализовать запуск задания больших данных, когда создание кластера, выполнение задания и удаление кластера выполняются по расписанию.

В рамках этого руководства рассматриваются следующие задачи:

> [!div class="checklist"]
> * Создание учетной записи хранения Azure
> * Суть происходящего в фабрике данных Azure
> * Создание фабрики данных с помощью портала Azure
> * Создание связанных служб
> * Создание конвейера
> * Активация конвейера
> * Мониторинг конвейера
> * Проверка выходных данных

Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/), прежде чем начинать работу.

## <a name="prerequisites"></a>Предварительные требования

* Установите [модуль Az](https://docs.microsoft.com/powershell/azure/overview) для PowerShell.

* Субъект-служба Azure Active Directory. После создания субъект-службы, обязательно получите **идентификатор приложения** и **ключ проверки подлинности** с помощью инструкций в соответствующей статье. Эти значения вам понадобятся позже (в рамках этого руководства). Кроме того, убедитесь, что субъект-службе должна быть назначена роль *участника* подписки или группы ресурсов, в которой создается кластер. Инструкции по получению требуемых значений и назначению правильных ролей см. в разделе [Создание субъект-службы Azure Active Directory ](../active-directory/develop/howto-create-service-principal-portal.md).

## <a name="create-preliminary-azure-objects"></a>Создание предварительных объектов Azure

В этом разделе вы создадите различные объекты, используемые для кластера HDInsight, который создается по требованию. Созданная учетная запись хранения будет содержать пример сценария [HiveQL](https://cwiki.apache.org/confluence/display/Hive/LanguageManual) (`partitionweblogs.hql`), который позволяет моделировать пример задания [Apache Hive](https://hive.apache.org/), выполняющегося в кластере.

В этом разделе используется сценарий Azure PowerShell для создания учетной записи хранения и копирования необходимых файлов в учетную запись хранения. Ниже описаны действия для примера скрипта Azure PowerShell, приведенного в этом разделе.

1. Входит в Azure.
2. Создает группы ресурсов Azure.
3. Создает учетную запись хранения Azure.
4. Создание контейнера больших двоичных объектов в учетной записи хранения
5. Копирует пример сценария HiveQL (**partitionweblogs.hql**) в контейнер больших двоичных объектов. Сценарий доступен по адресу [https://hditutorialdata.blob.core.windows.net/adfhiveactivity/script/partitionweblogs.hql](https://hditutorialdata.blob.core.windows.net/adfhiveactivity/script/partitionweblogs.hql). Пример сценария, который уже доступен в другом общедоступном контейнере больших двоичных объектов. Приведенный ниже сценарий PowerShell создает копии этих файлов в учетной записи хранилища Azure, которую он создал.

**Чтобы создать учетную запись хранения и скопировать файлы с помощью Azure PowerShell:**

> [!IMPORTANT]  
> Укажите имена для группы ресурсов Azure и учетную запись хранения Azure, которая будет создана с помощью скрипта.
> Запишите **имя группы ресурсов**, **имя учетной записи хранения** и **ключ учетной записи хранения**, выводимые скриптом. Они потребуются в следующем разделе.

```powershell
$resourceGroupName = "<Azure Resource Group Name>"
$storageAccountName = "<Azure Storage Account Name>"
$location = "East US"

$sourceStorageAccountName = "hditutorialdata"  
$sourceContainerName = "adfv2hiveactivity"

$destStorageAccountName = $storageAccountName
$destContainerName = "adfgetstarted" # don't change this value.

####################################
# Connect to Azure
####################################
#region - Connect to Azure subscription
Write-Host "`nConnecting to your Azure subscription ..." -ForegroundColor Green
$sub = Get-AzSubscription -ErrorAction SilentlyContinue
if(-not($sub))
{
    Connect-AzAccount
}

# If you have multiple subscriptions, set the one to use
# Select-AzSubscription -SubscriptionId "<SUBSCRIPTIONID>"

#endregion

####################################
# Create a resource group, storage, and container
####################################

#region - create Azure resources
Write-Host "`nCreating resource group, storage account and blob container ..." -ForegroundColor Green

New-AzResourceGroup `
    -Name $resourceGroupName `
    -Location $location

New-AzStorageAccount `
    -ResourceGroupName $resourceGroupName `
    -Name $destStorageAccountName `
    -Kind StorageV2 `
    -Location $location `
    -SkuName Standard_LRS `
    -EnableHttpsTrafficOnly 1

$destStorageAccountKey = (Get-AzStorageAccountKey `
    -ResourceGroupName $resourceGroupName `
    -Name $destStorageAccountName)[0].Value

$sourceContext = New-AzStorageContext `
    -StorageAccountName $sourceStorageAccountName `
    -Anonymous

$destContext = New-AzStorageContext `
    -StorageAccountName $destStorageAccountName `
    -StorageAccountKey $destStorageAccountKey

New-AzStorageContainer `
    -Name $destContainerName `
    -Context $destContext
#endregion

####################################
# Copy files
####################################
#region - copy files
Write-Host "`nCopying files ..." -ForegroundColor Green

$blobs = Get-AzStorageBlob `
    -Context $sourceContext `
    -Container $sourceContainerName `
    -Blob "hivescripts\hivescript.hql"

$blobs|Start-AzStorageBlobCopy `
    -DestContext $destContext `
    -DestContainer $destContainerName `
    -DestBlob "hivescripts\partitionweblogs.hql"

Write-Host "`nCopied files ..." -ForegroundColor Green
Get-AzStorageBlob `
    -Context $destContext `
    -Container $destContainerName
#endregion

Write-host "`nYou will use the following values:" -ForegroundColor Green
write-host "`nResource group name: $resourceGroupName"
Write-host "Storage Account Name: $destStorageAccountName"
write-host "Storage Account Key: $destStorageAccountKey"

Write-host "`nScript completed" -ForegroundColor Green
```

**Проверка создания учетной записи хранения**

1. Выполните вход на [портал Azure](https://portal.azure.com).
1. В левой части последовательно выберите **Все службы** > **Общие** > **Группы ресурсов**.
1. Выберите имя группы ресурсов, созданной в сценарии PowerShell. Если отображается слишком много групп ресурсов, используйте фильтр.
1. В представлении **Обзор** должен отображаться один ресурс, если только группа ресурсов не является общей для других проектов. Этим ресурсом будет учетная запись хранения с именем, которое вы указали ранее. Выберите имя учетной записи хранения.
1. Щелкните плитку **Контейнеры**.
1. Выберите контейнер **adfgetstarted** . Перед вами папка с именем **hivescripts**.
1. Откройте папку, она должна содержать пример файла сценария **partitionweblogs.hql**.

## <a name="understand-the-azure-data-factory-activity"></a>Суть происходящего в фабрике данных Azure

[Фабрика данных Azure](../data-factory/introduction.md) координирует и автоматизирует процессы перемещения и преобразования данных. С ее помощью вы можете создать кластер Hadoop в HDInsight, когда это требуется для обработки входящих срезов данных. После завершения обработки кластер можно удалить.

В фабрике данных Azure фабрика данных может содержать один или несколько конвейеров. Конвейер данных включает одно или несколько действий. Существует два вида действий:

- Для перемещения данных из исходного хранилища данных в целевое используются [действия перемещения данных](../data-factory/copy-activity-overview.md).
- [Действия по преобразованию данных](../data-factory/transform-data.md). Для перемещения и обработки данных используются действия преобразования данных. Действие Hive HDInsight — одно из действий преобразования, которое поддерживает фабрика данных. В этом руководстве используется действие преобразования Hive.

В этой статье идет речь о настройке действия Hive для создания по требованию кластера Hadoop в HDInsight. При выполнении действия по обработке данных, происходит следующее.

1. Автоматически создается кластер Hadoop HDInsight для своевременной обработки среза. 

2. Для обработки входных данных выполняется скрипт HiveQL в кластере. В этом руководстве описываются действия скрипта HiveQL, связанного с действием Hive.

    - Он использует существующую таблицу (*hivesampletable*) для создания другой таблицы **HiveSampleOut**.
    - Скрипт заполняет таблицу **HiveSampleOut** только определенными столбцами из исходной таблицы *hivesampletable*.
    
3. По завершении обработки кластер Hadoop HDInsight удаляется и не используется в течение заданного времени (параметр timeToLive). Если во время простоя (параметр timeToLive) можно обработать следующий срез данных, для этого используется тот же кластер.  

## <a name="create-a-data-factory"></a>Создание фабрики данных

1. Войдите на [портале Azure](https://portal.azure.com/).

2. В меню слева последовательно выберите **+ Создать ресурс** > **Аналитика** > **Фабрика данных**.

    ![Фабрика данных Azure на портале](./media/hdinsight-hadoop-create-linux-clusters-adf/data-factory-azure-portal.png "Фабрика данных Azure на портале")

3. Введите или выберите следующие значения для плитки **Новая фабрика данных**.

    |Свойство  |Значение  |
    |---------|---------|
    |ИМЯ | Введите имя фабрики данных. Оно должно быть глобально уникальным.|
    |Subscription | Выберите подписку Azure. |
    |Resource group | Выберите **Использовать существующую** и затем выберите группу ресурсов, созданную с помощью сценария PowerShell. |
    |Version (версия) | Оставьте **V2**. |
    |Location | Автоматически задается то расположение, которое было указано при предыдущем создании группы ресурсов. В этом руководстве расположение установлено как **восточная часть США**. |
    |Включение доступа к GIT|Снимите этот флажок.|

    ![Создание Фабрики данных Azure с помощью портала Azure](./media/hdinsight-hadoop-create-linux-clusters-adf/create-data-factory-portal.png "Создание Фабрики данных Azure с помощью портала Azure")

4. Нажмите кнопку **Создать**. Создание фабрики данных может занять от 2 до 4 минут.

5. После создания фабрики данных вы получите уведомление **Развертывание прошло успешно** с кнопкой **Перейти к ресурсу**.  Нажмите кнопку **Перейти к ресурсу**, чтобы открыть представление фабрики данных по умолчанию.

6. Выберите **Создание и мониторинг** для запуска портала создания и наблюдения фабрики данных Azure.

    ![Общие сведения о портале Фабрики данных Azure](./media/hdinsight-hadoop-create-linux-clusters-adf/data-factory-portal-overview.png "Общие сведения о Фабрике данных Azure")

## <a name="create-linked-services"></a>Создание связанных служб

В этом разделе вы создаете две связанные службы на своей фабрике данных.

- **Связанную службу хранилища Azure**, которая связывает учетную запись хранения Azure с фабрикой данных. Это хранилище используется кластером HDInsight по запросу. Оно также содержит сценарий Hive, выполняющийся в кластере.
- **Связанную службу HDInsight по запросу**. Фабрика данных Azure автоматически создает кластер HDInsight и запускает сценарий Hive. Кластер Hadoop удаляется, если он не используется в течение заданного времени.

### <a name="create-an-azure-storage-linked-service"></a>Создание связанной службы хранилища Azure

1. В левой области страницы **Начать работу** выберите значок **Создание**.

    ![Создание связанной службы Фабрики данных Azure](./media/hdinsight-hadoop-create-linux-clusters-adf/data-factory-edit-tab.png "Создание связанной службы Фабрики данных Azure")

2. Выберите в нижнем левом углу окна кнопку **Подключения**, а затем выберите **+Создать**.

    ![Создание подключений в Фабрике данных Azure](./media/hdinsight-hadoop-create-linux-clusters-adf/data-factory-create-new-connection.png "Создание подключений в Фабрике данных Azure")

3. В диалоговом окне **New Linked Service** (Новая связанная служба) выберите **Хранилище BLOB-объектов Azure** и щелкните **Продолжить**.

    ![Создание связанной службы хранилища Azure для Фабрики данных](./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-data-factory-storage-linked-service.png "Создание связанной службы хранилища Azure для Фабрики данных")

4. Введите следующие значения для связанной службы хранилища.

    |Свойство |Значение |
    |---|---|
    |ИМЯ |Укажите `HDIStorageLinkedService`.|
    |Подписка Azure. |Выберите подписку в раскрывающемся списке.|
    |Имя учетной записи хранения |Выберите созданную учетную запись службы хранилища Azure, созданную в рамках сценария PowerShell.|

    Нажмите копку **Проверить подключение**, и если проверка прошла успешно, щелкните **Создать**.

    ![Указание имени для связанной службы хранилища Azure](./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-data-factory-storage-linked-service-details.png "Указание имени для связанной службы хранилища Azure")

### <a name="create-an-on-demand-hdinsight-linked-service"></a>Создание связанной службы HDInsight по запросу

1. Снова нажмите кнопку **+ Создать**, чтобы создать еще одну связанную службу.

2. В окне **New Linked Service** (Новая связанная служба) выберите вкладку **Службы вычислений**.

3. Выберите **Azure HDInsight**, а затем **Продолжить**.

    ![Создание связанной службы HDInsight для Фабрики данных Azure](./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-data-factory-linked-service.png "Создание связанной службы HDInsight для Фабрики данных Azure")

4. В окне **New Linked Service** (Новая связанная служба) введите следующие значения и оставьте остальные по умолчанию.

    | Свойство | Значение |
    | --- | --- |
    | ИМЯ | Укажите `HDInsightLinkedService`.|
    | type | Выберите **On-demand HDInsight** (HDInsight по требованию). |
    | Связанная служба хранилища Azure | Выберите `HDIStorageLinkedService`. |
    | Тип кластера | Выберите **hadoop** |
    | Срок жизни | Укажите продолжительность времени, в течение которого кластер HDInsight должен быть доступен до его автоматического удаления|
    | Идентификатор субъекта-службы | Укажите идентификатор приложения субъекта-службы Azure Active Directory, созданного в рамках предварительных требований. |
    | Ключ субъект-службы | Укажите ключ аутентификации для субъекта-службы Azure Active Directory. |
    | Префикс имени кластера | Укажите значение, которое будет добавлено как префикс ко всем типам кластера, созданным фабрикой данных. |
    |Subscription |Выберите подписку в раскрывающемся списке.|
    | Выбор группы ресурсов | Выберите группу ресурсов, созданную в рамках сценария PowerShell, которая использовалась ранее.|
    | Тип ОС/Имя пользователя кластера SSH | Введите имя пользователя SSH, обычно `sshuser`. |
    | Тип ОС/Пароль кластера SSH | Укажите пароль пользователя SSH. |
    | Тип ОС/Имя пользователя кластера | Введите имя пользователя кластера, обычно `admin`. |
    | Тип ОС/Пароль кластера | Укажите пароль для пользователя кластера. |

    Щелкните **Создать**.

    ![Указание значений для связанной службы HDInsight](./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-data-factory-linked-service-details.png "Указание значений для связанной службы HDInsight")

## <a name="create-a-pipeline"></a>Создание конвейера

1. Нажмите кнопку **+** (плюс) и выберите **Pipeline** (Конвейер).

    ![Создание конвейера в Фабрике данных Azure](./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-data-factory-create-pipeline.png "Создание конвейера в Фабрике данных Azure")

2. В панели инструментов **Действия** разверните **HDInsight** и перетащите действие **Hive** в область конструктора конвейера. Во вкладке **Общие** укажите имя для действия.

    ![Добавление действий в конвейер Фабрики данных](./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-data-factory-add-hive-pipeline.png "Добавление действий в конвейер Фабрики данных")

3. Убедитесь, что выбрано действие Hive, выберите вкладку **HDI Cluster** (Кластер HDI) и в раскрывающемся списке **HDInsight Linked Service** (Связанная служба HDInsight) выберите связанную службу **HDInsightLinkedService**, созданную ранее для HDInsight.

    ![Указание сведений о кластере HDInsight для конвейера](./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-hive-activity-select-hdinsight-linked-service.png "Указание сведений о кластере HDInsight для конвейера")

4. Выберите вкладку **Скрипт** и затем сделайте следующее:

    1. Для параметра **Служба, связанная со скриптом** выберите в раскрывающемся списке **HDIStorageLinkedService**. Это — связанная служба хранилища, созданная ранее.

    1. Для **Путь к файлу** выберите **Поиск в хранилище** и перейдите в расположение, где находится образец скрипта Hive. Если ранее выполнялся скрипт PowerShell, это расположение должно быть `adfgetstarted/hivescripts/partitionweblogs.hql`.

        ![Указание сведения о скрипте Hive для конвейера](./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-data-factory-provide-script-path.png "Указание сведения о скрипте Hive для конвейера")

    1. В разделе **Расширенный** > **Параметры** выберите **Автозаполнение из скрипта**. Этот параметр ищет любые параметры в сценарии Hive, которым требуются значения во время выполнения. Использованный сценарий (**partitionweblogs.hql**) имеет параметр **Вывод**. Укажите **значение** в формате `wasbs://adfgetstarted@<StorageAccount>.blob.core.windows.net/outputfolder/` для указания имеющейся папки в службе хранилища Azure. Путь следует вводить с учетом регистра. Это путь, где будут храниться выходные данные сценария. Схема `wasbs` необходима, так как у учетных записей хранения теперь по умолчанию включено требование безопасной передачи.
    
        ![Указание параметров для скрипта Hive](./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-data-factory-provide-script-parameters.png "Указание параметров для скрипта Hive")

1. Чтобы проверить конвейер, выберите **Проверить**. Чтобы закрыть окно проверки, нажмите кнопку **>>** Стрелка вправо.

    ![Проверка конвейера Фабрики данных Azure](./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-data-factory-validate-all.png "Проверка конвейера Фабрики данных Azure")

1. Наконец, выберите **Опубликовать все** для публикации артефактов фабрики данных Azure.

    ![Публикация конвейера Фабрики данных](./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-data-factory-publish-pipeline.png "Публикация конвейера Фабрики данных")

## <a name="trigger-a-pipeline"></a>Активация конвейера

1. Из панели инструментов на поверхности конструктора выберите **Добавить триггер** > **Trigger Now** (Активировать сейчас).

    ![Активация конвейера Фабрики данных Azure](./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-data-factory-trigger-pipeline.png "Активация конвейера Фабрики данных Azure")

2. Выберите **Готово** во всплывающей боковой панели.

## <a name="monitor-a-pipeline"></a>Мониторинг конвейера

1. Перейдите на вкладку **Мониторинг** слева. Вы увидите, что запуск конвейера появится в списке **Pipeline Runs** (Запуски конвейера). Обратите внимание на состояние выполнения в столбце **Состояние**.

    ![Мониторинг конвейера Фабрики данных Azure](./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-data-factory-monitor-pipeline.png "Мониторинг конвейера Фабрики данных Azure")

1. Щелкните **Обновить**, чтобы обновить состояние.

1. Можно также выбрать значок **Представление выполнения действия** для просмотра выполнения действия связанного с конвейером. На снимке экрана ниже видно только одно выполнение действия, так как в созданном конвейере есть только одно действие. Чтобы вернуться к предыдущему представлению, выберите **Конвейеры** в верхней части страницы.

    ![Мониторинг действий конвейера Фабрики данных Azure](./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-data-factory-monitor-pipeline-activity.png "Мониторинг действий конвейера Фабрики данных Azure")

## <a name="verify-the-output"></a>Проверка выходных данных

1. Чтобы проверить выходные данные, на портале Azure перейдите к учетной записи хранения, используемой в этом руководстве. Там должны быть следующие папки или контейнеры.

    - **adfgerstarted/outputfolder**, содержащий выходные данные сценария Hive, который был запущен в рамках конвейера.

    - Контейнер **adfhdidatafactory-\<linked-service-name>-\<timestamp>** . Этот контейнер по умолчанию является местом хранения кластера HDInsight, который был создан в рамках выполнения конвейера.

    - Контейнер **adfjobs**, содержащий журналы заданий фабрики данных Azure.  

        ![Проверка выходных данных конвейера Фабрики данных Azure](./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-data-factory-verify-output.png "Проверка выходных данных конвейера Фабрики данных Azure")

## <a name="clean-up-resources"></a>Очистка ресурсов

Если вы создаете кластер HDInsight по требованию, явно удалять кластер HDInsight не нужно. Удаление кластера заложено в основе конфигурации, которая указывается при создании конвейера. Тем не менее даже после удаления кластера, учетные записи хранения, связанные с кластером, продолжают существовать. Это сделано намеренно, чтобы сохранить данные без изменений. Но если вы не хотите сохранять данные, можно удалить созданную учетную запись хранения.

Кроме того можно целиком удалить группу ресурсов, созданную в этом руководстве. Это приведет к удалению учетной записи хранения и созданной фабрики данных Azure.

### <a name="delete-the-resource-group"></a>удаление группы ресурсов.

1. Выполните вход на [портал Azure](https://portal.azure.com).
1. Выберите **Группы ресурсов** на левой панели.
1. Выберите имя группы ресурсов, созданной в сценарии PowerShell. Если отображается слишком много групп ресурсов, используйте фильтр. Он открывает группу ресурсов.
1. В элементе **Ресурсы** должны отображаться учетная запись хранения по умолчанию и фабрика данных, если только группа ресурсов не является общей для других проектов.
1. Выберите **Удалить группу ресурсов**. При этом также будет удалена учетная запись хранения и данные, хранящиеся в ней.

    ![Удаление группы ресурсов на портале Azure](./media/hdinsight-hadoop-create-linux-clusters-adf/delete-resource-group.png "Удалить группу ресурсов")

1. Введите имя группы ресурсов, чтобы подтвердить удаление, и нажмите **Удалить**.

## <a name="next-steps"></a>Дополнительная информация
В этой статье вы узнали, как использовать фабрику данных Azure для создания кластера HDInsight по требованию и выполнения заданий [Apache Hive](https://hive.apache.org/). Перейдите к следующей статье, чтобы узнать, как создавать кластеры HDInsight с настраиваемой конфигурацией.

> [!div class="nextstepaction"]
>[Создание кластеров Azure HDInsight с настраиваемой конфигурацией](hdinsight-hadoop-provision-linux-clusters.md)
