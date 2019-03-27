---
title: Добавочное копирование новых файлов с помощью фабрики данных только на основе времени секционированных имени файла | Документация Майкрософт
description: Создание фабрики данных Azure и применение средства копирования данных для добавочной загрузки новых файлов, только на основе времени секционированных имени файла.
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
ms.date: 1/24/2019
ms.openlocfilehash: c89764d746f07e6100b1f250d4c107bb700fe014
ms.sourcegitcommit: f0f21b9b6f2b820bd3736f4ec5c04b65bdbf4236
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/26/2019
ms.locfileid: "58445856"
---
# <a name="incrementally-copy-new-files-based-on-time-partitioned-file-name-by-using-the-copy-data-tool"></a>Добавочное копирование новых файлов, на основе времени секционированных имени файла с помощью средства копирования данных

В этом руководстве вы создадите фабрику данных с помощью портала Azure. Затем используется инструмент копирования данных для создания конвейера, который постепенно копирует новые файлы, на основе имени секционированную файловую времени из хранилища BLOB-объектов Azure в хранилище BLOB-объектов Azure. 

> [!NOTE]
> Если вы еще не работали с фабрикой данных Azure, ознакомьтесь со статьей [Введение в фабрику данных Azure](introduction.md).

Вот какие шаги выполняются в этом учебнике:

> [!div class="checklist"]
> * Создали фабрику данных.
> * Создание конвейера с помощью средства копирования данных.
> * Мониторинг конвейера и выполнения действий.

## <a name="prerequisites"></a>Технические условия

* **Подписка Azure.** Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/) , прежде чем начинать работу.
* **Учетная запись хранения Azure.** Использование хранилища BLOB-объектов как _источника_ и _приемника_ хранилища данных. Если у вас нет учетной записи хранения Azure, см. инструкции по [ее созданию](../storage/common/storage-quickstart-create-account.md).

### <a name="create-two-containers-in-blob-storage"></a>Создание двух контейнеров в хранилище BLOB-объектов

Подготовьте хранилище больших двоичных объектов для работы с руководством, выполнив следующие действия.

1. Создайте контейнер с именем **источника**.  Создать путь папки на **2019/02/26/14** в контейнере. Создайте пустой текстовый файл и назовите его как **file1.txt**. Отправка file1.txt в путь к папке **источника/2019/02/26/14** вашей учетной записи хранения.  Это можно сделать при помощи таких средств, как [обозреватель службы хранилища Azure](https://storageexplorer.com/).
    
    ![Передача файлов](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/upload-file.png)
    
    > [!NOTE]
    > Измените имя папки с время в формате UTC.  Например, если текущее время UTC — 14:03:00 на 26 февраля 2019 г., можно создать путь к папке как **источника/2019/02/26/14/** правилом из **источника / {Year} / {Month} / {Day} / {Hour} /**.

2. Создайте контейнер с именем **назначения**. Это можно сделать при помощи таких средств, как [обозреватель службы хранилища Azure](https://storageexplorer.com/).

## <a name="create-a-data-factory"></a>Создание фабрики данных

1. В меню слева, выберите **создать ресурс** > **данные и аналитика** > **фабрики данных**: 
   
   ![Выбор фабрики данных в области "Создать"](./media/quickstart-create-data-factory-portal/new-azure-data-factory-menu.png)

2. На странице **Новая фабрика данных** в поле **Имя** введите **ADFTutorialDataFactory**. 
      
    ![Новая фабрика данных](./media/tutorial-copy-data-tool/new-azure-data-factory.png)
    
    Имя фабрики данных должно быть _глобально уникальным_. Вы можете получить следующее сообщение об ошибке.
   
   ![Сообщение об ошибке фабрики данных](./media/tutorial-copy-data-tool/name-not-available-error.png)
   
   Если вы увидите следующую ошибку касательно значения имени, введите другое имя фабрики данных. Например, _**ваше_имя**_**ADFTutorialDataFactory**. Правила именования для артефактов службы "Фабрика данных" см. в [этой](naming-rules.md) статье.
3. Выберите **подписку** Azure, в которой нужно создать фабрику данных. 
4. Для **группы ресурсов** выполните одно из следующих действий:
     
    a. Выберите **Использовать существующую**и укажите существующую группу ресурсов в раскрывающемся списке.

    2. Выберите **Создать новую**и укажите имя группы ресурсов. 
         
    Сведения о группах ресурсов см. в статье [Общие сведения об Azure Resource Manager](../azure-resource-manager/resource-group-overview.md).

5. В качестве **версии** выберите **V2**.
6. В качестве **расположения** выберите расположение фабрики данных. В раскрывающемся списке отображаются только поддерживаемые расположения. Хранилища данных (например, служба хранилища Azure и база данных SQL) и вычислительные ресурсы (например, Azure HDInsight), используемые фабрикой данных, могут располагаться в других регионах или расположениях.
7. Кроме того, установите флажок **Закрепить на панели мониторинга**. 
8. Нажмите кнопку **Создать**.
9. На панели мониторинга на плитке **Deploying Data Factory** (Развертывание фабрики данных) отображается состояние процесса.

    ![Плитка Deploying data factory (Развертывание фабрики данных)](media/tutorial-copy-data-tool/deploying-data-factory.png)
10. Когда создание завершится, откроется домашняя страница **Фабрика данных**.
   
    ![Домашняя страница фабрики данных](./media/tutorial-copy-data-tool/data-factory-home-page.png)
11. Щелкните плитку **Author & Monitor** (Создание и мониторинг), чтобы запустить на отдельной вкладке пользовательский интерфейс фабрики данных Azure. 

## <a name="use-the-copy-data-tool-to-create-a-pipeline"></a>Создание конвейера с помощью средства копирования данных

1. На **приступим к работе** выберите **копирования данных** title, чтобы запустить средство копирования данных. 

   ![Плитка средства копирования данных](./media/tutorial-copy-data-tool/copy-data-tool-tile.png)
   
2. На **свойства** странице, выполните следующие действия:

    a. В разделе **имя задачи**, введите **DeltaCopyFromBlobPipeline**.

    2. В разделе **задач периодичность или расписание задач**выберите **регулярно запускаться по расписанию**.

    c. В разделе **активировать тип**выберите **"переворачивающегося" окна**.
    
    d. В разделе **повторения**, введите **1 ч**. 
    
    д. Щелкните **Далее**. 
    
    Пользовательский интерфейс фабрики данных создаст конвейер с указанным именем задачи. 

    ![Страница свойств](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/copy-data-tool-properties-page.png)
3. На странице **Исходное хранилище данных** сделайте следующее:

    a. Нажмите кнопку **+ создать новое соединение**, чтобы добавить подключение.

    ![Страница исходного хранилища данных](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/source-data-store-page.png)
    
    2. Выберите **хранилище BLOB-объектов** из коллекции и нажмите кнопку **Продолжить**.

    ![Страница исходного хранилища данных](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/source-data-store-page-select-blob.png)
    
    c. На **новая связанная служба** выберите учетную запись хранения из **имя учетной записи хранения** , а затем нажмите **Готово**.
    
    ![Страница исходного хранилища данных](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/source-data-store-page-linkedservice.png)
    
    d. Выберите только что созданную связанную службу, а затем нажмите кнопку **Далее**. 
    
   ![Страница исходного хранилища данных](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/source-data-store-page-select-linkedservice.png)
4. На странице **Choose the output file or folder** (Выбор файла или папки входных данных) выполните следующие действия.
    
    a. Найдите и выберите **источника** контейнер, затем выберите **Выбор**.
    
    ![Выбор файла или папки входных данных](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/choose-input-file-folder.png)
    
    2. В разделе **поведения при загрузке файла**выберите **добавочную загрузку: имена секционированы время папку или файл**.
    
    ![Выбор файла или папки входных данных](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/choose-loading-behavior.png)
    
    c. Запись пути к папке динамических как **источника / {year} / {month} / {day} / {hour} /** и изменить формат следующими способами:
    
    ![Выбор файла или папки входных данных](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/input-file-name.png)
    
    d. Проверьте **двоичное копирование** и нажмите кнопку **Далее**.
    
    ![Выбор файла или папки входных данных](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/check-binary-copy.png)     
5. На **целевое хранилище данных** выберите **AzureBlobStorage**, который имеет то же хранилище учетной записи в качестве источника хранилища данных и нажмите кнопку **Далее**.

    ![Страница целевого хранилища данных](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/destination-data-store-page-select-linkedservice.png) 
6. На **Выбор выходного файла или папки** выполните следующие действия:
    
    a. Найдите и выберите **назначения** папку, нажмите кнопку **Выбор**.
    
    ![Выбор целевого файла или папки](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/choose-output-file-folder.png)   
    
    2. Запись пути к папке динамических как **источника / {year} / {month} / {day} / {hour} /** и изменить формат следующими способами:
    
    ![Выбор целевого файла или папки](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/input-file-name2.png)    
    
    c. Нажмите кнопку **Далее**.
    
    ![Выбор целевого файла или папки](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/click-next-after-output-folder.png)  
7. На странице **Параметры** выберите **Далее**. 

    ![Страница «Параметры»](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/settings-page.png)  
8. Просмотрите параметры на странице **Сводка**, а затем нажмите кнопку **Далее**.

    ![Страница "Сводка"](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/summary-page.png)
    
9. На **странице развертывания** выберите **Мониторинг**, чтобы отслеживать созданный конвейер (задачу).
    ![Страница развертывания](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/deployment-page.png)
    
10. Обратите внимание, что слева автоматически выбирается вкладка **Мониторинг**.  Вам потребуется дождаться выполнения при его активации автоматически конвейера (о после одного часа).  При запуске, **действия** столбца ссылки для просмотра сведений о запусках действий и повторно запустить конвейер. Выберите **обновить** обновите список и выберите **Просмотр выполнений действий** ссылку в **действия** столбца. 

    ![Мониторинг выполнений конвейера](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/monitor-pipeline-runs1.png)
11. В этом конвейере определено только одно действие (действие копирования), поэтому вы увидите только одну запись. Вы увидите, скопированный из исходного файла (file1.txt) **источника/2019/02/26/14/** для **назначения/2019/02/26/14/** с тем же именем.  

    ![Мониторинг выполнений конвейера](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/monitor-pipeline-runs2.png)
    
    Это также можно проверить с помощью обозревателя службы хранилища Azure (https://storageexplorer.com/) для проверки файлов.
    
    ![Мониторинг выполнений конвейера](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/monitor-pipeline-runs3.png)
12. Создайте еще один пустой текстовый файл с новым именем как **file2.txt**. Отправьте файл file2.txt путь к папке **источника/2019/02/26/15** вашей учетной записи хранения.   Это можно сделать при помощи таких средств, как [обозреватель службы хранилища Azure](https://storageexplorer.com/).   
    
    ![Мониторинг выполнений конвейера](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/monitor-pipeline-runs4.png)
    
    > [!NOTE]
    > Возможно, о том, что новый путь к папке должен быть создан. Измените имя папки с время в формате UTC.  Например, если текущее время UTC — 3:20 PM на 26 февраля 2019 г., можно создать путь к папке как **источника/2019/02/26/15/** правилом из **{Year} / {Month} / {Day} / {Hour} /**.
    
13. Чтобы вернуться к **запусков конвейера** представление, выберите **все запуски конвейеров**и дождитесь в одном конвейере попытку активации автоматически после другой один час.  

    ![Мониторинг выполнений конвейера](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/monitor-pipeline-runs5.png)

14. Выберите **просмотрите сведения о выполнении действия** для второго конвейера, выполнять, когда он поступает и сделайте то же самое, чтобы просмотреть сведения.  

    ![Мониторинг выполнений конвейера](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/monitor-pipeline-runs6.png)
    
    Вы увидите, скопированный из исходного файла (file2.txt) **источника/2019/02/26/15/** для **назначения/2019/02/26/15/** с тем же именем.
    
    ![Мониторинг выполнений конвейера](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/monitor-pipeline-runs7.png) 
    
    Это также можно проверить с помощью обозревателя службы хранилища Azure (https://storageexplorer.com/) для проверки файлов в **назначения** контейнера
    
    ![Мониторинг выполнений конвейера](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/monitor-pipeline-runs8.png)

    
## <a name="next-steps"></a>Дальнейшие действия
Перейдите к следующему руководству, чтобы узнать о преобразовании данных с помощью кластера Spark в Azure:

> [!div class="nextstepaction"]
>[Transform data in the cloud by using Spark activity in Azure Data Factory](tutorial-transform-data-spark-portal.md) (Преобразование данных в облаке с помощью действий Spark в фабрике данных Azure)