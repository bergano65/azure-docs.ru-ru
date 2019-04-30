---
title: Репликация транзакций с помощью Базы данных SQL Azure | Документация Майкрософт
description: Сведения об использовании репликации транзакций SQL Server с отдельными базами данных, базами данных в составе пула и базами данных экземпляра в Базе данных SQL Azure.
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
ms.date: 02/08/2019
ms.openlocfilehash: 409c1abd7e9f532bb243ecab00228b402215c77e
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/23/2019
ms.locfileid: "61313692"
---
# <a name="transactional-replication-with-single-pooled-and-instance-databases-in-azure-sql-database"></a>Репликация транзакций с отдельными базами данных, базами данных в составе пула и базами данных экземпляра в Базе данных SQL Azure.

Репликация транзакций — это функция Базы данных SQL Azure и SQL Server, которая позволяет реплицировать данные из таблиц в Базе данных SQL Azure или SQL Server в таблицы удаленных баз данных. Она обеспечивает синхронизацию множества таблиц в различных базах данных.

## <a name="when-to-use-transactional-replication"></a>Когда использовать репликацию транзакций

Репликация транзакций удобна в следующих случаях:
- Публикация изменений, внесенных в одной или нескольких таблицах в базе данных, и их распределение в одну или несколько баз данных SQL Server или SQL Azure, которые подписаны на изменения.
- Поддержание нескольких распределенных баз данных в синхронизированном состоянии.
- Миграция баз данных из SQL Server или Управляемого экземпляра в другую базу данных путем постоянной публикации изменений.

## <a name="overview"></a>Обзор

На следующем рисунке показаны ключевые компоненты репликации транзакций:  

![Репликация с Базой данных SQL](media/replication-to-sql-database/replication-to-sql-database.png)

**Издатель** — это экземпляр или сервер, который публикует изменения, внесенные в некоторых таблицах (статьях), отправляя обновления распространителю. Публикация в базу данных SQL Azure с локального SQL Server поддерживается в следующих версиях SQL Server:

- SQL Server 2019 (предварительная версия);
- с SQL Server 2016 в SQL 2017;
- SQL Server 2014 с пакетом обновления 1(SP1) и накопительным пакетом обновления 3 (CU3) или более поздней версии (12.00.4427);
- SQL Server 2014 RTM с накопительным пакетом обновления 10 (CU10) (12.00.2556);
- SQL Server 2012 с пакетом обновления 3 (SP3) или более поздней версии (11.0.6020);
- SQL Server 2012 с пакетом обновления 2 (SP2) и накопительным пакетом обновления 8 (CU8) (11.0.5634.0).
- Для других версий SQL Server, которые не поддерживают публикацию в объекты в Azure, можно использовать метод [повторной публикации данных](https://docs.microsoft.com/sql/relational-databases/replication/republish-data), чтобы перемещать данные в более новые версии SQL Server. 

**Распространитель** — это экземпляр или сервер, который собирает изменения в статьях от издателя и распространяет их подписчикам. Распространителем может быть Управляемый экземпляр Базы данных SQL Azure или SQL Server (любая версия, которая не ниже версии издателя). 

**Подписчик** — это экземпляр или сервер, который принимает изменения, внесенные в издателе. Подписчиками могут быть отдельные базы данных, базы данных в составе пула и базы данных экземпляра в Базе данных SQL Azure или в базах данных SQL Server. Подписчика отдельной базы данных или базы данных в составе пула необходимо настроить как принудительного подписчика. 

| Роль | Отдельные базы данных и базы данных в составе пула | Базы данных экземпляра |
| :----| :------------- | :--------------- |
| **Издатель** | Нет | Да | 
| **Распространитель** | Нет | Да|
| **Подписчик по запросу** | Нет | Да|
| **Принудительный подписчик**| Да | Да|
| &nbsp; | &nbsp; | &nbsp; |

  >[!NOTE]
  > Подписки по запросу не поддерживается, если распространитель — базы данных экземпляра, и подписчик не. 

Существуют разные [типы репликации](https://docs.microsoft.com/sql/relational-databases/replication/types-of-replication).


| Репликация | Отдельные базы данных и базы данных в составе пула | Базы данных экземпляра|
| :----| :------------- | :--------------- |
| [**Транзакций**](https://docs.microsoft.com/sql/relational-databases/replication/transactional/transactional-replication) | Да (только в качестве подписчика) | Да | 
| [**Моментальных снимков**](https://docs.microsoft.com/sql/relational-databases/replication/snapshot-replication) | Да (только в качестве подписчика) | Да|
| [**Слиянием**](https://docs.microsoft.com/sql/relational-databases/replication/merge/merge-replication) | Нет | Нет|
| [**Одноранговая**](https://docs.microsoft.com/sql/relational-databases/replication/transactional/peer-to-peer-transactional-replication) | Нет | Нет|
| **Односторонняя** | Да | Да|
| [**Двунаправленная**](https://docs.microsoft.com/sql/relational-databases/replication/transactional/bidirectional-transactional-replication) | Нет | Да|
| [**Обновляемых подписок**](https://docs.microsoft.com/sql/relational-databases/replication/transactional/updatable-subscriptions-for-transactional-replication) | Нет | Нет|
| &nbsp; | &nbsp; | &nbsp; |

  >[!NOTE]
  > - Попытка настроить репликацию с использованием более старой версии может привести к ошибке MSSQL_REPL20084 (The process could not connect to Subscriber (Процессу не удалось подключиться к Подписчику)) или MSSQ_REPL40532 ("Не удается открыть сервер \<имя>, запрашиваемый с использованием имени для входа. Вход в систему не выполнен").
  > - Чтобы использовать все функции Базы данных SQL Azure, необходимо использовать последние версии [SQL Server Management Studio (SSMS)](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) и [SQL Server Data Tools (SSDT)](https://docs.microsoft.com/sql/ssdt/download-sql-server-data-tools-ssdt).
  
  ### <a name="supportabilty-matrix-for-instance-databases-and-on-premises-systems"></a>Матрица Supportabilty для базы данных экземпляра и локальных систем
  Матрица поддержки репликации, например баз данных является таким же, как для локального сервера SQL. 
  
  | **Издатель**   | **Распространитель** | **подписчик** |
| :------------   | :-------------- | :------------- |
| SQL Server 2017 | SQL Server 2017 | SQL Server 2017 <br/> SQL Server 2016 <br/> SQL Server 2014 |
| SQL Server 2016 | SQL Server 2017 <br/> SQL Server 2016 | SQL Server 2017 <br/>SQL Server 2016 <br/> SQL Server 2014 <br/> SQL Server 2012 |
| SQL Server 2014 | SQL Server 2017 <br/> SQL Server 2016 <br/> SQL Server 2014 <br/>| SQL Server 2017 <br/> SQL Server 2016 <br/> SQL Server 2014 <br/> SQL Server 2012 <br/> SQL Server 2008 R2 <br/> SQL Server 2008 |
| SQL Server 2012 | SQL Server 2017 <br/> SQL Server 2016 <br/> SQL Server 2014 <br/>SQL Server 2012 <br/> | SQL Server 2016 <br/> SQL Server 2014 <br/> SQL Server 2012 <br/> SQL Server 2008 R2 <br/> SQL Server 2008 | 
| SQL Server 2008 R2 <br/> SQL Server 2008 | SQL Server 2017 <br/> SQL Server 2016 <br/> SQL Server 2014 <br/>SQL Server 2012 <br/> SQL Server 2008 R2 <br/> SQL Server 2008 | SQL Server 2014 <br/> SQL Server 2012 <br/> SQL Server 2008 R2 <br/> SQL Server 2008 <br/>  |
| &nbsp; | &nbsp; | &nbsp; |

## <a name="requirements"></a>Требования

- При подключении используется аутентификация SQL между участниками репликации. 
- Общий ресурс учетной записи хранения Azure для рабочей папки, используемой для репликации. 
- Порт 445 (исходящий TCP-ПОРТ) должен быть открыт в правилах безопасности из подсети управляемого экземпляра, для доступа к файловым ресурсом Azure. 
- Порт 1433 (исходящий TCP) должен быть открыт, если издатель или распространитель находятся в Управляемом экземпляре, а подписчик — в локальной среде.

  >[!NOTE]
  > Может появиться ошибка 53 при подключении к файлу хранилища Azure, если исходящий сетевой безопасности группа сети порт 445 заблокирован при распространитель — базы данных экземпляра, а подписчик — в локальной. [Задайте для виртуальной сети группы безопасности сети](/azure/storage/files/storage-troubleshoot-windows-file-connection-problems) для устранения этой проблемы. 

### <a name="compare-data-sync-with-transactional-replication"></a>Сравнение синхронизации данных с репликацией транзакций

| | Синхронизация данных | Репликация транзакций |
|---|---|---|
| Преимущества | — Поддержка режима "активный — активный"<br/>— Двусторонняя передача данных между локальной базой данных и службой "База данных SQL Azure" | — Низкая задержка<br/>— Согласованность транзакций<br/>— Повторное использование существующей топологии после миграции |
| Недостатки | — Задержка 5 мин или дольше<br/>— Отсутствует согласованность транзакций<br/>— Большее влияние на производительность | — Не поддерживается публикация из отдельной базы данных SQL Azure или базы данных в составе пула.<br/>— Дорогое обслуживание |
| | | |

## <a name="common-configurations"></a>Распространенные конфигурации

Как правило, издатель и распространитель должны находиться либо в облаке, либо в локальной среде. Поддерживаются следующие конфигурации: 

### <a name="publisher-with-local-distributor-on-a-managed-instance"></a>Издатель с локальным распространителем в Управляемом экземпляре

![Единственный экземпляр в качестве издателя и распространителя](media/replication-with-sql-database-managed-instance/01-single-instance-asdbmi-pubdist.png)

Издатель и распространитель настроены в пределах единственного Управляемого экземпляра и распространяют изменения в другой локальный сервер SQL Server, Управляемый экземпляр или отдельную базу данных. В этой конфигурации нельзя настроить Управляемый экземпляр издателя или распространителя с [георепликацией и группами автоматической отработки отказа](sql-database-auto-failover-group.md).

### <a name="publisher-with-remote-distributor-on-a-managed-instance"></a>Издатель с удаленным распространителем в Управляемом экземпляре

В этой конфигурации один Управляемый экземпляр публикует изменения в распространителе, размещенном в другом Управляемом экземпляре, который может обслуживать множество исходных Управляемых экземпляров и распространять изменения на один или несколько целевых объектов в Управляемом экземпляре, отдельной базе данных, объединенной в пул базе данных или SQL Server.

![Раздельные экземпляры для издателя и распространителя](media/replication-with-sql-database-managed-instance/02-separate-instances-asdbmi-pubdist.png)

Издателя и распространителя можно настроить в двух Управляемых экземплярах. В этой конфигурации:

- оба Управляемые экземпляры находятся в той же виртуальной сети;
- оба Управляемые экземпляры находятся в том же расположении;
- Управляемые экземпляры, на которых размещены базы данных издателя и распространителя, не могут быть [геореплицированы с помощью групп автоматической отработки отказа](sql-database-auto-failover-group.md).

### <a name="publisher-and-distributor-on-premises-with-a-subscriber-on-a-single-pooled-and-instance-database"></a>Локальные издатель и распространитель с подписчиком в отдельной базе данных, базе данных в составе пула и базе данных экземпляра 

![База данных SQL Azure в качестве подписчика](media/replication-with-sql-database-managed-instance/03-azure-sql-db-subscriber.png)
 
В этой конфигурации База данных SQL Azure (отдельная база данных, база данных в составе пула и база данных экземпляра) является подписчиком. Эта конфигурация поддерживает миграцию из локальной среды в Azure. Если подписчик находится в отдельной базе данных или базе данных в составе пула, он должен находиться в режиме принудительной отправки.  


## <a name="next-steps"></a>Дальнейшие действия

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
