---
title: Перенос пакетов SSIS в управляемый экземпляр S'L
titleSuffix: Azure Database Migration Service
description: Узнайте, как перенести пакеты и проекты интеграционных служб серверов S'L Server (SSIS) в управляемый экземпляр базы данных Azure S'L с помощью службы миграции базы данных Azure или помощника по миграции данных.
services: database-migration
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019
ms.topic: article
ms.date: 02/20/2020
ms.openlocfilehash: 97a466ab033a42016c0d82465d1f98e2dcae8080
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80297184"
---
# <a name="migrate-sql-server-integration-services-packages-to-an-azure-sql-database-managed-instance"></a>Перенос пакетов SQL Server Integration Services в Управляемый экземпляр Базы данных SQL Azure
Если вы используете службы интеграции серверов серверов (SSIS) и хотите перенести свои проекты/пакеты SSIS из источника SSISDB, размещенного на сервере S'L Server, в пункт назначения SSISDB, размещенный в управляемой базе данных Azure S'L, вы можете использовать службу миграции базы данных Azure.

Если версия SSIS, которую вы используете, раньше 2012 года или вы используете типы пакетов SSISDB, перед миграцией ваших проектов/пакетов SSIS необходимо преобразовать их с помощью преобразования проекта интеграции, который также может быть запущен с SSMS. Дополнительные сведения см. в статье [Преобразование проектов в модель развертывания проекта](https://docs.microsoft.com/sql/integration-services/packages/deploy-integration-services-ssis-projects-and-packages?view=sql-server-2017#convert).

> [!NOTE]
> Служба миграции баз данных Azure (DMS) в настоящее время не поддерживает базу данных Azure S'L в качестве целевого пункта назначения миграции. Для перераспределения проектов/пакетов SSIS в базу данных Azure S-L см. [Redeploy SQL Server Integration Services packages to Azure SQL Database](https://docs.microsoft.com/azure/dms/how-to-migrate-ssis-packages)

Вы узнаете, как выполнять следующие задачи:
> [!div class="checklist"]
>
> * Оценка проектов и пакетов исходной SSIS.
> * Перенос проектов и пакетов SSIS в Azure.

## <a name="prerequisites"></a>Предварительные требования

Для выполнения этих действий вам потребуется следующее:

* Создание виртуальной сети Microsoft Azure для службы миграции баз данных Azure с помощью модели развертывания azure Resource Manager, которая обеспечивает подключение к исходным серверам с помощью [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) или [VPN.](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways) Для получения дополнительной информации см. статью [Сетевые топологии для базы данных Azure S'L, управляемые миграциями экземпляров с помощью службы миграции базы данных Azure.]( https://aka.ms/dmsnetworkformi) Для получения дополнительной информации о создании виртуальной сети, см [Виртуальная сеть Документация](https://docs.microsoft.com/azure/virtual-network/), и особенно быстро начать статьи с пошаговой детали.
* Чтобы гарантировать, что правила виртуальной сетевой группы безопасности не блокируют следующие входящие порты связи в службу миграции базы данных Azure: 443, 53, 9354, 445, 12000. Для получения более подробной информации о виртуальной сети NSG фильтрации трафика, [см.](https://docs.microsoft.com/azure/virtual-network/virtual-network-vnet-plan-design-arm)
* Настройка [Windows Firewall для доступа к исходной базе данных.](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access?view=sql-server-2017)
* Чтобы открыть windows Firewall, чтобы позволить миграционной службе базы данных Azure получить доступ к исходному серверу S'L Server, который по умолчанию является портом TCP 1433.
* Если вы запустили несколько именованных экземпляров SQL Server, использующих динамические порты, вы можете включить службу обозревателя SQL и разрешить доступ к UDP-порту 1434 через брандмауэры. Это позволит службе Azure Database Migration Service подключиться к именованному экземпляру на исходном сервере.
* Если перед исходными базами данных используется брандмауэр, его правила должны разрешать службе Azure Database Migration Service доступ к исходным базам данных для миграции и файлам SMB через порт 445.
* База данных Azure S'L удавалась разместить SSISDB. Если вам нужно создать один, следуйте подробной информации в статье [Создание Azure S S'L Базы данных управляемых экземпляров](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-get-started).
* Для обеспечения того, чтобы логины, используемые для подключения исходного сервера s'L Ис-Сервера и целевого управляемого экземпляра, были членами роли сервера sysadmin.
* Проверить, что SSIS подготовлен на фабрике данных Azure (ADF), содержащей время интеграции Azure-SSIS (IR), с пунктом назначения SSISDB, размещенным в управляемом экземпляре базы данных Azure S'L (как описано в статье [Создание времени выполнения интеграции Azure-SSIS в Azure Data Factory).](https://docs.microsoft.com/azure/data-factory/create-azure-ssis-integration-runtime)

## <a name="assess-source-ssis-projectspackages"></a>Оценка проектов и пакетов исходной SSIS

Хотя оценка источника SSISDB еще не интегрирована в помощник по миграции баз данных (DMA), ваши проекты/пакеты SSIS будут оценены/проверены по мере их передислокации в пункт назначения SSISDB, размещенный в управляемом экземпляре базы данных Azure S'L.

## <a name="register-the-microsoftdatamigration-resource-provider"></a>Регистрация поставщика ресурсов Microsoft.DataMigration

1. Войдите на портал Azure, щелкните **Все службы** и выберите **Подписки**.

    ![Отображение подписок на портале](media/how-to-migrate-ssis-packages-mi/portal-select-subscriptions.png)

2. Выберите подписку, в которой нужно создать экземпляр Azure Database Migration Service, а затем щелкните **Поставщики ресурсов**.

    ![Отображение поставщиков ресурсов](media/how-to-migrate-ssis-packages-mi/portal-select-resource-provider.png)

3. Поиск миграции, а затем справа **от Microsoft.DataMigration**, выберите **Регистр**.

    ![Регистрация поставщика ресурсов](media/how-to-migrate-ssis-packages-mi/portal-register-resource-provider.png)

## <a name="create-an-azure-database-migration-service-instance"></a>Создание экземпляра Azure Database Migration Service

1. На портале Azure выберите **: создайте ресурс,** высоздавайте **миграционную службу базы данных Azure,** а затем выберите **миграционную службу базы данных Azure** из списка выпадающих данных.

     ![Azure Marketplace](media/how-to-migrate-ssis-packages-mi/portal-marketplace.png)

2. На экране **Azure Database Migration Service** выберите **Создать**.

    ![Создание экземпляра Azure Database Migration Service](media/how-to-migrate-ssis-packages-mi/dms-create1.png)

3. На экране **Создание службы миграции** укажите имя службы, подписку и новую или существующую группу ресурсов.

4. Выберите расположение, в котором хотите создать экземпляр DMS.

5. Выберите существующую виртуальную сеть или создайте ее.

    Виртуальная сеть предоставляет миграционной службе базы данных Azure доступ к исходной серверу S'L и целевому экземпляру, управляемому базой данных Azure S'L.

    Для получения дополнительной информации о том, как создать виртуальную сеть на портале Azure, смотрите статью [Создание виртуальной сети с помощью портала Azure](https://aka.ms/DMSVnet).

    Подробные сведения см. в статье [Сетевые топологии для переноса Управляемого экземпляра Базы данных Azure SQL с помощью Azure Database Migration Service](https://aka.ms/dmsnetworkformi).

6. Выберите ценовую категорию.

    Для получения дополнительной информации о затратах и уровнях [ценообразования,](https://aka.ms/dms-pricing)см.

    ![Создание службы DMS](media/how-to-migrate-ssis-packages-mi/dms-create-service2.png)

7. Выберите **Создать**, чтобы создать службу.

## <a name="create-a-migration-project"></a>Создание проекта миграции

После создания экземпляра службы найдите его на портале Azure, откройте и создайте проект миграции.

1. На портале Azure щелкните **Все службы**, выполните поиск по запросу "Azure Database Migration Service" и выберите **Azure Database Migration Services** (Службы Azure Database Migration Service).

    ![Поиск всех экземпляров Azure Database Migration Service](media/how-to-migrate-ssis-packages-mi/dms-search.png)

2. На экране **миграционной службы базы данных Azure** ищите имя созданного экземпляра, а затем выберите экземпляр.

3. Выберите **+ Новый проект миграции**.

4. На экране **проекта «Новая миграция»** укажите имя для проекта, в текстовом поле **типа исходного сервера,** выберите сервер **S'L Server**в текстовом поле типа **Целевого сервера,** выберите **Управляемую базу данных Azure S'L,** а затем для **выбора типа деятельности**, выберите **миграцию пакета SSIS.**

   ![Создание проекта DMS](media/how-to-migrate-ssis-packages-mi/dms-create-project2.png)

5. Выберите **Создать**, чтобы создать проект.

## <a name="specify-source-details"></a>Указание сведений об источнике

1. На экране **Migration source detail** (Сведения об источнике миграции) задайте сведения о подключении для исходного SQL Server.

2. Если на сервере не установлен доверенный сертификат, установите флажок **Доверять сертификату сервера**.

    Если доверенный сертификат не установлен, SQL Server создаст самозаверяющий сертификат при запуске экземпляра. Этот сертификат используется с целью шифрования учетных данных для клиентских подключений.

    > [!CAUTION]
    > Соединения TLS, которые шифруются с помощью сертификата, подписанного самостоятельно, не обеспечивают сильной безопасности. Они уязвимы для атак "злоумышленник в середине". Вы не должны полагаться на TLS, используя самоподписанные сертификаты в производственной среде или на серверах, подключенных к Интернету.

   ![Сведения об источнике](media/how-to-migrate-ssis-packages-mi/dms-source-details1.png)

3. Нажмите кнопку **Сохранить**.

## <a name="specify-target-details"></a>Указание сведений о цели

1. На **экране целевой детали миграции** укажите детали соединения для цели.

     ![Детали цели](media/how-to-migrate-ssis-packages-mi/dms-target-details2.png)

2. Нажмите кнопку **Сохранить**.

## <a name="review-the-migration-summary"></a>Просмотр сводки по миграции

1. На экране **Сводка по миграции** в текстовом поле **Имя активности** задайте имя действия миграции.

2. Для **проекта SSIS (ы) и опции перезаписи среды (s)** указано, следует ли перезаписать или игнорировать существующие проекты и среды SSIS.

    ![Сводка по проекту миграции](media/how-to-migrate-ssis-packages-mi/dms-project-summary2.png)

3. Проверьте и подтвердите сведения, связанные с проектом миграции.

## <a name="run-the-migration"></a>Выполнение миграции

* Выберите **Запустить миграцию**.

## <a name="next-steps"></a>Дальнейшие действия

* Просмотрите [руководство по миграции базы данных Майкрософт](https://datamigration.microsoft.com/).
