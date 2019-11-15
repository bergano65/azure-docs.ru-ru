---
title: Функции и команды Apache Cassandra, поддерживаемые API Cassandra для Azure Cosmos DB
description: Сведения о поддержке функций Apache Cassandra в API Cassandra для Azure Cosmos DB
author: kanshiG
ms.author: govindk
ms.reviewer: sngun
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: overview
ms.date: 09/24/2018
ms.openlocfilehash: 12df79696033e69abbf48f053c1a594be9409cda
ms.sourcegitcommit: bc7725874a1502aa4c069fc1804f1f249f4fa5f7
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/07/2019
ms.locfileid: "73721107"
---
# <a name="apache-cassandra-features-supported-by-azure-cosmos-db-cassandra-api"></a>Функции Apache Cassandra, поддерживаемые API Cassandra для Azure Cosmos DB 

Azure Cosmos DB — это глобально распределенная многомодельная служба базы данных Майкрософт. Взаимодействие с API Cassandra для Azure Cosmos DB осуществляется посредством клиентских [драйверов](https://cassandra.apache.org/doc/latest/getting_started/drivers.html?highlight=driver) Cassandra с открытым исходным кодом, совместимым с [сетевым протоколом](https://github.com/apache/cassandra/blob/trunk/doc/native_protocol_v4.spec) Cassandra Query Language (CQL) v4. 

С помощью API Cassandra для Azure Cosmos DB вы можете воспользоваться преимуществами API-интерфейсов Apache Cassandra, а также корпоративными возможностями, доступными в Azure Cosmos DB. В их число входят [глобальное распределение](distribute-data-globally.md), [секционирование автоматического масштабирования](partition-data.md), гарантии доступности и задержки, шифрование при хранении, создание резервных копий и многое другое.

## <a name="cassandra-protocol"></a>Протокол Cassandra 

API Cassandra для Azure Cosmos DB совместим с CQL **v4**. Ниже перечислены поддерживаемые команды CQL, средства, ограничения и исключения. Любой драйвер клиента, который распознает эти протоколы, должен иметь возможность подключения к API Cassandra для Azure Cosmos DB.

## <a name="cassandra-driver"></a>Драйвер Cassandra

API Cassandra для Azure Cosmos DB поддерживает следующие версии драйверов Cassandra:

* [Java 3.5 и выше](https://github.com/datastax/java-driver)  
* [C# 3.5 и выше](https://github.com/datastax/csharp-driver)  
* [Nodejs 3.5 и выше](https://github.com/datastax/nodejs-driver)  
* [Python 3.15 и выше](https://github.com/datastax/python-driver)  
* [C++ 2.9](https://github.com/datastax/cpp-driver)   
* [PHP 1.3](https://github.com/datastax/php-driver)  
* [Gocql](https://github.com/gocql/gocql)  
 
## <a name="cql-data-types"></a>Типы данных CQL 

API Cassandra для Azure Cosmos DB поддерживает следующие типы данных CQL:

* ascii  
* bigint  
* blob-объект  
* Логическое  
* Счетчик  
* date  
* decimal  
* Double  
* float;  
* frozen  
* inet  
* int  
* list  
* set  
* smallint  
* text  
* time  
* timestamp  
* timeuuid  
* tinyint  
* tuple  
* uuid  
* varchar  
* varint  
* tuples  
* udts  
* map  

## <a name="cql-functions"></a>Функции CQL

API Cassandra для Azure Cosmos DB поддерживает следующие функции CQL:

* по маркеру  
* Статистические функции
  * min, max, avg, count
* Функции преобразования BLOB-объектов 
  * typeAsBlob(value)  
  * blobAsType(value)
* Функции UUID и timeuuid 
  * dateOf()  
  * now()  
  * minTimeuuid()  
  * unixTimestampOf()  
  * toDate(timeuuid)  
  * toTimestamp(timeuuid)  
  * toUnixTimestamp(timeuuid)  
  * toDate(timestamp)  
  * toUnixTimestamp(timestamp)  
  * toTimestamp(date)  
  * toUnixTimestamp(date) 
  


## <a name="cassandra-api-limits"></a>Ограничения API Cassandra

API Cassandra для Azure Cosmos DB не имеет ограничений на размер данных, хранящихся в таблице. В таблицах могут храниться сотни терабайт или петабайт данных с соблюдением ограничений для ключей секций. Каждый эквивалент сущности или строки не имеет никаких ограничений на число столбцов. Однако общий размер сущности не должен превышать 2 МБ. Объем данных на ключ секции не может превышать 10 ГБ, как и во всех других API.

## <a name="tools"></a>Средства 

API Cassandra для Azure Cosmos DB — это платформа управляемой службы. Ей не требуются расходы на управление или такие служебные программы, как сборщик мусора, виртуальная машина Java (JVM) и Nodetool для управления кластером. Она поддерживает средства, такие как cqlsh, использующее совместимость с двоичным CQLv4. 

* Кроме того, для управления учетной записью применяются обозреватель данных на портале Azure, метрики, журналы диагностики, PowerShell и интерфейс командной строки.

## <a name="cql-shell"></a>Оболочка CQL  

Программа командной строки CQLSH входит в состав Apache Cassandra 3.1.1 и работает без дополнительной настройки со следующими переменными среды.

**Windows:**

При использовании Windows рекомендуется включить [файловую систему Windows для Linux](https://docs.microsoft.com/en-us/windows/wsl/install-win10#install-the-windows-subsystem-for-linux). Затем вы можете выполнять приведенные ниже команды Linux.

**UNIX/Linux и Mac:**

```bash
# Install default-jre and default-jdk
sudo apt install default-jre
sudo apt-get update
sudo apt install default-jdk

# Import the Baltimore CyberTrust root certificate:
curl https://cacert.omniroot.com/bc2025.crt > bc2025.crt
keytool -importcert -alias bc2025ca -file bc2025.crt

# Install the Cassandra libraries in order to get CQLSH:
echo "deb http://www.apache.org/dist/cassandra/debian 311x main" | sudo tee -a /etc/apt/sources.list.d/cassandra.sources.list
curl https://www.apache.org/dist/cassandra/KEYS | sudo apt-key add -
sudo apt-get update
sudo apt-get install cassandra

# Export the SSL variables:
export SSL_VERSION=TLSv1_2
export SSL_VALIDATE=false

# Connect to Azure Cosmos DB API for Cassandra:
cqlsh <YOUR_ACCOUNT_NAME>.cassandra.cosmosdb.azure.com 10350 -u <YOUR_ACCOUNT_NAME> -p <YOUR_ACCOUNT_PASSWORD> --ssl

```

## <a name="cql-commands"></a>Команды CQL

Azure Cosmos DB поддерживает следующие команды базы данных для учетных записей API Cassandra.

* CREATE KEYSPACE (Настройки репликации для этой команды игнорируются)
* CREATE TABLE 
* ALTER TABLE 
* USE 
* INSERT 
* SELECT 
* UPDATE 
* BATCH — поддерживаются только нерегистрируемые команды 
* УДАЛИТЬ

Все операции CRUD, выполняемые с помощью пакета SDK, совместимого с CQL версии 4, возвращают дополнительные сведения об ошибке и использованных единицах запроса. Команды DELETE и UPDATE должны обрабатываться с учетом управления ресурсами, чтобы обеспечить наиболее эффективное использование подготовленной пропускной способности.

* Примечание. Значение gc_grace_seconds должно равняться нулю, если оно указывается.

```csharp
var tableInsertStatement = table.Insert(sampleEntity); 
var insertResult = await tableInsertStatement.ExecuteAsync(); 
 
foreach (string key in insertResult.Info.IncomingPayload) 
        { 
            byte[] valueInBytes = customPayload[key]; 
            double value = Encoding.UTF8.GetString(valueInBytes); 
            Console.WriteLine($"CustomPayload:  {key}: {value}"); 
        } 
```

## <a name="consistency-mapping"></a>Сопоставление согласованности 

API Cassandra для Azure Cosmos DB предоставляет согласованные операции чтения.  Сопоставление согласованности подробно описано [здесь](consistency-levels-across-apis.md#cassandra-mapping).

## <a name="permission-and-role-management"></a>Управление разрешениями и ролями

Azure Cosmos DB поддерживает управление доступом на основе ролей (RBAC) для подготовки, смены ключей, просмотра метрик, а также пароли (ключи) для чтения и записи или только для чтения, которые можно получить с помощью [портала Azure](https://portal.azure.com). Azure Cosmos DB не поддерживает роли для действий CRUD.

## <a name="keyspace-and-table-options"></a>Параметры пространства ключей и таблицы

Параметры для имени региона, класса, replication_factor и центра обработки данных в команде "Создать пространство ключей" в настоящее время игнорируются. Для добавления регионов система использует базовый метод репликации [глобального распространения](global-dist-under-the-hood.md) Azure Cosmos DB. Если необходимо присутствие данных между регионами, вы можете включить их на уровне учетной записи с помощью PowerShell, интерфейса командной строки или портала. Дополнительные сведения см. в статье [Добавление регионов](how-to-manage-database-account.md#addremove-regions-from-your-database-account). Невозможно отключить Durable_writes, потому что Azure Cosmos DB гарантирует длительность каждой записи. В каждом регионе Azure Cosmos DB реплицирует данные по набору реплик, который состоит из 4 реплик. [Конфигурацию](global-dist-under-the-hood.md) этого набора реплик невозможно изменить.
 
При создании таблицы игнорируются все параметры, кроме gc_grace_seconds, для которого должно быть задано значение ноль.
Пространство ключей и таблица имеют дополнительную опцию с именем "cosmosdb_provisioned_throughput" с минимальным значением 400 единиц запроса/секунду. Пропускная способность пространства ключей позволяет разделять пропускную способность между несколькими таблицами, и это полезно для сценариев, когда не все таблицы используют предоставленную пропускную способность. Команда "Alter Table" позволяет изменить выделенную пропускную способность по регионам. 

```
CREATE  KEYSPACE  sampleks WITH REPLICATION = {  'class' : 'SimpleStrategy'}   AND cosmosdb_provisioned_throughput=2000;  

CREATE TABLE sampleks.t1(user_id int PRIMARY KEY, lastname text) WITH cosmosdb_provisioned_throughput=2000; 

ALTER TABLE gks1.t1 WITH cosmosdb_provisioned_throughput=10000 ;

```


## <a name="usage-of-cassandra-retry-connection-policy"></a>Использование политики повторных подключений Cassandra

Azure Cosmos DB — это система управления ресурсами. Это означает, что в данную секунду можно выполнять определенное количество операций на основе единиц запроса, потребляемых операциями. Если приложение превысит этот лимит в течение данной секунды, запросы будут ограничены по скорости, и будут выданы исключения. API Cassandra в Azure Cosmos DB преобразует эти исключения в ошибки перегрузки в собственном протоколе Cassandra. Чтобы приложение могло перехватывать и повторять запросы в случае ограничения скорости, предусмотрены расширения [spark](https://mvnrepository.com/artifact/com.microsoft.azure.cosmosdb/azure-cosmos-cassandra-spark-helper) и [Java](https://github.com/Azure/azure-cosmos-cassandra-extensions). Если вы используете другие пакеты SDK для доступа к API Cassandra в Azure Cosmos DB, создайте политику подключения для повторения этих исключений.

## <a name="next-steps"></a>Дополнительная информация

- Начните с [создания учетной записи API Cassandra, базы данных и таблицы](create-cassandra-api-account-java.md) с помощью приложения Java.

