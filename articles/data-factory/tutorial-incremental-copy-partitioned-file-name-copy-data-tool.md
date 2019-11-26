---
title: Добавочное копирование новых файлов на основе времени секционированного файла
description: Создайте фабрику данных Azure, а затем используйте средство Копирование данных для последовательной загрузки новых файлов только на основе временного секционированного имени файла.
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
# <a name="incrementally-copy-new-files-based-on-time-partitioned-file-name-by-using-the-copy-data-tool"></a>Добавочное копирование новых файлов на основе временного секционированного файла с помощью средства Копирование данных

В этом руководстве вы создадите фабрику данных с помощью портала Azure. Затем вы используете средство Копирование данных для создания конвейера, который постепенно копирует новые файлы на основе временного секционированного имени из хранилища BLOB-объектов Azure в хранилище BLOB-объектов Azure. 

> [!NOTE]
> Если вы еще не работали с фабрикой данных Azure, ознакомьтесь со статьей [Введение в фабрику данных Azure](introduction.md).

Вот какие шаги выполняются в этом учебнике:

> [!div class="checklist"]
> * Создание фабрики данных.
> * Создание конвейера с помощью средства копирования данных.
> * Мониторинг конвейера и выполнения действий.

## <a name="prerequisites"></a>предварительным требованиям

* **Подписка Azure**. Если у вас еще нет подписки Azure, создайте [бесплатную учетную запись](https://azure.microsoft.com/free/), прежде чем начинать работу.
* **Учетная запись хранения Azure**. Используйте хранилище BLOB-объектов в качестве _источника_ и _приемника_ хранилища данных. Если у вас нет учетной записи хранения Azure, см. инструкции по [ее созданию](../storage/common/storage-quickstart-create-account.md).

### <a name="create-two-containers-in-blob-storage"></a>Создание двух контейнеров в хранилище BLOB-объектов

Подготовьте хранилище BLOB-объектов для учебника, выполнив следующие действия.

1. Создайте контейнер с именем **Source**.  Создайте путь к папке в контейнере как **2019/02/26/14** . Создайте пустой текстовый файл и присвойте ему имя **file1. txt**. Передайте file1. txt в папку путь к папке **/2019/02/26/14** в учетной записи хранения.  Это можно сделать при помощи таких средств, как [обозреватель службы хранилища Azure](https://storageexplorer.com/).
    
    ![Передача файлов](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/upload-file.png)
    
    > [!NOTE]
    > Измените имя папки, указав время в формате UTC.  Например, если текущее время в формате UTC равно 2:03 на Фев 26th, 2019, можно создать путь к папке в формате **Source/2019/02/26/14/** с помощью правила **источник/{год}/{месяц}/{день}/{Hour}/** .

2. Создайте контейнер с именем **Destination**. Это можно сделать при помощи таких средств, как [обозреватель службы хранилища Azure](https://storageexplorer.com/).

## <a name="create-a-data-factory"></a>Создать фабрику данных

1. В меню слева выберите **Создать ресурс** > **Данные и аналитика** > **Фабрика данных**. 
   
   ![Выбор фабрики данных в области "Создать"](./media/doc-common-process/new-azure-data-factory-menu.png)

2. На странице **Новая фабрика данных** в поле **Имя** введите **ADFTutorialDataFactory**. 
    
    Имя фабрики данных должно быть _глобально уникальным_. Вы можете получить следующее сообщение об ошибке.
   
   ![Сообщение об ошибке фабрики данных](./media/doc-common-process/name-not-available-error.png)
   
   Если вы увидите следующую ошибку касательно значения имени, введите другое имя фабрики данных. Например, _**ваше_имя**_ **ADFTutorialDataFactory**. Правила именования для артефактов службы "Фабрика данных" см. в [этой](naming-rules.md) статье.
3. Выберите **подписку** Azure, в которой нужно создать фабрику данных. 
4. Для **группы ресурсов** выполните одно из следующих действий:
     
    a. Выберите **Использовать существующую**и укажите существующую группу ресурсов в раскрывающемся списке.

    b. Выберите **Создать новую**и укажите имя группы ресурсов. 
         
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

1. На странице Приступая к **работе** выберите заголовок **копирование данных** , чтобы запустить средство копирование данных. 

   ![Плитка средства копирования данных](./media/doc-common-process/get-started-page.png)
   
2. На странице **Свойства** выполните следующие действия.

    a. В разделе **имя задачи**введите **делтакопифромблобпипелине**.

    b. В разделе " **ритмичность задач" или "Расписание задач**" выберите **регулярное выполнение по расписанию**.

    c. В разделе **тип триггера**выберите **окно "переворачивающегося"** .
    
    d. В разделе **повторение**введите **1 час**. 
    
    д. Нажмите кнопку **Далее**. 
    
    Пользовательский интерфейс фабрики данных создаст конвейер с указанным именем задачи. 

    ![Страница свойств](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/copy-data-tool-properties-page.png)
3. На странице **Исходное хранилище данных** сделайте следующее:

    a. Щелкните **+ создать новое соединение**, чтобы добавить подключение.

    ![Страница исходного хранилища данных](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/source-data-store-page.png)
    
    b. Выберите **хранилище BLOB-объектов Azure** из коллекции и нажмите кнопку **продолжить**.

    ![Страница исходного хранилища данных](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/source-data-store-page-select-blob.png)
    
    c. На странице **Новая связанная служба** выберите учетную запись хранения из списка **имя учетной записи хранения** и нажмите кнопку **Готово**.
    
    ![Страница исходного хранилища данных](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/source-data-store-page-linkedservice.png)
    
    d. Выберите только что созданную связанную службу, а затем нажмите кнопку **Далее**. 
    
   ![Страница исходного хранилища данных](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/source-data-store-page-select-linkedservice.png)
4. На странице **Choose the output file or folder** (Выбор файла или папки входных данных) выполните следующие действия.
    
    a. Найдите и выберите **Исходный** контейнер, а затем нажмите **кнопку Выбрать**.
    
    ![Выбор файла или папки входных данных](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/choose-input-file-folder.png)
    
    b. В разделе **поведение при загрузке файлов**выберите **добавочная загрузка: временная секционированная папка или имена файлов**.
    
    ![Выбор файла или папки входных данных](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/choose-loading-behavior.png)
    
    c. Запишите путь динамической папки в формате " **источник/{год}/{месяц}/{день}/{час}/** " и измените формат следующим образом:
    
    ![Выбор файла или папки входных данных](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/input-file-name.png)
    
    d. Проверьте **двоичное копирование** и нажмите кнопку **Далее**.
    
    ![Выбор файла или папки входных данных](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/check-binary-copy.png)     
5. На странице **целевое хранилище данных** выберите **AzureBlobStorage**, которая является той же учетной записью хранения, что и хранилище источников данных, и нажмите кнопку **Далее**.

    ![Страница целевого хранилища данных](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/destination-data-store-page-select-linkedservice.png) 
6. На странице **Выбор выходного файла или папки** выполните следующие действия.
    
    a. Найдите и выберите папку **назначения** , а затем нажмите кнопку **выбрать**.
    
    ![Выбор целевого файла или папки](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/choose-output-file-folder.png)   
    
    b. Запишите путь динамической папки в формате " **источник/{год}/{месяц}/{день}/{час}/** " и измените формат следующим образом:
    
    ![Выбор целевого файла или папки](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/input-file-name2.png)    
    
    c. Щелкните **Далее**.
    
    ![Выбор целевого файла или папки](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/click-next-after-output-folder.png)  
7. На странице **Параметры** выберите **Далее**. 

    ![Страница «Параметры»](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/settings-page.png)  
8. Просмотрите параметры на странице **Сводка**, а затем нажмите кнопку **Далее**.

    ![Страница "Сводка"](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/summary-page.png)
    
9. На **странице развертывания** выберите **Мониторинг**, чтобы отслеживать созданный конвейер (задачу).
    ](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/deployment-page.png) страницы развертывания ![
    
10. Обратите внимание, что слева автоматически выбирается вкладка **Мониторинг**.  Необходимо дождаться выполнения конвейера при автоматическом запуске (примерно через один час).  При запуске в столбце **действия** содержатся ссылки для просмотра сведений о выполнении действия и повторного запуска конвейера. Выберите **Обновить** , чтобы обновить список, и выберите ссылку **Просмотреть запуски действия** в столбце **действия** . 

    ![Мониторинг выполнений конвейера](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/monitor-pipeline-runs1.png)
11. В этом конвейере определено только одно действие (действие копирования), поэтому вы увидите только одну запись. Исходный файл (file1. txt) был скопирован из **Source/2019/02/26/14/** to **Destination/2019/02/26/14/** с тем же именем файла.  

    ![Мониторинг выполнений конвейера](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/monitor-pipeline-runs2.png)
    
    Для проверки файлов также можно использовать Обозреватель службы хранилища Azure (https://storageexplorer.com/).
    
    ![Мониторинг выполнений конвейера](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/monitor-pipeline-runs3.png)
12. Создайте еще один пустой текстовый файл с новым именем в формате **file2. txt**. Отправьте файл file2. txt в папку Path **Source/2019/02/26/15** в учетной записи хранения.   Это можно сделать при помощи таких средств, как [обозреватель службы хранилища Azure](https://storageexplorer.com/).   
    
    ![Мониторинг выполнений конвейера](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/monitor-pipeline-runs4.png)
    
    > [!NOTE]
    > Возможно, вы знаете, что требуется создать новый путь к папке. Измените имя папки, указав время в формате UTC.  Например, если текущее время в формате UTC равно 3:20 на Фев 26th, 2019, можно создать путь к папке как **Source/2019/02/26/15/** с помощью правила **{year}/{Монс}/{дай}/{Хаур}/** .
    
13. Чтобы вернуться к представлению **запусков конвейеров** , выберите **все конвейеры**и дождитесь автоматического запуска одного и того же конвейера через час.  

    ![Мониторинг выполнений конвейера](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/monitor-pipeline-runs5.png)

14. Выберите **Просмотреть выполнение действия** во втором конвейере, когда оно поступает, и сделайте то же самое для просмотра подробностей.  

    ![Мониторинг выполнений конвейера](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/monitor-pipeline-runs6.png)
    
    Исходный файл (file2. txt) был скопирован из **Source/2019/02/26/15/** to **Destination/2019/02/26/15/** с тем же именем файла.
    
    ![Мониторинг выполнений конвейера](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/monitor-pipeline-runs7.png) 
    
    Вы также можете проверить это же с помощью Обозреватель службы хранилища Azure (https://storageexplorer.com/) для сканирования файлов в **целевом** контейнере
    
    ![Мониторинг выполнений конвейера](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/monitor-pipeline-runs8.png)

    
## <a name="next-steps"></a>Дополнительная информация
Перейдите к следующему руководству, чтобы узнать о преобразовании данных с помощью кластера Spark в Azure:

> [!div class="nextstepaction"]
>[Transform data in the cloud by using Spark activity in Azure Data Factory](tutorial-transform-data-spark-portal.md) (Преобразование данных в облаке с помощью действий Spark в фабрике данных Azure)