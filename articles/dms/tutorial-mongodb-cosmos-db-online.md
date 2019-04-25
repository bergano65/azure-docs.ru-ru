---
title: Руководство по Использование службы Azure Database Migration Service для переноса баз данных MongoDB в API Azure Cosmos DB для MongoDB по сети | Документация Майкрософт
description: Узнайте, как выполнять перенос баз данных из локального экземпляра MongoDB в API Azure Cosmos DB для MongoDB по сети с помощью Azure Database Migration Service.
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
ms.openlocfilehash: 173343677d6c44135037978e1c5b60313251ba43
ms.sourcegitcommit: bf509e05e4b1dc5553b4483dfcc2221055fa80f2
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/22/2019
ms.locfileid: "60003842"
---
# <a name="tutorial-migrate-mongodb-to-azure-cosmos-dbs-api-for-mongodb-online-using-dms-preview"></a>Руководство по Перенос MongoDB в API Azure Cosmos DB для MongoDB по сети с помощью DMS (предварительная версия)
С помощью Azure Database Migration Service можно переносить базы данных по сети (с минимальным простоем) из локального или облачного экземпляра MongoDB в API Azure Cosmos DB для MongoDB.

Из этого руководства вы узнаете, как выполнять следующие задачи:
> [!div class="checklist"]
> * создание экземпляра Azure Database Migration Service;
> * создание проекта миграции с помощью Azure Database Migration Service;
> * выполнение миграции.
> * мониторинг миграции.
> * Когда будете готовы, выполните миграцию.

В этом руководстве выполняется перенос набора данных в MongoDB, размещенной в виртуальной машине Azure, в API Azure Cosmos DB для MongoDB с минимальным простоем благодаря использованию Azure Database Migration Service. Если у вас еще не настроен источник MongoDB, обратитесь к статье [Установка и настройка базы данных MongoDB на виртуальной машине Windows в Azure](https://docs.microsoft.com/azure/virtual-machines/windows/install-mongodb).

> [!NOTE]
> Чтобы выполнить подключенную миграцию с помощью Azure Database Migration Service, требуется создать экземпляр ценовой категории "Премиум".

> [!IMPORTANT]
> Чтобы оптимизировать процесс миграции, Майкрософт рекомендует создать экземпляр Azure Database Migration Service в том же регионе Azure, в котором размещена целевая база данных. Перемещение данных между регионами и географическими областями может замедлить миграцию.

[!INCLUDE [online-offline](../../includes/database-migration-service-offline-online.md)]

В этой статье описан перенос баз данных по сети из MongoDB в API Azure Cosmos DB для MongoDB. Чтобы узнать, как выполнить миграцию в автономном режиме, см. [руководство по миграции MongoDB в API Azure Cosmos DB для MongoDB в автономном режиме с помощью DMS](tutorial-mongodb-cosmos-db.md).

## <a name="prerequisites"></a>Предварительные требования
Для работы с этим руководством вам потребуется следующее:
- [Завершите шаги, которые необходимо выполнить перед миграцией](../cosmos-db/mongodb-pre-migration.md), включая оценку пропускной способности, а также выбор ключа секции и политики индексирования.
- [Создайте приложение API Azure Cosmos DB для учетной записи MongoDB](https://ms.portal.azure.com/#create/Microsoft.DocumentDB).
- Создайте виртуальную сеть Azure для Azure Database Migration Service с помощью модели развертывания Azure Resource Manager, которая обеспечивает подключение "сеть — сеть" к локальным исходным серверам с помощью [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) или [VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways).

    > [!NOTE]
    > Если вы используете ExpressRoute с пиринговой связью с сетью корпорации Майкрософт, во время настройки виртуальной сети добавьте в подсеть, в которой будет подготовлена служба, следующие [конечные точки службы](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview):
    > - целевую конечную точку базы данных (например, конечная точка SQL, конечная точка Cosmos DB и т. д.);
    > - конечную точку службы хранилища;
    > - конечную точку служебной шины.
    >
    > Эта настройка необходима, потому что у Azure Database Migration Service нет подключения к Интернету.

- Убедитесь, что правила группы безопасности сети для виртуальной сети не блокируют следующие входящие порты для Azure Database Migration Service: 443, 53, 9354, 445 и 12000. Дополнительные сведения о фильтрации трафика, предназначенного для виртуальной сети Azure, с помощью NSG см. в статье [Фильтрация сетевого трафика с помощью групп безопасности сети](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg).
- Измените правила брандмауэра исходного сервера, чтобы предоставить Azure Database Migration Service доступ к исходному серверу MongoDB. По умолчанию используется TCP-порт 27017.
- Если перед исходными базами данных развернуто устройство брандмауэра, вам может понадобиться добавить правила брандмауэра, чтобы позволить службе Azure Database Migration Service обращаться к исходным базам данных для выполнения миграции.

## <a name="register-the-microsoftdatamigration-resource-provider"></a>Регистрация поставщика ресурсов Microsoft.DataMigration
1. Войдите на портал Azure, щелкните **Все службы** и выберите **Подписки**.

   ![Отображение подписок на портале](media/tutorial-mongodb-to-cosmosdb-online/portal-select-subscription1.png)
       
2. Выберите подписку, в которой нужно создать экземпляр Azure Database Migration Service, а затем щелкните **Поставщики ресурсов**.
 
    ![Отображение поставщиков ресурсов](media/tutorial-mongodb-to-cosmosdb-online/portal-select-resource-provider.png)
    
3.  В поле поиска введите migration, а затем справа от **Microsoft.DataMigration** щелкните **Зарегистрировать**.
 
    ![Регистрация поставщика ресурсов](media/tutorial-mongodb-to-cosmosdb-online/portal-register-resource-provider.png)    

## <a name="create-an-instance"></a>Создание экземпляра
1.  На портале Azure выберите **+Создать ресурс**, введите в поле поиска "Azure Database Migration Service", а затем в раскрывающемся списке выберите **Azure Database Migration Service**.

    ![Azure Marketplace](media/tutorial-mongodb-to-cosmosdb-online/portal-marketplace.png)

2.  На экране **Azure Database Migration Service** выберите **Создать**.
 
    ![Создание экземпляра Azure Database Migration Service](media/tutorial-mongodb-to-cosmosdb-online/dms-create1.png)
  
3.  На экране **Создание службы миграции** укажите имя службы, подписку и новую или существующую группу ресурсов.

4. Выберите расположение, в котором нужно создать экземпляр Azure Database Migration Service. 

5. Выберите существующую виртуальную сеть или создайте новую.

    Виртуальная сеть предоставляет Azure Database Migration Service доступ к исходному экземпляру MongoDB и целевой учетной записи Azure Cosmos DB.

    Дополнительные сведения о создании виртуальной сети на портале Azure см. в статье [Создание виртуальной сети с помощью портала Azure](https://aka.ms/DMSVnet).

6. Выберите номер SKU ценовой категории "Премиум".

    > [!NOTE]
    > Миграция по сети поддерживается, только если используется уровень "Премиум". Дополнительные сведения о ценовых категориях и затратах см. на [странице с описанием цен](https://aka.ms/dms-pricing).

    Если вам нужна помощь в выборе подходящей категории Azure Database Migration Service, прочтите рекомендации в [этой записи блога](https://go.microsoft.com/fwlink/?linkid=861067).  

     ![Настройка параметров экземпляра Database Migration Service](media/tutorial-mongodb-to-cosmosdb-online/dms-settings3.png)

7.  Выберите **Создать**, чтобы создать службу.

## <a name="create-a-migration-project"></a>Создание проекта миграции
После создания службы найдите ее на портале Azure, откройте и создайте проект миграции.

1. На портале Azure щелкните **Все службы**, выполните поиск по запросу "Azure Database Migration Service" и выберите **Azure Database Migration Services** (Службы Azure Database Migration Service).
 
    ![Поиск всех экземпляров Azure Database Migration Service](media/tutorial-mongodb-to-cosmosdb-online/dms-search.png)

2. На экране **Azure Database Migration Services** найдите имя созданного экземпляра Azure Database Migration Service и выберите его.

    Вы также можете обнаружить экземпляр службы Azure Database Migration Service на панели поиска портала Azure.

    ![Использование панели поиска на портале Azure](media/tutorial-mongodb-to-cosmosdb-online/dms-search-portal.png)

3. Выберите **+ Новый проект миграции**.

4. На экране **New migration project** (Создать проект миграции) задайте имя проекта, в текстовом поле **Source server type** (Тип исходного сервера) выберите **MongoDB**, в текстовом поле **Target server type** (Тип целевого сервера) выберите **Cosmos DB (API MongoDB)**, а затем для параметра **Выберите тип действия** выберите значение **Online data migration [preview]** (Перенос данных по сети [предварительная версия]).

    ![Создание проекта Database Migration Service](media/tutorial-mongodb-to-cosmosdb-online/dms-create-project1.png)

5.  Выберите **Сохранить** и **Создать и выполнить действие**, чтобы создать проект и выполнить действие миграции.

## <a name="specify-source-details"></a>Указание сведений об источнике
1. На экране **Сведения об источнике** задайте сведения о подключении для исходного сервера MongoDB.

    Доступно три режима для подключения к источнику:
   * **Стандартный режим**, в котором принимается полное доменное имя или IP-адрес, номер порта и учетные данные для подключения.
   * **Режим строки подключения**, в котором принимается строка подключения MongoDB, как описано в статье о [формате URI строки подключения](https://docs.mongodb.com/manual/reference/connection-string/).
   * **Данные из службы хранилища Azure**, в котором принимается URL-адрес SAS контейнера BLOB-объектов. Выберите **BLOB-объект содержит дампы BSON**, если в контейнере BLOB-объектов есть дампы BSON, созданные [средством bsondump](https://docs.mongodb.com/manual/reference/program/bsondump/) MongoDB, и отмените выбор, если контейнер содержит файлы JSON.

     Если вы выбрали этот параметр, убедитесь, что строка подключения к учетной записи хранения выглядит следующим образом:

     ```
     https://blobnameurl/container?SASKEY
     ```
     Кроме того, учитывайте следующие сведения, соответствующие данным о типе дампа в хранилище Azure.

     * Для дампов BSON данные в контейнере больших двоичных объектов должны быть в формате bsondump так, чтобы файлы данных помещались в папках с именами содержащих баз данных в формате "коллекция.bson". Файлы метаданных (если таковые имеются) должны быть названы в формате *коллекция*.metadata.json.

     * Для дампов JSON файлы в контейнере больших двоичных объектов должны размещаться в папках с именами содержащих баз данных. В каждой папке баз данных файлы данных должны быть помещены в подпапку с именем "data" и названы в формате *коллекция*.json. Файлы метаданных (если таковые имеются) должны быть помещены в подпапку с именем "metadata" и названы в том же формате: *коллекция*.json. Файлы метаданных должны быть в том же формате, что и файлы, созданные инструментом bsondump MongoDB.

   Если разрешение DNS-имен невозможно, можно использовать IP-адрес.

   ![Указание сведений об источнике](media/tutorial-mongodb-to-cosmosdb-online/dms-specify-source1.png)

2. Щелкните **Сохранить**.

   > [!NOTE]
   > Адрес исходного сервера должен быть адресом основного сервера, если источником является набор реплик, и маршрутизатором, если источником является сегментированный кластер MongoDB. Если используется сегментированный кластер MongoDB, служба Azure Database Migration Service также должна иметь возможность подключения к отдельным сегментам в кластере. Для этого, возможно, потребуется открыть брандмауэр на нескольких компьютерах.          

## <a name="specify-target-details"></a>Указание сведений о цели
1. На экране **Migration target details** (Сведения о целевом объекте переноса) укажите сведения о подключении для целевой учетной записи Azure Cosmos DB. Это предварительно подготовленная учетная запись API Azure Cosmos DB для MongoDB, в которую нужно перенести данные MongoDB.

    ![Указание сведений о цели](media/tutorial-mongodb-to-cosmosdb-online/dms-specify-target1.png)

2. Щелкните **Сохранить**.

## <a name="map-to-target-databases"></a>Сопоставление с целевыми базами данных
1. На экране **Map to target databases** (Сопоставить с целевыми базами данных) сопоставьте исходную и целевую базы данных для миграции.

    Если в целевой базе данных содержится такое же имя базы данных, что и в исходной базе данных, Azure Database Migration Service выберет целевую базу данных по умолчанию.

    Если строка **Создать** отображается рядом с именем базы данных, это значит, что Azure Database Migration Service не удалось найти целевую базу данных, и служба автоматически создаст ее.

    На этом этапе переноса укажите пропускную способность в ЕЗ, если для базы данных необходимо использовать общую пропускную способность. В Cosmos DB пропускную способность можно подготовить на уровне базы данных или индивидуально для каждой коллекции. Пропускная способность измеряется в [единицах запроса](https://docs.microsoft.com/azure/cosmos-db/request-units) (ЕЗ). Дополнительные сведения о ценах Azure Cosmos DB см. [здесь](https://azure.microsoft.com/pricing/details/cosmos-db/).

    ![Сопоставление с целевыми базами данных](media/tutorial-mongodb-to-cosmosdb-online/dms-map-target-databases1.png)

2. Щелкните **Сохранить**.

3. На экране **Параметр коллекции** разверните список коллекций и просмотрите коллекции, которые будут перенесены.

    Обратите внимание, что Azure Database Migration Service автоматически выбирает все коллекции, которые существуют в экземпляре источника MongoDB и не существуют в целевой учетной записи Azure Cosmos DB. Чтобы повторно перенести коллекции, уже содержащие данные, необходимо явным образом выбрать их на этом экране.

    Вы можете указать количество ЕЗ, которое необходимо использовать коллекциям. Обычно достаточно использовать значение в диапазоне от 500 (не менее 1000 для сегментированных коллекций) до 4000. Azure Database Migration Service предлагает интеллектуальные значения по умолчанию на основании размера коллекции.

    Вы можете также указать ключ сегмента, чтобы воспользоваться преимуществами [секционирования в Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/partitioning-overview) для обеспечения наилучшей масштабируемости. Обязательно просмотрите [рекомендации по выбору ключа сегмента и секции](https://docs.microsoft.com/azure/cosmos-db/partitioning-overview#choose-partitionkey). Если у вас нет ключа секции, в качестве ключа сегмента вы всегда можете использовать **_id**, чтобы улучшить пропускную способность.

    ![Выбор таблиц коллекций](media/tutorial-mongodb-to-cosmosdb-online/dms-collection-setting1.png)

4. Щелкните **Сохранить**.

5. На экране **Сводка по миграции** в текстовом поле **Имя активности** задайте имя действия миграции.

    ![Сводка по миграции](media/tutorial-mongodb-to-cosmosdb-online/dms-migration-summary1.png)

## <a name="run-the-migration"></a>Выполнение миграции
- Выберите **Запустить миграцию**.

   Появится окно действия миграции, в котором будет указано **состояние** действия.

   ![Состояние действия](media/tutorial-mongodb-to-cosmosdb-online/dms-activity-status1.png)

## <a name="monitor-the-migration"></a>Мониторинг миграции
- На экране действия миграции нажимайте **Обновить**, чтобы периодически обновлять содержимое экрана, пока в поле **Состояние** не будет указано значение **Идет повторное воспроизведение**.

   > [!NOTE]
   > Вы можете выбрать действие, чтобы получить сведения о метриках миграции на уровне базы данных и на уровне коллекции.

   ![Повторное воспроизведение состояния действия](media/tutorial-mongodb-to-cosmosdb-online/dms-activity-replaying.png)

## <a name="verify-data-in-cosmos-db"></a>Проверка данных в Cosmos DB

1. Внесите изменения в исходную базу данных MongoDB.
2. Подключитесь к COSMOS DB, чтобы убедиться, что данные реплицированы с исходного сервера MongoDB.

    ![Повторное воспроизведение состояния действия](media/tutorial-mongodb-to-cosmosdb-online/dms-verify-data.png)
 
## <a name="complete-the-migration"></a>Выполнение миграции

* Когда все документы с исходного сервера станут доступны на целевом сервере COSMOS DB, выберите **Готово** в контекстном меню действия миграции, чтобы завершить ее.

    Это действие приведет к окончанию повторного воспроизведения всех ожидающих изменений и завершению миграции.

    ![Повторное воспроизведение состояния действия](media/tutorial-mongodb-to-cosmosdb-online/dms-finish-migration.png)

## <a name="post-migration-optimization"></a>Оптимизация после переноса

Для управления данными, перенесенными из базы данных MongoDB в API Azure Cosmos DB для MongoDB, можно подключиться к Azure Cosmos DB. После переноса можно также выполнить другие шаги, включая оптимизацию политики индексирования, обновление уровня согласованности по умолчанию и настройку глобального распределения для своей учетной записи Azure Cosmos DB. Узнайте больше об [оптимизации после переноса](../cosmos-db/mongodb-post-migration.md). 

## <a name="additional-resources"></a>Дополнительные ресурсы

 * [Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/)

## <a name="next-steps"></a>Дополнительная информация
- Просмотрите другие сценарии в [руководстве по миграции базы данных Майкрософт](https://datamigration.microsoft.com/).
