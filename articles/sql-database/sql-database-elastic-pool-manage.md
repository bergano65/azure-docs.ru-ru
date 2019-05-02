---
title: Управление эластичными пулами - базы данных Azure SQL | Документация Майкрософт
description: Создание эластичных пулов в базе данных SQL Azure и управление ими.
services: sql-database
ms.service: sql-database
ms.subservice: elastic-pools
ms.custom: seoapril2019
ms.devlang: ''
ms.topic: conceptual
author: oslake
ms.author: moslake
ms.reviewer: carlrab
manager: craigg
ms.date: 03/12/2019
ms.openlocfilehash: debf91f04cff3cb9705ebc5915e2e665679230a9
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/18/2019
ms.locfileid: "59267588"
---
# <a name="manage-elastic-pools-in-azure-sql-database"></a>Управление эластичными пулами базы данных SQL Azure

С эластичным пулом вы определяете количество ресурсов, которое необходимо эластичному пулу для обработки рабочей нагрузки баз данных, входящих в пул, и количество ресурсов для каждой базы данных в пуле.

## <a name="azure-portal-manage-elastic-pools-and-pooled-databases"></a>Портал Azure: управление эластичными пулами и базами данных в пуле

Все параметры пула можно найти в колонке **Настройка пула**. Чтобы перейти к ней, найдите эластичный пул на портале и щелкните **Настроить пул** в верхней части колонки или в меню ресурсов слева.

Здесь можно внести любое сочетание следующих изменений и сохранить их массово:

1. Изменить уровень служб пула.
2. Увеличить или уменьшить уровень производительности (единиц DTU или виртуальных ядер) и размер хранилища.
3. Добавить базы данных в пул или удалить их.
4. Установить минимальное (гарантированное) и максимальное ограничения производительности для баз данных в пулах.
5. Просмотреть сводку затрат, чтобы узнать о любых изменениях в счете в результате выбора новых значений.

![Колонка настройки эластичного пула](./media/sql-database-elastic-pool-manage-portal/configure-pool.png)

## <a name="powershell-manage-elastic-pools-and-pooled-databases"></a>PowerShell: управление эластичными пулами и базами данных в пуле

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> Модуль PowerShell Azure Resource Manager по-прежнему поддерживается базой данных SQL Azure, но все будущие разработки — для модуля Az.Sql. Для этих командлетов см. в разделе [AzureRM.Sql](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). Аргументы для команд в модуле Az и в модуле AzureRm практически идентичны.

Для создания эластичных пулов и баз данных в пуле для базы данных SQL и управления ими с помощью Azure PowerShell используйте приведенные ниже командлеты PowerShell. Если вам нужно выполнить установку или обновление PowerShell, см. статью [об установке модуля Azure PowerShell](/powershell/azure/install-az-ps). Сведения о создании серверов Базы данных SQL для эластичного пула и управлении ими см. в [этой статье](sql-database-servers.md). Сведения о создании правил брандмауэра и управлении ими с помощью PowerShell см. в [этой статье](sql-database-firewall-configure.md#manage-server-level-ip-firewall-rules-using-azure-powershell).

> [!TIP]
> Образцы скриптов PowerShell см. в статьях [Создание эластичных пулов и перемещение баз данных между пулами и из пула с помощью PowerShell](scripts/sql-database-move-database-between-pools-powershell.md) и [Отслеживание и масштабирование эластичного пула SQL в Базе данных SQL Azure с помощью PowerShell](scripts/sql-database-monitor-and-scale-pool-powershell.md).
>

| Командлет | ОПИСАНИЕ |
| --- | --- |
|[New-AzSqlElasticPool](/powershell/module/az.sql/new-azsqlelasticpool)|Создает эластичный пул.|
|[Get-AzSqlElasticPool](/powershell/module/az.sql/get-azsqlelasticpool)|Получает эластичные пулы и значения их свойств.|
|[Set-AzSqlElasticPool](/powershell/module/az.sql/set-azsqlelasticpool)|Изменяет свойства эластичного пула. Например, используйте свойство **StorageMB** для изменения максимального размера хранилища эластичного пула.|
|[Remove-AzSqlElasticPool](/powershell/module/az.sql/remove-azsqlelasticpool)|Удаляет эластичный пул.|
|[Get-AzSqlElasticPoolActivity](/powershell/module/az.sql/get-azsqlelasticpoolactivity)|Получает состояние операций в эластичном пуле.|
|[New-AzSqlDatabase](/powershell/module/az.sql/new-azsqldatabase)|Создает новую базу данных в существующем пуле или отдельную базу данных. |
|[Get-AzSqlDatabase](/powershell/module/az.sql/get-azsqldatabase)|Получает одну или несколько баз данных.|
|[Set-AzSqlDatabase](/powershell/module/az.sql/set-azsqldatabase)|Определяет свойства базы данных или перемещает ее в эластичный пул, из пула либо между пулами.|
|[Remove-AzSqlDatabase](/powershell/module/az.sql/remove-azsqldatabase)|Удаляет базу данных.|

> [!TIP]
> Создание большого количества баз данных в эластичном пуле может занять некоторое время, если эта операция выполняется с помощью портала или командлетов PowerShell, которые создают базы данных поочередно. Сведения об автоматическом создании баз данных в эластичном пуле см. в документе [CreateOrUpdateElasticPoolAndPopulate](https://gist.github.com/billgib/d80c7687b17355d3c2ec8042323819ae).

## <a name="azure-cli-manage-elastic-pools-and-pooled-databases"></a>Azure CLI: управление эластичными пулами и базами данных в пуле

Для создания эластичных пулов базы данных SQL с помощью [Azure CLI](/cli/azure) используйте приведенные ниже команды [Azure CLI для базы данных SQL](/cli/azure/sql/db). Запускайте интерфейс командной строки в браузере с помощью [Cloud Shell](/azure/cloud-shell/overview) либо [установите](/cli/azure/install-azure-cli) его на платформе macOS, Linux или Windows.

> [!TIP]
> Примеры скриптов для Azure CLI см. в статьях [Перемещение базы данных Azure SQL в эластичном пуле SQL с помощью интерфейса командной строки](scripts/sql-database-move-database-between-pools-cli.md) и [Масштабирование эластичного пула SQL в Базе данных SQL Azure с помощью Azure CLI](scripts/sql-database-scale-pool-cli.md).
>

| Командлет | ОПИСАНИЕ |
| --- | --- |
|[az sql elastic-pool create](/cli/azure/sql/elastic-pool#az-sql-elastic-pool-create)|Создает эластичный пул.|
|[az sql elastic-pool list](/cli/azure/sql/elastic-pool#az-sql-elastic-pool-list)|Возвращает список эластичных пулов на сервере.|
|[az sql elastic-pool list-dbs](/cli/azure/sql/elastic-pool#az-sql-elastic-pool-list-dbs)|Возвращает список баз данных в пуле эластичных баз данных.|
|[az sql elastic-pool list-editions](/cli/azure/sql/elastic-pool#az-sql-elastic-pool-list-editions)|Также содержит параметры доступных DTU пула, ограничений хранилища и параметры отдельных баз данных. Чтобы снизить уровень детализации, ограничения дополнительного хранилища и параметры каждой базы данных скрыты по умолчанию.|
|[az sql elastic-pool update](/cli/azure/sql/elastic-pool#az-sql-elastic-pool-update)|Обновляет эластичный пул.|
|[az sql elastic-pool delete](/cli/azure/sql/elastic-pool#az-sql-elastic-pool-delete)|Удаляет эластичный пул.|

## <a name="transact-sql-manage-pooled-databases"></a>Transact-SQL: управление базами данных в пуле

Для создания и перемещения баз данных в существующих эластичных пулах или для возвращения сведений о пуле эластичных баз данных SQL с помощью Transact-SQL используйте следующие команды T-SQL. Можно выполнить эти команды на портале Azure, в [SQL Server Management Studio](/sql/ssms/use-sql-server-management-studio), [Visual Studio Code](https://code.visualstudio.com/docs) или любой другой программе, которая может подключаться к серверу Базы данных SQL Azure и передавать команды Transact-SQL. Сведения о создании правил брандмауэра и управлении ими с помощью Transact-SQL см. в [этой статье](sql-database-firewall-configure.md#manage-ip-firewall-rules-using-transact-sql).

> [!IMPORTANT]
> С помощью Transact-SQL невозможно создать, обновить или удалить пул эластичных баз данных SQL Azure. Вы можете добавить или удалить базы данных из эластичного пула, а также вернуть сведения о существующих эластичных пулах, используя динамические административные представления.
>

| Команда | ОПИСАНИЕ |
| --- | --- |
|[CREATE DATABASE (база данных SQL Azure)](/sql/t-sql/statements/create-database-azure-sql-database)|Создает новую базу данных в существующем пуле или отдельную базу данных. Для создания базы данных требуется подключение к базе данных master.|
| [ALTER DATABASE (база данных SQL Azure)](/sql/t-sql/statements/alter-database-azure-sql-database) |Перемещает базы данных из, в или между эластичными пулами.|
|[DROP DATABASE (Transact-SQL)](/sql/t-sql/statements/drop-database-transact-sql)|Удаляет базу данных.|
|[sys.elastic_pool_resource_stats (база данных SQL Azure)](/sql/relational-databases/system-catalog-views/sys-elastic-pool-resource-stats-azure-sql-database)|Возвращает статистику использования ресурсов для всех пулов эластичных баз данных на сервере Базы данных SQL. Для каждого эластичного пула имеется одна строка на каждые 15 секунд окна отчета (четыре строки в минуту). Сюда входят сведения об использовании ЦП, хранилища, операциях ввода-вывода, журнал, а также использование параллельных запросов и сеансов всеми базами данных в пуле.|
|[sys.database_service_objectives (база данных SQL Azure)](/sql/relational-databases/system-catalog-views/sys-database-service-objectives-azure-sql-database)|Возвращает сведения о выпуске (уровень служб), целевой службе (ценовую категорию), а также имя эластичного пула (при наличии) для базы данных SQL Azure или хранилища данных SQL Azure. В системе базы данных master на сервере Базы данных SQL Azure возвращает сведения обо всех базах данных. Для использования хранилища данных SQL Azure необходимо подключиться к базе данных master.|

## <a name="rest-api-manage-elastic-pools-and-pooled-databases"></a>REST API: управление эластичными пулами и базами данных в пуле

Для создания эластичных пулов и базах данных в пуле для базы данных SQL и управления ими используйте эти запросы REST API.

| Команда | ОПИСАНИЕ |
| --- | --- |
|[Создание или обновление эластичных пулов](https://docs.microsoft.com/rest/api/sql/elasticpools/createorupdate)|Создает эластичный пул или обновляет имеющийся.|
|[Удаление эластичных пулов](https://docs.microsoft.com/rest/api/sql/elasticpools/delete)|Удаляет эластичный пул.|
|[Получение эластичных пулов](https://docs.microsoft.com/rest/api/sql/elasticpools/get)|Получает эластичный пул.|
|[Список эластичных пулов на сервере](https://docs.microsoft.com/rest/api/sql/elasticpools/listbyserver)|Возвращает список эластичных пулов на сервере.|
|[Обновление эластичных пулов](https://docs.microsoft.com/rest/api/sql/elasticpools/listbyserver)|Обновляет имеющийся эластичный пул.|
|[Действия эластичного пула](https://docs.microsoft.com/rest/api/sql/elasticpoolactivities)|Возвращает действия эластичного пула.|
|[Действия базы данных в эластичном пуле](https://docs.microsoft.com/rest/api/sql/elasticpooldatabaseactivities)|Возвращает действия баз данных в эластичном пуле.|
|[Базы данных: создание или обновление](https://docs.microsoft.com/rest/api/sql/databases/createorupdate)|Создает новую базу данных или обновляет имеющуюся.|
|[Базы данных: получение](https://docs.microsoft.com/rest/api/sql/databases/get)|Получает базу данных.|
|[Базы данных: вывод списка по эластичному пулу](https://docs.microsoft.com/rest/api/sql/databases/listbyelasticpool)|Возвращает список баз данных в пуле эластичных баз данных.|
|[Базы данных: вывод списка по серверу](https://docs.microsoft.com/rest/api/sql/databases/listbyserver)|Возвращает список баз данных на сервере.|
|[Базы данных: обновление](https://docs.microsoft.com/rest/api/sql/databases/update)|Обновляет имеющуюся базу данных.|

## <a name="next-steps"></a>Дальнейшие действия

* Дополнительные сведения о шаблонах разработки для приложений SaaS, использующих пулы эластичных БД, см. в статье [Шаблоны разработки для мультитенантных приложений SaaS с использованием базы данных Azure SQL](sql-database-design-patterns-multi-tenancy-saas-applications.md).
* Дополнительные сведения об эластичных пулах см. в статье [Общие сведения о приложении SaaS Wingtip](sql-database-wtp-overview.md).
