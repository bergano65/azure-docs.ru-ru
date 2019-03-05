---
title: Руководство. Использование службы Azure Database Migration Service для миграции MongoDB в API Azure Cosmos DB для MongoDB в автономном режиме | Документация Майкрософт
description: Узнайте, как выполнять миграцию из локальной MongoDB в API Azure Cosmos DB для MongoDB в автономном режиме с помощью Azure Database Migration Service.
services: dms
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: douglasl
ms.service: dms
ms.workload: data-services
ms.custom: mvc, tutorial
ms.topic: article
ms.date: 12/11/2018
ms.openlocfilehash: 5fd3200ab787a26b11feb121b5db125e4a79365c
ms.sourcegitcommit: fdd6a2927976f99137bb0fcd571975ff42b2cac0
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/27/2019
ms.locfileid: "56960391"
---
# <a name="tutorial-migrate-mongodb-to-azure-cosmos-dbs-api-for-mongodb-offline-using-dms"></a>Руководство. Миграция MongoDB в API Azure Cosmos DB для MongoDB в автономном режиме с помощью DMS
С помощью Azure Database Migration Service можно выполнять автономную (однократную) миграцию баз данных из локального или облачного экземпляра MongoDB в API Azure Cosmos DB для MongoDB.

Из этого руководства вы узнаете, как выполнять следующие задачи:
> [!div class="checklist"]
> * создание экземпляра Azure Database Migration Service;
> * создание проекта миграции с помощью Azure Database Migration Service;
> * выполнение миграции.
> * мониторинг миграции.

В этом руководстве выполняется миграция набора данных в MongoDB, размещенной в виртуальной машине Azure, в API Azure Cosmos DB для MongoDB с помощью Azure Database Migration Service. Если у вас еще не настроен источник MongoDB, обратитесь к статье [Установка и настройка базы данных MongoDB на виртуальной машине Windows в Azure](https://docs.microsoft.com/azure/virtual-machines/windows/install-mongodb).

## <a name="prerequisites"></a>Предварительные требования
Для работы с этим руководством вам потребуется следующее:
- [Создайте приложение API Azure Cosmos DB для учетной записи MongoDB](https://ms.portal.azure.com/#create/Microsoft.DocumentDB).
- Создайте виртуальную сеть для Azure Database Migration Service с помощью модели развертывания Azure Resource Manager, которая обеспечивает подключение "сеть — сеть" к локальным исходным серверам с помощью [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) или [VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways).
- Убедитесь, что правила группы безопасности сети для виртуальной сети Azure не блокируют порты: 443, 53, 9354, 445 и 12000. Дополнительные сведения о фильтрации трафика, предназначенного для виртуальной сети Azure, с помощью NSG см. в статье [Фильтрация сетевого трафика с помощью групп безопасности сети](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg).
- Откройте брандмауэр Windows, чтобы предоставить Azure Database Migration Service доступ к исходному серверу MongoDB. По умолчанию это TCP-порт 27017.
- Если перед исходными базами данных развернуто устройство брандмауэра, вам может понадобиться добавить правила брандмауэра, чтобы позволить службе Azure Database Migration Service обращаться к исходным базам данных для выполнения миграции.

## <a name="register-the-microsoftdatamigration-resource-provider"></a>Регистрация поставщика ресурсов Microsoft.DataMigration
1. Войдите на портал Azure, щелкните **Все службы** и выберите **Подписки**.

   ![Отображение подписок на портале](media/tutorial-mongodb-to-cosmosdb/portal-select-subscription1.png)
       
2. Выберите подписку, в которой нужно создать экземпляр Azure Database Migration Service, а затем щелкните **Поставщики ресурсов**.
 
    ![Отображение поставщиков ресурсов](media/tutorial-mongodb-to-cosmosdb/portal-select-resource-provider.png)
    
3.  В поле поиска введите migration, а затем справа от **Microsoft.DataMigration** щелкните **Зарегистрировать**.
 
    ![Регистрация поставщика ресурсов](media/tutorial-mongodb-to-cosmosdb/portal-register-resource-provider.png)    

## <a name="create-an-instance"></a>Создание экземпляра
1.  На портале Azure выберите **+Создать ресурс**, введите в поле поиска "Azure Database Migration Service", а затем в раскрывающемся списке выберите **Azure Database Migration Service**.

    ![Azure Marketplace](media/tutorial-mongodb-to-cosmosdb/portal-marketplace.png)

2.  На экране **Azure Database Migration Service** выберите **Создать**.
 
    ![Создание экземпляра Azure Database Migration Service](media/tutorial-mongodb-to-cosmosdb/dms-create1.png)
  
3.  На экране **Создание службы миграции** укажите имя службы, подписку и новую или существующую группу ресурсов.

4. Выберите расположение, в котором нужно создать экземпляр Azure Database Migration Service. 

5. Выберите существующую виртуальную сеть или создайте новую.

    Виртуальная сеть предоставляет Azure Database Migration Service доступ к исходному экземпляру MongoDB и целевой учетной записи Azure Cosmos DB.

    Дополнительные сведения о создании виртуальной сети на портале Azure см. в статье [Создание виртуальной сети с помощью портала Azure](https://aka.ms/DMSVnet).

6. Выберите ценовую категорию.

    Дополнительные сведения о ценовых категориях и затратах см. на [странице с описанием цен](https://aka.ms/dms-pricing).

    Если вам нужна помощь в выборе подходящей категории Azure Database Migration Service, прочтите рекомендации в [этой записи блога](https://go.microsoft.com/fwlink/?linkid=861067).  

     ![Настройка параметров экземпляра Database Migration Service](media/tutorial-mongodb-to-cosmosdb/dms-settings2.png)

7.  Выберите **Создать**, чтобы создать службу.

## <a name="create-a-migration-project"></a>Создание проекта миграции
После создания службы найдите ее на портале Azure, откройте и создайте проект миграции.

1. На портале Azure щелкните **Все службы**, выполните поиск по запросу "Azure Database Migration Service" и выберите **Azure Database Migration Services** (Службы Azure Database Migration Service).
 
      ![Поиск всех экземпляров Azure Database Migration Service](media/tutorial-mongodb-to-cosmosdb/dms-search.png)

2. На экране **Azure Database Migration Services** найдите имя созданного экземпляра Azure Database Migration Service и выберите его.

3. Выберите **+ Новый проект миграции**.

4. На экране **Новый проект миграции** задайте имя для проекта, в текстовом поле **Тип исходного сервера** выберите **MongoDB**, в текстовом поле **Тип целевого сервера** выберите **CosmosDB (MongoDB API)**, а затем для параметра **Выберите тип действия** выберите значение **Автономная миграция данных**. 

    ![Создание проекта Database Migration Service](media/tutorial-mongodb-to-cosmosdb/dms-create-project.png)

5.  Выберите **Создать и выполнить действие**, чтобы создать проект и выполнить действие миграции.

## <a name="specify-source-details"></a>Указание сведений об источнике
1. На экране **Сведения об источнике** задайте сведения о подключении для исходного сервера MongoDB.
    
   Вы можете также использовать режим строки подключения и указать расположение для контейнера файлов хранилища больших двоичных объектов, где вы создали резервную копию данных коллекции, которую требуется перенести.

   > [!NOTE]
   > Azure Database Migration Service может перенести документы BSON или JSON в коллекции API Azure Cosmos DB для MongoDB.
    
   Если разрешение DNS-имен невозможно, можно использовать IP-адрес.

   ![Указание сведений об источнике](media/tutorial-mongodb-to-cosmosdb/dms-specify-source.png)

2. Щелкните **Сохранить**.

## <a name="specify-target-details"></a>Указание сведений о цели
1. На экране **Migration target details** (Сведения о целевом объекте переноса) укажите сведения о подключении для целевой учетной записи Azure Cosmos DB. Это предварительно подготовленная учетная запись API Azure Cosmos DB для MongoDB, в которую нужно перенести данные MongoDB.

    ![Указание сведений о цели](media/tutorial-mongodb-to-cosmosdb/dms-specify-target.png)

2. Щелкните **Сохранить**.

## <a name="map-to-target-databases"></a>Сопоставление с целевыми базами данных
1. На экране **Map to target databases** (Сопоставить с целевыми базами данных) сопоставьте исходную и целевую базы данных для миграции.

    Если в целевой базе данных содержится такое же имя базы данных, что и в исходной базе данных, Azure Database Migration Service выберет целевую базу данных по умолчанию.

    Если строка **Создать** отображается рядом с именем базы данных, это значит, что Azure Database Migration Service не удалось найти целевую базу данных, и служба автоматически создаст ее.

    На этом этапе миграции можно [подготовить пропускную способность](https://docs.microsoft.com/azure/cosmos-db/set-throughput). В Cosmos DB пропускную способность можно подготовить на уровне базы данных или индивидуально для каждой коллекции. Пропускная способность измеряется в [единицах запроса](https://docs.microsoft.com/azure/cosmos-db/request-units) (ЕЗ). Дополнительные сведения о ценах Azure Cosmos DB см. [здесь](https://azure.microsoft.com/pricing/details/cosmos-db/).

    ![Сопоставление с целевыми базами данных](media/tutorial-mongodb-to-cosmosdb/dms-map-target-databases.png)

2. Щелкните **Сохранить**.
3. На экране **Параметр коллекции** разверните список коллекций и просмотрите коллекции, которые будут перенесены.

    Обратите внимание, что Azure Database Migration Service автоматически выбирает все коллекции, которые существуют в экземпляре источника MongoDB и не существуют в целевой учетной записи Azure Cosmos DB. Чтобы повторно перенести коллекции, уже содержащие данные, необходимо явным образом выбрать их в этой колонке.

    Вы можете указать количество ЕЗ, которое необходимо использовать коллекциям. Azure Database Migration Service предлагает интеллектуальные значения по умолчанию на основании размера коллекции.

    Вы можете также указать ключ сегмента, чтобы воспользоваться преимуществами [секционирования в Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/partitioning-overview) для обеспечения наилучшей масштабируемости. Обязательно просмотрите [рекомендации по выбору ключа сегмента и секции](https://docs.microsoft.com/azure/cosmos-db/partitioning-overview#choose-partitionkey).

    ![Выбор таблиц коллекций](media/tutorial-mongodb-to-cosmosdb/dms-collection-setting.png)

4. Щелкните **Сохранить**.

5. На экране **Сводка по миграции** в текстовом поле **Имя активности** задайте имя действия миграции.

    ![Сводка по миграции](media/tutorial-mongodb-to-cosmosdb/dms-migration-summary.png)

## <a name="run-the-migration"></a>Выполнение миграции
- Выберите **Запустить миграцию**.

    Появится окно действия миграции, в котором будет указано **состояние** действия **Не запущено**.

    ![Состояние действия](media/tutorial-mongodb-to-cosmosdb/dms-activity-status.png)

## <a name="monitor-the-migration"></a>Мониторинг миграции
- На экране действия миграции нажимайте кнопку **Обновить**, чтобы обновить содержимое экрана, пока **состояние** миграции не поменяется на **Завершено**.

   > [!NOTE]
   > Вы можете выбрать действие, чтобы получить сведения о метриках миграции на уровне базы данных и на уровне коллекции.

    ![Состояние действия: завершено](media/tutorial-mongodb-to-cosmosdb/dms-activity-completed.png)

## <a name="verify-data-in-cosmos-db"></a>Проверка данных в Cosmos DB

- После завершения миграции вы можете проверить свою учетную запись Azure Cosmos DB, чтобы убедиться, что все коллекции успешно перенесены.

    ![Состояние действия: завершено](media/tutorial-mongodb-to-cosmosdb/dms-cosmosdb-data-explorer.png)

## <a name="additional-resources"></a>Дополнительные ресурсы

 * [Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/)

## <a name="next-steps"></a>Дополнительная информация
- Просмотрите другие сценарии в [руководстве по миграции базы данных Майкрософт](https://datamigration.microsoft.com/).
