---
title: Создание счетчиков производительности для отслеживания производительности диспетчера сопоставления сегментов
description: Класс ShardMapManager и счетчики производительности для маршрутизации, зависящей от данных
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: seoapril2019
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
manager: craigg
ms.date: 02/07/2019
ms.openlocfilehash: 5c6c923c86ea0c5968079188c87ec3988ec30142
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/18/2019
ms.locfileid: "59494924"
---
# <a name="create-performance-counters-to-track-performance-of-shard-map-manager"></a>Создание счетчиков производительности для отслеживания производительности диспетчера сопоставления сегментов

Счетчики производительности используются для отслеживания производительности [маршрутизация, зависящая от данных](sql-database-elastic-scale-data-dependent-routing.md) операций. Эти счетчики можно найти в системном мониторе в категории "Эластичная база данных: управление сегментами".

Вы можете сохранять данные о производительности [диспетчера карты сегментов](sql-database-elastic-scale-shard-map-management.md), особенно при использовании [маршрутизации, зависящей от данных](sql-database-elastic-scale-data-dependent-routing.md). Счетчики создаются с помощью методов класса Microsoft.Azure.SqlDatabase.ElasticScale.Client.  


**Последняя версия** перейдите к [Microsoft.Azure.SqlDatabase.ElasticScale.Client](https://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Client/). См. также статью [Обновление приложения для использования новой версии клиентской библиотеки эластичной базы данных](sql-database-elastic-scale-upgrade-client-library.md).

## <a name="prerequisites"></a>Технические условия

* Чтобы пользователь мог создавать категории производительности и счетчики, он должен быть членом локальной группы **Администраторы** на компьютере, где размещается приложение.  
* Чтобы пользователь мог создавать экземпляры счетчика производительности и обновлять показания счетчиков, он должен быть членом группы **Администраторы** или **Пользователи системного монитора**.

## <a name="create-performance-category-and-counters"></a>Создание счетчиков и категорий производительности

Чтобы создать счетчик, вызовите метод CreatePerformanceCategoryAndCounters класса [ShardMapManagementFactory](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanagerfactory). Этот метод может выполнять только администратор:

    ShardMapManagerFactory.CreatePerformanceCategoryAndCounters()  

Для выполнения метода можно также использовать [этот](https://gallery.technet.microsoft.com/scriptcenter/Elastic-DB-Tools-for-Azure-17e3d283) сценарий PowerShell.
Этот метод создает следующие счетчики производительности.  

* **Кэшированные сопоставления**: число сопоставлений, кэшируемых для карты сегментов.
* **Число операций DDR в секунду**: число операций маршрутизации, зависящих от данных, для карты сегментов. Этот счетчик обновляется при успешном подключении к целевому сегменту после вызова метода [OpenConnectionForKey()](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmap.openconnectionforkey).
* **Число попаданий при поиске сопоставлений в кэше в секунду**: число успешных операций поиска сопоставлений в кэше для карты сегментов.
* **Число промахов при поиске сопоставлений в кэше в секунду**: число неуспешных операций поиска сопоставлений в кэше для карты сегментов.
* **Число добавляемых или обновляемых сопоставлений в кэше в секунду**: число добавления или обновления сопоставлений в кэше для карты сегментов.
* **Число удаляемых из кэша сопоставлений в секунду**: число удаления сопоставлений из кэша для карты сегментов.

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

### <a name="see-also"></a>См. также

[Общие сведения о возможностях эластичных баз данных](sql-database-elastic-scale-introduction.md)  

[!INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Anchors-->
<!--Image references-->
