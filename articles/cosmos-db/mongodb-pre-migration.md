---
title: Шаги перед переносом для переноса данных в API Azure Cosmos DB для MongoDB
description: Этот документ содержит общие сведения о предварительных требованиях для переноса данных из MongoDB в Cosmos DB.
author: roaror
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: conceptual
ms.date: 04/17/2019
ms.author: roaror
ms.openlocfilehash: 4dc7038d0ff5180f15a43268fd3f3aa0cbb0c7a0
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/25/2019
ms.locfileid: "75445198"
---
# <a name="pre-migration-steps-for-data-migrations-from-mongodb-to-azure-cosmos-dbs-api-for-mongodb"></a>Действия, выполняемые перед переносом данных из MongoDB в API Azure Cosmos DB для MongoDB

Перед переносом данных из MongoDB (локально или в облаке (IaaS)) в API Azure Cosmos DB для MongoDB необходимо выполнить следующие действия.

1. [Создание учетной записи Azure Cosmos DB](#create-account)
2. [Оценка пропускной способности, необходимой для рабочих нагрузок](#estimate-throughput)
3. [Выбор оптимального ключа секции для данных](#partitioning)
4. [Общие сведения о политике индексирования, которую можно задать для данных](#indexing)

Если вы уже выполнили описанные выше предварительные требования для миграции, см. инструкции по переносу [данных MongoDB в API Azure Cosmos DB для MongoDB](../dms/tutorial-mongodb-cosmos-db.md) для фактических инструкций по переносу данных. Если нет, в этом документе приводятся инструкции по обработке этих предварительных требований. 

## <a id="create-account"></a>Создание учетной записи Azure Cosmos DB 

Перед началом миграции необходимо [создать учетную запись Azure Cosmos с помощью API Azure Cosmos DB для MongoDB](create-mongodb-dotnet.md). 

При создании учетной записи можно выбрать параметры для [глобального распределения](distribute-data-globally.md) данных. Кроме того, можно включить запись в нескольких регионах (или конфигурацию с несколькими хозяевами), которая позволяет использовать в качестве региона записи и чтения.

![Создание учетной записи](./media/mongodb-pre-migration/account-creation.png)

## <a id="estimate-throughput"></a>Оценка потребности в пропускной способности для рабочих нагрузок

Перед началом миграции с помощью [Database Migration Service (DMS)](../dms/dms-overview.md)необходимо оценить объем пропускной способности, которую следует подготавливать для баз данных и коллекций Azure Cosmos.

Пропускная способность может быть подготовлена на следующих:

- Коллекция

- База данных

> [!NOTE]
> Кроме того, можно использовать сочетание выше, где некоторые коллекции в базе данных могут иметь выделенную подготовленную пропускную способность, а другие могут совместно использовать пропускную способность. Дополнительные сведения см. в разделе [Настройка пропускной способности для базы данных и страницы контейнера](set-throughput.md) .
>

Сначала необходимо решить, нужно ли подготавливать пропускную способность на уровне базы данных или коллекции или их сочетание. Как правило, рекомендуется настроить выделенную пропускную способность на уровне коллекции. Пропускная способность подготовки на уровне базы данных позволяет коллекциям в базе данных совместно использовать подготовленную пропускную способность. Однако при общей пропускной способности нет никакой гарантии на конкретную пропускную способность каждой отдельной коллекции, и вы не получаете предсказуемую производительность для какой-либо конкретной коллекции.

Если вы не знаете, сколько пропускной способности следует выделить для каждой отдельной коллекции, можно выбрать пропускную способность на уровне базы данных. Подготовленную пропускную способность, настроенную в базе данных Azure Cosmos, можно рассматривать как логическую эквивалентную мощности вычислений виртуальной машины MongoDB или физического сервера, но более экономична благодаря возможности эластичного масштабирования. Дополнительные сведения см. [в статье о подготовке пропускной способности в контейнерах и базах данных Azure Cosmos](set-throughput.md).

При подготовке пропускной способности на уровне базы данных все коллекции, созданные в этой базе данных, должны быть созданы с помощью ключа секции или сегмента. Дополнительные сведения о секционировании см. [в разделе секционирование и горизонтальное масштабирование в Azure Cosmos DB](partition-data.md). Если вы не укажете ключ секции или сегментирования во время миграции, Azure Database Migration Service автоматически заполнит поле ключа сегмента атрибутом *_id* , автоматически создаваемым для каждого документа.

### <a name="optimal-number-of-request-units-rus-to-provision"></a>Оптимальное количество единиц запросов (RUs) для инициализации

В Azure Cosmos DB пропускная способность подготавливается заранее и измеряется в единицах запроса (RU) в секунду. Если у вас есть рабочие нагрузки, которые запускают MongoDB на виртуальной машине или локально, подумайте о том, как простая абстракция для физических ресурсов, например для размера виртуальной машины или локального сервера и ресурсов, которыми они обладают, например, памяти, ЦП, операций ввода-вывода. 

В отличие от виртуальных машин или локальных серверов, сервер RUs легко масштабируется в любое время. Вы можете изменить число подготовленных получателей в течение нескольких секунд, и вы оплачиваете только максимальное количество, которое вы подготовили для данного периода в один час. Дополнительные сведения см. в статье [Единицы запроса в базе данных Azure Cosmos DB](request-units.md).

Ниже приведены ключевые факторы, влияющие на количество необходимых получателей.
- **Элемент (т. е. размер документа)** : при увеличении размера элемента или документа число получателей, потребляемое для чтения или записи элемента или документа, также увеличивается.
- **Число свойств элемента**. Предполагается, что при [индексировании по умолчанию](index-overview.md) для всех свойств количество операций чтения, потребляемых для записи элемента, увеличивается по мере увеличения числа свойств элемента. Можно уменьшить потребление единиц запросов для операций записи, [ограничивая количество индексированных свойств](index-policy.md).
- **Параллельные операции**. используемые единицы запросов также зависят от частоты, с которой выполняются различные операции CRUD (например, операции записи, чтения, обновления, удаления) и более сложные запросы. [Монгостат](https://docs.mongodb.com/manual/reference/program/mongostat/) можно использовать для вывода требований к параллелизму текущих данных MongoDB.
- **Шаблоны запросов**. сложность запроса влияет на количество единиц запросов, потребляемых запросом.

Если вы экспортируете файлы JSON с помощью [монгоекспорт](https://docs.mongodb.com/manual/reference/program/mongoexport/) и понимаете, сколько операций записи, чтения, обновления и удаления выполняется в секунду, можно использовать планировщик ресурсов [Azure Cosmos DB](https://www.documentdb.com/capacityplanner) , чтобы оценить начальное число получателей, которые будут подготавливаться. Планировщик ресурсов не имеет множителя на стоимость более сложных запросов. Таким образом, если у вас есть сложные запросы к данным, будет использоваться дополнительный параметр RUs. В калькуляторе также предполагается, что все поля индексируются, и используется согласованность сеансов. Лучшим способом понять стоимость запросов является перенос данных (или образцов данных) в Azure Cosmos DB, [Подключение к конечной точке Cosmos DB](connect-mongodb-account.md) и запуск примера запроса из оболочки MongoDB с помощью команды `getLastRequestStastistics`, чтобы получить плату за запрос, в результате чего будет выводиться количество использованных запросов:

`db.runCommand({getLastRequestStatistics: 1})`

Эта команда выводит документ JSON следующего вида:

```{  "_t": "GetRequestStatisticsResponse",  "ok": 1,  "CommandName": "find",  "RequestCharge": 10.1,  "RequestDurationInMilliSeconds": 7.2}```

После того как вы понимаете количество запросов, использованных запросом, и количество запросов, которое необходимо для этого запроса, можно настроить число подготовленных получателей. Оптимизация RUs не является одноразовым событием. Вы должны непрерывно оптимизировать или масштабировать подготовленный объект RUs, в зависимости от того, не ожидает ли вы большого объема трафика, а не от интенсивной рабочей нагрузки или импорта данных.

## <a id="partitioning"></a>Выбор ключа секции
Секционирование — это ключевой момент, который следует учесть перед миграцией в глобально распределенную базу данных, например Azure Cosmos DB. Azure Cosmos DB использует секционирование для масштабирования отдельных контейнеров в базе данных в соответствии с требованиями к масштабируемости и производительности приложения. При секционировании элементы в контейнере делятся на отдельные подмножества, называемые логическими секциями. Дополнительные сведения и рекомендации по выбору правильного ключа секции для данных см. в [разделе Выбор ключа секции](https://docs.microsoft.com/azure/cosmos-db/partitioning-overview#choose-partitionkey). 

## <a id="indexing"></a>Индексирование данных
По умолчанию Azure Cosmos DB индексирует все поля данных после приема. [Политику индексирования](index-policy.md) можно изменить в Azure Cosmos DB в любое время. На самом деле, при переносе данных часто рекомендуется отключить индексирование, а затем снова включить его, когда данные уже находятся в Cosmos DB. Дополнительные сведения об индексировании см. в разделе [индексирование в Azure Cosmos DB](index-overview.md) . 

[Azure Database Migration Service](../dms/tutorial-mongodb-cosmos-db.md) автоматически переносит коллекции MongoDB с уникальными индексами. Однако перед миграцией необходимо создать уникальные индексы. Azure Cosmos DB не поддерживает создание уникальных индексов, если в коллекциях уже есть данные. Дополнительные сведения см. в статье об [уникальных ключах в Azure Cosmos DB](unique-keys.md).

## <a name="next-steps"></a>Дальнейшие действия
* [Перенесите данные MongoDB в Cosmos DB с помощью Database Migration Service.](../dms/tutorial-mongodb-cosmos-db.md) 
* [Обеспечение пропускной способности в контейнерах и базах данных Azure Cosmos](set-throughput.md)
* [Partitioning in Azure Cosmos DB](partition-data.md) (Секционирование в Azure Cosmos DB)
* [Глобальное распределение в Azure Cosmos DB](distribute-data-globally.md)
* [Индексирование в Azure Cosmos DB](index-overview.md)
* [Единицы запросов в Azure Cosmos DB](request-units.md)
