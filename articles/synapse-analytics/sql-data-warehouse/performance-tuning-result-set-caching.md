---
title: Performance tuning with result set caching (Настройка производительности путем кэширования результирующего набора)
description: Обзор функции кэширования наборов результатов для пула Synapse S'L в Azure Synapse Analytics
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 10/10/2019
ms.author: xiaoyul
ms.reviewer: nidejaco;
ms.custom: azure-synapse
ms.openlocfilehash: 4eef8a3a83456a9f2066311b9339b26b83afa009
ms.sourcegitcommit: d597800237783fc384875123ba47aab5671ceb88
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/03/2020
ms.locfileid: "80633801"
---
# <a name="performance-tuning-with-result-set-caching"></a>Performance tuning with result set caching (Настройка производительности путем кэширования результирующего набора)

При включении кэширования набора результатов, s'L Analytics автоматически кэширует запрос в базе данных пользователей для повторного использования.  Это позволяет последующим выполнениям запросов получать результаты непосредственно из сохраняемого кэша, поэтому повторная вычисления не требуется.   Кэширование набора результатов улучшает производительность запроса и снижает использование вычислительных ресурсов.  Кроме того, запросы с использованием кэшированных результатов не используют слоты для параллелизма и, таким образом, не учитываются в существующих лимитах параллелизма. Для обеспечения безопасности пользователи могут получить доступ к кэшированным результатам только в том случае, если у них есть те же разрешения на доступ к данным, что и у пользователей, создающих кэшированные результаты.  

## <a name="key-commands"></a>Ключевые команды

[Включите кэширование результатов ON/OFF для базы данных пользователей](/sql/t-sql/statements/alter-database-transact-sql-set-options?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)

[Включите кэширование результатов ON/OFF для базы данных пользователей](/sql/t-sql/statements/alter-database-transact-sql-set-options?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)

[Включите кэширование результатов ON/OFF для сеанса](/sql/t-sql/statements/set-result-set-caching-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)

[Проверьте размер набора кэшированных результатов](/sql/t-sql/database-console-commands/dbcc-showresultcachespaceused-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)  

[Очистка кэша](/sql/t-sql/database-console-commands/dbcc-dropresultsetcache-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)

## <a name="whats-not-cached"></a>Что не кэшируется  

При включении кэширования результирующих наборов для базы данных результаты будут сохраняться в кэше (пока он не заполнится) для всех запросов, кроме следующих:

- запросы с недетерминированными функциями, такими как DateTime.Now();
- запросы с пользовательскими функциями;
- Запросы с использованием таблиц с включенной безопасностью уровня строки или безопасностью уровня столбцов
- запросы, возвращающие данные с размером строк более 64 КБ.

> [!IMPORTANT]
> Операции для создания кэша набора результатов и извлечения данных из кэша происходят на узле управления экземпляра пула Synapse S'L.
> Если кэширование результирующего набора включено, выполнение запросов, возвращающих большие (например, более одного миллиона строк) наборы, может привести к высокой загрузке ЦП на управляющем узле и снизить общую скорость реакции экземпляра.  Как правило, такие запросы используются в ходе исследования данных, а также при выполнении операций извлечения, преобразования и загрузки. Чтобы избежать чрезмерной загрузки управляющего узла и снижения производительности, перед выполнение запросов такого типа пользователям следует отключить кэширование результирующего набора для базы данных.  

Запустите этот запрос для времени, заработаваемого операциями кэширования, настроенным результатом для запроса:

```sql
SELECT step_index, operation_type, location_type, status, total_elapsed_time, command
FROM sys.dm_pdw_request_steps
WHERE request_id  = <'request_id'>;
```

Ниже приведен пример вывода для запроса, выполняемого с отключением кэширования набора результатов.

![Запрос-шаги-с-rsc-отключен](./media/performance-tuning-result-set-caching/query-steps-with-rsc-disabled.png)

Ниже приведен пример вывода для запроса, выполняемого с включенным кэшированием набора результатов.

![Запрос-шаги-с-rsc-включено](./media/performance-tuning-result-set-caching/query-steps-with-rsc-enabled.png)

## <a name="when-cached-results-are-used"></a>При использовании кэшированных результатов

Для запроса повторно используется кэшированный результирующий набор при соблюдении всех следующих требований:

- Пользователь, выполняющий запрос, имеет доступ ко всем таблицам, на которые ссылается запрос.
- Имеется точное соответствие между новым запросом и предыдущим запросом, породившим результирующий набор в кэше.
- Нет изменений в данных или схеме в таблицах, на основе которых создан кэшированный результирующий набор.

Выполните эту команду, чтобы проверить, как был выполнен запрос — с попаданием или промахом в кэше результатов. Столбец result_set_cache возвращает 1 для попадания кэша, 0 для промаха кэша и отрицательных значений по причинам, по которым кэширование набора результатов не использовалось. Проверьте [sys.dm_pdw_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) для получения подробной информации.

```sql
SELECT request_id, command, result_set_cache FROM sys.dm_pdw_exec_requests
WHERE request_id = <'Your_Query_Request_ID'>
```

## <a name="manage-cached-results"></a>Управление кэшированными результатами

Максимальный размер кэша результирующих наборов составляет 1 ТБ на базу данных.  Кэшированные результаты автоматически аннулируются при изменении данных базового запроса.  

Выселение кэша управляется s'L Analytics автоматически в соответствии с этим графиком:

- Каждые 48 часов, если набор результатов не был использован или был признан недействительным.
- Когда кэш набора результата приближается к максимальному размеру.

Пользователи могут вручную опорожнить весь кэш набора результатов, используя один из этих параметров:

- Отключение функции кэша результирующего набора для базы данных.
- Выполнить DBCC DROPRESULTSETCACHE при подключении к базе данных

Приостановка базы данных не опорожняет набор кэшированных результатов.  

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные советы по разработке см. в статье [Проектные решения и методики программирования для хранилища данных SQL](sql-data-warehouse-overview-develop.md).
