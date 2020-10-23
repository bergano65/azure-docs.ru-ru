---
title: Руководство по миграции из SQL Server RDS в Базу данных SQL по сети
titleSuffix: Azure Database Migration Service
description: Узнайте, как выполнить миграцию по сети из SQL Server RDS в отдельную базу данных или управляемый экземпляр Базы данных SQL Azure с помощью Azure Database Migration Service.
services: dms
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019
ms.topic: tutorial
ms.date: 01/08/2020
ms.openlocfilehash: 12725c28c3e128317301bc51f9ce93f76021cc2b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/09/2020
ms.locfileid: "91291373"
---
# <a name="tutorial-migrate-rds-sql-server-to-azure-sql-database-or-an-azure-sql-managed-instance-online-using-dms"></a>Руководство по миграции из SQL Server RDS в Базу данных SQL Azure или Управляемый экземпляр Базы данных SQL Azure по сети с помощью DMS

Вы можете использовать Azure Database Migration Service для переноса баз данных из экземпляра SQL Server RDS в [Базу данных SQL Azure](https://docs.microsoft.com/azure/sql-database/) или [Управляемый экземпляр SQL Azure](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-index) с минимальным временем простоя в работе приложений. В этом руководстве показано, как перенести базу данных **Adventureworks2012**, восстановленную в экземпляре SQL Server RDS версии SQL Server 2012 (или более поздней), в Базу данных SQL или Управляемый экземпляр SQL с помощью Azure Database Migration Service.

В этом руководстве описано следующее:
> [!div class="checklist"]
> * создание базы данных в Базе данных SQL Azure или Управляемом экземпляре SQL; 
> * перенос примера схемы с помощью помощника по миграции данных;
> * создание экземпляра Azure Database Migration Service;
> * создание проекта миграции с помощью Azure Database Migration Service;
> * выполнение миграции.
> * Мониторинг миграции.
> * скачивание отчета о миграции.

> [!NOTE]
> Чтобы выполнить подключенную миграцию с помощью Azure Database Migration Service, требуется создать экземпляр ценовой категории "Премиум". Дополнительные сведения см. на странице с [ценами](https://azure.microsoft.com/pricing/details/database-migration/) на Azure Database Migration Service.

> [!IMPORTANT]
> Чтобы оптимизировать процесс миграции, Майкрософт рекомендует создать экземпляр Azure Database Migration Service в том же регионе Azure, в котором размещена целевая база данных. Перемещение данных между регионами и географическими областями может замедлить процесс миграции и привести к ошибкам.

[!INCLUDE [online-offline](../../includes/database-migration-service-offline-online.md)]

В этой статье показано, как выполнить миграцию из SQL Server RDS в Базу данных SQL или Управляемый экземпляр SQL по сети.

## <a name="prerequisites"></a>Предварительные требования

Для работы с этим руководством вам потребуется следующее:

* Создайте [базу данных SQL Server RDS](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/CHAP_GettingStarted.CreatingConnecting.SQLServer.html).
* Создайте базу данных в [Базе данных SQL Azure на портале Azure](https://docs.microsoft.com/azure/sql-database/sql-database-get-started-portal) или [Управляемом экземпляре SQL](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-get-started), а затем создайте пустую базу данных с именем **AdventureWorks2012**. 
* Скачайте и установите [Помощник по миграции данных](https://www.microsoft.com/download/details.aspx?id=53595) (DMA) версии 3.3 или более поздней.
* Создайте виртуальную сеть Azure для Azure Database Migration Service, используя модель развертывания с помощью Azure Resource Manager. Если вы выполняете миграцию в Управляемый экземпляр SQL, создайте экземпляр DMS в той же виртуальной сети, которая используется для Управляемого экземпляра SQL, но в другой подсети.  Если же для DMS используется другая виртуальная сеть, создайте пиринг между этими двумя виртуальными сетями. Дополнительные сведения о создании виртуальной сети приведены в [документации по виртуальным сетям](https://docs.microsoft.com/azure/virtual-network/). В частности, уделите внимание кратким руководствам с пошаговыми инструкциями.

    > [!NOTE]
    > Если вы используете ExpressRoute с пиринговым подключением к сети, управляемой Майкрософт, во время настройки виртуальной сети добавьте в подсеть, в которой будет подготовлена служба, следующие [конечные точки](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview):
    >
    > * целевую конечную точку базы данных (например, конечная точка SQL, конечная точка Cosmos DB и т. д.);
    > * конечную точку службы хранилища;
    > * конечную точку служебной шины.
    >
    > Эта настройка необходима, потому что у Azure Database Migration Service нет подключения к Интернету. 

* Убедитесь, что правила группы безопасности сети для виртуальной сети не блокируют следующие входящие порты для Azure Database Migration Service: 443, 53, 9354, 445, 12000. См. дополнительные сведения о [фильтрации трафика, предназначенного для виртуальной сети, с помощью групп безопасности сети](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg).
* Настройте [брандмауэр Windows для доступа к ядру СУБД](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access).
* Откройте брандмауэр Windows, чтобы предоставить Azure Database Migration Service доступ к исходному серверу SQL Server. По умолчанию это TCP-порт 1433.
* Для Базы данных SQL создайте [правило брандмауэра](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure) на уровне сервера, чтобы предоставить службе Azure Database Migration Service доступ к целевой базе данных. Задайте диапазон подсети в виртуальной сети, которая используется для Azure Database Migration Service.
* Убедитесь, что учетные данные, используемые для подключения к исходному экземпляру SQL Server RDS, принадлежат учетной записи с ролью сервера Processadmin и ролью базы данных db_owner во всех базах данных, которые необходимо перенести.
* Убедитесь, что учетные данные, используемые для подключения к целевой базе данных, имеют разрешение CONTROL DATABASE для целевой базы данных в Базе данных SQL и член роли sysadmin при миграции в базу данных в Управляемом экземпляре SQL.
* Требуемая версия исходного сервера SQL Server RDS — SQL Server 2012 и выше. См. дополнительные сведения о том, как [определить версию, выпуск и уровень обновления системы SQL Server и ее компонентов](https://support.microsoft.com/help/321185/how-to-determine-the-version-edition-and-update-level-of-sql-server-an).
* Включите отслеживание измененных данных (CDC) в базе данных SQL Server RDS и во всех пользовательских таблицах, выбранных для переноса.
    > [!NOTE]
    > Чтобы включить CDC в базе данных SQL Server RDS, можно использовать такой скрипт:
    ```
    exec msdb.dbo.rds_cdc_enable_db 'AdventureWorks2012'
    ```
    > Чтобы включить CDC во всех таблицах, можно использовать такой скрипт:
    ```
    use <Database name>
    go
    exec sys.sp_cdc_enable_table 
    @source_schema = N'Schema name', 
    @source_name = N'table name', 
    @role_name = NULL, 
    @supports_net_changes = 1 --for PK table 1, non PK tables 0
    GO
    ```
* Отключите триггеры в целевой базе данных.
    > [!NOTE]
    > Триггеры в целевой базе данных можно найти с помощью следующего запроса:
    ```
    Use <Database name>
    go
    select * from sys.triggers
    DISABLE TRIGGER (Transact-SQL)
    ```
    Дополнительные сведения см. в статье [DISABLE TRIGGER (Transact-SQL)](https://docs.microsoft.com/sql/t-sql/statements/disable-trigger-transact-sql?view=sql-server-2017).

## <a name="migrate-the-sample-schema"></a>Перенос примера схемы
Используйте DMA для миграции схемы.

> [!NOTE]
> Прежде чем создавать проект миграции в DMA, убедитесь, что база данных в Базе данных SQL или Управляемом экземпляре SQL уже подготовлена, как описано выше. В этом руководстве для базы данных используется имя **AdventureWorks2012**, но вы можете назвать ее иначе.

Чтобы выполнить миграцию схемы **AdventureWorks2012**, сделайте следующее:

1. В Помощнике по миграции данных щелкните значок New (+) (Создать (+)), а затем в разделе **Project type** (Тип проекта) выберите **Migration** (Миграция).
2. Укажите имя проекта в текстовом поле **Source server type** (Тип исходного сервера), выберите **SQL Server**, а затем в текстовом поле **Target server type** (Тип целевого сервера) выберите **База данных SQL Azure**.

    > [!NOTE]
    > В качестве типа целевого сервера выберите **База данных SQL Azure**, чтобы выполнить миграцию в Базу данных SQL Azure и Управляемый экземпляр SQL.

3. В разделе **Migration Scope** (Область переноса) выберите **Schema only** (Только схема).

    После выполнения предыдущих действий интерфейс Помощника по миграции данных должен выглядеть, как показано на следующем рисунке:

    ![Создание проекта в помощнике по миграции данных](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/dma-create-project.png)

4. Выберите **Создать**, чтобы создать проект.
5. В Помощнике по миграции данных задайте сведения о подключении к источнику SQL Server, нажмите **Подключение**, а затем выберите базу данных **AdventureWorks2012**.

    ![Сведения о подключении к источнику в помощнике по миграции данных](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/dma-source-connect.png)

6. В разделе **Подключиться к целевому серверу** щелкните **Далее**, укажите сведения о подключении к целевому объекту для базы данных в Базе данных SQL или Управляемом экземпляре SQL, щелкните **Подключить** и выберите подготовленную базу данных **AdventureWorksAzure**.

    ![Сведения о подключении к целевому объекту в помощнике по миграции данных](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/dma-target-connect.png)

7. Щелкните **Далее**, чтобы перейти на экран **Выбор объектов**, на котором можно указать объекты схемы в развертываемой базе данных **AdventureWorks2012**.

    По умолчанию выбраны все объекты.

    ![Создание сценариев SQL](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/dma-assessment-source.png)

8. Выберите **Создать скрипт SQL**, чтобы создать скрипт SQL, а затем просмотрите его на наличие ошибок.

    ![Скрипт схемы](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/dma-schema-script.png)

9. Щелкните **Развернуть схему**, чтобы развернуть схему, а затем проверьте целевой объект на наличие аномалий.

    ![Развертывание схемы](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/dma-schema-deploy.png)

## <a name="register-the-microsoftdatamigration-resource-provider"></a>Регистрация поставщика ресурсов Microsoft.DataMigration

1. Войдите на портал Azure, щелкните **Все службы** и выберите **Подписки**.

   ![Отображение подписок на портале](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/portal-select-subscription1.png)

2. Выберите подписку, в которой нужно создать экземпляр Azure Database Migration Service, а затем щелкните **Поставщики ресурсов**.

    ![Отображение поставщиков ресурсов](media/tutorial-sql-server-to-azure-sql-online/portal-select-resource-provider.png)

3. В поле поиска введите migration, а затем справа от **Microsoft.DataMigration** щелкните **Зарегистрировать**.

    ![Регистрация поставщика ресурсов](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/portal-register-resource-provider.png)    

## <a name="create-an-instance"></a>Создание экземпляра

1. На портале Azure выберите **+Создать ресурс**, введите в поле поиска "Azure Database Migration Service", а затем в раскрывающемся списке выберите **Azure Database Migration Service**.

    ![Azure Marketplace](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/portal-marketplace.png)

2. На экране **Azure Database Migration Service** выберите **Создать**.

    ![Создание экземпляра Azure Database Migration Service](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/dms-create1.png)
  
3. На экране **Создание службы миграции** укажите имя службы, подписку и новую или существующую группу ресурсов.

4. Выберите расположение, в котором нужно создать экземпляр Azure Database Migration Service. 

5. Выберите существующую виртуальную сеть или создайте новую.

    Виртуальная сеть предоставляет Azure Database Migration Service доступ к исходному экземпляру SQL Server и целевому экземпляру Базы данных SQL или Управляемому экземпляру SQL.

    См. статью [Краткое руководство. Создание виртуальной сети с помощью портала Azure](https://aka.ms/DMSVnet).

6. Выберите ценовую категорию. Для этой миграции по сети необходимо выбрать ценовую категорию "Премиум".

    Дополнительные сведения о ценовых категориях и затратах см. на [странице с описанием цен](https://aka.ms/dms-pricing).

     ![Настройка параметров экземпляра Database Migration Service](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/dms-settings3.png)

7. Выберите **Создать**, чтобы создать службу.

## <a name="create-a-migration-project"></a>Создание проекта миграции

После создания службы найдите ее на портале Azure, откройте и создайте проект миграции.

1. На портале Azure щелкните **Все службы**, выполните поиск по запросу "Azure Database Migration Service" и выберите **Azure Database Migration Services** (Службы Azure Database Migration Service).

      ![Поиск всех экземпляров Azure Database Migration Service](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/dms-search.png)

2. На экране **Azure Database Migration Services** найдите имя созданного экземпляра Azure Database Migration Service и выберите его.

     ![Поиск экземпляра службы миграции базы данных Azure](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/dms-instance-search.png)

3. Выберите **+ Новый проект миграции**.
4. На экране **New migration project** (Новый проект миграции) задайте имя проекта, в текстовом поле **Source server type** (Тип исходного сервера) выберите **AWS RDS для SQL Server**, а в текстовом поле **Target server type** (Тип целевого сервера) — **База данных SQL Azure**.

    > [!NOTE]
    > В качестве типа целевого сервера выберите **База данных SQL Azure**, чтобы выполнить миграцию в Базу данных SQL и Управляемый экземпляр SQL.

5. В разделе **Выберите тип действия** выберите **Миграция данных по сети**.

    > [!IMPORTANT]
    > Не забудьте выбрать режим **миграции данных по сети**, так как автономная миграция не поддерживается для этого сценария.

    ![Создание проекта Database Migration Service](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/dms-create-project4.png)

    > [!NOTE]
    > Кроме того, вы можете выбрать **Создать только проект**, чтобы создать проект миграции для отложенного выполнения.

6. Щелкните **Сохранить**.

7. Выберите **Создать и выполнить действие**, чтобы создать проект и выполнить действие миграции.

    ![Создание и выполнение действия Database Migration Service](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/dms-create-and-run-activity1.png)

## <a name="specify-source-details"></a>Указание сведений об источнике

1. На экране **Сведения об источнике миграции** задайте сведения о подключении для исходного экземпляра SQL Server.

    Используйте для имени исходного экземпляра SQL Server полное доменное имя (FQDN).

2. Если на исходном сервере доверенный сертификат не установлен, установите флажок **Доверять сертификату сервера**.

    Если доверенный сертификат не установлен, SQL Server создаст самозаверяющий сертификат при запуске экземпляра. Этот сертификат используется с целью шифрования учетных данных для клиентских подключений.

    > [!CAUTION]
    > TLS-соединения, шифруемые с помощью самозаверяющего сертификата, не обеспечивают надежной защиты. Они уязвимы для атак "злоумышленник в середине". Не следует надеяться на защиту TLS с самозаверяющими сертификатами в рабочей среде или на серверах, подключенных к Интернету.

   ![Сведения об источнике](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/dms-source-details3.png)

## <a name="specify-target-details"></a>Указание сведений о цели

1. Щелкните **Сохранить**, а затем на экране **Сведения о целевом объекте миграции** укажите сведения о подключении для целевой базы данных в Azure.

    ![Выбор цели](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/dms-select-target3.png)

2. Нажмите кнопку **Сохранить**, а затем на экране **Сопоставление с целевыми базами данных** сопоставьте исходную и целевую базы данных для миграции.

    Если в целевой базе данных содержится такое же имя базы данных, что и в исходной базе данных, Azure Database Migration Service выберет целевую базу данных по умолчанию.

    ![Сопоставление с целевыми базами данных](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/dms-map-targets-activity4.png)

3. На экране **Выбрать таблицы** выберите **Сохранить**, разверните список таблиц и просмотрите список затронутых полей.

    Azure Database Migration Service автоматически выбирает все пустые исходные таблицы в целевой базе данных. Чтобы повторно перенести таблицы, уже содержащие данные, необходимо явным образом выбрать их на этом экране.

    ![Выбор таблиц](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/dms-configure-setting-activity4.png)

4. Установите перечисленные ниже **расширенные параметры миграции по сети** и нажмите кнопку **Сохранить**.

    | Параметр | Описание |
    | ------------- | ------------- |
    | **Максимальное число параллельно загружаемых таблиц** | Определяет, сколько таблиц служба DMS обрабатывает параллельно во время миграции. Значение по умолчанию — 5, но можно установить другое значение, которое будет оптимально соответствовать особым требованиям, определенным на этапе тестовых миграций (если такие проводились). |
    | **При усечении исходной таблицы** | Определяет, усекает ли служба DMS целевую таблицу во время миграции. Этот параметр может быть полезен, если таблицы усекаются во время миграции. |
    | **Настройка параметров для данных больших объектов (LOB)** | Определяет, ограничивает ли служба DMS объемы переносимых данных LOB до определенного размера.  Если установлено ограничение на перенос данных LOB, данные LOB, размер которых превышает это ограничение, усекаются. Чтобы избежать потерь данных во время миграции в рабочую среду, рекомендуется выбирать параметр **Разрешить неограниченный размер LOB**. Если вы выбрали параметр переноса неограниченного количества данных LOB, установите флажок **Переместить данные LOB в одном блоке, если размер меньше указанного (в КБ)**, чтобы улучшить производительность. |

    ![Установка расширенных параметров миграции по сети](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/dms-advanced-online-migration-settings.png)

5. Нажмите кнопку **Сохранить** в окне **Migration summary** (Сводка по миграции) и в поле **Имя действия** введите имя действия миграции, а затем проверьте соответствие сведениям о целевом и исходном сервере, которые вы указали ранее.

    ![Сводка по миграции](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/dms-migration-summary.png)

## <a name="run-the-migration"></a>Выполнение миграции

* Выберите **Запустить миграцию**.

    Появится окно действия миграции и в поле **Состояние** будет указано **Инициализация**.

    ![Состояние действия — инициализация](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/dms-activity-status2.png)

## <a name="monitor-the-migration"></a>Мониторинг миграции

1. На экране действия миграции нажимайте **Обновить**, чтобы периодически обновлять содержимое экрана, пока в поле **Состояние** не будет указано **Выполнение**.

2. Выберите определенную базу данных, чтобы узнать состояние миграции для операций **полной загрузки данных** и **добавочной синхронизации данных**.

    ![Состояние действия — выполняется](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/dms-activity-in-progress.png)

## <a name="perform-migration-cutover"></a>Выполнение прямой миграции

После завершения начальной полной загрузки базы данных помечаются как **готовые к прямой миграции**.

1. Когда вы будете готовы выполнить миграцию базы данных, щелкните **Запустить прямую миграцию**.

    ![Запуск прямой миграции](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/dms-start-cutover.png)

2. Обязательно остановите все входящие транзакции в исходную базу данных. Подождите, пока в счетчике **Ожидающие изменения** появится значение **0**.
3. Выберите **Подтвердить**, а затем — **Применить**.
4. Когда состояние миграции базы данных изменится на **Завершено**, подключите свои приложения к новой целевой базе данных.

    ![Состояние действия — завершено](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/dms-activity-completed.png)

## <a name="next-steps"></a>Дальнейшие действия

* Дополнительные сведения об известных проблемах и ограничениях см. в статье [Известные проблемы и ограничения при миграции в Базу данных SQL Azure по сети](known-issues-azure-sql-online.md).
* Дополнительные сведения о Database Migration Service см. в статье [Что такое служба Azure Database Migration Service](https://docs.microsoft.com/azure/dms/dms-overview).
* Дополнительные сведения о Базе данных SQL см. в статье [Что такое База данных SQL Azure](https://docs.microsoft.com/azure/sql-database/sql-database-technical-overview).
* Дополнительные сведения об управляемых экземплярах SQL см. в статье [Что такое Управляемый экземпляр SQL Azure](https://docs.microsoft.com/azure/azure-sql/managed-instance/sql-managed-instance-paas-overview).
