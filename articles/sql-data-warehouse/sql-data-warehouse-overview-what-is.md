---
title: Что такое Azure Synapse Analytics (ранее — Хранилище данных SQL)?
description: Azure Synapse Analytics (ранее — Хранилище данных SQL) — это служба аналитики без ограничений, которая объединяет корпоративные хранилища данных и аналитику больших данных.
services: sql-data-warehouse
author: mlee3gsd
manager: craigg
ms.service: sql-data-warehouse
ms.topic: overview
ms.subservice: design
ms.date: 11/04/2019
ms.author: martinle
ms.reviewer: igorstan
ms.openlocfilehash: 07bf3070e42af8a03ce1fd7ea4445fc76557e8a3
ms.sourcegitcommit: 359930a9387dd3d15d39abd97ad2b8cb69b8c18b
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/06/2019
ms.locfileid: "73645513"
---
# <a name="what-is-azure-synapse-analytics-formerly-sql-dw"></a>Что такое Azure Synapse Analytics (ранее — Хранилище данных SQL)?

Azure Synapse — это служба аналитики без ограничений, которая объединяет корпоративные хранилища данных и аналитику больших данных. Служба позволяет вам запрашивать данные на своих условиях, используя бессерверные ресурсы по запросу или подготовленные ресурсы в любом масштабе. Azure Synapse объединяет эти две возможности вместе, предоставляя единый интерфейс для приема, подготовки, контроля и обслуживания данных для насущных потребностей бизнес-аналитики и машинного обучения.

В Azure Synapse есть четыре компонента.
- Аналитика SQL. Выполнение аналитики на основе T-SQL — общедоступная версия
    - Пул SQL (оплата за DWU по мере подготовки) 
    - SQL по запросу (оплата за обработанные ТБ) — (Предварительная версия)
- Spark. Глубокая интегрированная Apache Spark (предварительная версия) 
- Интеграция данных. Гибридная интеграция данных (предварительная версия)
- Studio. Унифицированное взаимодействие с пользователем.  (предварительная версия)

> [!NOTE]
> Чтобы получить доступ к функциям предварительной версии Azure Synapse, запросите доступ [здесь](https://aka.ms/synapsepreview). Корпорация Майкрософт будет рассматривать все запросы и реагировать как можно скорее.

## <a name="sql-analytics-and-sql-pool-in-azure-synapse"></a>SQL Analytics и пул SQL в Azure Synapse

Название SQL Analytics относится к функциям хранения корпоративных данных, которые стали общедоступны в Azure Synapse. 

Пул SQL представляет коллекцию аналитических ресурсов, которые подготавливаются при использовании SQL Analytics. Размер пула SQL определяется единицами хранения данных (DWU).

Импортируйте большие данные с помощью простых запросов T-SQL [PolyBase](/sql/relational-databases/polybase/polybase-guide?view=sql-server-2017&viewFallbackFrom=azure-sqldw-latest) и используйте возможности MPP для выполнения высокопроизводительной аналитики. По мере интеграции и анализа SQL Analytics станет единственным оптимальным и надежным вариантом получения надежных и своевременных аналитических сведений для вашей компании.  

## <a name="key-component-of-a-big-data-solution"></a>Ключевой компонент решения для больших данных

Хранилище данных — это ключевой компонент комплексного решения для больших данных в облаке.

![Решение хранилища данных](media/sql-data-warehouse-overview-what-is/data-warehouse-solution.png) 

В облачном решении для работы с данными данные принимаются в хранилища больших данных из различных источников. После отправки в хранилище больших данных алгоритмы Hadoop, Spark и машинного обучения выполняют подготовку и обучение данных. Когда данные будут готовы к сложному анализу, SQL Analytics запрашивает хранилища больших данных с помощью PolyBase. PolyBase использует стандартные запросы T-SQL для передачи данных в SQL Analytics.
 
SQL Analytics сохраняет данные в реляционных таблицах с хранением по столбцам. Этот формат значительно снижает затраты на хранение данных и повышает производительность запросов. После сохранения данных вы сможете вести аналитику в большом масштабе. По сравнению с традиционными системами баз данных запросы анализа выполняются за секунды вместо минут или за часы вместо дней. 

Результаты анализа можно передать в базы данных и приложения отчетности по всему миру. Специалисты по бизнес-аналитике могут получить данные для принятия обоснованных бизнес-решений.

## <a name="next-steps"></a>Дополнительная информация

- Знакомство с [архитектурой Azure Synapse](/azure/sql-data-warehouse/massively-parallel-processing-mpp-architecture)
- Быстрое создание [пула SQL](create-data-warehouse-portal.md)
- [Загрузите демонстрационные данные][load sample data].
- Просмотрите [видео](/azure/sql-data-warehouse/sql-data-warehouse-videos).

Или ознакомьтесь со следующими ресурсами, посвященными SQL Analytics.  
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
[create a data warehouse]: ./sql-data-warehouse-get-started-provision.md
[Migration documentation]: ./sql-data-warehouse-overview-migrate.md
[Azure Synapse Analytics solution partners]: ./sql-data-warehouse-partner-business-intelligence.md
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
[SLA for Azure Synapse Analytics]: https://azure.microsoft.com/support/legal/sla/sql-data-warehouse/v1_0/
[Volume Licensing]: https://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=37
[Service Level Agreements]: https://azure.microsoft.com/support/legal/sla/
