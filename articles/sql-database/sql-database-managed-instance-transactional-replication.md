---
title: Репликация транзакций с помощью Базы данных SQL Azure | Документация Майкрософт
description: Сведения об использовании репликации транзакций SQL Server с отдельными базами данных, базами данных в составе пула и базами данных экземпляров в Базе данных SQL Azure.
services: sql-database
ms.service: sql-database
ms.subservice: data-movement
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: MashaMSFT
ms.author: mathoma
ms.reviewer: carlrab
ms.date: 02/08/2019
ms.openlocfilehash: 86bd479eff48a7feb42557eb1d175345728f0a69
ms.sourcegitcommit: aa042d4341054f437f3190da7c8a718729eb675e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/09/2019
ms.locfileid: "68879056"
---
# <a name="transactional-replication-with-single-pooled-and-instance-databases-in-azure-sql-database"></a>Репликация транзакций с отдельными базами данных, базами данных в составе пула и базами данных экземпляров в Базе данных SQL Azure.

Репликация транзакций — это функция Базы данных SQL Azure и SQL Server, которая позволяет реплицировать данные из таблиц в базе данных SQL Azure или SQL Server в таблицы удаленных баз данных. Она обеспечивает синхронизацию множества таблиц в различных базах данных.

## <a name="when-to-use-transactional-replication"></a>Когда использовать репликацию транзакций

Репликация транзакций удобна в следующих случаях:
- Публикация изменений, внесенных в одной или нескольких таблицах в базе данных, и их распределение в одну или несколько баз данных SQL Server или SQL Azure, которые подписаны на изменения.
- Поддержание нескольких распределенных баз данных в синхронизированном состоянии.
- Перенос баз данных из одного SQL Server или управляемого экземпляра в другую базу данных путем непрерывной публикации изменений.

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

**Распространитель** — это экземпляр или сервер, который собирает изменения в статьях от издателя и распространяет их подписчикам. Распространителем может быть либо управляемый экземпляр базы данных SQL Azure, либо SQL Server (любая версия, если она равна или выше версии издателя). 

**Подписчик** — это экземпляр или сервер, который принимает изменения, внесенные в издателе. Подписчиками могут быть отдельные базы данных, базы данных в составе пула и базы данных экземпляров в Базе данных SQL Azure или в базах данных SQL Server. Подписчика отдельной базы данных или базы данных в пуле необходимо настроить как принудительного подписчика. 

| Role | Отдельные базы данных и базы данных в пуле | Базы данных экземпляров |
| :----| :------------- | :--------------- |
| **Издатель** | Нет | Да | 
| **Распространитель** | Нет | Да|
| **Подписчик по запросу** | Нет | Да|
| **Принудительный подписчик**| Да | Да|
| &nbsp; | &nbsp; | &nbsp; |

  >[!NOTE]
  > Подписка по запросу не поддерживается, если распространитель является базой данных экземпляра, а подписчик — нет. 

Существуют разные [типы репликации](https://docs.microsoft.com/sql/relational-databases/replication/types-of-replication).


| Репликация | Отдельные базы данных и базы данных в пуле | Базы данных экземпляра|
| :----| :------------- | :--------------- |
| [**Стандартная транзакционная**](https://docs.microsoft.com/sql/relational-databases/replication/transactional/transactional-replication) | Да (только в качестве подписчика) | Да | 
| [**Моментальных снимков**](https://docs.microsoft.com/sql/relational-databases/replication/snapshot-replication) | Да (только в качестве подписчика) | Да|
| [**Слиянием**](https://docs.microsoft.com/sql/relational-databases/replication/merge/merge-replication) | Нет | Нет|
| [**Одноранговая**](https://docs.microsoft.com/sql/relational-databases/replication/transactional/peer-to-peer-transactional-replication) | Нет | Нет|
| [**Двунаправленная**](https://docs.microsoft.com/sql/relational-databases/replication/transactional/bidirectional-transactional-replication) | Нет | Да|
| [**Обновляемых подписок**](https://docs.microsoft.com/sql/relational-databases/replication/transactional/updatable-subscriptions-for-transactional-replication) | Нет | Нет|
| &nbsp; | &nbsp; | &nbsp; |

  >[!NOTE]
  > - Попытка настроить репликацию с использованием более старой версии может привести к ошибке MSSQL_REPL20084 (The process could not connect to Subscriber (Процессу не удалось подключиться к Подписчику)) или MSSQ_REPL40532 ("Не удается открыть сервер \<имя>, запрашиваемый с использованием имени для входа. Вход в систему не выполнен").
  > - Чтобы использовать все функции Базы данных SQL Azure, необходимо использовать последние версии [SQL Server Management Studio (SSMS)](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) и [SQL Server Data Tools (SSDT)](https://docs.microsoft.com/sql/ssdt/download-sql-server-data-tools-ssdt).
  
  ### <a name="supportability-matrix-for-instance-databases-and-on-premises-systems"></a>Матрица поддержки для баз данных экземпляров и локальных систем
  Матрица поддержки репликации для баз данных экземпляров такая же, как и для локальной SQL Server. 
  
  | **Издатель**   | **Распространитель** | **Абонент** |
| :------------   | :-------------- | :------------- |
| SQL Server 2017 | SQL Server 2017 | SQL Server 2017 <br/> SQL Server 2016 <br/> SQL Server 2014 |
| SQL Server 2016 | SQL Server 2017 <br/> SQL Server 2016 | SQL Server 2017 <br/>SQL Server 2016 <br/> SQL Server 2014 <br/> SQL Server 2012 |
| SQL Server 2014 | SQL Server 2017 <br/> SQL Server 2016 <br/> SQL Server 2014 <br/>| SQL Server 2017 <br/> SQL Server 2016 <br/> SQL Server 2014 <br/> SQL Server 2012 <br/> SQL Server 2008 R2 <br/> SQL Server 2008 |
| SQL Server 2012 | SQL Server 2017 <br/> SQL Server 2016 <br/> SQL Server 2014 <br/>SQL Server 2012 <br/> | SQL Server 2016 <br/> SQL Server 2014 <br/> SQL Server 2012 <br/> SQL Server 2008 R2 <br/> SQL Server 2008 | 
| SQL Server 2008 R2 <br/> SQL Server 2008 | SQL Server 2017 <br/> SQL Server 2016 <br/> SQL Server 2014 <br/>SQL Server 2012 <br/> SQL Server 2008 R2 <br/> SQL Server 2008 | SQL Server 2014 <br/> SQL Server 2012 <br/> SQL Server 2008 R2 <br/> SQL Server 2008 <br/>  |
| &nbsp; | &nbsp; | &nbsp; |

## <a name="requirements"></a>Требования

- При подключении используется аутентификация SQL между участниками репликации. 
- Общий ресурс учетной записи хранения Azure для рабочей папки, используемой для репликации. 
- Порт 445 (исходящий трафик TCP) должен быть открыт в правилах безопасности подсети управляемого экземпляра для доступа к файловому ресурсу Azure. 
- Порт 1433 (исходящий трафик TCP) должен быть открыт, если издатель или распространитель находятся на управляемом экземпляре, а подписчик — в локальной среде.


>[!NOTE]
> - При подключении к файлу службы хранилища Azure может возникнуть ошибка 53, если порт исходящей сетевой группы безопасности (NSG) 445 заблокирован, если распространитель является экземпляром базы данных и подписчиком в локальной среде. Чтобы устранить эту проблему [, обновите NSG виртуальной сети](/azure/storage/files/storage-troubleshoot-windows-file-connection-problems) . 
> - Если базы данных издателя и распространителя в управляемых экземплярах используют [группы автоматической](sql-database-auto-failover-group.md)отработки отказа, администратор управляемого экземпляра должен [удалить все публикации на старом основном экземпляре и перенастроить их на новом первичном](sql-database-managed-instance-transact-sql-information.md#replication)экземпляре после отработки отказа.

### <a name="compare-data-sync-with-transactional-replication"></a>Сравнение синхронизации данных с репликацией транзакций

| | Синхронизация данных | Репликация транзакций |
|---|---|---|
| Преимущества | — Поддержка режима "активный — активный"<br/>— Двусторонняя передача данных между локальной базой данных и службой "База данных SQL Azure" | — Низкая задержка<br/>— Согласованность транзакций<br/>— Повторное использование существующей топологии после миграции |
| Недостатки | — Задержка 5 мин или дольше<br/>— Отсутствует согласованность транзакций<br/>— Большее влияние на производительность | — Не поддерживается публикация из отдельной базы данных SQL Azure или базы данных в пуле.<br/>— Дорогое обслуживание |
| | | |

## <a name="common-configurations"></a>Распространенные конфигурации

Как правило, издатель и распространитель должны находиться либо в облаке, либо в локальной среде. Поддерживаются следующие конфигурации: 

### <a name="publisher-with-local-distributor-on-a-managed-instance"></a>Издатель с локальным распространителем на управляемом экземпляре

![Единственный экземпляр в качестве издателя и распространителя](media/replication-with-sql-database-managed-instance/01-single-instance-asdbmi-pubdist.png)

Издатель и распространитель настраиваются в пределах одного управляемого экземпляра и распределяют изменения в другие управляемые экземпляры, отдельные базы данных, базы данных в составе пула или SQL Server локально. 

### <a name="publisher-with-remote-distributor-on-a-managed-instance"></a>Издатель с удаленным распространителем на управляемом экземпляре

В этой конфигурации один управляемый экземпляр публикует изменения в распространителе, размещенные на другом управляемом экземпляре, который может обслуживать множество исходных управляемых экземпляров и распространять изменения на один или несколько целевых объектов в управляемом экземпляре, отдельной базе данных, в составе пула или SQL Server.

![Раздельные экземпляры для издателя и распространителя](media/replication-with-sql-database-managed-instance/02-separate-instances-asdbmi-pubdist.png)

Издателя и распространителя можно настроить в двух управляемых экземплярах. В этой конфигурации есть некоторые ограничения: 

- Оба управляемых экземпляра находятся в одной виртуальной сети.
- Оба управляемые экземпляры находятся в том же расположении.


### <a name="publisher-and-distributor-on-premises-with-a-subscriber-on-a-single-pooled-and-instance-database"></a>Локальные издатель и распространитель с подписчиком в отдельной базе данных, базе данных в составе пула и базе данных экземпляра 

![База данных SQL Azure в качестве подписчика](media/replication-with-sql-database-managed-instance/03-azure-sql-db-subscriber.png)
 
В этой конфигурации База данных SQL Azure (отдельная база данных, база данных в составе пула и база данных экземпляра) является подписчиком. Эта конфигурация поддерживает миграцию из локальной среды в Azure. Если подписчик находится в отдельной базе данных или базе данных в пуле, он должен находиться в режиме принудительной отправки.  


## <a name="next-steps"></a>Следующие шаги

1. [Настройка репликации между двумя управляемыми экземплярами](replication-with-sql-database-managed-instance.md). 
1. [Создание публикации](https://docs.microsoft.com/sql/relational-databases/replication/publish/create-a-publication).
1. [Создайте принудительную подписку](https://docs.microsoft.com/sql/relational-databases/replication/create-a-push-subscription), используя имя сервера Базы данных SQL Azure в качестве подписчика (например, `N'azuresqldbdns.database.windows.net`) и имя базы данных SQL Azure в качестве целевой базы данных (например, **Adventureworks**). )
1. Сведения об [ограничениях репликации транзакций для управляемого экземпляра](sql-database-managed-instance-transact-sql-information.md#replication)



## <a name="see-also"></a>См. также  

- [Репликация с помощью MI и группы отработки отказа](sql-database-managed-instance-transact-sql-information.md#replication)
- [Репликация в Базу данных SQL](replication-to-sql-database.md)
- [Репликация на управляемый экземпляр](replication-with-sql-database-managed-instance.md)
- [Создание публикации](https://docs.microsoft.com/sql/relational-databases/replication/publish/create-a-publication)
- [Создание принудительной подписки](https://docs.microsoft.com/sql/relational-databases/replication/create-a-push-subscription/)
- [Типы репликации](https://docs.microsoft.com/sql/relational-databases/replication/types-of-replication)
- [Мониторинг (репликация)](https://docs.microsoft.com/sql/relational-databases/replication/monitor/monitoring-replication)
- [Инициализация подписки](https://docs.microsoft.com/sql/relational-databases/replication/initialize-a-subscription)  
