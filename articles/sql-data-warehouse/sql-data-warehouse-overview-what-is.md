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
ms.openlocfilehash: 68d39b4f363794d50fd05c2067502fc55d5d0170
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/05/2020
ms.locfileid: "78380707"
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

## <a name="next-steps"></a>Дальнейшие действия

- Знакомство с [архитектурой Azure Synapse](massively-parallel-processing-mpp-architecture.md)
- Быстрое создание [пула SQL](create-data-warehouse-portal.md)
- [Загрузите демонстрационные данные](sql-data-warehouse-load-sample-databases.md).
- Просмотрите [видео](https://azure.microsoft.com/documentation/videos/index/?services=sql-data-warehouse).

Или ознакомьтесь со следующими ресурсами, посвященными SQL Analytics.  
* Ищите информацию в [блогах](https://azure.microsoft.com/blog/tag/azure-sql-data-warehouse/).
* Отправьте [запрос на функцию](https://feedback.azure.com/forums/307516-sql-data-warehouse).
* [Создание запроса в службу поддержки](sql-data-warehouse-get-started-create-support-ticket.md)
* Ищите информацию на [форуме MSDN](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureSQLDataWarehouse).
* Ищите информацию на [форуме Stack Overflow](https://stackoverflow.com/questions/tagged/azure-sqldw).