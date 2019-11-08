---
title: Счетчики производительности для трассировки диспетчера сопоставления сегментов
description: Класс ShardMapManager и счетчики производительности для маршрутизации, зависящей от данных
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: seoapril2019, seo-lt-2019
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 02/07/2019
ms.openlocfilehash: de481dad9dd39b301a21142c67b1baf2209f76e2
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/08/2019
ms.locfileid: "73823905"
---
# <a name="create-performance-counters-to-track-performance-of-shard-map-manager"></a>Создание счетчиков производительности для наблюдения за производительностью диспетчера карт сегментов

Счетчики производительности используются для наблюдения за производительностью операций [маршрутизации, зависящих от данных](sql-database-elastic-scale-data-dependent-routing.md) . Эти счетчики можно найти в системном мониторе в категории "Эластичная база данных: управление сегментами".

Вы можете сохранять данные о производительности [диспетчера карты сегментов](sql-database-elastic-scale-shard-map-management.md), особенно при использовании [маршрутизации, зависящей от данных](sql-database-elastic-scale-data-dependent-routing.md). Счетчики создаются с помощью методов класса Microsoft.Azure.SqlDatabase.ElasticScale.Client.  


**Новая версия** : [Microsoft.Azure.SqlDatabase.ElasticScale.Client](https://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Client/). См. также статью [Обновление приложения для использования новой версии клиентской библиотеки эластичной базы данных](sql-database-elastic-scale-upgrade-client-library.md).

## <a name="prerequisites"></a>Предварительные требования

* Чтобы пользователь мог создавать категории производительности и счетчики, он должен быть членом локальной группы **Администраторы** на компьютере, где размещается приложение.  
* Чтобы пользователь мог создавать экземпляры счетчика производительности и обновлять показания счетчиков, он должен быть членом группы **Администраторы** или **Пользователи системного монитора**.

## <a name="create-performance-category-and-counters"></a>Создание счетчиков и категорий производительности

Чтобы создать счетчик, вызовите метод CreatePerformanceCategoryAndCounters класса [ShardMapManagementFactory](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanagerfactory). Этот метод может выполнять только администратор:

    ShardMapManagerFactory.CreatePerformanceCategoryAndCounters()  

Для выполнения метода можно также использовать [этот](https://gallery.technet.microsoft.com/scriptcenter/Elastic-DB-Tools-for-Azure-17e3d283) сценарий PowerShell.
Этот метод создает следующие счетчики производительности.  

* **Cached mappings**(Кэшированные сопоставления) — количество сопоставлений, кэшируемых для карты сегментов.
* **DDR operations/sec**(Число операций DDR в секунду) — скорость операций маршрутизации, зависящих от данных, для карты сегментов. Этот счетчик обновляется при успешном подключении к целевому сегменту после вызова метода [OpenConnectionForKey()](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmap.openconnectionforkey).
* **Mapping lookup cache hits/sec**(Число попаданий при поиске сопоставлений в кэше в секунду) — скорость успешных операций поиска сопоставлений в кэше для карты сегментов.
* **Mapping lookup cache misses/sec**(Число промахов при поиске сопоставлений в кэше в секунду) — скорость неуспешных операций поиска сопоставлений в кэше для карты сегментов.
* **Mappings added or updated in cache/sec**(Число добавляемых или обновляемых сопоставлений в кэше в секунду) — скорость добавления или обновления сопоставлений в кэше для карты сегментов.
* **Mappings removed from cache/sec**(Число удаляемых из кэша сопоставлений в секунду) — скорость удаления сопоставлений из кэша для карты сегментов.

Счетчики производительности создаются для каждой кэшированной карты сегментов каждого процесса.  

## <a name="notes"></a>Примечания

Создание счетчиков производительности инициируется следующими событиями.  

* Инициализация объекта [ShardMapManager](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager) с [безотложной загрузкой](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanagerloadpolicy), если в ShardMapManager есть карты сегментов. Сюда относятся методы [GetSqlShardMapManager](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanagerfactory.getsqlshardmapmanager) и [TryGetSqlShardMapManager](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanagerfactory.trygetsqlshardmapmanager).
* Успешный поиск карты сегментов (с помощью [GetShardMap()](https://msdn.microsoft.com/library/azure/dn824215.aspx), [GetListShardMap()](https://msdn.microsoft.com/library/azure/dn824212.aspx) или [GetRangeShardMap()](https://msdn.microsoft.com/library/azure/dn824173.aspx)).
* Успешное создание карты сегментов с помощью CreateShardMap().

Счетчики производительности обновляются при выполнении любых операций кэширования, связанных с картой сегментов или сопоставлениями. Успешное удаление сегментов с помощью метода DeleteShardMap() приводит к удалению экземпляра счетчика производительности.  

## <a name="best-practices"></a>Рекомендации

* Создание категории производительности и счетчиков следует выполнять только один раз, до создания объекта ShardMapManager. При каждом выполнении команды CreatePerformanceCategoryAndCounters() предыдущие значения счетчиков удаляются (данные теряются во всех экземплярах) и создаются новые.  
* Экземпляры счетчиков производительности создаются для каждого процесса. Любой сбой приложения или удаление карты сегментов из кэша приведет к удалению экземпляров счетчиков производительности.  

### <a name="see-also"></a>Дополнительные материалы

[Общие сведения о возможностях эластичных баз данных](sql-database-elastic-scale-introduction.md)  

[!INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Anchors-->
<!--Image references-->
