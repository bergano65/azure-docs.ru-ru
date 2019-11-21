---
title: Incrementally copy new files based on time partitioned file name
description: Create an Azure data factory and then use the Copy Data tool to incrementally load new files only based on time partitioned file name.
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
ms.openlocfilehash: 746b5cbcc58f6c722623446227417e6c94dd0a80
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/20/2019
ms.locfileid: "74217449"
---
# <a name="incrementally-copy-new-files-based-on-time-partitioned-file-name-by-using-the-copy-data-tool"></a>Incrementally copy new files based on time partitioned file name by using the Copy Data tool

В этом руководстве вы создадите фабрику данных с помощью портала Azure. Then, you use the Copy Data tool to create a pipeline that incrementally copies new files based on time partitioned file name from Azure Blob storage to Azure Blob storage. 

> [!NOTE]
> Если вы еще не работали с фабрикой данных Azure, ознакомьтесь со статьей [Введение в фабрику данных Azure](introduction.md).

Вот какие шаги выполняются в этом учебнике:

> [!div class="checklist"]
> * Создали фабрику данных.
> * Создание конвейера с помощью средства копирования данных.
> * Мониторинг конвейера и выполнения действий.

## <a name="prerequisites"></a>Технические условия

* **Подписка Azure**. Если у вас еще нет подписки Azure, создайте [бесплатную учетную запись](https://azure.microsoft.com/free/), прежде чем начинать работу.
* **Azure storage account**: Use Blob storage as the _source_  and _sink_ data store. Если у вас нет учетной записи хранения Azure, см. инструкции по [ее созданию](../storage/common/storage-quickstart-create-account.md).

### <a name="create-two-containers-in-blob-storage"></a>Create two containers in Blob storage

Prepare your Blob storage for the tutorial by performing these steps.

1. Create a container named **source**.  Create a folder path as **2019/02/26/14** in your container. Create an empty text file, and name it as **file1.txt**. Upload the file1.txt to the folder path **source/2019/02/26/14** in your storage account.  Это можно сделать при помощи таких средств, как [обозреватель службы хранилища Azure](https://storageexplorer.com/).
    
    ![Передача файлов](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/upload-file.png)
    
    > [!NOTE]
    > Please adjust the folder name with your UTC time.  For example, if the current UTC time is 2:03 PM on Feb 26th, 2019, you can create the folder path as **source/2019/02/26/14/** by the rule of **source/{Year}/{Month}/{Day}/{Hour}/** .

2. Create a container named **destination**. Это можно сделать при помощи таких средств, как [обозреватель службы хранилища Azure](https://storageexplorer.com/).

## <a name="create-a-data-factory"></a>Создание фабрики данных

1. В меню слева выберите **Создать ресурс** > **Данные и аналитика** > **Фабрика данных**. 
   
   ![Выбор фабрики данных в области "Создать"](./media/doc-common-process/new-azure-data-factory-menu.png)

2. На странице **Новая фабрика данных** в поле **Имя** введите **ADFTutorialDataFactory**. 
    
    Имя фабрики данных должно быть _глобально уникальным_. Вы можете получить следующее сообщение об ошибке.
   
   ![Сообщение об ошибке фабрики данных](./media/doc-common-process/name-not-available-error.png)
   
   Если вы увидите следующую ошибку касательно значения имени, введите другое имя фабрики данных. Например, **_ваше_имя_** **ADFTutorialDataFactory**. Правила именования для артефактов службы "Фабрика данных" см. в [этой](naming-rules.md) статье.
3. Выберите **подписку** Azure, в которой нужно создать фабрику данных. 
4. Для **группы ресурсов** выполните одно из следующих действий:
     
    а) Выберите **Использовать существующую**и укажите существующую группу ресурсов в раскрывающемся списке.

    б) Выберите **Создать новую**и укажите имя группы ресурсов. 
         
    Сведения о группах ресурсов см. в статье [Общие сведения об Azure Resource Manager](../azure-resource-manager/resource-group-overview.md).

5. В качестве **версии** выберите **V2**.
6. В качестве **расположения** выберите расположение фабрики данных. В раскрывающемся списке отображаются только поддерживаемые расположения. Хранилища данных (например, служба хранилища Azure и база данных SQL) и вычислительные ресурсы (например, Azure HDInsight), используемые фабрикой данных, могут располагаться в других регионах или расположениях.
7. Кроме того, установите флажок **Закрепить на панели мониторинга**. 
8. Нажмите кнопку **Создать**.
9. На панели мониторинга на плитке **Deploying Data Factory** (Развертывание фабрики данных) отображается состояние процесса.

    ![Плитка Deploying data factory (Развертывание фабрики данных)](media/tutorial-copy-data-tool/deploying-data-factory.png)
10. Когда создание завершится, откроется домашняя страница **Фабрика данных**.
   
    ![Домашняя страница фабрики данных](./media/doc-common-process/data-factory-home-page.png)
11. Щелкните плитку **Author & Monitor** (Создание и мониторинг), чтобы запустить на отдельной вкладке пользовательский интерфейс фабрики данных Azure. 

## <a name="use-the-copy-data-tool-to-create-a-pipeline"></a>Создание конвейера с помощью средства копирования данных

1. On the **Let's get started** page, select the **Copy Data** title to launch the Copy Data tool. 

   ![Плитка средства копирования данных](./media/doc-common-process/get-started-page.png)
   
2. On the **Properties** page, take the following steps:

    а) Under **Task name**, enter **DeltaCopyFromBlobPipeline**.

    б) Under **Task cadence or Task schedule**, select **Run regularly on schedule**.

    в) Under **Trigger type**, select **Tumbling Window**.
    
    г) Under **Recurrence**, enter **1 Hour(s)** . 
    
    д) Щелкните **Далее**. 
    
    Пользовательский интерфейс фабрики данных создаст конвейер с указанным именем задачи. 

    ![Страница свойств](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/copy-data-tool-properties-page.png)
3. На странице **Исходное хранилище данных** сделайте следующее:

    а) Click  **+ Create new connection**, to add a connection.

    ![Страница исходного хранилища данных](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/source-data-store-page.png)
    
    б) Select **Azure Blob Storage** from the gallery, and then click **Continue**.

    ![Страница исходного хранилища данных](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/source-data-store-page-select-blob.png)
    
    в) On the **New Linked Service** page, select your storage account from the **Storage account name** list, and then click **Finish**.
    
    ![Страница исходного хранилища данных](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/source-data-store-page-linkedservice.png)
    
    г) Select the newly created linked service, then click **Next**. 
    
   ![Страница исходного хранилища данных](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/source-data-store-page-select-linkedservice.png)
4. На странице **Choose the output file or folder** (Выбор файла или папки входных данных) выполните следующие действия.
    
    а) Browse and select the **source** container, then select **Choose**.
    
    ![Выбор файла или папки входных данных](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/choose-input-file-folder.png)
    
    б) Under **File loading behavior**, select **Incremental load: time-partitioned folder/file names**.
    
    ![Выбор файла или папки входных данных](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/choose-loading-behavior.png)
    
    в) Write the dynamic folder path as **source/{year}/{month}/{day}/{hour}/** , and change the format as followings:
    
    ![Выбор файла или папки входных данных](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/input-file-name.png)
    
    г) Check **Binary copy** and click **Next**.
    
    ![Выбор файла или папки входных данных](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/check-binary-copy.png)     
5. On the **Destination data store** page, select the **AzureBlobStorage**, which is the same storage account as data source store, and then click **Next**.

    ![Страница целевого хранилища данных](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/destination-data-store-page-select-linkedservice.png) 
6. On the **Choose the output file or folder** page, do the following steps:
    
    а) Browse and select the **destination** folder, then click **Choose**.
    
    ![Выбор целевого файла или папки](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/choose-output-file-folder.png)   
    
    б) Write the dynamic folder path as **source/{year}/{month}/{day}/{hour}/** , and change the format as followings:
    
    ![Выбор целевого файла или папки](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/input-file-name2.png)    
    
    в) Щелкните **Далее**.
    
    ![Выбор целевого файла или папки](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/click-next-after-output-folder.png)  
7. На странице **Параметры** выберите **Далее**. 

    ![Страница «Параметры»](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/settings-page.png)  
8. Просмотрите параметры на странице **Сводка**, а затем нажмите кнопку **Далее**.

    ![Страница "Сводка"](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/summary-page.png)
    
9. На **странице развертывания** выберите **Мониторинг**, чтобы отслеживать созданный конвейер (задачу).
    ![Deployment page](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/deployment-page.png)
    
10. Обратите внимание, что слева автоматически выбирается вкладка **Мониторинг**.  You need wait for the pipeline run when it is triggered automatically (about after one hour).  When it runs, the **Actions** column includes links to view activity run details and to rerun the pipeline. Select **Refresh** to refresh the list, and select the **View Activity Runs** link in the **Actions** column. 

    ![Мониторинг выполнений конвейера](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/monitor-pipeline-runs1.png)
11. В этом конвейере определено только одно действие (действие копирования), поэтому вы увидите только одну запись. You can see the source file (file1.txt) has been copied from  **source/2019/02/26/14/**  to **destination/2019/02/26/14/** with the same file name.  

    ![Мониторинг выполнений конвейера](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/monitor-pipeline-runs2.png)
    
    You can also verify the same by using Azure Storage Explorer (https://storageexplorer.com/) to scan the files.
    
    ![Мониторинг выполнений конвейера](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/monitor-pipeline-runs3.png)
12. Create another empty text file with the new name as **file2.txt**. Upload the file2.txt file to the folder path **source/2019/02/26/15** in your storage account.   Это можно сделать при помощи таких средств, как [обозреватель службы хранилища Azure](https://storageexplorer.com/).   
    
    ![Мониторинг выполнений конвейера](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/monitor-pipeline-runs4.png)
    
    > [!NOTE]
    > You might be aware that a new folder path is required to be created. Please adjust the folder name with your UTC time.  For example, if the current UTC time is 3:20 PM on Feb 26th, 2019, you can create the folder path as **source/2019/02/26/15/** by the rule of **{Year}/{Month}/{Day}/{Hour}/** .
    
13. To go back to the **Pipeline Runs** view, select **All Pipelines Runs**, and wait for the same pipeline being triggered again automatically after another one hour.  

    ![Мониторинг выполнений конвейера](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/monitor-pipeline-runs5.png)

14. Select **View Activity Run** for the second pipeline run when it comes, and do the same to review details.  

    ![Мониторинг выполнений конвейера](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/monitor-pipeline-runs6.png)
    
    You can see the source file (file2.txt) has been copied from  **source/2019/02/26/15/**  to **destination/2019/02/26/15/** with the same file name.
    
    ![Мониторинг выполнений конвейера](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/monitor-pipeline-runs7.png) 
    
    You can also verify the same by using Azure Storage Explorer (https://storageexplorer.com/) to scan the files in **destination** container
    
    ![Мониторинг выполнений конвейера](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/monitor-pipeline-runs8.png)

    
## <a name="next-steps"></a>Дальнейшие действия
Перейдите к следующему руководству, чтобы узнать о преобразовании данных с помощью кластера Spark в Azure:

> [!div class="nextstepaction"]
>[Transform data in the cloud by using Spark activity in Azure Data Factory](tutorial-transform-data-spark-portal.md) (Преобразование данных в облаке с помощью действий Spark в фабрике данных Azure)