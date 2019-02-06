---
title: Репликация транзакций с помощью Базы данных SQL Azure | Документация Майкрософт
description: Сведения об использовании репликации транзакций SQL Server с автономными базами данных, базами данных в пуле и базами данных экземпляра в Базе данных SQL Azure.
services: sql-database
ms.service: sql-database
ms.subservice: data-movement
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: MashaMSFT
ms.author: mathoma
ms.reviewer: carlrab
manager: craigg
ms.date: 01/25/2019
ms.openlocfilehash: 548bc9afb37f8c4a1c6c208a8741d1e3da0a784c
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/31/2019
ms.locfileid: "55469402"
---
# <a name="transactional-replication-with-standalone-pooled-and-instance-databases-in-azure-sql-database"></a>Репликация транзакций с автономными базами данных, базами данных в пуле и базами данных экземпляра в Базе данных SQL Azure.

Репликация транзакций — это функция Базы данных SQL Azure, Управляемого экземпляра и SQL Server, которая позволяет реплицировать данные из таблиц в Базе данных SQL Azure или SQL Server в таблицы удаленных баз данных. Она обеспечивает синхронизацию множества таблиц в различных базах данных.

## <a name="when-to-use-transactional-replication"></a>Когда использовать репликацию транзакций

Репликация транзакций удобна в следующих случаях:

- Публикация изменений, внесенных в одной или нескольких таблицах в базе данных, и их распределение в одну или несколько баз данных SQL Server или SQL Azure, которые подписаны на изменения.
- Поддержание нескольких распределенных баз данных в синхронизированном состоянии.
- Миграция баз данных из SQL Server или Управляемого экземпляра в другую базу данных путем постоянной публикации изменений.

## <a name="overview"></a>Обзор

На следующем рисунке показаны ключевые компоненты репликации транзакций:  

![Репликация с Базой данных SQL](media/replication-to-sql-database/replication-to-sql-database.png)


**Издатель** — это экземпляр или сервер, который публикует изменения, внесенные в некоторых таблицах (статьях), отправляя обновления распространителю. Публикация в Базу данных SQL Azure с локального SQL Server поддерживается в следующих версиях SQL Server:

    - SQL Server 2019 (предварительная версия);
    - с SQL Server 2016 в SQL 2017;
    - SQL Server 2014 с пакетом обновления 1(SP1) и накопительным пакетом обновления 3 (CU3) или более поздней версии (12.00.4427);
    - SQL Server 2014 RTM с накопительным пакетом обновления 10 (CU10) (12.00.2556);
    - SQL Server 2012 с пакетом обновления 3 (SP3) или более поздней версии (11.0.6020);
    - SQL Server 2012 с пакетом обновления 2 (SP2) и накопительным пакетом обновления 8 (CU8) (11.0.5634.0).
    - Для других версий SQL Server, которые не поддерживают публикацию в объекты в Azure, можно использовать метод [повторной публикации данных](https://docs.microsoft.com/sql/relational-databases/replication/republish-data), чтобы перемещать данные в более новые версии SQL Server. 

**Распространитель** — это экземпляр или сервер, который собирает изменения в статьях от издателя и распространяет их подписчикам. Распространителем может быть Управляемый экземпляр Базы данных SQL Azure или SQL Server (любая версия, которая не ниже версии издателя). 

**Подписчик** — это экземпляр или сервер, который принимает изменения, внесенные в издателе. Подписчиками могут быть автономные базы данных, базы данных в пуле и базы данных экземпляра в Базе данных SQL Azure или в базах данных SQL Server. Подписчик автономной или объединенной в пул базы данных должен быть настроен как принудительный подписчик. 

| Роль | Автономные и объединенные в пул базы данных | Базы данных экземпляра |
| :----| :------------- | :--------------- |
| **Издатель** | Нет  | Yes | 
| **Распространитель** | Нет  | Yes|
| **Подписчик по запросу** | Нет  | Yes|
| **Принудительный подписчик**| Yes | Yes|
| &nbsp; | &nbsp; | &nbsp; |

Существуют разные [типы репликации](https://docs.microsoft.com/sql/relational-databases/replication/types-of-replication?view=sql-server-2017).


| Репликация | Автономные и объединенные в пул базы данных | Базы данных экземпляра|
| :----| :------------- | :--------------- |
| [**Транзакций**](https://docs.microsoft.com/sql/relational-databases/replication/transactional/transactional-replication) | Да (только в качестве подписчика) | Yes | 
| [**Моментальных снимков**](https://docs.microsoft.com/sql/relational-databases/replication/snapshot-replication) | Да (только в качестве подписчика) | Yes|
| [**Слиянием**](https://docs.microsoft.com/sql/relational-databases/replication/merge/merge-replication) | Нет  | Нет |
| [**Одноранговая**](https://docs.microsoft.com/sql/relational-databases/replication/transactional/peer-to-peer-transactional-replication) | Нет  | Нет |
| **Односторонняя** | Yes | Yes|
| [**Двунаправленная**](https://docs.microsoft.com/sql/relational-databases/replication/transactional/bidirectional-transactional-replication) | Нет  | Yes|
| [**Обновляемых подписок**](https://docs.microsoft.com/sql/relational-databases/replication/transactional/updatable-subscriptions-for-transactional-replication) | Нет  | Нет |
| &nbsp; | &nbsp; | &nbsp; |

  >[!NOTE]
  > - Попытка настроить репликацию с использованием более старой версии может привести к ошибке MSSQL_REPL20084 (The process could not connect to Subscriber (Процессу не удалось подключиться к Подписчику)) или MSSQ_REPL40532 ("Не удается открыть сервер \<имя>, запрашиваемый с использованием имени для входа. Вход в систему не выполнен").
  > - Чтобы использовать все функции Базы данных SQL Azure, необходимо использовать последние версии [SQL Server Management Studio (SSMS)](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms?view=sql-server-2017) и [SQL Server Data Tools (SSDT)](https://docs.microsoft.com/sql/ssdt/download-sql-server-data-tools-ssdt?view=sql-server-2017).

## <a name="requirements"></a>Требования

- При подключении используется аутентификация SQL между участниками репликации. 
- Общий ресурс учетной записи хранения Azure для рабочей папки, используемой для репликации. 
- Порт 445 (исходящий TCP) должен быть открыт в правилах безопасности подсети Управляемого экземпляра для доступа к общей папке Azure. 
- Порт 1433 (исходящий TCP) должен быть открыт, если издатель или распространитель находятся в Управляемом экземпляре, а подписчик — в локальной среде. 

## <a name="common-configurations"></a>Распространенные конфигурации

Как правило, издатель и распространитель должны находиться либо в облаке, либо в локальной среде. Поддерживаются следующие конфигурации: 

### <a name="publisher-with-local-distributor-on-a-managed-instance"></a>Издатель с локальным распространителем в Управляемом экземпляре

![Единственный экземпляр в качестве издателя и распространителя ](media/replication-with-sql-database-managed-instance/01-single-instance-asdbmi-pubdist.png)

Издатель и распространитель настроены в пределах единственного Управляемого экземпляра и распространяют изменения в другой локальный сервер SQL Server, Управляемый экземпляр или отдельную базу данных. В этой конфигурации нельзя настроить Управляемый экземпляр издателя или распространителя с [георепликацией и группами автоматической отработки отказа](sql-database-auto-failover-group.md).

### <a name="publisher-with-remote-distributor-on-a-managed-instance"></a>Издатель с удаленным распространителем в Управляемом экземпляре

В этой конфигурации один Управляемый экземпляр публикует изменения в распространителе, размещенном в другом Управляемом экземпляре, который может обслуживать множество исходных Управляемых экземпляров и распространять изменения на один или несколько целевых объектов в Управляемом экземпляре, отдельной базе данных, объединенной в пул базе данных или SQL Server.

![Раздельные экземпляры для издателя и распространителя](media/replication-with-sql-database-managed-instance/02-separate-instances-asdbmi-pubdist.png)

Издателя и распространителя можно настроить в двух Управляемых экземплярах. В этой конфигурации:

- оба Управляемые экземпляры находятся в той же виртуальной сети;
- оба Управляемые экземпляры находятся в том же расположении;
- Управляемые экземпляры, на которых размещены базы данных издателя и распространителя, не могут быть [геореплицированы с помощью групп автоматической отработки отказа](sql-database-auto-failover-group.md).

### <a name="publisher-and-distributor-on-premises-with-a-subscriber-on-a-standalone-pooled-and-instance-database"></a>Локальные издатель и распространитель с подписчиком в автономной базе данных, базе данных в пуле и базе данных экземпляра 

![База данных SQL Azure в качестве подписчика](media/replication-with-sql-database-managed-instance/03-azure-sql-db-subscriber.png)
 
В этой конфигурации База данных SQL Azure (автономная база данных, база данных в пуле и база данных экземпляра) является подписчиком. Эта конфигурация поддерживает миграцию из локальной среды в Azure. Если подписчик находится в автономной или объединенной в пул базе данных, он должен находиться в принудительном режиме.  

## <a name="next-steps"></a>Дополнительная информация

1. [Настройка репликации транзакций для Управляемого экземпляра](replication-with-sql-database-managed-instance.md#configure-publishing-and-distribution-example). 
1. [Создание публикации](https://docs.microsoft.com/sql/relational-databases/replication/publish/create-a-publication).
1. [Создайте принудительную подписку](https://docs.microsoft.com/sql/relational-databases/replication/create-a-push-subscription), используя имя сервера Базы данных SQL Azure в качестве подписчика (например, `N'azuresqldbdns.database.windows.net`) и имя Базы данных SQL Azure в качестве целевой базы данных (например, **Adventureworks**). )


## <a name="see-also"></a>См. также  

- [Репликация в Базу данных SQL](replication-to-sql-database.md)
- [Репликация с использованием Управляемого экземпляра Базы данных SQL](replication-with-sql-database-managed-instance.md)
- [Создание публикации](https://docs.microsoft.com/sql/relational-databases/replication/publish/create-a-publication)
- [Создание принудительной подписки](https://docs.microsoft.com/sql/relational-databases/replication/create-a-push-subscription/)
- [Типы репликации](https://docs.microsoft.com/sql/relational-databases/replication/types-of-replication)
- [Мониторинг (репликация)](https://docs.microsoft.com/sql/relational-databases/replication/monitor/monitoring-replication)
- [Инициализация подписки](https://docs.microsoft.com/sql/relational-databases/replication/initialize-a-subscription)  
