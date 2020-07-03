---
title: Performance tuning with result set caching (Настройка производительности путем кэширования результирующего набора)
description: Общие сведения о функции кэширования результирующего набора для пула SQL синапсе в Azure синапсе Analytics
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
ms.openlocfilehash: eadbe13269ce1259b4560af117f5b15b3b294151
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2020
ms.locfileid: "81730605"
---
# <a name="performance-tuning-with-result-set-caching"></a>Performance tuning with result set caching (Настройка производительности путем кэширования результирующего набора)

Если включено кэширование результирующего набора, то SQL Analytics автоматически кэширует результаты запроса в пользовательской базе данных для повторяющегося использования.  Это позволяет последующим выполнениям запросов получать результаты непосредственно из материализованного кэша, поэтому перерасчет не требуется.   Кэширование результирующего набора улучшает производительность запросов и сокращает использование ресурсов вычислений.  Кроме того, запросы, использующие кэшированный набор результатов, не используют слоты выдачи и поэтому не учитываются для существующих ограничений параллелизма. В целях безопасности пользователи могут получить доступ к кэшированным результатам только в том случае, если они имеют те же разрешения на доступ к данным, что и пользователи, создающие кэшированные результаты.  

## <a name="key-commands"></a>Ключевые команды

[Включение/ВЫКЛЮЧЕНие кэширования результирующего набора для пользовательской базы данных](/sql/t-sql/statements/alter-database-transact-sql-set-options?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)

[Включение/ВЫКЛЮЧЕНие кэширования результирующего набора для пользовательской базы данных](/sql/t-sql/statements/alter-database-transact-sql-set-options?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)

[Включение/ВЫКЛЮЧЕНие кэширования результирующего набора для сеанса](/sql/t-sql/statements/set-result-set-caching-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)

[Проверка размера кэшированного результирующего набора](/sql/t-sql/database-console-commands/dbcc-showresultcachespaceused-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)  

[Очистка кэша](/sql/t-sql/database-console-commands/dbcc-dropresultsetcache-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)

## <a name="whats-not-cached"></a>Что не кэшировано  

При включении кэширования результирующих наборов для базы данных результаты будут сохраняться в кэше (пока он не заполнится) для всех запросов, кроме следующих:

- запросы с недетерминированными функциями, такими как DateTime.Now();
- запросы с пользовательскими функциями;
- Запросы, использующие таблицы с включенной безопасностью на уровне строк или на уровне столбцов
- запросы, возвращающие данные с размером строк более 64 КБ.
- Запросы, возвращающие большие объемы данных (>10 ГБ) 

> [!IMPORTANT]
> Операции по созданию кэша результирующего набора и извлечению данных из кэша происходят в управляющем узле экземпляра пула SQL синапсе.
> Если включено кэширование результирующего набора, выполнение запросов, возвращающих большие результирующие наборы (например, >1 ГБ), может привести к высокому регулированию управляющего узла и замедлить общий ответ на запрос экземпляра.  Как правило, такие запросы используются в ходе исследования данных, а также при выполнении операций извлечения, преобразования и загрузки. Чтобы избежать чрезмерной загрузки управляющего узла и снижения производительности, перед выполнение запросов такого типа пользователям следует отключить кэширование результирующего набора для базы данных.  

Выполнить этот запрос в течение времени, затраченного на выполнение операций кэширования результирующего набора для запроса:

```sql
SELECT step_index, operation_type, location_type, status, total_elapsed_time, command
FROM sys.dm_pdw_request_steps
WHERE request_id  = <'request_id'>;
```

Ниже приведен пример выходных данных для запроса, выполненного с отключенным кэшированием результирующего набора.

![Запрос-шаги-with-RSC-Disabled](./media/performance-tuning-result-set-caching/query-steps-with-rsc-disabled.png)

Ниже приведен пример выходных данных для запроса, выполненного с включенным кэшированием результирующего набора.

![Запрос — шаги с включенным параметром-RSC](./media/performance-tuning-result-set-caching/query-steps-with-rsc-enabled.png)

## <a name="when-cached-results-are-used"></a>При использовании кэшированных результатов

Для запроса повторно используется кэшированный результирующий набор при соблюдении всех следующих требований:

- Пользователь, выполняющий запрос, имеет доступ ко всем таблицам, на которые ссылается запрос.
- Имеется точное соответствие между новым запросом и предыдущим запросом, породившим результирующий набор в кэше.
- Нет изменений в данных или схеме в таблицах, на основе которых создан кэшированный результирующий набор.

Выполните эту команду, чтобы проверить, как был выполнен запрос — с попаданием или промахом в кэше результатов. Столбец result_cache_hit возвращает значение 1 для попадания в кэш, 0 для промахов кэша и отрицательные значения по причинам, почему кэширование результирующего набора не использовалось. Дополнительные сведения см. в описании [sys.dm_pdw_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest).

```sql
SELECT request_id, command, result_cache_hit FROM sys.dm_pdw_exec_requests
WHERE request_id = <'Your_Query_Request_ID'>
```

## <a name="manage-cached-results"></a>Управление кэшированными результатами

Максимальный размер кэша результирующих наборов составляет 1 ТБ на базу данных.  Кэшированные результаты автоматически становятся недействительными при изменении данных базового запроса.  

При вытеснении кэша с помощью SQL Analytics автоматически выполняется следующее расписание:

- Каждые 48 часов, если результирующий набор не использовался или был признан недействительным.
- Если размер кэша результирующего набора приближается к максимальному.

Пользователи могут вручную очистить весь кэш результирующего набора с помощью одного из следующих параметров:

- Отключение функции кэша результирующего набора для базы данных.
- Выполнение инструкции DBCC ДРОПРЕСУЛТСЕТКАЧЕ при подключении к базе данных

Приостановка базы данных не приведет к пустому кэшированному результирующему набору.  

## <a name="next-steps"></a>Дальнейшие шаги

Дополнительные советы по разработке см. в статье [Проектные решения и методики программирования для хранилища данных SQL](sql-data-warehouse-overview-develop.md).
