---
title: Руководство по миграции SQL Server в управляемый экземпляр Базы данных SQL Azure по сети с помощью службы Azure Database Migration Service | Документация Майкрософт
description: Узнайте, как выполнять перенос баз данных из локального экземпляра SQL Server в управляемый экземпляр Базы данных SQL Azure по сети с помощью Azure Database Migration Service.
services: dms
author: HJToland3
ms.author: jtoland
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: mvc, tutorial
ms.topic: article
ms.date: 04/03/2019
ms.openlocfilehash: d9d57df3ec8e859a1f3257cb54e423d0006286b1
ms.sourcegitcommit: a60a55278f645f5d6cda95bcf9895441ade04629
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/03/2019
ms.locfileid: "58880191"
---
# <a name="tutorial-migrate-sql-server-to-an-azure-sql-database-managed-instance-online-using-dms"></a>Руководство по Перенос баз данных SQL Server в управляемый экземпляр Базы данных SQL Azure по сети с помощью DMS

Azure Database Migration Service можно использовать для переноса баз данных из локального экземпляра SQL Server в [управляемый экземпляр Базы данных SQL Azure](../sql-database/sql-database-managed-instance.md) с минимальным временем простоя в работе приложений. Сведения о дополнительных методах, которые могут потребовать некоторых действий вручную, см. в статье [Перенос экземпляра SQL Server в управляемый экземпляр Базы данных SQL Azure](../sql-database/sql-database-managed-instance-migrate.md).

В этом руководстве выполняется миграция базы данных **Adventureworks2012** из локального экземпляра SQL Server в управляемый экземпляр базы данных SQL Azure с помощью Azure Database Migration Service с минимальным временем простоя.

Из этого руководства вы узнаете, как выполнять следующие задачи:
> [!div class="checklist"]
> - создание экземпляра Azure Database Migration Service;
> - создание проекта миграции и запуск миграции по сети с помощью Azure Database Migration Service;
> - мониторинг миграции.
> - Когда будете готовы, выполните прямую миграцию.

> [!NOTE]
> Чтобы выполнить подключенную миграцию с помощью Azure Database Migration Service, требуется создать экземпляр ценовой категории "Премиум".
> [!IMPORTANT]
> Чтобы оптимизировать процесс миграции, Майкрософт рекомендует создать экземпляр Azure Database Migration Service в том же регионе Azure, в котором размещена целевая база данных. Перемещение данных между регионами и географическими областями может замедлить процесс миграции и привести к ошибкам.

[!INCLUDE [online-offline](../../includes/database-migration-service-offline-online.md)]

В этой статье описывается перенос данных из SQL Server в управляемый экземпляр Базы данных SQL Azure по сети. Сведения о переносе в автономном режиме см. в руководстве по [переносу SQL Server в Управляемый экземпляр Базы данных SQL Azure с помощью DMS в автономном режиме](tutorial-sql-server-to-managed-instance.md).

## <a name="prerequisites"></a>Предварительные требования

Для работы с этим руководством вам потребуется следующее:

- Создайте виртуальную сеть Azure для Azure Database Migration Service с помощью модели развертывания Azure Resource Manager, которая обеспечивает подключение "сеть — сеть" к локальным исходным серверам с помощью [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) или [VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways). [Изучите сетевые топологии для переноса в управляемый экземпляр Базы данных Azure SQL с помощью Azure Database Migration Service](https://aka.ms/dmsnetworkformi).

    > [!NOTE]
    > Если вы используете ExpressRoute с пиринговой связью с сетью корпорации Майкрософт, во время настройки виртуальной сети добавьте в подсеть, в которой будет подготовлена служба, следующие [конечные точки службы](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview):
    > - целевую конечную точку базы данных (например, конечная точка SQL, конечная точка Cosmos DB и т. д.);
    > - конечную точку службы хранилища;
    > - конечную точку служебной шины.
    >
    > Эта настройка необходима, потому что у Azure Database Migration Service нет подключения к Интернету.

- Убедитесь, что правила группы безопасности сети для виртуальной сети не блокируют следующие входящие порты для Azure Database Migration Service: 443, 53, 9354, 445, 12000. Дополнительные сведения о фильтрации трафика, предназначенного для виртуальной сети Azure, с помощью NSG см. в статье [Фильтрация сетевого трафика с помощью групп безопасности сети](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg).
- Настройте [брандмауэр Windows для доступа к ядру исходной СУБД](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access).
- Откройте брандмауэр Windows, чтобы предоставить Azure Database Migration Service доступ к исходному серверу SQL Server. По умолчанию это TCP-порт 1433.
- Если вы запустили несколько именованных экземпляров SQL Server, использующих динамические порты, вы можете включить службу обозревателя SQL и разрешить доступ к UDP-порту 1434 через брандмауэры. Это позволит службе Azure Database Migration Service подключиться к именованному экземпляру на исходном сервере.
- Если перед исходными базами данных используется брандмауэр, его правила должны разрешать службе Azure Database Migration Service доступ к исходным базам данных для миграции и файлам SMB через порт 445.
- Создайте управляемый экземпляр Базы данных SQL Azure на портале Azure, следуя инструкциям из [этой статьи](https://aka.ms/sqldbmi).
- Убедитесь, что для подключения к исходному серверу SQL Server и целевому управляемому экземпляру используются имена, входящие в серверную роль sysadmin.
- Укажите сетевую папку SMB, содержащую все файлы полных резервных копий базы данных и файлы резервных копий журнала транзакций, которые могут использоваться службой Azure Database Migration Service для миграции базы данных.
- Предоставьте учетной записи службы, от имени которой выполняется исходный экземпляр SQL Server, права на запись в эту созданную сетевую папку, а учетной записи компьютера для исходного сервера — права на чтение и запись для этой папки.
- Запишите имя пользователя и пароль учетной записи Windows, которой предоставлены полные права доступа к этой сетевой папке. Служба Azure Database Migration Service олицетворяет пользователя с этими учетными данными, чтобы отправить файлы резервных копий в контейнер службы хранилища Azure для операции восстановления.
- Создайте идентификатор приложения Azure Active Directory, создающего ключ идентификатора приложения, который может использоваться службой DMS для подключения к целевому управляемому экземпляру Базы данных SQL Azure и контейнеру службы хранилища Azure. Дополнительные сведения см. в статье [Создание приложения Azure Active Directory и субъекта-службы с доступом к ресурсам с помощью портала](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-create-service-principal-portal).
- Создайте и запишите учетную запись хранения Azure с **уровнем производительности "Стандартный"**, которая позволяет службе DMS передавать файлы резервной копии базы данных и использовать их для переноса баз данных.  Убедитесь, что учетная запись хранения Azure создана в том же регионе, что и служба DMS.

## <a name="register-the-microsoftdatamigration-resource-provider"></a>Регистрация поставщика ресурсов Microsoft.DataMigration

1. Войдите на портал Azure, щелкните **Все службы** и выберите **Подписки**.

    ![Отображение подписок на портале](media/tutorial-sql-server-to-managed-instance-online/portal-select-subscriptions.png)        
2. Выберите подписку, в которой нужно создать экземпляр Azure Database Migration Service, а затем щелкните **Поставщики ресурсов**.

    ![Отображение поставщиков ресурсов](media/tutorial-sql-server-to-managed-instance-online/portal-select-resource-provider.png)

3. В поле поиска введите migration, а затем справа от **Microsoft.DataMigration** щелкните **Зарегистрировать**.

    ![Регистрация поставщика ресурсов](media/tutorial-sql-server-to-managed-instance-online/portal-register-resource-provider.png)   

## <a name="create-an-azure-database-migration-service-instance"></a>Создание экземпляра Azure Database Migration Service

1. На портале Azure выберите + **Создать ресурс**, введите в поле поиска **Azure Database Migration Service**, а затем в раскрывающемся списке выберите **Azure Database Migration Service**.

     ![Azure Marketplace](media/tutorial-sql-server-to-managed-instance-online/portal-marketplace.png)

2. На экране **Azure Database Migration Service** выберите **Создать**.

    ![Создание экземпляра Azure Database Migration Service](media/tutorial-sql-server-to-managed-instance-online/dms-create1.png)

3. На экране **Создание службы миграции** укажите имя службы, подписку и новую или существующую группу ресурсов.

4. Выберите расположение, в котором хотите создать экземпляр DMS.

5. Выберите существующую виртуальную сеть или создайте новую.

    Виртуальная сеть предоставляет Azure Database Migration Service доступ к исходному экземпляру SQL Server и целевому управляемому экземпляру Базы данных SQL Azure.

    Дополнительные сведения о создании виртуальной сети на портале Azure см. в статье [Создание виртуальной сети с помощью портала Azure](https://aka.ms/DMSVnet).

    Подробные сведения см. в статье [Сетевые топологии для переноса Управляемого экземпляра Базы данных Azure SQL с помощью Azure Database Migration Service](https://aka.ms/dmsnetworkformi).

6. Выберите номер SKU ценовой категории "Премиум".

    > [!NOTE]
    > Миграция по сети поддерживается, только если используется уровень "Премиум". 

    Дополнительные сведения о ценовых категориях и затратах см. на [странице с описанием цен](https://aka.ms/dms-pricing).

    ![Создание службы DMS](media/tutorial-sql-server-to-managed-instance-online/dms-create-service3.png)

7. Выберите **Создать**, чтобы создать службу.

## <a name="create-a-migration-project"></a>Создание проекта миграции

После создания экземпляра службы найдите его на портале Azure, откройте и создайте проект миграции.

1. На портале Azure щелкните **Все службы**, выполните поиск по запросу "Azure Database Migration Service" и выберите **Azure Database Migration Services** (Службы Azure Database Migration Service).

    ![Поиск всех экземпляров Azure Database Migration Service](media/tutorial-sql-server-to-managed-instance-online/dms-search.png)

2. На экране **Служба миграции баз данных Azure** найдите имя созданного экземпляра и выберите его.

3. Выберите **+ Новый проект миграции**.

4. На экране **New migration project** (Новый проект миграции) задайте имя для проекта, в текстовом поле **Source server type** (Тип исходного сервера) выберите **SQL Server**, в текстовом поле **Target server type** (Тип целевого сервера) выберите **Управляемый экземпляр Базы данных SQL Azure**, а затем в разделе **Choose type of activity** (Выберите тип действия) выберите **Online data migration** (Миграция данных по сети).

   ![Создание проекта DMS](media/tutorial-sql-server-to-managed-instance-online/dms-create-project3.png)

5. Выберите **Create and run activity** (Создать и выполнить действие), чтобы создать проект.

## <a name="specify-source-details"></a>Указание сведений об источнике

1. На экране **Migration source detail** (Сведения об источнике миграции) задайте сведения о подключении для исходного SQL Server.

2. Если на сервере не установлен доверенный сертификат, установите флажок **Доверять сертификату сервера**.

    Если доверенный сертификат не установлен, SQL Server создаст самозаверяющий сертификат при запуске экземпляра. Этот сертификат используется с целью шифрования учетных данных для клиентских подключений.

    > [!CAUTION]
    > SSL-соединения, шифруемые с помощью самозаверяющего сертификата, не обеспечивают надежной защиты. Они уязвимы для атак "злоумышленник в середине". В рабочей среде или на серверах, подключенных к Интернету, не следует применять самозаверяющие сертификаты для SSL.

   ![Сведения об источнике](media/tutorial-sql-server-to-managed-instance-online/dms-source-details2.png)

3. Щелкните **Сохранить**.

4. На экране **Выбор баз данных-источников** выберите базу данных **Adventureworks2012** для миграции.

   ![Выбор баз данных-источников](media/tutorial-sql-server-to-managed-instance-online/dms-source-database1.png)

    > [!IMPORTANT]
    > При использовании MSSQL Integration Services (SSIS), DMS не поддерживает перенос базы данных каталога для проектов или пакетов служб SSIS (SSISDB) из SQL Server в управляемый экземпляр Базы данных SQL Azure. Но можно подготовить SSIS в Фабрике данных Azure (ADF) и повторно развернуть проекты или пакеты служб SQL Server Integration Services в целевой базе данных SQL Server Integration Services, размещенной в управляемом экземпляре Базы данных SQL Azure. Дополнительные сведения о миграции пакетов SSIS см. в статье [Перенос пакетов SQL Server Integration Services в Azure](https://docs.microsoft.com/azure/dms/how-to-migrate-ssis-packages).

5. Щелкните **Сохранить**.

## <a name="specify-target-details"></a>Указание сведений о цели

1. На экране **Migration target details** (Сведения о целевом объекте миграции) укажите **идентификатор приложения** и **ключ**, которые будут использоваться экземпляром DMS для подключения к целевому экземпляру управляемого экземпляра Базы данных SQL Azure и учетной записи хранения Azure.

    Дополнительные сведения см. в статье [Создание приложения Azure Active Directory и субъекта-службы с доступом к ресурсам с помощью портала](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-create-service-principal-portal).

2. Выберите **подписку**, содержащую целевой экземпляр управляемого экземпляра Базы данных SQL Azure, а затем выберите целевой экземпляр.

    Если управляемый экземпляр Базы данных SQL Azure еще не подготовлен, щелкните [эту ссылку](https://aka.ms/SQLDBMI), чтобы подготовить его. Когда управляемый экземпляр Базы данных SQL Azure будет готов, вернитесь к этому проекту, чтобы выполнить миграцию.

3. Укажите **пользователя SQL** и **пароль**, чтобы подключиться к управляемому экземпляру Базы данных SQL Azure.

    ![Выбор цели](media/tutorial-sql-server-to-managed-instance-online/dms-target-details3.png)

4. Щелкните **Сохранить**.

## <a name="select-source-databases"></a>Выбор баз данных-источников

1. На экране **Выбор баз данных-источников** выберите базу данных-источник для миграции.

    ![Выбор баз данных-источников](media/tutorial-sql-server-to-managed-instance-online/dms-select-source-databases2.png)

2. Щелкните **Сохранить**.

## <a name="configure-migration-settings"></a>Настройка параметров миграции

1. На экране **Configure migration settings** (Настройка параметров миграции) укажите следующие сведения:

    | | |
    |--------|---------|
    |**Общая сетевая папка SMB** | Локальная сетевая папка SMB, содержащая файлы резервных копий базы данных и файлы резервных копий журнала транзакций, которые могут использоваться службой Azure Database Migration Service для миграции. Учетная запись службы, в которой выполняется исходный экземпляр SQL Server, должна иметь права записи и чтения для этой сетевой папки. Укажите полное доменное имя или IP-адрес сервера и сетевую папку, например "\\\servername.domainname.com\backupfolder" или "\\\IP address\backupfolder".|
    |**Имя пользователя** | Убедитесь, что пользователь Windows имеет полное право доступа к указанной ранее сетевой папке. Служба Azure Database Migration Service будет олицетворять пользователя с этими учетными данными, чтобы отправить файлы резервных копий в контейнер службы хранилища Azure для операции восстановления. |
    |**Пароль** | Пароль для пользователя |
    |**Подписка учетной записи хранения Azure** | Выберите подписку, которая содержит учетную запись хранения Azure. |
    |**Учетная запись хранения Azure** | Выберите учетную запись хранения Azure, в которую служба DMS может отправлять файлы резервной копии из общей сетевой папки SMB, а также использовать эту учетную запись для миграции базы данных.  Рекомендуется выбрать учетную запись хранения в том же регионе, что и служба DMS для получения оптимальной производительности при передаче файлов. |

    ![Настройка параметров миграции](media/tutorial-sql-server-to-managed-instance-online/dms-configure-migration-settings4.png)

2. Щелкните **Сохранить**.

## <a name="review-the-migration-summary"></a>Просмотр сводки по миграции

1. На экране **Сводка по миграции** в текстовом поле **Имя активности** задайте имя действия миграции.

2. Проверьте и подтвердите сведения, связанные с проектом миграции.

    ![Сводка по проекту миграции](media/tutorial-sql-server-to-managed-instance-online/dms-project-summary3.png)

## <a name="run-and-monitor-the-migration"></a>Запуск и мониторинг миграции

1. Выберите **Запустить миграцию**.

2. На экране действия миграции, выберите **Обновить**, чтобы обновить отображающиеся данные.

   ![Выполняется действие миграции](media/tutorial-sql-server-to-managed-instance-online/dms-monitor-migration2.png)

    Далее можно развернуть категории баз данных и имен для входа, чтобы отслеживать состояние переноса соответствующих объектов сервера.

   ![Выполняется действие миграции](media/tutorial-sql-server-to-managed-instance-online/dms-monitor-migration-extend2.png)

## <a name="performing-migration-cutover"></a>Выполнение прямой миграции

После восстановления полной резервной копии базы данных на целевом экземпляре управляемого экземпляра Базы данных SQL Azure база данных будет доступна для выполнения прямой миграции.

1. Когда вы будете готовы выполнить миграцию базы данных по сети, щелкните **Start Cutover** (Запустить прямую миграцию).

2. Остановите весь входящий трафик для баз данных-источников.

3. Создайте [резервную копию заключительного фрагмента журнала], сделайте файл резервной копии доступным в сетевой папке SMB и подождите, пока восстановится окончательная резервная копия журнала транзакций.

    На том этапе для параметра **Ожидающие изменения** будет присвоено значение 0.

4. Щелкните **Подтвердить**, а затем — **Apply** (Применить).

    ![Подготовка к выполнению прямой миграции](media/tutorial-sql-server-to-managed-instance-online/dms-complete-cutover.png)

5. Когда состояние миграции базы данных изменится на **Completed** (Завершено), подключите свои приложения к новому экземпляру управляемого экземпляра Базы данных SQL Azure.

    ![Завершенная прямая миграция](media/tutorial-sql-server-to-managed-instance-online/dms-cutover-complete.png)

## <a name="next-steps"></a>Дополнительная информация

- Дополнительные сведения о переносе базы данных в управляемый экземпляр с помощью команды T-SQL RESTORE см. в [этой статье](../sql-database/sql-database-managed-instance-restore-from-backup-tutorial.md).
- Сведения об управляемом экземпляре см. в статье [Использование расширенной защиты данных Базы данных SQL с виртуальными сетями и почти полной совместимостью](../sql-database/sql-database-managed-instance.md).
- Сведения о подключении приложений к управляемому экземпляру см. в статье [Подключение приложения к Управляемому экземпляру Базы данных SQL](../sql-database/sql-database-managed-instance-connect-app.md).
