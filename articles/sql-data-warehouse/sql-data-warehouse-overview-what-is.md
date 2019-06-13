---
title: Что такое хранилище данных SQL Azure? | Документация Майкрософт
description: Распределенная база данных корпоративного класса, способная обрабатывать петабайты реляционных и нереляционных данных. Это первое облачное хранилище данных в отрасли, в котором можно увеличивать и уменьшать количество вычислительных ресурсов, а также приостанавливать их работу за считанные секунды.
services: sql-data-warehouse
author: mlee3gsd
manager: craigg
ms.service: sql-data-warehouse
ms.topic: overview
ms.subservice: design
ms.date: 05/30/2019
ms.author: martinle
ms.reviewer: igorstan
mscustom: sqlfreshmay19
ms.openlocfilehash: a9126e9023091dd8c3df71f2aa2558a01227a8be
ms.sourcegitcommit: ef06b169f96297396fc24d97ac4223cabcf9ac33
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/31/2019
ms.locfileid: "66428028"
---
# <a name="what-is-azure-sql-data-warehouse"></a>Что такое хранилище данных SQL Azure?

Хранилище данных SQL — это облачное корпоративное хранилище данных (EDW), использующее массовую параллельную обработку (MPP) для быстрого выполнения сложных запросов с петабайтами данных. Используйте хранилище данных SQL как ключевой компонент решения для больших данных. Импортируйте большие данные в хранилище данных SQL с помощью простых запросов T-SQL [PolyBase](/sql/relational-databases/polybase/polybase-guide?view=sql-server-2017&viewFallbackFrom=azure-sqldw-latest) и используйте возможности MPP для выполнения высокопроизводительной аналитики. Во время интеграции и анализа хранилище данных станет единственным оптимальным и надежным вариантом получения аналитических сведений для вашей компании.  

## <a name="key-component-of-big-data-solution"></a>Ключевой компонент решения для больших данных

Хранилище данных SQL — это ключевой компонент комплексного решения для больших данных в облаке.

![Решение хранилища данных](media/sql-data-warehouse-overview-what-is/data-warehouse-solution.png) 

В облачном решении для работы с данными данные принимаются в хранилища больших данных из различных источников. После отправки в хранилище больших данных алгоритмы Hadoop, Spark и машинного обучения выполняют подготовку и обучение данных. Когда данные будут готовы к сложному анализу, хранилище данных SQL запрашивает хранилища больших данных с помощью PolyBase. PolyBase использует стандартные запросы T-SQL для передачи данных в хранилище данных SQL.
 
Хранилище данных SQL сохраняет данные в реляционных таблицах с хранением по столбцам. Этот формат значительно снижает затраты на хранение данных и повышает производительность запросов. После сохранения данных в хранилище данных SQL вы сможете выполнять аналитику в большом масштабе. По сравнению с традиционными системами баз данных запросы анализа выполняются за секунды вместо минут или за часы вместо дней. 

Результаты анализа можно передать в базы данных и приложения отчетности по всему миру. Специалисты по бизнес-аналитике могут получить данные для принятия обоснованных бизнес-решений.

## <a name="next-steps"></a>Дополнительная информация

- Изучите [архитектуру хранилища данных SQL Azure](/azure/sql-data-warehouse/massively-parallel-processing-mpp-architecture).
- Быстро [создайте хранилище данных SQL Azure][create a SQL Data Warehouse].
- [Загрузите демонстрационные данные][load sample data].
- Просмотрите [видео](/azure/sql-data-warehouse/sql-data-warehouse-videos).

Или ознакомьтесь со следующими ресурсами, посвященными хранилищу данных SQL.  
* Ищите информацию в [Блоги].
* Отправьте [Запросы функций].
* Ищите информацию в [Блоги группы консультирования клиентов].
* [Создание запроса в службу поддержки]
* Ищите информацию на [Форум MSDN].
* Ищите информацию на [Форум Stack Overflow].


<!--Image references-->
[1]: ./media/sql-data-warehouse-overview-what-is/dwarchitecture.png

<!--Article references-->
[Создание запроса в службу поддержки]: ./sql-data-warehouse-get-started-create-support-ticket.md
[load sample data]: ./sql-data-warehouse-load-sample-databases.md
[create a SQL Data Warehouse]: ./sql-data-warehouse-get-started-provision.md
[Migration documentation]: ./sql-data-warehouse-overview-migrate.md
[SQL Data Warehouse solution partners]: ./sql-data-warehouse-partner-business-intelligence.md
[Integrated tools overview]: ./sql-data-warehouse-overview-integrate.md
[Backup and restore overview]: ./sql-data-warehouse-restore-database-overview.md
[Azure glossary]: ../azure-glossary-cloud-terminology.md

<!--MSDN references-->

<!--Other Web references-->
[Блоги]: https://azure.microsoft.com/blog/tag/azure-sql-data-warehouse/
[Блоги группы консультирования клиентов]: https://blogs.msdn.microsoft.com/sqlcat/tag/sql-dw/
[Запросы функций]: https://feedback.azure.com/forums/307516-sql-data-warehouse
[Форум MSDN]: https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=AzureSQLDataWarehouse
[Форум Stack Overflow]: https://stackoverflow.com/questions/tagged/azure-sqldw
[Twitter]: https://twitter.com/hashtag/SQLDW
[Videos]: https://azure.microsoft.com/documentation/videos/index/?services=sql-data-warehouse
[SLA for SQL Data Warehouse]: https://azure.microsoft.com/support/legal/sla/sql-data-warehouse/v1_0/
[Volume Licensing]: https://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=37
[Service Level Agreements]: https://azure.microsoft.com/support/legal/sla/
