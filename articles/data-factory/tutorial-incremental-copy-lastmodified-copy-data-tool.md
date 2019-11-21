---
title: Data tool to copy new and updated files incrementally
description: Create an Azure data factory and then use the Copy Data tool to incrementally load new files based on LastModifiedDate.
services: data-factory
documentationcenter: ''
author: dearandyxu
ms.author: yexu
ms.reviewer: ''
manager: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 1/24/2019
ms.openlocfilehash: 5c20196bd243d025d58f7cc08e015e1e0038e178
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/20/2019
ms.locfileid: "74217787"
---
# <a name="incrementally-copy-new-and-changed-files-based-on-lastmodifieddate-by-using-the-copy-data-tool"></a>Incrementally copy new and changed files based on LastModifiedDate by using the Copy Data tool

In this tutorial, you'll use the Azure portal to create a data factory. Then, you'll use the Copy Data tool to create a pipeline that incrementally copies new and changed files only, based on their **LastModifiedDate** from Azure Blob storage to Azure Blob storage.

By doing so, ADF will scan all the files from the source store, apply the file filter by their LastModifiedDate, and copy the new and updated file only since last time to the destination store.  Please note that if you let ADF scan huge amounts of files but only copy a few files to destination, you would still expect the long duration due to file scanning is time consuming as well.   

> [!NOTE]
> Если вы еще не работали с фабрикой данных Azure, ознакомьтесь со статьей [Введение в фабрику данных Azure](introduction.md).

In this tutorial, you will perform the following tasks:

> [!div class="checklist"]
> * Создали фабрику данных.
> * Создание конвейера с помощью средства копирования данных.
> * Мониторинг конвейера и выполнения действий.

## <a name="prerequisites"></a>Технические условия

* **Подписка Azure**. Если у вас еще нет подписки Azure, создайте [бесплатную учетную запись](https://azure.microsoft.com/free/), прежде чем начинать работу.
* **Azure storage account**: Use Blob storage as the _source_ and _sink_ data store. Если у вас нет учетной записи хранения Azure, см. инструкции по [ее созданию](../storage/common/storage-quickstart-create-account.md).

### <a name="create-two-containers-in-blob-storage"></a>Create two containers in Blob storage

Prepare your Blob storage for the tutorial by performing these steps.

1. Create a container named **source**. You can use various tools to perform this task, such as [Azure Storage Explorer](https://storageexplorer.com/).

2. Create a container named **destination**. 

## <a name="create-a-data-factory"></a>Создание фабрики данных

1. В меню слева выберите **Создать ресурс** > **Данные и аналитика** > **Фабрика данных**. 
   
   ![Выбор фабрики данных в области "Создать"](./media/doc-common-process/new-azure-data-factory-menu.png)

2. На странице **Новая фабрика данных** в поле **Имя** введите **ADFTutorialDataFactory**. 
 
   Имя фабрики данных должно быть _глобально уникальным_. Вы можете получить следующее сообщение об ошибке.
   
   ![Сообщение об ошибке фабрики данных](./media/doc-common-process/name-not-available-error.png)

   Если вы увидите следующую ошибку касательно значения имени, введите другое имя фабрики данных. Например, **_ваше_имя_** **ADFTutorialDataFactory**. Правила именования для артефактов службы "Фабрика данных" см. в [этой](naming-rules.md) статье.
3. Select the Azure **subscription** in which you'll create the new data factory. 
4. Для **группы ресурсов** выполните одно из следующих действий:
     
    * Выберите **Использовать существующий** и выберите существующую группу ресурсов из раскрывающегося списка.

    * Выберите **Создать** и введите имя группы ресурсов. 
         
    Сведения о группах ресурсов см. в статье [Общие сведения об Azure Resource Manager](../azure-resource-manager/resource-group-overview.md).

5. Under **version**, select **V2**.
6. В качестве **расположения** выберите расположение фабрики данных. В раскрывающемся списке отображаются только поддерживаемые расположения. The data stores (for example, Azure Storage and SQL Database) and computes (for example, Azure HDInsight) that your data factory uses can be in other locations and regions.
7. Кроме того, установите флажок **Закрепить на панели мониторинга**. 
8. Нажмите кнопку **Создать**.
9. On the dashboard, refer to the **Deploying Data Factory** tile to see the process status.

    ![Deploying Data Factory Tile](media/tutorial-copy-data-tool/deploying-data-factory.png)
10. Когда создание завершится, откроется домашняя страница **Фабрика данных**.
   
    ![Домашняя страница фабрики данных](./media/doc-common-process/data-factory-home-page.png)
11. To open the Azure Data Factory user interface (UI) on a separate tab, select the **Author & Monitor** tile. 

## <a name="use-the-copy-data-tool-to-create-a-pipeline"></a>Создание конвейера с помощью средства копирования данных

1. On the **Let's get started** page, select the **Copy Data** title to open the Copy Data tool. 

   ![Плитка средства копирования данных](./media/doc-common-process/get-started-page.png)
   
2. On the **Properties** page, take the following steps:

    а) Under **Task name**, enter **DeltaCopyFromBlobPipeline**.

    б) Under **Task cadence** or **Task schedule**, select **Run regularly on schedule**.

    в) Under **Trigger Type**, select **Tumbling Window**.
    
    г) Under **Recurrence**, enter **15 Minute(s)** . 
    
    д) Щелкните **Далее**. 
    
    Пользовательский интерфейс фабрики данных создаст конвейер с указанным именем задачи. 

    ![Страница свойств](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/copy-data-tool-properties-page.png)
    
3. На странице **Исходное хранилище данных** сделайте следующее:

    а) Select  **+ Create new connection**, to add a connection.
    
    ![Страница исходного хранилища данных](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/source-data-store-page.png)

    б) В коллекции выберите **Хранилище BLOB-объектов Azure** и щелкните **Продолжить**.
    
    ![Страница исходного хранилища данных](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/source-data-store-page-select-blob.png)

    в) On the **New Linked Service** page, select your storage account from the **Storage account name** list and then select **Finish**.
    
    ![Страница исходного хранилища данных](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/source-data-store-page-linkedservice.png)
    
    г) Select the newly created linked service and then select **Next**. 
    
   ![Страница исходного хранилища данных](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/source-data-store-page-select-linkedservice.png)

4. На странице **Choose the input file or folder** (Выбор входного файла или папки) выполните следующие действия:
    
    а) Browse and select the **source** folder, and then select **Choose**.
    
    ![Выбор файла или папки входных данных](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/choose-input-file-folder.png)
    
    б) Under **File loading behavior**, select **Incremental load: LastModifiedDate**.
    
    ![Выбор файла или папки входных данных](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/choose-loading-behavior.png)
    
    в) Check **Binary copy** and select **Next**.
    
     ![Выбор файла или папки входных данных](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/check-binary-copy.png)
     
5. On the **Destination data store** page, select **AzureBlobStorage**. This is the same storage account as the source data store. Затем нажмите кнопку **Далее**.

    ![Страница целевого хранилища данных](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/destination-data-store-page-select-linkedservice.png)
    
6. На странице **Choose the output file or folder** (Выбор целевого файла или папки) выполните следующие действия:
    
    а) Browse and select the **destination** folder, and then select **Choose**.
    
    ![Выбор целевого файла или папки](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/choose-output-file-folder.png)
    
    б) Щелкните **Далее**.
    
     ![Выбор целевого файла или папки](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/click-next-after-output-folder.png)
    
7. На странице **Параметры** выберите **Далее**. 

    ![Страница «Параметры»](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/settings-page.png)
    
8. On the **Summary** page, review the settings and then select **Next**.

    ![Страница "Сводка"](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/summary-page.png)
    
9. На **странице развертывания** выберите **Мониторинг**, чтобы отслеживать созданный конвейер (задачу).

    ![Страница развертывания](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/deployment-page.png)
    
10. Обратите внимание, что слева автоматически выбирается вкладка **Мониторинг**. В столбце **действий** отображаются ссылки для просмотра сведений о запусках действий и (или) повторного выполнения на конвейере. Select **Refresh** to refresh the list, and select the **View Activity Runs** link in the **Actions** column. 

    ![Refresh list and select View Activity Runs](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs1.png)

11. There's only one activity (the copy activity) in the pipeline, so you see only one entry. Чтобы увидеть сведения об операции копирования, щелкните ссылку **Сведения** (значок очков) в столбце **Действия**. 

    ![Copy activity is in pipeline](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs2.png)
    
    Because there is no file in the **source** container in your Blob storage account, you will not see any file copied to the **destination** container in your Blob storage account.
    
    ![No file in source container or destination container](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs3.png)
    
12. Create an empty text file and name it **file1.txt**. Upload this text file to the **source** container in your storage account. Это можно сделать при помощи таких средств, как [обозреватель службы хранилища Azure](https://storageexplorer.com/).   

    ![Create file1.txt and upload to source container](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs3-1.png)
    
13. To go back to the **Pipeline Runs** view, select **All Pipeline Runs**, and wait for the same pipeline to be triggered again automatically.  

    ![Select All Pipeline Runs](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs4.png)

14. Select **View Activity Run** for the second pipeline run when you see it. Then review the details in the same way you did for the first pipeline run.  

    ![Select View Activity Run and review details](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs5.png)

    You will that see one file (file1.txt) has been copied from the **source** container to the **destination** container of your Blob storage account.
    
    ![File1.txt has been copied from source container to destination container](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs6.png)
    
15. Create another empty text file and name it **file2.txt**. Upload this text file to the **source** container in your Blob storage account.   
    
16. Repeat steps 13 and 14 for this second text file. You will see that only the new file (file2.txt) has been copied from the **source** container to the **destination** container of your storage account in the next pipeline run.  
    
    ![File2.txt has been copied from source container to destination container](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs7.png)

    You can also verify this by using [Azure Storage Explorer](https://storageexplorer.com/) to scan the files.
    
    ![Scan files using Azure Storage Explorer](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs8.png)

    
## <a name="next-steps"></a>Дальнейшие действия
Advance to the following tutorial to learn about transforming data by using an Apache Spark cluster on Azure:

> [!div class="nextstepaction"]
>[Transform data in the cloud by using an Apache Spark cluster](tutorial-transform-data-spark-portal.md)