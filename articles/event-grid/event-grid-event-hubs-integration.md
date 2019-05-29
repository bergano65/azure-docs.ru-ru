---
title: Отправка данных Центров событий Azure в хранилище данных в службе "Сетка событий"
description: Описывается, как перенести данные в Хранилище данных SQL с помощью службы "Сетка событий Azure" и Центров событий Хранилище данных SQL использует функцию Azure, чтобы получить файл записи.
services: event-grid
author: spelluru
manager: timlt
ms.service: event-grid
ms.topic: tutorial
ms.date: 01/13/2019
ms.author: spelluru
ms.openlocfilehash: 1ae7a18660d2a7324bc5897d6b3952da42b6c4b2
ms.sourcegitcommit: 6ea7f0a6e9add35547c77eef26f34d2504796565
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/14/2019
ms.locfileid: "65603441"
---
# <a name="tutorial-stream-big-data-into-a-data-warehouse"></a>Руководство. Потоковая передача больших данных в хранилище данных
[Сетка событий](overview.md) Azure — интеллектуальная служба маршрутизации событий, позволяющая реагировать на уведомления (события) приложений и служб. Например, она может активировать функции Azure для обработки данных Центров событий, собранных в хранилище BLOB-объектов Azure или Data Lake Storage, и переноса данных в другие репозитории данных. В этом [примере интеграции Центров событий Azure и службы "Сетка событий"](https://github.com/Azure/azure-event-hubs/tree/master/samples/e2e/EventHubsCaptureEventGridDemo) показано, как с помощью Центров событий Azure и службы "Сетка событий Azure" легко перенести данные Центров событий из хранилища BLOB-объектов в Хранилище данных SQL.

![Обзор приложений](media/event-grid-event-hubs-integration/overview.png)

Эта схема демонстрирует рабочий процесс решения, создаваемого в рамках этого руководства. 

1. Данные, отправляемые в концентратор событий Azure, записываются в хранилище BLOB-объектов Azure.
2. После сбора данных создается событие и отправляется в Сетку событий Azure. 
3. Сетка событий переадресовывает данные этого события в приложение-функцию Azure.
4. Приложение-функция использует URL-адрес большого двоичного объекта из данных события, чтобы извлечь большой двоичный объект из хранилища. 
5. Приложение-функция переносит данные больших двоичных объектов в хранилище данных Azure SQL. 

Ниже перечислены действия, которые вы выполните в этой статье.

> [!div class="checklist"]
> * Используете шаблон Azure Resource Manager для развертывания инфраструктуры: концентратор событий, учетная запись хранения, приложение-функция, хранилище данных SQL.
> * Создадите таблицу в хранилище данных.
> * Добавите код в приложение-функцию.
> * Оформите подписку на событие. 
> * Запустите приложение, отправляющее данные в концентратор событий.
> * Просмотрите данные, перенесенные в хранилище данных.

## <a name="prerequisites"></a>Предварительные требования

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Для работы с этим руководством требуется:

* Подписка Azure. Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/), прежде чем начинать работу.
* [Visual Studio 2019](https://www.visualstudio.com/vs/) с рабочими нагрузками для разработки классических приложений .NET, Azure, ASP.NET, Node.js, Python и веб-разработки.
* Скачать на свой компьютер [пример проекта EventHubsCaptureEventGridDemo](https://github.com/Azure/azure-event-hubs/tree/master/samples/e2e/EventHubsCaptureEventGridDemo).

## <a name="deploy-the-infrastructure"></a>Развертывание инфраструктуры
На этом шаге вы развернете необходимую инфраструктуру с помощью [шаблона Resource Manager](https://github.com/Azure/azure-docs-json-samples/blob/master/event-grid/EventHubsDataMigration.json). При развертывании шаблона создаются следующие ресурсы.

* Концентратор событий со включенной функцией "Сбор".
* Учетная запись хранения для собранных файлов. 
* План службы приложений для размещения приложения-функции.
* Приложение-функция для обработки события.
* SQL Server для размещения хранилища данных.
* Хранилище данных SQL для хранения перенесенных данных.

### <a name="launch-azure-cloud-shell-in-azure-portal"></a>Запуск Azure Cloud Shell на портале Azure

1. Войдите на [портале Azure](https://portal.azure.com). 
2. Нажмите кнопку **Cloud Shell**, которая находится вверху.

    ![Портал Azure](media/event-grid-event-hubs-integration/azure-portal.png)
3. В нижней части окна веб-браузера вы увидите открытый Cloud Shell.

    ![Cloud Shell](media/event-grid-event-hubs-integration/launch-cloud-shell.png) 
4. Если в Cloud Shell у вас будет выбор между **Bash** и **PowerShell**, выберите **Bash**. 
5. Если вы используете Cloud Shell в первый раз, создайте учетную запись хранилища, выбрав **Create storage** (Создать хранилище). Azure Cloud Shell требуется учетная запись хранилища Azure для хранения некоторых файлов. 

    ![Создание хранилища для Cloud Shell](media/event-grid-event-hubs-integration/create-storage-cloud-shell.png)
6. Подождите, пока не инициализируется Cloud Shell. 

    ![Создание хранилища для Cloud Shell](media/event-grid-event-hubs-integration/cloud-shell-initialized.png)


### <a name="use-azure-cli"></a>Использование Azure CLI

1. Создайте группу ресурсов Azure, выполнив следующую команду в окне CLI: 
    1. Скопируйте следующую команду и вставьте ее в окно Cloud Shell.

        ```azurecli
        az group create -l eastus -n <Name for the resource group>
        ```
    1. Укажите имя для **группы ресурсов**
    2. Нажмите клавишу **ВВОД**. 

        Вот пример: 
    
        ```azurecli
        user@Azure:~$ az group create -l eastus -n ehubegridgrp
        {
          "id": "/subscriptions/00000000-0000-0000-0000-0000000000000/resourceGroups/ehubegridgrp",
          "location": "eastus",
          "managedBy": null,
          "name": "ehubegridgrp",
          "properties": {
            "provisioningState": "Succeeded"
          },
          "tags": null
        }
        ```
2. Разверните все ресурсы, упомянутые в предыдущем разделе (концентратор событий, учетная запись хранения, приложение-функция, хранилище данных SQL), выполнив следующую команду интерфейса командной строки: 
    1. Скопируйте эту команду и вставьте ее в окно Cloud Shell. Или же вы можете скопировать и вставить команду в свой любимый редактор, задать значения, а затем скопировать ее в Cloud Shell. 

        ```azurecli
        az group deployment create \
            --resource-group rgDataMigrationSample \
            --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/event-grid/EventHubsDataMigration.json \
            --parameters eventHubNamespaceName=<event-hub-namespace> eventHubName=hubdatamigration sqlServerName=<sql-server-name> sqlServerUserName=<user-name> sqlServerPassword=<password> sqlServerDatabaseName=<database-name> storageName=<unique-storage-name> functionAppName=<app-name>
        ```
    2. Укажите значения для следующих сущностей.
        1. Имя группы ресурсов, созданной ранее.
        2. Название пространства имен концентратора событий. 
        3. Имя концентратора событий. Вы можете оставить имеющееся значение (hubdatamigration).
        4. Имя для SQL Server.
        5. Имя и пароль пользователя SQL. 
        6. Имя хранилища данных SQL.
        7. Имя учетной записи хранения. 
        8. Имя приложения-функции. 
    3.  Нажмите клавишу **ВВОД** в окне Cloud Shell, чтобы запустить команду. Этот процесс может занять некоторое время, так как вы создаете не один ресурс. После выполнения команды убедитесь, что сбоев не было. 
    

### <a name="use-azure-powershell"></a>Использование Azure PowerShell

1. В Azure Cloud Shell перейдите в режим PowerShell. Щелкните стрелку вниз в левом верхнем углу Azure Cloud Shell и выберите **PowerShell**.

    ![Переключение в режим PowerShell](media/event-grid-event-hubs-integration/select-powershell-cloud-shell.png)
2. Создайте группу ресурсов, выполнив следующую команду: 
    1. Скопируйте следующую команду и вставьте ее в окно Cloud Shell.

        ```powershell
        New-AzResourceGroup -Name rgDataMigration -Location westcentralus
        ```
    2. Укажите имя для **группы ресурсов**.
    3. Нажмите клавишу ВВОД. 
3. Разверните все ресурсы, упомянутые в предыдущем разделе (концентратор событий, учетная запись хранения, приложение-функция, хранилище данных SQL), выполнив следующую команду:
    1. Скопируйте эту команду и вставьте ее в окно Cloud Shell. Или же вы можете скопировать и вставить команду в свой любимый редактор, задать значения, а затем скопировать ее в Cloud Shell. 

        ```powershell
        New-AzResourceGroupDeployment -ResourceGroupName rgDataMigration -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/event-grid/EventHubsDataMigration.json -eventHubNamespaceName <event-hub-namespace> -eventHubName hubdatamigration -sqlServerName <sql-server-name> -sqlServerUserName <user-name> -sqlServerDatabaseName <database-name> -storageName <unique-storage-name> -functionAppName <app-name>
        ```
    2. Укажите значения для следующих сущностей.
        1. Имя группы ресурсов, созданной ранее.
        2. Название пространства имен концентратора событий. 
        3. Имя концентратора событий. Вы можете оставить имеющееся значение (hubdatamigration).
        4. Имя для SQL Server.
        5. Имя и пароль пользователя SQL. 
        6. Имя хранилища данных SQL.
        7. Имя учетной записи хранения. 
        8. Имя приложения-функции. 
    3.  Нажмите клавишу **ВВОД** в окне Cloud Shell, чтобы запустить команду. Этот процесс может занять некоторое время, так как вы создаете не один ресурс. После выполнения команды убедитесь, что сбоев не было. 

### <a name="close-the-cloud-shell"></a>Закрытие Cloud Shell 
Закройте Cloud Shell, нажав кнопку **Cloud Shell** на портале или кнопку **X** в правом верхнем углу окна Cloud Shell. 

### <a name="verify-that-the-resources-are-created"></a>Проверка создания ресурсов

1. На портале Azure в меню слева выберите **Группы ресурсов**. 
2. Отфильтруйте список групп ресурсов, введя имя нужной группы ресурсов в поле поиска. 
3. Выберите в списке свою группу ресурсов.

    ![Выберите группу ресурсов](media/event-grid-event-hubs-integration/select-resource-group.png)
4. Убедитесь, что в группе ресурсов находятся следующие ресурсы:

    ![Ресурсы в группе ресурсов](media/event-grid-event-hubs-integration/resources-in-resource-group.png)

### <a name="create-a-table-in-sql-data-warehouse"></a>Создание таблицы в хранилище данных SQL
Создайте таблицу в хранилище данных, выполнив сценарий [CreateDataWarehouseTable.sql](https://github.com/Azure/azure-event-hubs/blob/master/samples/e2e/EventHubsCaptureEventGridDemo/scripts/CreateDataWarehouseTable.sql). Чтобы запустить скрипт, воспользуйтесь Visual Studio или редактором запросов на портале. В следующих шагах продемонстрировано, как использовать редактор запросов. 

1. В списке ресурсов группы ресурсов выберите свое хранилище данных SQL. 
2. На странице хранилища данных SQL в левом меню выберите **Редактор запросов (предварительная версия)** . 

    ![Страница хранилища данных SQL](media/event-grid-event-hubs-integration/sql-data-warehouse-page.png)
2. Введите имя **пользователя** и **пароль** для SQL Server и щелкните **ОК**. 

    ![Проверка подлинности SQL Server](media/event-grid-event-hubs-integration/sql-server-authentication.png)
4. Скопируйте и вставьте следующий код в окно запроса: 

    ```sql
    CREATE TABLE [dbo].[Fact_WindTurbineMetrics] (
        [DeviceId] nvarchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NULL, 
        [MeasureTime] datetime NULL, 
        [GeneratedPower] float NULL, 
        [WindSpeed] float NULL, 
        [TurbineSpeed] float NULL
    )
    WITH (CLUSTERED COLUMNSTORE INDEX, DISTRIBUTION = ROUND_ROBIN);
    ```

    ![Выполнение SQL-запроса](media/event-grid-event-hubs-integration/run-sql-query.png)
5. Не закрывайте эту вкладку или окно, так как в конце руководства это поможет вам проверить успешность создания данных. 


## <a name="publish-the-azure-functions-app"></a>Публикация приложения службы "Функции Azure"

1. Запустите Visual Studio.
2. Откройте решение **EventHubsCaptureEventGridDemo.sln**, скачанное с портала [GitHub](https://github.com/Azure/azure-event-hubs/tree/master/samples/e2e/EventHubsCaptureEventGridDemo) во время выполнения предыдущих шагов.
3. В обозревателе решений щелкните правой кнопкой мыши **FunctionEGDWDumper** и выберите **Публиковать**.

   ![Публикация приложения-функции](media/event-grid-event-hubs-integration/publish-function-app.png)
4. Если вы видите такой экран, щелкните **Начало**. 

   ![Кнопка начала публикации](media/event-grid-event-hubs-integration/start-publish-button.png) 
5. На странице **Выберите целевой объект публикации** нажмите **Выбрать существующий**, а потом щелкните **Создать профиль**. 

   ![Выбор целевого объекта публикации](media/event-grid-event-hubs-integration/publish-select-existing.png)
6. На странице службы приложений, выберите свою **подписку Azure**, затем **приложение-функцию** в группе ресурсов и нажмите кнопку **ОК**. 

   ![Страница службы приложений](media/event-grid-event-hubs-integration/publish-app-service.png) 
1. После настройки профиля в Visual Studio выберите **Публиковать**.

   ![Выбор публикации](media/event-grid-event-hubs-integration/select-publish.png)

После публикации функции вы можете подписаться на событие.

## <a name="subscribe-to-the-event"></a>Оформление подписки на событие

1. В новой вкладке или новом окне веб-браузера откройте [портал Azure](https://portal.azure.com).
2. На портале Azure в меню слева выберите **Группы ресурсов**. 
3. Отфильтруйте список групп ресурсов, введя имя нужной группы ресурсов в поле поиска. 
4. Выберите в списке свою группу ресурсов.

    ![Выберите группу ресурсов](media/event-grid-event-hubs-integration/select-resource-group.png)
4. Выберите в списке план службы приложений. 
5. На странице плана службы приложений в меню слева выберите **Приложения**, а затем приложение-функцию. 

    ![Выбор приложения-функции](media/event-grid-event-hubs-integration/select-function-app-app-service-plan.png)
6. Разверните приложение-функцию, функции, а затем выберите свою функцию. 

    ![Выбор функции Azure](media/event-grid-event-hubs-integration/select-function-add-button.png)
7. Выберите **Добавить подписку сетки событий** на панели инструментов. 
8. На странице **Create Event Grid Subscription** (Создание подписки на Сетку событий) сделайте следующее. 
    1. В разделе **Сведения о разделе** выполните следующие действия:
        1. Выберите подписку Azure.
        2. Выберите группу ресурсов Azure.
        3. Выберите пространство имен Центров событий.
    2. На странице **сведений о подписке на события** введите имя для подписки (например, captureEventSub) и щелкните **Создать**. 

        ![Создание подписки для службы "Сетка событий"](media/event-grid-event-hubs-integration/create-event-subscription.png)

## <a name="run-the-app-to-generate-data"></a>Запуск приложения для создания данных
Вы завершили настройку концентратора событий, хранилища данных SQL, приложения-функции Azure и подписки на события. Прежде чем запустить приложение, создающее данные для концентратора событий, настройте несколько значений.

1. На портале Azure, как и раньше, перейдите к группе ресурсов. 
2. Выберите пространство имен Центров событий.
3. На странице **пространства имен Центров событий** выберите **Политики общего доступа** в меню слева.
4. В списке политик выберите **RootManageSharedAccessKey**. 
5. Щелкните синюю кнопку копирования справа от текстового поля **Connection string-primary key** (Первичный ключ строки подключения). 

    ![Строка подключения пространства имен концентратора событий](media/event-grid-event-hubs-integration/get-connection-string.png)
1. Вернитесь к решению Visual Studio. 
2. В проекте WindTurbineDataGenerator откройте файл **program.cs**.
5. Замените две константы. Использовать скопированное значение **EventHubConnectionString**. Используйте имя концентратора событий **hubdatamigration**. Если вы указали другое имя, укажите именно его. 

   ```cs
   private const string EventHubConnectionString = "Endpoint=sb://demomigrationnamespace.servicebus.windows.net/...";
   private const string EventHubName = "hubdatamigration";
   ```

6. Выполните сборку решения. Запустите приложение **WindTurbineGenerator.exe**. 
7. Через несколько минут запросите перенесенные данные в таблице хранилища данных.

    ![Результаты запроса](media/event-grid-event-hubs-integration/query-results.png)

### <a name="event-data-generated-by-the-event-hub"></a>Данные событий, сгенерированные концентратором событий
Служба "Сетка событий" распределяет данные события между подписчиками. В следующем примере показаны данные событий, сгенерированные при записи потока данных, проходящих через концентратор событий, в большой двоичный объект. В частности, обратите внимание, что свойство `fileUrl` в объекте `data` указывает на большой двоичный объект в хранилище. Приложение-функция использует этот URL-адрес для получения файла большого двоичного объекта с записанными данными.

```json
[
    {
        "topic": "/subscriptions/<guid>/resourcegroups/rgDataMigrationSample/providers/Microsoft.EventHub/namespaces/tfdatamigratens",
        "subject": "eventhubs/hubdatamigration",
        "eventType": "Microsoft.EventHub.CaptureFileCreated",
        "eventTime": "2017-08-31T19:12:46.0498024Z",
        "id": "14e87d03-6fbf-4bb2-9a21-92bd1281f247",
        "data": {
            "fileUrl": "https://tf0831datamigrate.blob.core.windows.net/windturbinecapture/tfdatamigratens/hubdatamigration/1/2017/08/31/19/11/45.avro",
            "fileType": "AzureBlockBlob",
            "partitionId": "1",
            "sizeInBytes": 249168,
            "eventCount": 1500,
            "firstSequenceNumber": 2400,
            "lastSequenceNumber": 3899,
            "firstEnqueueTime": "2017-08-31T19:12:14.674Z",
            "lastEnqueueTime": "2017-08-31T19:12:44.309Z"
        }
    }
]
```


## <a name="next-steps"></a>Дополнительная информация

* Сведения о различиях между службами обмена сообщениями в Azure см. в статье [Выбор между службами обмена сообщениями Azure](compare-messaging-services.md).
* Общие сведения о службе "Сетка событий" см. в разделе [Общие сведения о службе "Сетка событий Azure"](overview.md).
* Общие сведения о функции "Сбор" в Центрах событий см. в статье [Включение функции "Сбор" в Центрах событий с помощью портала Azure](../event-hubs/event-hubs-capture-enable-through-portal.md).
* Дополнительные сведения о настройке и запуске примера см. в [примере использования функции "Сбор" в Центрах событий и службы "Сетка событий"](https://github.com/Azure/azure-event-hubs/tree/master/samples/e2e/EventHubsCaptureEventGridDemo).
