---
title: Репликация транзакций
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
ms.openlocfilehash: 41dd336bdb74fbe745ab48ebd3c168af0492ae2c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75691001"
---
# <a name="transactional-replication-with-single-pooled-and-instance-databases-in-azure-sql-database"></a>Репликация транзакций с отдельными базами данных, базами данных в составе пула и базами данных экземпляров в Базе данных SQL Azure.

Репликация транзакций — это функция Базы данных SQL Azure и SQL Server, которая позволяет реплицировать данные из таблиц в базе данных SQL Azure или SQL Server в таблицы удаленных баз данных. Она обеспечивает синхронизацию множества таблиц в различных базах данных.

## <a name="when-to-use-transactional-replication"></a>Когда использовать репликацию транзакций

Репликация транзакций удобна в следующих случаях:
- Публикация изменений, внесенных в одной или нескольких таблицах в базе данных, и их распределение в одну или несколько баз данных SQL Server или SQL Azure, которые подписаны на изменения.
- Поддержание нескольких распределенных баз данных в синхронизированном состоянии.
- Переносите базы данных с одного сервера или управляемого экземпляра в другую базу данных, непрерывно публикуя изменения.

## <a name="overview"></a>Обзор

На следующем рисунке показаны ключевые компоненты репликации транзакций:  

![Репликация с Базой данных SQL](media/replication-to-sql-database/replication-to-sql-database.png)

**Издатель** — это экземпляр или сервер, который публикует изменения, внесенные в некоторых таблицах (статьях), отправляя обновления распространителю. Публикация в базу данных SQL Azure с локального SQL Server поддерживается в следующих версиях SQL Server:

- SQL Server 2019 (предварительная версия)
- с SQL Server 2016 в SQL 2017;
- SQL Server 2014 с пакетом обновления 1(SP1) и накопительным пакетом обновления 3 (CU3) или более поздней версии (12.00.4427);
- SQL Server 2014 RTM с накопительным пакетом обновления 10 (CU10) (12.00.2556);
- SQL Server 2012 с пакетом обновления 3 (SP3) или более поздней версии (11.0.6020);
- SQL Server 2012 с пакетом обновления 2 (SP2) и накопительным пакетом обновления 8 (CU8) (11.0.5634.0).
- Для других версий SQL Server, которые не поддерживают публикацию в объекты в Azure, можно использовать метод [повторной публикации данных](https://docs.microsoft.com/sql/relational-databases/replication/republish-data), чтобы перемещать данные в более новые версии SQL Server. 

**Распространитель** — это экземпляр или сервер, который собирает изменения в статьях от издателя и распространяет их подписчикам. Дистрибьютором может быть либо управляемый экземпляр базы данных Azure S'L, либо сервер S'L (любая версия, пока она равна или выше, чем версия Издателя). 

**Подписчик** — это экземпляр или сервер, который принимает изменения, внесенные в издателе. Подписчиками могут быть отдельные базы данных, базы данных в составе пула и базы данных экземпляров в Базе данных SQL Azure или в базах данных SQL Server. Подписчика отдельной базы данных или базы данных в пуле необходимо настроить как принудительного подписчика. 

| Роль | Отдельные базы данных и базы данных в пуле | Базы данных экземпляров |
| :----| :------------- | :--------------- |
| **Издателя** | нет | Да | 
| **Распространитель** | нет | Да|
| **Подписчик по запросу** | нет | Да|
| **Принудительный подписчик**| Да | Да|
| &nbsp; | &nbsp; | &nbsp; |

  >[!NOTE]
  > Подписка pull не поддерживается, когда дистрибьютор является базой данных Instance, а абонент омрачить его нет. 

Существуют разные [типы репликации](https://docs.microsoft.com/sql/relational-databases/replication/types-of-replication).


| Репликация | Отдельные базы данных и базы данных в пуле | Базы данных экземпляра|
| :----| :------------- | :--------------- |
| [**Стандартные транзакционные**](https://docs.microsoft.com/sql/relational-databases/replication/transactional/transactional-replication) | Да (только в качестве подписчика) | Да | 
| [**Снимок**](https://docs.microsoft.com/sql/relational-databases/replication/snapshot-replication) | Да (только в качестве подписчика) | Да|
| [**Репликация слияния**](https://docs.microsoft.com/sql/relational-databases/replication/merge/merge-replication) | нет | нет|
| [**Одноранговая**](https://docs.microsoft.com/sql/relational-databases/replication/transactional/peer-to-peer-transactional-replication) | нет | нет|
| [**Двунаправленная**](https://docs.microsoft.com/sql/relational-databases/replication/transactional/bidirectional-transactional-replication) | нет | Да|
| [**Updatable подписки**](https://docs.microsoft.com/sql/relational-databases/replication/transactional/updatable-subscriptions-for-transactional-replication) | нет | нет|
| &nbsp; | &nbsp; | &nbsp; |

  >[!NOTE]
  > - Попытка настроить репликацию с использованием более старой версии может привести к ошибке MSSQL_REPL20084 (The process could not connect to Subscriber (Процессу не удалось подключиться к Подписчику)) или MSSQ_REPL40532 ("Не удается открыть сервер \<имя>, запрашиваемый с использованием имени для входа. Вход в систему не выполнен").
  > - Чтобы использовать все функции Базы данных SQL Azure, необходимо использовать последние версии [SQL Server Management Studio (SSMS)](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) и [SQL Server Data Tools (SSDT)](https://docs.microsoft.com/sql/ssdt/download-sql-server-data-tools-ssdt).
  
  ### <a name="supportability-matrix-for-instance-databases-and-on-premises-systems"></a>Матрица поддержки для баз данных инстанций и систем на территории
  Матрица поддержки репликации, например, база данных такая же, как и в предварительном помещении сервера S'L Server. 
  
| **Издателя**   | **Распространитель** | **Абонент** |
| :------------   | :-------------- | :------------- |
| SQL Server 2019 | SQL Server 2019 | SQL Server 2019 <br/> SQL Server 2017 <br/> SQL Server 2016 <br/>  |
| SQL Server 2017 | SQL Server 2019 <br/>SQL Server 2017 | SQL Server 2019 <br/> SQL Server 2017 <br/> SQL Server 2016 <br/> SQL Server 2014 |
| SQL Server 2016 | SQL Server 2019 <br/>SQL Server 2017 <br/> SQL Server 2016 | SQL Server 2019 <br/> SQL Server 2017 <br/>SQL Server 2016 <br/> SQL Server 2014 <br/> SQL Server 2012 |
| SQL Server 2014 | SQL Server 2019 <br/> SQL Server 2017 <br/> SQL Server 2016 <br/> SQL Server 2014 <br/>| SQL Server 2017 <br/> SQL Server 2016 <br/> SQL Server 2014 <br/> SQL Server 2012 <br/> SQL Server 2008 R2 <br/> SQL Server 2008 |
| SQL Server 2012 | SQL Server 2019 <br/> SQL Server 2017 <br/> SQL Server 2016 <br/> SQL Server 2014 <br/>SQL Server 2012 <br/> | SQL Server 2016 <br/> SQL Server 2014 <br/> SQL Server 2012 <br/> SQL Server 2008 R2 <br/> SQL Server 2008 | 
| SQL Server 2008 R2 <br/> SQL Server 2008 | SQL Server 2019 <br/> SQL Server 2017 <br/> SQL Server 2016 <br/> SQL Server 2014 <br/>SQL Server 2012 <br/> SQL Server 2008 R2 <br/> SQL Server 2008 |  SQL Server 2014 <br/> SQL Server 2012 <br/> SQL Server 2008 R2 <br/> SQL Server 2008 <br/>  |
| &nbsp; | &nbsp; | &nbsp; |

## <a name="requirements"></a>Требования

- При подключении используется аутентификация SQL между участниками репликации. 
- Общий ресурс учетной записи хранения Azure для рабочей папки, используемой для репликации. 
- Порт 445 (исходящий TCP) должен быть открыт в правилах безопасности подсети управляемого экземпляра для доступа к доле файла Azure. 
- Порт 1433 (Исходящий TCP) должен быть открыт, если Издатель/Дистрибьютор находится на управляемом экземпляре, а абонент находится на месте.
- Все типы участников репликации (Издатель, Дистрибьютор, Pull Абонент, и Push Абонент) могут быть размещены на управляемых экземпляров, но издатель и дистрибьютор должен быть либо в облаке или как на месте.
- Если либо издатель, дистрибьютор, и / или абонента существуют в различных виртуальных сетях, то VPN пиринг должен быть создан между каждой организацией, таким образом, что есть VPN пиринг между издателем и дистрибьютором, и / или есть VPN пиринг между дистрибьютором и абонентом. 


>[!NOTE]
> - Вы можете столкнуться с ошибкой 53 при подключении к файлу хранения Azure, если порт 445 группы безопасности исходящих сетей (NSG) заблокирован, когда дистрибьютор является базой данных экземпляров и абонент находится на месте. [Обновление vNet NSG](/azure/storage/files/storage-troubleshoot-windows-file-connection-problems) для решения этой проблемы. 


### <a name="compare-data-sync-with-transactional-replication"></a>Сравнение синхронизации данных с репликацией транзакций

| | Синхронизация данных | репликация транзакций |
|---|---|---|
| Преимущества | — Поддержка режима "активный — активный"<br/>— Двусторонняя передача данных между локальной базой данных и службой "База данных SQL Azure" | — Низкая задержка<br/>— Согласованность транзакций<br/>— Повторное использование существующей топологии после миграции |
| Недостатки | — Задержка 5 мин или дольше<br/>— Отсутствует согласованность транзакций<br/>— Большее влияние на производительность | — Не поддерживается публикация из отдельной базы данных SQL Azure или базы данных в пуле.<br/>— Дорогое обслуживание |
| | | |

## <a name="common-configurations"></a>Распространенные конфигурации

Как правило, издатель и распространитель должны находиться либо в облаке, либо в локальной среде. Поддерживаются следующие конфигурации: 

### <a name="publisher-with-local-distributor-on-a-managed-instance"></a>Издатель с местным дистрибьютором на управляемом экземпляре

![Единственный экземпляр в качестве издателя и распространителя](media/replication-with-sql-database-managed-instance/01-single-instance-asdbmi-pubdist.png)

Издателя и дистрибьютора настраивают в пределах одного управляемого экземпляра и распределяют изменения в другой управляемый экземпляр, единую базу данных, объединенную базу данных или на базе данных S'L Server. 

### <a name="publisher-with-remote-distributor-on-a-managed-instance"></a>Издатель с удаленным дистрибьютором на управляемом экземпляре

В этой конфигурации один управляемый экземпляр публикует изменения в дистрибуции, размещенные на другом управляемом экземпляре, который может обслуживать многие экземпляры, управляемые исходными данными, и распространять изменения по одной или многим целям в управляемом экземпляре, единой базе данных, объединенной базе данных или Сервер S'L.

![Раздельные экземпляры для издателя и распространителя](media/replication-with-sql-database-managed-instance/02-separate-instances-asdbmi-pubdist.png)

Издателя и распространителя можно настроить в двух управляемых экземплярах. Есть некоторые ограничения с этой конфигурацией: 

- Оба управляемых экземпляра находятся на одном vNet.
- Оба управляемые экземпляры находятся в том же расположении.


### <a name="publisher-and-distributor-on-premises-with-a-subscriber-on-a-single-pooled-and-instance-database"></a>Локальные издатель и распространитель с подписчиком в отдельной базе данных, базе данных в составе пула и базе данных экземпляра 

![База данных SQL Azure в качестве подписчика](media/replication-with-sql-database-managed-instance/03-azure-sql-db-subscriber.png)
 
В этой конфигурации База данных SQL Azure (отдельная база данных, база данных в составе пула и база данных экземпляра) является подписчиком. Эта конфигурация поддерживает миграцию из локальной среды в Azure. Если подписчик находится в отдельной базе данных или базе данных в пуле, он должен находиться в режиме принудительной отправки.  

## <a name="with-failover-groups"></a>С группами неудач

Если георепликация включена в экземпляре **издателя** или **дистрибьютора** в [группе failover,](sql-database-auto-failover-group.md)администратор управляемого экземпляра должен очистить все публикации на старом первичном и перенастроить их на новый основной после сбоя происходит. В этом сценарии необходимы следующие действия:

1. Остановить все задания репликации, работающие в базе данных, если таковые имеются.
2. Отбросьте метаданные подписки от издателя, запустив следующий сценарий в базе данных издателя:

   ```sql
   EXEC sp_dropsubscription @publication='<name of publication>', @article='all',@subscriber='<name of subscriber>'
   ```             
 
1. Удаление метаданных подписки от абонента. Выполнить следующий скрипт в базе данных подписки на экземпляре абонента:

   ```sql
   EXEC sp_subscription_cleanup
      @publisher = N'<full DNS of publisher, e.g. example.ac2d23028af5.database.windows.net>', 
      @publisher_db = N'<publisher database>', 
      @publication = N'<name of publication>'; 
   ```                

1. Принудительно отбросьте все объекты репликации от издателя, запустив следующий скрипт в опубликованной базе данных:

   ```sql
   EXEC sp_removedbreplication
   ```

1. Принудительно отбросьте старого дистрибьютора из исходного первичного экземпляра (если не удается вернуться к старой первичной, которая используется для дистрибьютора). Выполнить следующий скрипт в основной базе данных в старом экземпляре управляемого дистрибьютора:

   ```sql
   EXEC sp_dropdistributor 1,1
   ```

Если георепликация включена в экземпляре **абонента** в группе сбоев, публикация должна быть настроена для подключения к конечная точка слушателя группы сбоев для экземпляра, управляемого абонентом. В случае сбоя последующие действия администратора управляемого экземпляра зависят от типа сбоя: 

- Для сбоя без потери данных репликация продолжит работать после сбоя. 
- Для сбоя с потерей данных, репликация будет работать также. Он будет воспроизводить потерянные изменения снова. 
- Для сбоя с потерей данных, но потеря данных находится за пределами периода хранения базы данных распределения, администратору управляемого экземпляра необходимо будет переиздать базу данных подписки. 

## <a name="next-steps"></a>Дальнейшие действия

- [Настройка репликации между издателем MI и абонентом](replication-with-sql-database-managed-instance.md)
- [Настройка репликации между издателем MI, дистрибьютором MI и абонентом сервера S'L](sql-database-managed-instance-configure-replication-tutorial.md)
- [Создайте публикацию](https://docs.microsoft.com/sql/relational-databases/replication/publish/create-a-publication).
- [Создайте принудительную подписку](https://docs.microsoft.com/sql/relational-databases/replication/create-a-push-subscription), используя имя сервера Базы данных SQL Azure в качестве подписчика (например, `N'azuresqldbdns.database.windows.net`) и имя базы данных SQL Azure в качестве целевой базы данных (например, **Adventureworks**). )


Для получения дополнительной информации о настройке транзакционной репликации см.



## <a name="see-also"></a>См. также  

- [Репликация с MI и группой неудач](sql-database-managed-instance-transact-sql-information.md#replication)
- [Репликация в Базу данных SQL](replication-to-sql-database.md)
- [Репликация к управляемому экземпляру](replication-with-sql-database-managed-instance.md)
- [Создание публикации](https://docs.microsoft.com/sql/relational-databases/replication/publish/create-a-publication)
- [Создание принудительной подписки](https://docs.microsoft.com/sql/relational-databases/replication/create-a-push-subscription/)
- [Типы репликации](https://docs.microsoft.com/sql/relational-databases/replication/types-of-replication)
- [Наблюдение (репликация)](https://docs.microsoft.com/sql/relational-databases/replication/monitor/monitoring-replication)
- [Инициализация подписки](https://docs.microsoft.com/sql/relational-databases/replication/initialize-a-subscription)  
