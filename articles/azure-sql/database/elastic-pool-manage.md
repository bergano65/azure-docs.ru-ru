---
title: Управление пулами эластичных БД
description: Создавайте эластичные пулы базы данных SQL Azure и управляйте ими с помощью портал Azure, PowerShell, Azure CLI, Transact-SQL (T-SQL) и API-интерфейса RESTful.
services: sql-database
ms.service: sql-database
ms.subservice: elastic-pools
ms.topic: conceptual
author: oslake
ms.author: moslake
ms.reviewer: sstein
ms.date: 03/12/2019
ms.custom: seoapril2019 sqldbrb=1
ms.openlocfilehash: bd3917c42859d4240fbb3a75f951ff38a548e204
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/09/2020
ms.locfileid: "91330814"
---
# <a name="manage-elastic-pools-in-azure-sql-database"></a>Управление эластичными пулами в базе данных SQL Azure
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

С эластичным пулом вы определяете количество ресурсов, которое необходимо эластичному пулу для обработки рабочей нагрузки баз данных, входящих в пул, и количество ресурсов для каждой базы данных в пуле.

## <a name="azure-portal"></a>Портал Azure

Все параметры пула можно найти в колонке **Настройка пула**. Чтобы получить здесь, найдите эластичный пул в портал Azure и щелкните **Настройка пула** в верхней части колонки или в меню ресурсов слева.

Здесь можно внести любое сочетание следующих изменений и сохранить их массово:

1. Изменить уровень служб пула.
2. Увеличить или уменьшить уровень производительности (единиц DTU или виртуальных ядер) и размер хранилища.
3. Добавить базы данных в пул или удалить их.
4. Установить минимальное (гарантированное) и максимальное ограничения производительности для баз данных в пулах.
5. Просмотреть сводку затрат, чтобы узнать о любых изменениях в счете в результате выбора новых значений.

![Колонка настройки эластичного пула](./media/elastic-pool-manage/configure-pool.png)

## <a name="powershell"></a>PowerShell

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]
> [!IMPORTANT]
> Модуль PowerShell Azure Resource Manager по-прежнему поддерживается базой данных SQL Azure, но вся будущая разработка сосредоточена на модуле Az.Sql. Сведения об этих командлетах см. в разделе [AzureRM.Sql](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). Аргументы команд в модулях Az и AzureRm практически идентичны.

Для создания эластичных пулов и баз данных в пуле для базы данных SQL и управления ими с помощью Azure PowerShell используйте приведенные ниже командлеты PowerShell. Если вам нужно выполнить установку или обновление PowerShell, см. статью [об установке модуля Azure PowerShell](/powershell/azure/install-az-ps). Сведения о создании серверов для эластичного пула и управлении ими см. в разделе [Создание серверов и управление ими](logical-servers.md). Сведения о создании правил брандмауэра и управлении ими с помощью PowerShell см. в [этой статье](firewall-configure.md#use-powershell-to-manage-server-level-ip-firewall-rules).

> [!TIP]
> Образцы скриптов PowerShell см. в статьях [Создание эластичных пулов и перемещение баз данных между пулами и из пула с помощью PowerShell](scripts/move-database-between-elastic-pools-powershell.md) и [Отслеживание и масштабирование эластичного пула SQL в Базе данных SQL Azure с помощью PowerShell](scripts/monitor-and-scale-pool-powershell.md).
>

| Командлет | Описание |
| --- | --- |
|[New-AzSqlElasticPool](/powershell/module/az.sql/new-azsqlelasticpool)|Создает эластичный пул.|
|[Get-Азсклеластикпул](/powershell/module/az.sql/get-azsqlelasticpool)|Получает эластичные пулы и значения их свойств.|
|[Set-AzSqlElasticPool](/powershell/module/az.sql/set-azsqlelasticpool)|Изменяет свойства эластичного пула. Например, используйте свойство **StorageMB** для изменения максимального размера хранилища эластичного пула.|
|[Remove-Азсклеластикпул](/powershell/module/az.sql/remove-azsqlelasticpool)|Удаляет эластичный пул.|
|[Get-Азсклеластикпулактивити](/powershell/module/az.sql/get-azsqlelasticpoolactivity)|Получает состояние операций в эластичном пуле.|
|[New-AzSqlDatabase](/powershell/module/az.sql/new-azsqldatabase)|Создает новую базу данных в существующем пуле или отдельную базу данных. |
|[Get-AzSqlDatabase](/powershell/module/az.sql/get-azsqldatabase)|Получает одну или несколько баз данных.|
|[Set-AzSqlDatabase](/powershell/module/az.sql/set-azsqldatabase)|Определяет свойства базы данных или перемещает ее в эластичный пул, из пула либо между пулами.|
|[Remove-AzSqlDatabase](/powershell/module/az.sql/remove-azsqldatabase)|Удаляет базу данных.|

> [!TIP]
> Создание большого количества баз данных в эластичном пуле может занять некоторое время, если эта операция выполняется с помощью портала или командлетов PowerShell, которые создают базы данных поочередно. Сведения об автоматическом создании баз данных в эластичном пуле см. в документе [CreateOrUpdateElasticPoolAndPopulate](https://gist.github.com/billgib/d80c7687b17355d3c2ec8042323819ae).

## <a name="azure-cli"></a>Azure CLI

Для создания эластичных пулов базы данных SQL с помощью [Azure CLI](/cli/azure) используйте приведенные ниже команды [Azure CLI для базы данных SQL](/cli/azure/sql/db). Запускайте интерфейс командной строки в браузере с помощью [Cloud Shell](/azure/cloud-shell/overview) либо [установите](/cli/azure/install-azure-cli) его на платформе macOS, Linux или Windows.

> [!TIP]
> Примеры сценариев Azure CLI см. в статье [Использование интерфейса командной строки для перемещения базы данных SQL в эластичном пуле SQL](scripts/move-database-between-elastic-pools-cli.md) и [Использование Azure CLI для масштабирования эластичного пула SQL в базе данных SQL Azure](scripts/scale-pool-cli.md).
>

| Командлет | Описание |
| --- | --- |
|[az sql elastic-pool create](/cli/azure/sql/elastic-pool#az-sql-elastic-pool-create)|Создает эластичный пул.|
|[az sql elastic-pool list](/cli/azure/sql/elastic-pool#az-sql-elastic-pool-list)|Возвращает список эластичных пулов на сервере.|
|[az sql elastic-pool list-dbs](/cli/azure/sql/elastic-pool#az-sql-elastic-pool-list-dbs)|Возвращает список баз данных в пуле эластичных баз данных.|
|[az sql elastic-pool list-editions](/cli/azure/sql/elastic-pool#az-sql-elastic-pool-list-editions)|Также содержит параметры доступных DTU пула, ограничений хранилища и параметры отдельных баз данных. Чтобы снизить уровень детализации, ограничения дополнительного хранилища и параметры каждой базы данных скрыты по умолчанию.|
|[az sql elastic-pool update](/cli/azure/sql/elastic-pool#az-sql-elastic-pool-update)|Обновляет эластичный пул.|
|[az sql elastic-pool delete](/cli/azure/sql/elastic-pool#az-sql-elastic-pool-delete)|Удаляет эластичный пул.|

## <a name="transact-sql-t-sql"></a>Transact-SQL (T-SQL)

Для создания и перемещения баз данных в существующих эластичных пулах или для возвращения сведений о пуле эластичных баз данных SQL с помощью Transact-SQL используйте следующие команды T-SQL. Эти команды можно выполнить с помощью портал Azure, [SQL Server Management Studio](/sql/ssms/use-sql-server-management-studio), [Visual Studio Code](https://code.visualstudio.com/docs)или любой другой программы, которая может подключаться к серверу и ПЕРЕДАВАТЬ команды Transact-SQL. Сведения о создании правил брандмауэра и управлении ими с помощью Transact-SQL см. в [этой статье](firewall-configure.md#use-transact-sql-to-manage-ip-firewall-rules).

> [!IMPORTANT]
> С помощью Transact-SQL невозможно создать, обновить или удалить пул эластичных баз данных SQL Azure. Вы можете добавить или удалить базы данных из эластичного пула, а также вернуть сведения о существующих эластичных пулах, используя динамические административные представления.
>

| Get-Help | Описание |
| --- | --- |
|[CREATE DATABASE (база данных SQL Azure)](/sql/t-sql/statements/create-database-azure-sql-database)|Создает новую базу данных в существующем пуле или отдельную базу данных. Для создания новой базы данных необходимо подключение к базе данных master.|
| [CREATE DATABASE (база данных SQL Azure)](/sql/t-sql/statements/alter-database-azure-sql-database) |Перемещает базы данных из, в или между эластичными пулами.|
|[DROP DATABASE (Transact-SQL)](/sql/t-sql/statements/drop-database-transact-sql)|Удаляет базу данных.|
|[sys.elastic_pool_resource_stats (база данных SQL Azure)](/sql/relational-databases/system-catalog-views/sys-elastic-pool-resource-stats-azure-sql-database)|Возвращает статистику использования ресурсов для всех эластичных пулов на сервере. Для каждого эластичного пула имеется одна строка на каждые 15 секунд окна отчета (четыре строки в минуту). Сюда входят сведения об использовании ЦП, хранилища, операциях ввода-вывода, журнал, а также использование параллельных запросов и сеансов всеми базами данных в пуле.|
|[sys.database_service_objectives (база данных SQL Azure)](/sql/relational-databases/system-catalog-views/sys-database-service-objectives-azure-sql-database)|Возвращает выпуск (уровень служб), Цель обслуживания (ценовая категория) и имя эластичного пула (если таковые имеются) для базы данных в базе данных SQL или Azure синапсе Analytics (ранее — хранилище данных SQL). При входе в базу данных master на сервере возвращает сведения обо всех базах данных. Для Azure синапсе Analytics необходимо подключиться к базе данных master.|

## <a name="rest-api"></a>REST API

Для создания эластичных пулов и баз данных в пуле для базы данных SQL и управления ими используйте эти запросы REST API.

| Get-Help | Описание |
| --- | --- |
|[Создание или обновление эластичных пулов](https://docs.microsoft.com/rest/api/sql/elasticpools/createorupdate)|Создает эластичный пул или обновляет имеющийся.|
|[Пулы эластичных БД — удаление](https://docs.microsoft.com/rest/api/sql/elasticpools/delete)|Удаляет эластичный пул.|
|[Пулы эластичных БД — получение](https://docs.microsoft.com/rest/api/sql/elasticpools/get)|Получает эластичный пул.|
|[Пулы эластичных БД — список по серверам](https://docs.microsoft.com/rest/api/sql/elasticpools/listbyserver)|Возвращает список эластичных пулов на сервере.|
|[Пулы эластичных БД — обновление](https://docs.microsoft.com/rest/api/sql/elasticpools/listbyserver)|Обновляет имеющийся эластичный пул.|
|[Действия эластичного пула](https://docs.microsoft.com/rest/api/sql/elasticpoolactivities)|Возвращает действия эластичного пула.|
|[Действия базы данных эластичного пула](https://docs.microsoft.com/rest/api/sql/elasticpooldatabaseactivities)|Возвращает действия баз данных в эластичном пуле.|
|[Базы данных — создание или обновление](https://docs.microsoft.com/rest/api/sql/databases/createorupdate)|Создает новую базу данных или обновляет имеющуюся.|
|[Базы данных: получение](https://docs.microsoft.com/rest/api/sql/databases/get)|Получает базу данных.|
|[Базы данных — список по эластичному пулу](https://docs.microsoft.com/rest/api/sql/databases/listbyelasticpool)|Возвращает список баз данных в пуле эластичных баз данных.|
|[Базы данных — список по серверам](https://docs.microsoft.com/rest/api/sql/databases/listbyserver)|Возвращает список баз данных на сервере.|
|[Базы данных: обновление](https://docs.microsoft.com/rest/api/sql/databases/update)|Обновляет имеющуюся базу данных.|

## <a name="next-steps"></a>Дальнейшие шаги

* Дополнительные сведения о шаблонах разработки для приложений SaaS, использующих пулы эластичных БД, см. в статье [Шаблоны разработки для мультитенантных приложений SaaS с использованием Базы данных Azure SQL](saas-tenancy-app-design-patterns.md).
* Дополнительные сведения об эластичных пулах см. в статье [Общие сведения о приложении SaaS Wingtip](saas-dbpertenant-wingtip-app-overview.md).
