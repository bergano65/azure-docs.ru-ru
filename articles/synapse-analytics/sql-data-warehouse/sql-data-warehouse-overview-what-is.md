---
title: Что такое Azure Synapse Analytics (ранее — Хранилище данных SQL)?
description: Azure Synapse Analytics (ранее — Хранилище данных SQL) — это служба аналитики без ограничений, которая объединяет корпоративные хранилища данных и аналитику больших данных.
services: synapse-analytics
author: mlee3gsd
manager: craigg
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: sql-dw
ms.date: 11/04/2019
ms.author: martinle
ms.reviewer: igorstan
ms.openlocfilehash: 8840791c7b18d1efa499c2826a6eaf041a6da787
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/04/2020
ms.locfileid: "93317486"
---
# <a name="what-is-azure-synapse-analytics-formerly-sql-dw"></a>Что такое Azure Synapse Analytics (ранее — Хранилище данных SQL)?

> [!NOTE]
>Также изучите [документацию по Azure Synapse (предварительная версия рабочих областей)](../overview-what-is.md).
>

Azure Synapse — это служба аналитики, которая объединяет корпоративные хранилища данных и аналитику больших данных. Служба позволяет вам запрашивать данные на своих условиях, используя бессерверные ресурсы по запросу или подготовленные ресурсы в любом масштабе. Azure Synapse объединяет эти две возможности вместе, предоставляя единый интерфейс для приема, подготовки, контроля и обслуживания данных для насущных потребностей бизнес-аналитики и машинного обучения.

В Azure Synapse есть четыре компонента.

- SQL Synapse: Выполнение аналитики на основе T-SQL — общедоступная версия
  - Выделенный пул SQL (оплата за DWU по мере подготовки)
  - Бессерверный пул SQL (оплата за обработанные ТБ; предварительная версия)
- Spark. Apache Spark с глубокой интеграцией (предварительная версия)
- Конвейеры Synapse: Гибридная интеграция данных (предварительная версия)
- Studio. Унифицированное взаимодействие с пользователем. (предварительная версия)

## <a name="dedicated-sql-pool-in-azure-synapse"></a>Выделенный пул SQL в Azure Synapse

Название "выделенный пул SQL" относится к функциям хранения корпоративных данных, которые стали общедоступны в Azure Synapse.

Выделенный пул SQL представляет коллекцию аналитических ресурсов, которые подготавливаются при использовании SQL Synapse. Размер выделенного пула SQL определяется единицами использования хранилища данных (DWU).

Импортируйте большие данные с помощью запросов T-SQL [PolyBase](/sql/relational-databases/polybase/polybase-guide?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) и используйте возможности обработчика распределенных запросов для выполнения высокопроизводительной аналитики. По мере интеграции и анализа данных SQL Synapse станет единственным оптимальным и надежным вариантом получения надежных и своевременных аналитических сведений для вашей компании. 

## <a name="key-component-of-a-big-data-solution"></a>Ключевой компонент решения для больших данных

Хранилище данных — это ключевой компонент комплексного решения для больших данных в облаке.

![Решение хранилища данных](./media/sql-data-warehouse-overview-what-is/data-warehouse-solution.png)

В облачном решении для работы с данными данные принимаются в хранилища больших данных из различных источников. После отправки в хранилище больших данных алгоритмы Hadoop, Spark и машинного обучения выполняют подготовку и обучение данных. Когда данные будут готовы к сложному анализу, выделенный пул SQL запрашивает хранилища больших данных с помощью PolyBase. PolyBase использует стандартные запросы T-SQL для передачи данных в таблицы выделенного пула SQL.

Выделенный пул SQL сохраняет данные в реляционных таблицах с хранением по столбцам. Этот формат значительно снижает затраты на хранение данных и повышает производительность запросов. После сохранения данных вы сможете вести аналитику в большом масштабе. По сравнению с традиционными системами баз данных запросы анализа выполняются за секунды вместо минут или за часы вместо дней.

Результаты анализа можно передать в базы данных и приложения отчетности по всему миру. Специалисты по бизнес-аналитике могут получить данные для принятия обоснованных бизнес-решений.

## <a name="next-steps"></a>Дальнейшие действия

- Знакомство с [архитектурой Azure Synapse](massively-parallel-processing-mpp-architecture.md)
- Быстрое [создание выделенного пула SQL](create-data-warehouse-portal.md)
- [Загрузите демонстрационные данные](load-data-from-azure-blob-storage-using-polybase.md).
- Просмотрите [видео](https://azure.microsoft.com/documentation/videos/index/?services=sql-data-warehouse).

Или ознакомьтесь со следующими ресурсами, посвященными SQL Analytics.

- Ищите информацию в [блогах](https://azure.microsoft.com/blog/tag/azure-sql-data-warehouse/).
- Отправьте [запрос на функцию](https://feedback.azure.com/forums/307516-sql-data-warehouse).
- [Создание запроса в службу поддержки](sql-data-warehouse-get-started-create-support-ticket.md)
- Ищите информацию на [странице вопросов (раздел вопросов и ответов на сайте Майкрософт)](https://docs.microsoft.com/answers/topics/azure-synapse-analytics.html)
- Ищите информацию на [форуме Stack Overflow](https://stackoverflow.com/questions/tagged/azure-sqldw).
