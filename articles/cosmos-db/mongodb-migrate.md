---
title: Перенос данных MongoDB на Azure Cosmos DB с помощью mongoimport и mongorestore
description: Вы узнаете, как использовать mongoimport и mongorestore для импорта данных в Cosmos DB.
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: tutorial
ms.date: 12/26/2018
author: sivethe
ms.author: sivethe
Customer intent: As a developer, I want to migrate the data from my existing MongoDB to Cosmos DB.
ms.openlocfilehash: 9226a49af67659ebd7bebd9beca397830ee808bb
ms.sourcegitcommit: 8330a262abaddaafd4acb04016b68486fba5835b
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/04/2019
ms.locfileid: "54039056"
---
# <a name="migrate-your-mongodb-data-to-azure-cosmos-db"></a>Перенос данных MongoDB в Azure Cosmos DB

 Это руководство содержит инструкции по переносу данных из MongoDB в службу Azure Cosmos DB, настроенную для использования API Cosmos DB для MongoDB. Если вы импортируете данные из MongoDB и планируете использовать их с API SQL для Azure Cosmos DB, это можно сделать с помощью [средства переноса данных](import-data.md).

Изучив данный учебник, вы научитесь:

> [!div class="checklist"]
> * составлять план переноса;
> * переносить данные с помощью mongoimport;
> * переносить данные с помощью mongorestore.

Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/), прежде чем начинать работу.

## <a name="prerequisites"></a>Предварительные требования

Просмотрите и выполните приведенные ниже предварительные требования перед запуском переноса.

### <a name="plan-for-the-migration"></a>Планирование переноса

В этом разделе описывается планирование переноса данных. Ми оценим затраты в единицах запросов, определим задержку между компьютером и облачной службой, вычислим размер пакета и количество рабочих ролей вставки.


#### <a name="pre-create-and-scale-your-collections"></a>Создание своих коллекций заранее и их масштабирование

Прежде чем выполнять миграцию с помощью mongoimport или mongorestore, создайте все свои коллекции с помощью [портала Azure](https://portal.azure.com) или драйверов и инструментов MongoDB. 

Увеличьте пропускную способность коллекций для миграции на [портале Azure](https://portal.azure.com). Более высокая пропускная способность позволяет избежать ограничения скорости и выполнить перенос быстрее. Чтобы снизить затраты, вы можете снизить пропускную способность сразу после переноса.

В дополнение к подготовке пропускной способности на уровне коллекции вы можете подготавливать общую пропускную способность на уровне базы данных для набора коллекций. Необходимо предварительно создать базу данных и коллекции, а также определить ключ сегмента для каждой коллекции в базе данных с общей пропускной способностью.

Вы можете создать сегментированные коллекции с помощью предпочтительного средства, драйвера или пакета SDK. В этом примере для создания сегментированной коллекции используется оболочка Mongo:

```bash
db.runCommand( { shardCollection: "admin.people", key: { region: "hashed" } } )
```
    
Эта команда возвращает следующие результаты:

```JSON
{
    "_t" : "ShardCollectionResponse",
    "ok" : 1,
    "collectionsharded" : "admin.people"
}
```

#### <a name="calculate-the-approximate-ru-charge-for-a-single-document-write"></a>Вычисление приблизительной стоимости ЕЗ при записи одного документа

Из оболочки MongoDB подключитесь к учетной записи Cosmos, настроенной на использование API Cosmos DB для MongoDB. Инструкции доступны в статье [Подключение приложения MongoDB к Azure Cosmos DB](connect-mongodb-account.md).

Затем выполните пример команды insert, используя один из примеров документов:
   
```bash
db.coll.insert({ "playerId": "a067ff", "hashedid": "bb0091", "countryCode": "hk" })
```
        
Выполните команду `db.runCommand({getLastRequestStatistics: 1})`.

Вы получите примерно такие выходные данные:
     
```bash
globaldb:PRIMARY> db.runCommand({getLastRequestStatistics: 1})
{
    "_t": "GetRequestStatisticsResponse",
    "ok": 1,
    "CommandName": "insert",
    "RequestCharge": 10,
    "RequestDurationInMilliSeconds": NumberLong(50)
}
```
        
Запишите значение RequestCharge (стоимость запроса).
    
#### <a name="determine-the-latency-from-your-machine-to-cosmos-db"></a>Определение задержки между своим компьютером и Cosmos DB
    
Включите ведение подробного журнала из оболочки MongoDB с помощью команды `setVerboseShell(true)`.
    
Выполните простой запрос к базе данных с помощью команды `db.coll.find().limit(1)`.

Вы получите примерно такие выходные данные:

```bash
Fetched 1 record(s) in 100(ms)
```
        
Перед выполнением переноса удалите вставленный документ, чтобы убедиться в отсутствии повторяющихся документов. Документы можно удалить с помощью команды `db.coll.remove({})`.

#### <a name="calculate-the-approximate-values-for-the-batchsize-and-numinsertionworkers-properties"></a>Вычисление приблизительных значений для свойств batchSize и numInsertionWorkers

Для свойства **batchSize** разделите общую подготовленную пропускную способность (ЕЗ/с) на единицы запросов, потребляемые для записи одного документа, как показано в разделе "Определение задержки между своим компьютером и Cosmos DB". Если вычисленное значение меньше или равно 24, используйте это число в качестве значения свойства. Если вычисленное значение больше 24, задайте значение свойства 24.
    
Для определения значения свойства **numInsertionWorkers** используйте следующую формулу:

`numInsertionWorkers = (Provisioned RUs throughput * Latency in seconds) / (batchSize * Consumed RUs for a single write)`

Мы можем использовать следующие значения для вычисления значения свойства **numInsertionWorkers**:

| Свойство | Значение |
|--------|-----|
| **batchSize** | 24 |
| Подготовленные единицы запросов | 10 000 |
| Latency | 0,100 с |
| Потребленные единицы запросов | 10 ЕЗ |
| **numInsertionWorkers** | (10 000 ЕЗ x 0,100 с) / (24 x 10 ЕЗ) = **4,1666** |

Выполните команду миграции **monogoimport**. Параметры команды описаны далее в этой статье.

```bash
mongoimport.exe --host cosmosdb-mongodb-account.documents.azure.com:10255 -u cosmosdb-mongodb-account -p <Your_MongoDB_password> --ssl --sslAllowInvalidCertificates --jsonArray --db dabasename --collection collectionName --file "C:\sample.json" --numInsertionWorkers 4 --batchSize 24
```

Вы также можете использовать команду **monogorestore**. Убедитесь, что для всех коллекций установлена пропускная способность, которая равна числу ЕЗ, используемых в предыдущих вычислениях, или превышает его.
   
```bash
mongorestore.exe --host cosmosdb-mongodb-account.documents.azure.com:10255 -u cosmosdb-mongodb-account -p <Your_MongoDB_password> --ssl --sslAllowInvalidCertificates ./dumps/dump-2016-12-07 --numInsertionWorkersPerCollection 4 --batchSize 24
```

### <a name="complete-the-prerequisites"></a>Выполнение предварительных требований

После планирования миграции выполните указанные ниже действия: 

* **Получение примера данных**. Убедитесь, что у вас есть несколько примеров данных перед началом миграции. 

* **Увеличение пропускной способности**. Продолжительность переноса данных зависит от пропускной способности, подготовленной для отдельной коллекции или базы данных. Увеличьте пропускную способность для крупных миграций. После переноса уменьшите пропускную способность для экономии расходов. 

* **Включение SSL.**  В Cosmos DB строгие требования к безопасности и стандарты. Обязательно включите SSL при взаимодействии с учетной записью Cosmos. Процедуры, описанные в оставшейся части статьи, включают инструкции по включению SSL для команд mongoimport и mongorestore.

* **Создание ресурсов Cosmos DB.** Перед началом переноса данных заранее создайте коллекции на портале Azure. Для перехода на учетную запись Cosmos с пропускной способностью, подготовленной на уровне базы данных, при создании коллекций укажите ключ раздела.

* **Получение строки подключения.** На [портале Azure](https://portal.azure.com) щелкните запись **Azure Cosmos DB** слева. В разделе **Подписки** выберите имя своей учетной записи. В разделе **Строка подключения** выберите **Строка подключения**. В правой части портала отображаются сведения, необходимые для подключения к учетной записи:

    ![Сведения о строке подключения](./media/mongodb-migrate/ConnectionStringBlade.png)

## <a name="use-mongoimport"></a>Использование mongoimport

Чтобы импортировать данные в учетную запись Cosmos, используйте следующий шаблон.

```bash
mongoimport.exe --host <your_hostname>:10255 -u <your_username> -p <your_password> --db <your_database> --collection <your_collection> --ssl --sslAllowInvalidCertificates --type json --file "C:\sample.json"
```

Укажите вместо заполнителей \<your_hostname>, \<your_username> и \<your_password> имя хоста, имя пользователя и пароль для своей учетной записи. В следующем примере мы используем **sampleDB** в качестве значения для \<your_database> и **sampleColl** как значение для \<your_collection>:

```bash
mongoimport.exe --host cosmosdb-mongodb-account.documents.azure.com:10255 -u cosmosdb-mongodb-account -p <Your_MongoDB_password> --ssl --sslAllowInvalidCertificates --db sampleDB --collection sampleColl --type json --file "C:\Users\admin\Desktop\*.json"
```

## <a name="use-mongorestore"></a>Использование mongorestore

Чтобы восстановить данные в учетной записи Cosmos, настроенной с API для MongoDB, используйте приведенный ниже шаблон для выполнения импорта.

```bash
mongorestore.exe --host <your_hostname>:10255 -u <your_username> -p <your_password> --db <your_database> --collection <your_collection> --ssl --sslAllowInvalidCertificates <path_to_backup>
```

Укажите вместо заполнителей \<your_hostname>, \<your_username> и \<your_password> имя хоста, имя пользователя и пароль для своей учетной записи. В следующем примере мы используем **./dumps/dump-2016-12-07** в качестве значения для \<path_to_backup>:

```bash
mongorestore.exe --host cosmosdb-mongodb-account.documents.azure.com:10255 -u cosmosdb-mongodb-account -p <Your_MongoDB_password> --db mydatabase --collection mycollection --ssl --sslAllowInvalidCertificates ./dumps/dump-2016-12-07
```

## <a name="clean-up-resources"></a>Очистка ресурсов

Вы можете удалить группу ресурсов, учетную запись Cosmos и все связанные ресурсы, когда они больше не нужны. Чтобы удалить группу ресурсов, выполните шаги, описанные ниже:

1. Перейдите к группе ресурсов, в которой создана учетная запись Cosmos.
1. Выберите **Удалить группу ресурсов**.
1. Подтвердите имя удаляемой группы ресурсов и выберите **Удалить**.

## <a name="next-steps"></a>Дополнительная информация

Перейдите к следующему руководству, из которого вы узнаете, как запрашивать данные с помощью API Azure Cosmos DB для MongoDB. 

> [!div class="nextstepaction"]
> [Руководство. Выполнение запросов в Azure Cosmos DB с использованием API MongoDB](../cosmos-db/tutorial-query-mongodb.md)
