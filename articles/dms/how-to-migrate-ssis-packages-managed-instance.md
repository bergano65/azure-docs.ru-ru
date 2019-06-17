---
title: Перенос пакетов служб интеграции SQL Server в управляемый экземпляр базы данных SQL Azure | Документация Майкрософт
description: Узнайте, как перенести пакеты служб интеграции SQL Server в управляемый экземпляр базы данных SQL Azure.
services: database-migration
author: HJToland3
ms.author: jtoland
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: mvc
ms.topic: article
ms.date: 06/08/2019
ms.openlocfilehash: 82a047616c199e37bfa22f53e02f3f7b224b47c1
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/13/2019
ms.locfileid: "67083188"
---
# <a name="migrate-sql-server-integration-services-packages-to-an-azure-sql-database-managed-instance"></a>Перенос пакетов служб интеграции SQL Server в управляемый экземпляр базы данных SQL Azure
Если вы используете SQL Server Integration Services (SSIS) и необходимо выполнить перенос проектов и пакетов служб SSIS из источника SSISDB, размещенной на SQL Server в место назначения SSISDB, размещенной на управляемый экземпляр базы данных SQL Azure, можно использовать Azure Database Migration Service.

Если версия служб SSIS, используется более ранняя, чем 2012 или использовать типы не SSISDB хранилище пакетов, прежде чем миграция your SSIS проектов и пакетов, необходимо преобразовать их с помощью мастера Integration Services проекта преобразования, которое можно также запустить из среды SSMS. Дополнительные сведения см. в статье [Преобразование проектов в модель развертывания проекта](https://docs.microsoft.com/sql/integration-services/packages/deploy-integration-services-ssis-projects-and-packages?view=sql-server-2017#convert).

> [!NOTE]
> Azure Database Migration Service (DMS) сейчас не поддерживает базы данных SQL Azure как в целевой объект назначения миграции. Чтобы выполнить повторное развертывание проектов и пакетов служб SSIS в базу данных SQL Azure, см. в статье [пакетов повторное развертывание SQL Server Integration Services, базу данных SQL Azure](https://docs.microsoft.com/azure/dms/how-to-migrate-ssis-packages).

В этой статье раскрываются следующие темы:
> [!div class="checklist"]
>
> * Оценка проектов и пакетов исходной SSIS.
> * Перенос проектов и пакетов SSIS в Azure.

## <a name="prerequisites"></a>Технические условия

Для выполнения этих действий вам потребуется следующее:

* Создание виртуальной сети Azure (VNet) для Azure Database Migration Service с помощью модели развертывания Azure Resource Manager, которая предоставляет возможность подключения сеть сеть к локальным исходным серверам с помощью [ExpressRoute ](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) или [VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways). Дополнительные сведения см. в статье [сетевые топологии для базы данных SQL управляемого переноса экземпляра с помощью Azure Database Migration Service]( https://aka.ms/dmsnetworkformi). Дополнительные сведения о создании виртуальной сети приведены в [документации по виртуальным сетям](https://docs.microsoft.com/azure/virtual-network/). В частности, уделите внимание кратким руководствам с пошаговыми инструкциями.
* Чтобы убедиться, что правила группы безопасности сети для виртуальной сети не блокирует следующие порты входящего соединения для Azure Database Migration Service: 443, 53, 9354, 445, 12000. Дополнительные сведения о фильтрации трафика, предназначенного для виртуальной сети Azure, с помощью NSG см. в статье [Plan virtual networks](https://docs.microsoft.com/azure/virtual-network/virtual-network-vnet-plan-design-arm) (Планирование виртуальных сетей).
* Для настройки вашей [брандмауэра Windows для доступа к ядру СУБД источника](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access?view=sql-server-2017).
* Чтобы открыть брандмауэр Windows, чтобы предоставить Azure Database Migration Service доступ к исходному SQL Server, который по умолчанию используется TCP-порт 1433.
* Если вы запустили несколько именованных экземпляров SQL Server, использующих динамические порты, вы можете включить службу обозревателя SQL и разрешить доступ к UDP-порту 1434 через брандмауэры. Это позволит службе Azure Database Migration Service подключиться к именованному экземпляру на исходном сервере.
* Если перед исходными базами данных используется брандмауэр, его правила должны разрешать службе Azure Database Migration Service доступ к исходным базам данных для миграции и файлам SMB через порт 445.
* Базе данных SQL управляемого экземпляра, для размещения SSISDB. Если вам нужно создать его, выполните сведениями в статье [создать управляемый экземпляр базы данных SQL Azure](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-get-started).
* Чтобы убедиться, что имена входа, используемый для соединения с источником управляемого экземпляра SQL Server и целевой являются членами роли сервера sysadmin.
* Чтобы убедиться, что службы SSIS подготавливается в фабрики данных Azure (ADF) содержащий среды выполнения интеграции Azure SSIS (IR) с назначением SSISDB, размещенной на базе данных SQL управляемого экземпляра (как описано в статье [создайте Azure SSIS Среда выполнения интеграции в фабрике данных Azure](https://docs.microsoft.com/azure/data-factory/create-azure-ssis-integration-runtime)).

## <a name="assess-source-ssis-projectspackages"></a>Оценка проектов и пакетов исходной SSIS

Во время оценки источника SSISDB еще не интегрированы в базу данных Migration Assistant (DMA), проектов и пакетов служб SSIS, оценивается ли/проверит, так как они могут быть повторное развертывание выполнено в место назначения SSISDB размещено на управляемый экземпляр базы данных SQL Azure.

## <a name="register-the-microsoftdatamigration-resource-provider"></a>Регистрация поставщика ресурсов Microsoft.DataMigration

1. Войдите на портал Azure, щелкните **Все службы** и выберите **Подписки**.

    ![Отображение подписок на портале](media/how-to-migrate-ssis-packages-mi/portal-select-subscriptions.png)

2. Выберите подписку, в которой нужно создать экземпляр Azure Database Migration Service, а затем щелкните **Поставщики ресурсов**.

    ![Отображение поставщиков ресурсов](media/how-to-migrate-ssis-packages-mi/portal-select-resource-provider.png)

3. В поле поиска введите migration, а затем справа от **Microsoft.DataMigration** щелкните **Зарегистрировать**.

    ![Регистрация поставщика ресурсов](media/how-to-migrate-ssis-packages-mi/portal-register-resource-provider.png)

## <a name="create-an-azure-database-migration-service-instance"></a>Создание экземпляра Azure Database Migration Service

1. На портале Azure выберите + **Создать ресурс**, введите в поле поиска **Azure Database Migration Service**, а затем в раскрывающемся списке выберите **Azure Database Migration Service**.

     ![Azure Marketplace](media/how-to-migrate-ssis-packages-mi/portal-marketplace.png)

2. На экране **Azure Database Migration Service** выберите **Создать**.

    ![Создание экземпляра Azure Database Migration Service](media/how-to-migrate-ssis-packages-mi/dms-create1.png)

3. На экране **Создание службы миграции** укажите имя службы, подписку и новую или существующую группу ресурсов.

4. Выберите расположение, в котором хотите создать экземпляр DMS.

5. Создайте виртуальную сеть или выберите имеющуюся.

    Виртуальная сеть предоставляет доступ к исходному серверу SQL Server и целевой базы данных SQL управляемого экземпляра Azure Database Migration Service.

    Дополнительные сведения о создании виртуальной сети на портале Azure см. в статье [Создание виртуальной сети с помощью портала Azure](https://aka.ms/DMSVnet).

    Подробные сведения см. в статье [Сетевые топологии для переноса Управляемого экземпляра Базы данных Azure SQL с помощью Azure Database Migration Service](https://aka.ms/dmsnetworkformi).

6. Выберите ценовую категорию.

    Дополнительные сведения о ценовых категориях и затратах см. на [странице с описанием цен](https://aka.ms/dms-pricing).

    ![Создание службы DMS](media/how-to-migrate-ssis-packages-mi/dms-create-service2.png)

7. Выберите **Создать**, чтобы создать службу.

## <a name="create-a-migration-project"></a>Создание проекта миграции

После создания экземпляра службы найдите его на портале Azure, откройте и создайте проект миграции.

1. На портале Azure щелкните **Все службы**, выполните поиск по запросу "Azure Database Migration Service" и выберите **Azure Database Migration Services** (Службы Azure Database Migration Service).

    ![Поиск всех экземпляров Azure Database Migration Service](media/how-to-migrate-ssis-packages-mi/dms-search.png)

2. На экране **Служба миграции баз данных Azure** найдите имя созданного экземпляра и выберите его.

3. Выберите **+ Новый проект миграции**.

4. На **новый проект миграции** укажите имя проекта, в **тип исходного сервера** текстовое поле, выберите **SQL Server**в **целевого сервера Тип** текстовое поле, выберите **Azure базы данных SQL управляемого экземпляра**, а затем для **выберите тип действия**выберите **миграциипакетовслужбSSIS**.

   ![Создание проекта DMS](media/how-to-migrate-ssis-packages-mi/dms-create-project2.png)

5. Выберите **Создать**, чтобы создать проект.

## <a name="specify-source-details"></a>Указание сведений об источнике

1. На экране **Migration source detail** (Сведения об источнике миграции) задайте сведения о подключении для исходного SQL Server.

2. Если на сервере не установлен доверенный сертификат, установите флажок **Доверять сертификату сервера**.

    Если доверенный сертификат не установлен, SQL Server создаст самозаверяющий сертификат при запуске экземпляра. Этот сертификат используется с целью шифрования учетных данных для клиентских подключений.

    > [!CAUTION]
    > SSL-соединения, шифруемые с помощью самозаверяющего сертификата, не обеспечивают надежной защиты. Они уязвимы для атак "злоумышленник в середине". В рабочей среде или на серверах, подключенных к Интернету, не следует применять самозаверяющие сертификаты для SSL.

   ![Сведения об источнике](media/how-to-migrate-ssis-packages-mi/dms-source-details1.png)

3. Щелкните **Сохранить**.

## <a name="specify-target-details"></a>Указание сведений о цели

1. На **сведения о целевом объекте миграции** укажите сведения о подключении для целевого объекта.

     ![Данные целевого объекта](media/how-to-migrate-ssis-packages-mi/dms-target-details2.png)

2. Щелкните **Сохранить**.

## <a name="review-the-migration-summary"></a>Просмотр сводки по миграции

1. На экране **Сводка по миграции** в текстовом поле **Имя активности** задайте имя действия миграции.

2. Для **проекты служб SSIS и окружений параметр перезаписи**, укажите, следует ли перезаписать или игнорировать существующих проектов служб SSIS и сред.

    ![Сводка по проекту миграции](media/how-to-migrate-ssis-packages-mi/dms-project-summary2.png)

3. Проверьте и подтвердите сведения, связанные с проектом миграции.

## <a name="run-the-migration"></a>Выполнение миграции

* Выберите **Запустить миграцию**.

## <a name="next-steps"></a>Дальнейшие действия

* Просмотрите [руководство по миграции базы данных Майкрософт](https://datamigration.microsoft.com/).
