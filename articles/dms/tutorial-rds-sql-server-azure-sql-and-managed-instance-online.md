---
title: Руководство. Миграция RDS SQL Server в базу данных SQL
titleSuffix: Azure Database Migration Service
description: Узнайте, как выполнить оперативную миграцию из RDS SQL Server в отдельную базу данных SQL Azure или управляемый экземпляр с помощью Azure Database Migration Service.
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
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/25/2020
ms.locfileid: "91291373"
---
# <a name="tutorial-migrate-rds-sql-server-to-azure-sql-database-or-an-azure-sql-managed-instance-online-using-dms"></a>Руководство по переносу SQL Server RDS в базу данных SQL Azure или Azure SQL Управляемый экземпляр Online с помощью DMS

Вы можете использовать Azure Database Migration Service для переноса баз данных из экземпляра RDS SQL Server в [базу данных SQL Azure](https://docs.microsoft.com/azure/sql-database/) или [управляемый экземпляр Azure SQL](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-index) с минимальным временем простоя. В этом руководстве вы перенесете базу данных **Adventureworks2012** , восстановленную в экземпляре RDS SQL Server SQL Server 2012 (или более поздней версии), в базу данных sql или управляемый экземпляр SQL с помощью Azure Database Migration Service.

В этом руководстве вы узнаете, как:
> [!div class="checklist"]
> * Создание базы данных в базе данных SQL Azure или Управляемый экземпляр SQL. 
> * перенос примера схемы с помощью помощника по миграции данных;
> * создание экземпляра Azure Database Migration Service;
> * создание проекта миграции с помощью Azure Database Migration Service;
> * выполнение миграции.
> * Мониторинг миграции.
> * скачивание отчета о миграции.

> [!NOTE]
> Чтобы выполнить подключенную миграцию с помощью Azure Database Migration Service, требуется создать экземпляр ценовой категории "Премиум". Дополнительные сведения см. на странице [цен](https://azure.microsoft.com/pricing/details/database-migration/) на Azure Database Migration Service.

> [!IMPORTANT]
> Чтобы оптимизировать процесс миграции, Майкрософт рекомендует создать экземпляр Azure Database Migration Service в том же регионе Azure, в котором размещена целевая база данных. Перемещение данных между регионами и географическими областями может замедлить процесс миграции и привести к ошибкам.

[!INCLUDE [online-offline](../../includes/database-migration-service-offline-online.md)]

В этой статье описывается оперативная миграция из RDS SQL Server в базу данных SQL или Управляемый экземпляр SQL.

## <a name="prerequisites"></a>Предварительные требования

Для работы с этим руководством вам потребуется следующее:

* Создайте [базу данных SQL Server RDS](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/CHAP_GettingStarted.CreatingConnecting.SQLServer.html).
* [Создайте базу данных в базе данных SQL Azure на портал Azure](https://docs.microsoft.com/azure/sql-database/sql-database-get-started-portal) или [Создайте базу данных в управляемый экземпляр SQL](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-get-started), а затем создайте пустую базу данных с именем **AdventureWorks2012**. 
* Скачайте и установите [Помощник по миграции данных](https://www.microsoft.com/download/details.aspx?id=53595) (DMA) версии 3.3 или более поздней.
* Создайте виртуальная сеть Microsoft Azure для Azure Database Migration Service с помощью модели развертывания Azure Resource Manager. Если выполняется миграция на Управляемый экземпляр SQL, создайте экземпляр DMS в той же виртуальной сети, которая используется для Управляемый экземпляр SQL, но в другой подсети.  Кроме того, если для DMS используется другая виртуальная сеть, необходимо создать пиринг между двумя виртуальными сетями. Дополнительные сведения о создании виртуальной сети см. в [документации по виртуальной сети](https://docs.microsoft.com/azure/virtual-network/), особенно в кратком руководстве, где приведены пошаговые инструкции.

    > [!NOTE]
    > При установке виртуальной сети с помощью ExpressRoute с пирингом сети в корпорацию Майкрософт добавьте следующие [конечные точки](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview) службы в подсеть, в которой будет подготовлена служба:
    >
    > * целевую конечную точку базы данных (например, конечная точка SQL, конечная точка Cosmos DB и т. д.);
    > * конечную точку службы хранилища;
    > * конечную точку служебной шины.
    >
    > Эта настройка необходима, потому что у Azure Database Migration Service нет подключения к Интернету. 

* Убедитесь, что правила группы безопасности сети виртуальной сети не блокируют следующие порты входящих подключений для Azure Database Migration Service: 443, 53, 9354, 445, 12000. Дополнительные сведения о фильтрации трафика NSG в виртуальной сети см. в статье [Фильтрация сетевого трафика с помощью групп безопасности сети](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg).
* Настройте [брандмауэр Windows для доступа к ядру СУБД](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access).
* Откройте брандмауэр Windows, чтобы предоставить Azure Database Migration Service доступ к исходному серверу SQL Server. По умолчанию это TCP-порт 1433.
* Для базы данных SQL создайте [правило брандмауэра](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure) на уровне сервера, чтобы разрешить Azure Database Migration Service доступ к целевой базе данных. Укажите диапазон подсети виртуальной сети, используемый для Azure Database Migration Service.
* Убедитесь, что учетные данные, используемые для подключения к исходному экземпляру SQL Server RDS, принадлежат учетной записи с ролью сервера Processadmin и ролью базы данных db_owner во всех базах данных, которые необходимо перенести.
* Убедитесь, что учетные данные, используемые для подключения к целевой базе данных, имеют разрешение CONTROL DATABASE на целевую базу данных в базе данных SQL и член роли sysadmin при миграции в базу данных в SQL Управляемый экземпляр.
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
* Отключите триггеры базы данных в целевой базе данных.
    > [!NOTE]
    > Триггеры базы данных в целевой базе данных можно найти с помощью следующего запроса:
    ```
    Use <Database name>
    go
    select * from sys.triggers
    DISABLE TRIGGER (Transact-SQL)
    ```
    Дополнительные сведения см. в статье [DISABLE TRIGGER (Transact-SQL)](https://docs.microsoft.com/sql/t-sql/statements/disable-trigger-transact-sql?view=sql-server-2017).

## <a name="migrate-the-sample-schema"></a>Перенос примера схемы
Используйте DMA для переноса схемы.

> [!NOTE]
> Перед созданием проекта миграции в DMA убедитесь, что вы уже подготовили базу данных в базе данных SQL или Управляемый экземпляр SQL, как упоминалось в предварительных требованиях. В рамках этого руководства предполагается, что имя базы данных **AdventureWorks2012**, но вы можете указать любое имя.

Чтобы перенести схему **AdventureWorks2012** , выполните следующие действия.

1. В Помощнике по миграции данных щелкните значок New (+) (Создать (+)), а затем в разделе **Project type** (Тип проекта) выберите **Migration** (Миграция).
2. Укажите имя проекта в текстовом поле **Source server type** (Тип исходного сервера), выберите **SQL Server**, а затем в текстовом поле **Target server type** (Тип целевого сервера) выберите **База данных SQL Azure**.

    > [!NOTE]
    > В качестве типа целевого сервера выберите **база данных SQL Azure** для миграции в базу данных SQL Azure, а также в SQL управляемый экземпляр.

3. В разделе **Migration Scope** (Область переноса) выберите **Schema only** (Только схема).

    После выполнения предыдущих действий интерфейс Помощника по миграции данных должен выглядеть, как показано на следующем рисунке:

    ![Создание проекта в помощнике по миграции данных](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/dma-create-project.png)

4. Выберите **Создать**, чтобы создать проект.
5. В Помощнике по миграции данных задайте сведения о подключении к источнику SQL Server, нажмите **Подключение**, а затем выберите базу данных **AdventureWorks2012**.

    ![Сведения о подключении к источнику в помощнике по миграции данных](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/dma-source-connect.png)

6. Нажмите кнопку **Далее**, в разделе **Подключение к целевому серверу**укажите сведения о целевом подключении для базы данных в базе данных SQL или управляемый экземпляр SQL, нажмите кнопку **подключить**, а затем выберите базу данных **адвентуреворксазуре** , которую вы предварительно подготовили.

    ![Сведения о подключении к целевому объекту в помощнике по миграции данных](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/dma-target-connect.png)

7. Нажмите кнопку **Далее** , чтобы перейти к экрану **Выбор объектов** , на котором можно указать объекты схемы в базе данных **AdventureWorks2012** , которые необходимо развернуть.

    По умолчанию выбраны все объекты.

    ![Создание сценариев SQL](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/dma-assessment-source.png)

8. Выберите **Создать скрипт SQL**, чтобы создать скрипт SQL, а затем просмотрите его на наличие ошибок.

    ![Скрипт схемы](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/dma-schema-script.png)

9. Выберите **развернуть схему** , чтобы развернуть схему, а затем после развертывания схемы проверьте целевое значение на наличие аномалий.

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

    Виртуальная сеть предоставляет Azure Database Migration Service с доступом к исходной SQL Server и целевой базе данных SQL или Управляемый экземпляр SQL.

    Дополнительные сведения о создании виртуальной сети в портал Azure см. в статье [Создание виртуальной сети с помощью портал Azure](https://aka.ms/DMSVnet).

6. Выберите ценовую категорию. Для этой миграции по сети необходимо выбрать ценовую категорию "Премиум".

    Дополнительные сведения о затратах и ценовых категориях см. на [странице с ценами](https://aka.ms/dms-pricing).

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
    > В качестве типа целевого сервера выберите **база данных SQL Azure** для миграции в базу данных SQL, а также в SQL управляемый экземпляр.

5. В разделе **Выбор типа действия** выберите **Перенос данных в сети**.

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
    > TLS-подключения, зашифрованные с помощью самозаверяющего сертификата, не обеспечивают надежную защиту. Они уязвимы для атак "злоумышленник в середине". Не следует полагаться на TLS, используя самозаверяющие сертификаты в рабочей среде или на серверах, подключенных к Интернету.

   ![Сведения об источнике](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/dms-source-details3.png)

## <a name="specify-target-details"></a>Указание сведений о цели

1. Нажмите кнопку **сохранить**, а затем на экране **сведения о целевом объекте миграции** укажите сведения о подключении для целевой базы данных в Azure.

    ![Выбор цели](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/dms-select-target3.png)

2. Нажмите кнопку **Сохранить**, а затем на экране **Сопоставление с целевыми базами данных** сопоставьте исходную и целевую базы данных для миграции.

    Если в целевой базе данных содержится такое же имя базы данных, что и в исходной базе данных, Azure Database Migration Service выберет целевую базу данных по умолчанию.

    ![Сопоставление с целевыми базами данных](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/dms-map-targets-activity4.png)

3. На экране **Выбрать таблицы** выберите **Сохранить**, разверните список таблиц и просмотрите список затронутых полей.

    Azure Database Migration Service автоматически выбирает все пустые исходные таблицы, существующие в целевой базе данных. Чтобы повторно перенести таблицы, уже содержащие данные, необходимо явным образом выбрать их на этом экране.

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

    Откроется окно действие миграции, в котором будет **инициализировано** **состояние** действия.

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
4. Когда состояние переноса базы данных изменится на **завершено**, Подключите свои приложения к новой целевой базе данных.

    ![Состояние действия — завершено](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/dms-activity-completed.png)

## <a name="next-steps"></a>Дальнейшие действия

* Сведения об известных проблемах и ограничениях при выполнении оперативной миграции в Azure см. в статье [Известные проблемы и решения для оперативной миграции](known-issues-azure-sql-online.md).
* Дополнительные сведения о Database Migration Service см. в статье [что такое Database Migration Service?](https://docs.microsoft.com/azure/dms/dms-overview).
* Дополнительные сведения о базе данных SQL см. в статье [что такое служба базы данных SQL?](https://docs.microsoft.com/azure/sql-database/sql-database-technical-overview).
* Сведения об управляемых экземплярах SQL см. в статье [что такое sql управляемый экземпляр](https://docs.microsoft.com/azure/azure-sql/managed-instance/sql-managed-instance-paas-overview).
