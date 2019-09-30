---
title: Руководство по Использование службы Azure Database Migration Service для переноса MongoDB в API Azure Cosmos DB для MongoDB в автономном режиме | Документация Майкрософт
description: Узнайте, как перенести локальный экземпляр MongoDB в API Azure Cosmos DB для MongoDB в автономном режиме с помощью Azure Database Migration Service.
services: dms
author: HJToland3
ms.author: jtoland
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: mvc, tutorial
ms.topic: article
ms.date: 09/25/2019
ms.openlocfilehash: 96540a8ea40efcc3a2d115980999c8d470b85180
ms.sourcegitcommit: 3f22ae300425fb30be47992c7e46f0abc2e68478
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/25/2019
ms.locfileid: "71265948"
---
# <a name="tutorial-migrate-mongodb-to-azure-cosmos-dbs-api-for-mongodb-offline-using-dms"></a>Руководство по Миграция MongoDB в API Azure Cosmos DB для MongoDB в автономном режиме с помощью DMS

С помощью Azure Database Migration Service можно выполнять автономную (однократную) миграцию баз данных из локального или облачного экземпляра MongoDB в API Azure Cosmos DB для MongoDB.

Из этого руководства вы узнаете, как выполнять следующие задачи:
> [!div class="checklist"]
>
> * создание экземпляра Azure Database Migration Service;
> * создание проекта миграции с использованием Azure Database Migration Service.
> * выполнение миграции.
> * мониторинг миграции.

В этом руководстве показано, как перенести набор данных из базы данных MongoDB, размещенной на виртуальной машине Azure, в API Azure Cosmos DB для MongoDB с помощью Azure Database Migration Service. Если у вас еще не настроен источник MongoDB, обратитесь к статье [Установка и настройка базы данных MongoDB на виртуальной машине Windows в Azure](https://docs.microsoft.com/azure/virtual-machines/windows/install-mongodb).

## <a name="prerequisites"></a>Предварительные требования

Для работы с этим руководством вам потребуется следующее:

* [Завершите шаги, которые необходимо выполнить перед миграцией](../cosmos-db/mongodb-pre-migration.md), включая оценку пропускной способности, а также выбор ключа секции и политики индексирования.
* [Создайте приложение API Azure Cosmos DB для учетной записи MongoDB](https://ms.portal.azure.com/#create/Microsoft.DocumentDB).
* Создайте виртуальную сеть Azure для Azure Database Migration Service, используя модель развертывания с помощью Azure Resource Manager, которая обеспечивает подключение "сеть — сеть" к локальным исходным серверам с помощью [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) или [VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways). Дополнительные сведения о создании виртуальной сети приведены в [документации по виртуальным сетям](https://docs.microsoft.com/azure/virtual-network/). В частности, уделите внимание кратким руководствам с пошаговыми инструкциями.

    > [!NOTE]
    > Если вы используете ExpressRoute с пиринговой связью с сетью корпорации Майкрософт, во время настройки виртуальной сети добавьте в подсеть, в которой будет подготовлена служба, следующие [конечные точки](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview):
    >
    > * целевую конечную точку базы данных (например, конечная точка SQL, конечная точка Cosmos DB и т. д.);
    > * конечную точку службы хранилища;
    > * конечную точку служебной шины.
    >
    > Такая конфигурация вызвана тем, что у Azure Database Migration Service нет подключения к Интернету.

* Убедитесь, что правила группы безопасности сети для виртуальной сети не блокируют следующие порты: 53, 443, 445, 9354 и 10000–20000. Дополнительные сведения о фильтрации трафика, предназначенного для виртуальной сети Azure, с помощью NSG см. в статье [Plan virtual networks](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg) (Планирование виртуальных сетей).
* Откройте брандмауэр Windows, чтобы предоставить Azure Database Migration Service доступ к исходному серверу MongoDB. По умолчанию это TCP-порт 27017.
* Если перед исходными базами данных развернуто устройство брандмауэра, вам может понадобиться добавить правила брандмауэра, чтобы позволить службе Azure Database Migration Service обращаться к исходным базам данных для выполнения миграции.

## <a name="register-the-microsoftdatamigration-resource-provider"></a>Регистрация поставщика ресурсов Microsoft.DataMigration

1. Войдите на портал Azure, щелкните **Все службы** и выберите **Подписки**.

   ![Отображение подписок на портале](media/tutorial-mongodb-to-cosmosdb/portal-select-subscription1.png)

2. Выберите подписку, в которой нужно создать экземпляр Azure Database Migration Service, а затем щелкните **Поставщики ресурсов**.

    ![Отображение поставщиков ресурсов](media/tutorial-mongodb-to-cosmosdb/portal-select-resource-provider.png)

3. В поле поиска введите migration, а затем справа от **Microsoft.DataMigration** щелкните **Зарегистрировать**.

    ![Регистрация поставщика ресурсов](media/tutorial-mongodb-to-cosmosdb/portal-register-resource-provider.png)    

## <a name="create-an-instance"></a>Создание экземпляра

1. На портале Azure выберите **+Создать ресурс**, введите в поле поиска "Azure Database Migration Service", а затем в раскрывающемся списке выберите **Azure Database Migration Service**.

    ![Azure Marketplace](media/tutorial-mongodb-to-cosmosdb/portal-marketplace.png)

2. На экране **Azure Database Migration Service** выберите **Создать**.

    ![Создание экземпляра Azure Database Migration Service](media/tutorial-mongodb-to-cosmosdb/dms-create1.png)
  
3. На экране **Создание службы миграции** укажите имя службы, подписку и новую или существующую группу ресурсов.

4. Выберите расположение, в котором нужно создать экземпляр Azure Database Migration Service. 

5. Создайте виртуальную сеть или выберите имеющуюся.

    Виртуальная сеть предоставляет Azure Database Migration Service доступ к исходному экземпляру MongoDB и целевой учетной записи Azure Cosmos DB.

    Дополнительные сведения о создании виртуальной сети на портале Azure см. в статье [Краткое руководство. Создание виртуальной сети с помощью портала Azure](https://aka.ms/DMSVnet).

6. Выберите ценовую категорию.

    Дополнительные сведения о ценовых категориях и затратах см. на [странице с описанием цен](https://aka.ms/dms-pricing).

    ![Настройка параметров экземпляра Database Migration Service](media/tutorial-mongodb-to-cosmosdb/dms-settings2.png)

7. Выберите **Создать**, чтобы создать службу.

## <a name="create-a-migration-project"></a>Создание проекта миграции

После создания службы найдите ее на портале Azure, откройте и создайте проект миграции.

1. На портале Azure щелкните **Все службы**, выполните поиск по запросу "Azure Database Migration Service" и выберите **Azure Database Migration Services** (Службы Azure Database Migration Service).

      ![Поиск всех экземпляров Azure Database Migration Service](media/tutorial-mongodb-to-cosmosdb/dms-search.png)

2. На экране **Службы миграции баз данных Azure** найдите и выберите имя созданного экземпляра Azure Database Migration Service.

3. Выберите **+ Новый проект миграции**.

4. На экране **Новый проект миграции** задайте имя для проекта, в текстовом поле **Тип исходного сервера** выберите **MongoDB**, в текстовом поле **Тип целевого сервера** выберите **CosmosDB (MongoDB API)** , а затем для параметра **Выберите тип действия** выберите значение **Автономная миграция данных**. 

    ![Создание проекта Database Migration Service](media/tutorial-mongodb-to-cosmosdb/dms-create-project.png)

5. Выберите **Создать и выполнить действие**, чтобы создать проект и выполнить действие миграции.

## <a name="specify-source-details"></a>Указание сведений об источнике

1. На экране **Сведения об источнике** задайте сведения о подключении для исходного сервера MongoDB.

   > [!IMPORTANT]
   > Azure Database Migration Service не поддерживает Azure Cosmos DB в качестве источника.

    Доступно три режима для подключения к источнику:
   * **Стандартный режим**, в котором принимается полное доменное имя или IP-адрес, номер порта и учетные данные для подключения.
   * **Режим строки подключения**, в котором принимается строка подключения MongoDB, как описано в статье о [формате URI строки подключения](https://docs.mongodb.com/manual/reference/connection-string/).
   * **Данные из службы хранилища Azure**, в котором принимается URL-адрес SAS контейнера BLOB-объектов. Выберите **BLOB-объект содержит дампы BSON**, если в контейнере BLOB-объектов есть дампы BSON, созданные [средством bsondump](https://docs.mongodb.com/manual/reference/program/bsondump/) MongoDB, и отмените выбор, если контейнер содержит файлы JSON.

     Если вы выбрали этот параметр, убедитесь, что строка подключения к учетной записи хранения выглядит следующим образом:

     ```
     https://blobnameurl/container?SASKEY
     ```

     Эту строку подключения SAS к контейнеру BLOB-объектов можно найти в обозревателе хранилища Azure Storage. Создание SAS для нужного контейнера позволит вам получить URL-адрес в запрошенном выше формате.
     
     Кроме того, учитывайте следующие сведения, соответствующие данным о типе дампа в хранилище Azure.

     * Для дампов BSON данные в контейнере больших двоичных объектов должны быть в формате bsondump так, чтобы файлы данных помещались в папках с именами содержащих баз данных в формате "коллекция.bson". Файлы метаданных (если таковые имеются) должны быть названы в формате *коллекция*.metadata.json.

     * Для дампов JSON файлы в контейнере больших двоичных объектов должны размещаться в папках с именами содержащих баз данных. В каждой папке баз данных файлы данных должны быть помещены в подпапку с именем "data" и названы в формате *коллекция*.json. Файлы метаданных (если таковые имеются) должны быть помещены в подпапку с именем "metadata" и названы в том же формате: *коллекция*.json. Файлы метаданных должны быть в том же формате, что и файлы, созданные инструментом bsondump MongoDB.

    > [!IMPORTANT]
    > Не рекомендуется использовать самозаверяющий сертификат на сервере Mongo. Но если он все же применяется, подключитесь к серверу в **режиме строки подключения** и убедитесь, что в строке подключения используются двойные кавычки.
    >
    >```
    >&sslVerifyCertificate=false
    >```

   Если разрешение DNS-имен невозможно, можно использовать IP-адрес.

   ![Указание сведений об источнике](media/tutorial-mongodb-to-cosmosdb/dms-specify-source.png)

2. Щелкните **Сохранить**.

## <a name="specify-target-details"></a>Указание сведений о цели

1. На экране **Migration target details** (Сведения о целевом объекте переноса) укажите сведения о подключении для целевой учетной записи Azure Cosmos DB. Это предварительно подготовленная учетная запись API Azure Cosmos DB для MongoDB, в которую нужно перенести данные MongoDB.

    ![Указание сведений о цели](media/tutorial-mongodb-to-cosmosdb/dms-specify-target.png)

2. Щелкните **Сохранить**.

## <a name="map-to-target-databases"></a>Сопоставление с целевыми базами данных

1. На экране **Map to target databases** (Сопоставить с целевыми базами данных) сопоставьте исходную и целевую базы данных для миграции.

    Если в целевой базе данных содержится база данных с тем же именем, что у исходной базы данных, Azure Database Migration Service по умолчанию выберет целевую базу данных.

    Если строка **Создать** отображается рядом с именем базы данных, это значит, что Azure Database Migration Service не удалось найти целевую базу данных, и служба автоматически создаст ее.

    На этом этапе миграции можно [подготовить пропускную способность](https://docs.microsoft.com/azure/cosmos-db/set-throughput). В Cosmos DB пропускную способность можно подготовить на уровне базы данных или индивидуально для каждой коллекции. Пропускная способность измеряется в [единицах запроса](https://docs.microsoft.com/azure/cosmos-db/request-units) (ЕЗ). Дополнительные сведения о ценах Azure Cosmos DB см. [здесь](https://azure.microsoft.com/pricing/details/cosmos-db/).

    ![Сопоставление с целевыми базами данных](media/tutorial-mongodb-to-cosmosdb/dms-map-target-databases.png)

2. Щелкните **Сохранить**.
3. На экране **Параметр коллекции** разверните список коллекций и просмотрите коллекции, которые будут перенесены.

    Azure Database Migration Service автоматически выбирает все коллекции, которые существуют в экземпляре источника MongoDB и не существуют в целевой учетной записи Azure Cosmos DB. Чтобы повторно перенести коллекции, уже содержащие данные, необходимо явным образом выбрать их в этой колонке.

    Вы можете указать количество ЕЗ, которое необходимо использовать коллекциям. Azure Database Migration Service предлагает интеллектуальные значения по умолчанию с учетом размера коллекции.

    > [!NOTE]
    > При необходимости выполните перенос базы данных и коллекции в параллельном режиме с помощью нескольких экземпляров Azure Database Migration Service, чтобы ускорить процесс.

    Вы можете также указать ключ сегмента, чтобы воспользоваться преимуществами [секционирования в Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/partitioning-overview) для обеспечения наилучшей масштабируемости. Обязательно просмотрите [рекомендации по выбору ключа сегмента и секции](https://docs.microsoft.com/azure/cosmos-db/partitioning-overview#choose-partitionkey).

    ![Выбор таблиц коллекций](media/tutorial-mongodb-to-cosmosdb/dms-collection-setting.png)

4. Щелкните **Сохранить**.

5. На экране **Сводка по миграции** в текстовом поле **Имя активности** задайте имя действия миграции.

    ![Сводка по миграции](media/tutorial-mongodb-to-cosmosdb/dms-migration-summary.png)

## <a name="run-the-migration"></a>Выполнение миграции

* Выберите **Запустить миграцию**.

    Появится окно действия миграции, в котором будет указано **состояние** действия **Не запущено**.

    ![Состояние действия](media/tutorial-mongodb-to-cosmosdb/dms-activity-status.png)

## <a name="monitor-the-migration"></a>Мониторинг миграции

* На экране действия миграции нажимайте кнопку **Обновить**, чтобы обновить содержимое экрана, пока **состояние** миграции не поменяется на **Завершено**.

   > [!NOTE]
   > Вы можете выбрать действие, чтобы получить сведения о метриках миграции на уровне базы данных и на уровне коллекции.

    ![Состояние действия: завершено](media/tutorial-mongodb-to-cosmosdb/dms-activity-completed.png)

## <a name="verify-data-in-cosmos-db"></a>Проверка данных в Cosmos DB

* После завершения миграции вы можете проверить свою учетную запись Azure Cosmos DB, чтобы убедиться, что все коллекции успешно перенесены.

    ![Состояние действия: завершено](media/tutorial-mongodb-to-cosmosdb/dms-cosmosdb-data-explorer.png)

## <a name="post-migration-optimization"></a>Оптимизация после миграции

Для управления данными, перенесенными из базы данных MongoDB в API Azure Cosmos DB для MongoDB, можно подключиться к Azure Cosmos DB. После переноса можно также выполнить другие действия, включая оптимизацию политики индексирования, обновление уровня согласованности по умолчанию и настройку глобального распределения для своей учетной записи Azure Cosmos DB. См. подробнее об [оптимизации после переноса](../cosmos-db/mongodb-post-migration.md).

## <a name="additional-resources"></a>Дополнительные ресурсы

* [Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/)

## <a name="next-steps"></a>Дополнительная информация

* Просмотрите другие сценарии в [руководстве по миграции базы данных Майкрософт](https://datamigration.microsoft.com/).
