---
title: Перенос SQL Server в Управляемый экземпляр Базы данных SQL Azure по сети с помощью службы Azure Database Migration Service | Документация Майкрософт
description: Узнайте, как выполнять перенос баз данных из локального экземпляра SQL Server в Управляемый экземпляр Базы данных SQL Azure по сети с помощью Azure Database Migration Service.
services: dms
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: ''
ms.service: dms
ms.workload: data-services
ms.custom: mvc, tutorial
ms.topic: article
ms.date: 10/10/2018
ms.openlocfilehash: 56bd3ab61045ff99601ed50cd27332b370204795
ms.sourcegitcommit: 7b0778a1488e8fd70ee57e55bde783a69521c912
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/10/2018
ms.locfileid: "49068032"
---
# <a name="migrate-sql-server-to-azure-sql-database-managed-instance-online-using-dms"></a>Перенос SQL Server в Управляемый экземпляр Базы данных SQL Azure с помощью DMS в автономном режиме
Azure Database Migration Service можно использовать для переноса баз данных из локального экземпляра SQL Server в [Управляемый экземпляр Базы данных SQL Azure](../sql-database/sql-database-managed-instance.md) с минимальным временем простоя в работе приложений. Сведения о дополнительных методах, которые могут потребовать некоторых действий вручную, см. в статье [Перенос экземпляра SQL Server в Управляемый экземпляр базы данных SQL Azure](../sql-database/sql-database-managed-instance-migrate.md).

>[!IMPORTANT]
>Проекты миграции из SQL Server в Управляемый экземпляр базы данных SQL Azure по сети находятся в стадии предварительной версии. На них распространяются [Дополнительные условия использования для предварительных версий Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

В этом руководстве выполняется миграция базы данных **Adventureworks2012** из локального экземпляра SQL Server в Управляемый экземпляр базы данных SQL Azure с помощью Azure Database Migration Service с минимальным временем простоя.

Из этого руководства вы узнаете, как выполнять следующие задачи:
> [!div class="checklist"]
> * создание экземпляра Azure Database Migration Service;
> * создание проекта миграции и запуск миграции по сети с помощью Azure Database Migration Service;
> * мониторинг миграции.
> * Когда будете готовы, выполните прямую миграцию.

## <a name="prerequisites"></a>Предварительные требования
Для работы с этим руководством вам потребуется следующее:

- Создайте виртуальную сеть для Azure Database Migration Service с помощью модели развертывания Azure Resource Manager, которая обеспечивает подключение "сеть — сеть" к локальным исходным серверам с помощью [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) или [VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways). Изучите [сетевые топологии для миграции Управляемого экземпляра Базы данных Azure SQL с помощью Azure Database Migration Service](https://aka.ms/dmsnetworkformi).
- Убедитесь, что правила группы безопасности сети для виртуальной сети Azure (VNET) не блокируют порты 443, 53, 9354, 445 и 12000. Дополнительные сведения о фильтрации трафика, предназначенного для виртуальной сети Azure, с помощью NSG см. в статье [Фильтрация сетевого трафика с помощью групп безопасности сети](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg).
- Настройте [брандмауэр Windows для доступа к ядру исходной СУБД](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access).
- Откройте брандмауэр Windows, чтобы предоставить Azure Database Migration Service доступ к исходному серверу SQL Server. По умолчанию это TCP-порт 1433.
- Если вы запустили несколько именованных экземпляров SQL Server, использующих динамические порты, вы можете включить службу обозревателя SQL и разрешить доступ к UDP-порту 1434 через брандмауэры. Это позволит службе Azure Database Migration Service подключиться к именованному экземпляру на исходном сервере.
- Если перед исходными базами данных используется брандмауэр, его правила должны разрешать службе Azure Database Migration Service доступ к исходным базам данных для миграции и файлам SMB через порт 445.
- Создайте Управляемый экземпляр Базы данных SQL Azure на портале Azure, следуя инструкциям из [этой статьи](https://aka.ms/sqldbmi).
- Убедитесь, что для подключения к исходному серверу SQL Server и целевому управляемому экземпляру используются имена, входящие в серверную роль sysadmin.
- Укажите сетевую папку SMB, содержащую все файлы полных резервных копий базы данных и файлы резервных копий журнала транзакций, которые могут использоваться службой Azure Database Migration Service для миграции базы данных.
- Предоставьте учетной записи службы, от имени которой выполняется исходный экземпляр SQL Server, права на запись в эту созданную сетевую папку, а учетной записи компьютера для исходного сервера — права на чтение и запись для этой папки.
- Запишите имя пользователя и пароль учетной записи Windows, которой предоставлены полные права доступа к этой сетевой папке. Служба Azure Database Migration Service олицетворяет пользователя с этими учетными данными, чтобы отправить файлы резервных копий в контейнер службы хранилища Azure для операции восстановления.
- Создайте идентификатор приложения Azure Active Directory, создающего ключ идентификатора приложения, который может использоваться службой DMS для подключения к целевому Управляемому экземпляру Базы данных SQL Azure и контейнеру службы хранилища Azure. Дополнительные сведения см. в статье [Создание приложения Azure Active Directory и субъекта-службы с доступом к ресурсам с помощью портала](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-create-service-principal-portal).
- Создайте и запишите учетную запись хранения Azure с **уровнем производительности "Стандартный"**, которая позволяет службе DMS передавать файлы резервной копии базы данных и использовать их для переноса баз данных.  Убедитесь, что учетная запись хранения Azure создана в том же регионе, что и служба DMS.

## <a name="register-the-microsoftdatamigration-resource-provider"></a>Регистрация поставщика ресурсов Microsoft.DataMigration

1. Войдите на портал Azure, щелкните **Все службы** и выберите **Подписки**.

    ![Отображение подписок на портале](media\tutorial-sql-server-to-managed-instance-online\portal-select-subscriptions.png)        
2. Выберите подписку, в которой нужно создать экземпляр Azure Database Migration Service, а затем щелкните **Поставщики ресурсов**.

    ![Отображение поставщиков ресурсов](media\tutorial-sql-server-to-managed-instance-online\portal-select-resource-provider.png)

3. В поле поиска введите migration, а затем справа от **Microsoft.DataMigration** щелкните **Зарегистрировать**.

    ![Регистрация поставщика ресурсов](media\tutorial-sql-server-to-managed-instance-online\portal-register-resource-provider.png)   

## <a name="create-an-azure-database-migration-service-instance"></a>Создание экземпляра Azure Database Migration Service

1. На портале Azure выберите + **Создать ресурс**, введите в поле поиска **Azure Database Migration Service**, а затем в раскрывающемся списке выберите **Azure Database Migration Service**.

     ![Azure Marketplace](media\tutorial-sql-server-to-managed-instance-online\portal-marketplace.png)

2. На экране **Azure Database Migration Service** выберите **Создать**.

    ![Создание экземпляра Azure Database Migration Service](media\tutorial-sql-server-to-managed-instance-online\dms-create1.png)

3. На экране **Создание службы миграции** укажите имя службы, подписку и новую или существующую группу ресурсов.

4.  Выберите расположение, в котором хотите создать экземпляр DMS.

5. Выберите существующую виртуальную сеть или создайте новую.
 
    Виртуальная сеть предоставляет Azure Database Migration Service доступ к исходному экземпляру SQL Server и конечному Управляемому экземпляру базы данных SQL Azure.

    Дополнительные сведения о создании виртуальной сети на портале Azure см. в статье [Создание виртуальной сети с помощью портала Azure](https://aka.ms/DMSVnet).

    Подробные сведения см. в статье [Сетевые топологии для переноса Управляемого экземпляра базы данных Azure SQL с помощью Azure Database Migration Service](https://aka.ms/dmsnetworkformi).

6. Выберите номер SKU из ценовой категории "Критически важный для бизнеса (предварительная версия)".

    > [!NOTE]
    > Миграция по сети поддерживается, только если используется уровень "Критически важный для бизнеса (предварительная версия)". 
   
    Дополнительные сведения о ценовых категориях и затратах см. на [странице с описанием цен](https://aka.ms/dms-pricing).
   
    ![Создание службы DMS](media\tutorial-sql-server-to-managed-instance-online\dms-create-service3.png)

7.  Выберите **Создать**, чтобы создать службу.

## <a name="create-a-migration-project"></a>Создание проекта миграции

После создания экземпляра службы найдите его на портале Azure, откройте и создайте проект миграции.

1. На портале Azure щелкните **Все службы**, выполните поиск по запросу "Azure Database Migration Service" и выберите **Azure Database Migration Services** (Службы Azure Database Migration Service).

    ![Поиск всех экземпляров Azure Database Migration Service](media\tutorial-sql-server-to-managed-instance-online\dms-search.png)

2. На экране **Azure Database Migration Service** найдите имя созданного экземпляра и выберите его.
 
3. Выберите **+ Новый проект миграции**.

4. На экране **New migration project** (Новый проект миграции) задайте имя для проекта, в текстовом поле **Source server type** (Тип исходного сервера) выберите **SQL Server**, в текстовом поле **Target server type** (Тип целевого сервера) выберите **Azure SQL Database Managed Instance** (Управляемый экземпляр Базы данных SQL Azure), а затем в разделе **Выберите тип действия** нажмите кнопку **Online data migration (preview)** (Автономная миграция данных (предварительная версия)).

   ![Создание проекта DMS](media\tutorial-sql-server-to-managed-instance-online\dms-create-project3.png)

5. Выберите **Create and run activity** (Создать и выполнить действие), чтобы создать проект.

## <a name="specify-source-details"></a>Указание сведений об источнике

1. На экране **Migration source detail** (Сведения об источнике миграции) задайте сведения о подключении для исходного SQL Server.

2. Если на сервере не установлен доверенный сертификат, установите флажок **Доверять сертификату сервера**.

    Если доверенный сертификат не установлен, SQL Server создаст самозаверяющий сертификат при запуске экземпляра. Этот сертификат используется с целью шифрования учетных данных для клиентских подключений.

    > [!CAUTION]
    > SSL-соединения, шифруемые с помощью самозаверяющего сертификата, не обеспечивают надежной защиты. Они уязвимы для атак "злоумышленник в середине". В рабочей среде или на серверах, подключенных к Интернету, не следует применять самозаверяющие сертификаты для SSL.

   ![Сведения об источнике](media\tutorial-sql-server-to-managed-instance-online\dms-source-details2.png)

3. Щелкните **Сохранить**.

## <a name="specify-target-details"></a>Указание сведений о цели

1.  На экране **Migration target details** (Сведения о целевом объекте миграции) укажите **идентификатор приложения** и **ключ**, которые будут использоваться экземпляром DMS для подключения к целевому экземпляру Управляемого экземпляра Базы данных SQL Azure и учетной записи хранения Azure.

    Дополнительные сведения см. в статье [Создание приложения Azure Active Directory и субъекта-службы с доступом к ресурсам с помощью портала](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-create-service-principal-portal).
    
2. Выберите **подписку**, содержащую целевой экземпляр Управляемого экземпляра Базы данных SQL Azure, а затем выберите целевой экземпляр.

    Если Управляемый экземпляр базы данных SQL Azure еще не подготовлен, щелкните [эту ссылку](https://aka.ms/SQLDBMI), чтобы подготовить его. Когда экземпляр Управляемого экземпляра базы данных SQL Azure будет готов, вернитесь к этому проекту, чтобы выполнить миграцию.

3. Укажите **пользователя SQL** и **пароль**, чтобы подключиться к целевому экземпляру Управляемого экземпляра Базы данных SQL Azure.

       ![Select Target](media\tutorial-sql-server-to-managed-instance-online\dms-target-details3.png)

2.  Щелкните **Сохранить**.

## <a name="select-source-databases"></a>Выбор баз данных-источников

1. На экране **Выбор баз данных-источников** выберите базу данных-источник для миграции.

    ![Выбор баз данных-источников](media\tutorial-sql-server-to-managed-instance-online\dms-select-source-databases2.png)

2. Щелкните **Сохранить**.

## <a name="configure-migration-settings"></a>Настройка параметров миграции
 
1. На экране **Configure migration settings** (Настройка параметров миграции) укажите следующие сведения:

    | | |
    |--------|---------|
    |**SMB Network location share** (Общая сетевая папка SMB) | Локальная сетевая папка SMB, содержащая файлы резервных копий базы данных и файлы резервных копий журнала транзакций, которые могут использоваться службой Azure Database Migration Service для миграции. Учетная запись службы, в которой выполняется исходный экземпляр SQL Server, должна иметь права записи и чтения для этой сетевой папки. Укажите полное доменное имя или IP-адрес сервера и сетевую папку, например "\\\servername.domainname.com\backupfolder" или "\\\IP address\backupfolder".|
    |**Имя пользователя** | Убедитесь, что пользователь Windows имеет полное право доступа к указанной ранее сетевой папке. Служба Azure Database Migration Service будет олицетворять пользователя с этими учетными данными, чтобы отправить файлы резервных копий в контейнер службы хранилища Azure для операции восстановления. |
    |**Пароль** | Пароль для пользователя |
    |**Subscription of the Azure Storage Account** (Подписка учетной записи хранения Azure) | Выберите подписку, которая содержит учетную запись хранения Azure. |
    |**Учетная запись хранения Azure** | Выберите учетную запись хранения Azure, в которую служба DMS может отправлять файлы резервной копии из общей сетевой папки SMB, а также использовать эту учетную запись для миграции базы данных.  Рекомендуется выбрать учетную запись хранения в том же регионе, что и служба DMS для получения оптимальной производительности при передаче файлов. |
    
    ![Настройка параметров миграции](media\tutorial-sql-server-to-managed-instance-online\dms-configure-migration-settings4.png)

2. Щелкните **Сохранить**.
 
## <a name="review-the-migration-summary"></a>Просмотр сводки по миграции

1. На экране **Сводка по миграции** в текстовом поле **Имя активности** задайте имя действия миграции.

2. Проверьте и подтвердите сведения, связанные с проектом миграции.
 
    ![Сводка по проекту миграции](media\tutorial-sql-server-to-managed-instance-online\dms-project-summary3.png)

## <a name="run-and-monitor-the-migration"></a>Запуск и мониторинг миграции

1. Выберите **Запустить миграцию**.

2. На экране действия миграции, выберите **Обновить**, чтобы обновить отображающиеся данные.
 
   ![Выполняется действие миграции](media\tutorial-sql-server-to-managed-instance-online\dms-monitor-migration2.png)

    Далее можно развернуть категории баз данных и имен для входа, чтобы отслеживать состояние переноса соответствующих объектов сервера.

   ![Выполняется действие миграции](media\tutorial-sql-server-to-managed-instance-online\dms-monitor-migration-extend2.png)

## <a name="performing-migration-cutover"></a>Выполнение прямой миграции

После восстановления полной резервной копии базы данных на целевом экземпляре Управляемого экземпляра Базы данных SQL Azure база данных будет доступна для выполнения прямой миграции.

1.  Когда вы будете готовы выполнить миграцию базы данных по сети, щелкните **Start Cutover** (Запустить прямую миграцию).

2.  Остановите весь входящий трафик для баз данных-источников.

3.  Создайте [резервную копию заключительного фрагмента журнала], сделайте файл резервной копии доступным в сетевой папке SMB и подождите, пока восстановится окончательная резервная копия журнала транзакций.

    На том этапе для параметра **Ожидающие изменения** будет присвоено значение 0.

4.  Щелкните **Подтвердить**, а затем — **Apply** (Применить).

    ![Подготовка к выполнению прямой миграции](media\tutorial-sql-server-to-managed-instance-online\dms-complete-cutover.png)

5.  Когда состояние миграции базы данных изменится на **Completed** (Завершено), подключите свои приложения к новому экземпляру Управляемого экземпляра Базы данных SQL Azure.

    ![Завершенная прямая миграция](media\tutorial-sql-server-to-managed-instance-online\dms-cutover-complete.png)

## <a name="next-steps"></a>Дополнительная информация

- Дополнительные сведения о переносе базы данных в Управляемый экземпляр с помощью команды T-SQL RESTORE см. в [этой статье](../sql-database/sql-database-managed-instance-restore-from-backup-tutorial.md).
- Сведения об Управляемом экземпляре см. в [обзоре Управляемого экземпляра](../sql-database/sql-database-managed-instance.md).
- Сведения о подключении приложений к Управляемому экземпляру см. в статье [Подключение приложения к Управляемому экземпляру Базы данных SQL](../sql-database/sql-database-managed-instance-connect-app.md).
