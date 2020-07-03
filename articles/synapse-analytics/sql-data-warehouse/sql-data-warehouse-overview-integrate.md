---
title: Создание интегрированных решений
description: Средства и партнеры решений, которые интегрируются с пулом синапсе SQL.
services: synapse-analytics
author: mlee3gsd
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/17/2018
ms.author: martinle
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: 2f6e091b6e0285bea5fef9e4d0be40faec936c6b
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2020
ms.locfileid: "80633139"
---
# <a name="integrate-other-services-with-a-sql-analytics-data-warehouse"></a>Интеграция других служб с хранилищем данных аналитики SQL

Функция аналитики SQL в Azure синапсе Analytics позволяет пользователям интегрироваться с многими другими службами в Azure. С помощью SQL Analytics можно создать хранилище данных с помощью ресурса пула SQL, который затем может использовать несколько дополнительных служб, некоторые из которых включают:

* Power BI
* Фабрика данных Azure
* Машинное обучение Azure
* Azure Stream Analytics

Дополнительные сведения о службах Integration Services в Azure см. в статье [партнеры по интеграции](sql-data-warehouse-partner-data-integration.md).

## <a name="power-bi"></a>Power BI

Интеграция Power BI позволяет сочетать мощность вычислений хранилища данных с помощью динамической отчетности и визуализации Power BI. На данный момент интеграция с Power BI включает:

* **Прямое подключение**: более расширенное подключение с использованием логического включение к хранилищу данных, подготовленному с помощью пула SQL. Это обеспечивает более быстрый анализ в большем масштабе.
* **Открыть в Power BI**: кнопка "открыть в Power BI" передает сведения об экземпляре Power BI для упрощения подключения.

Дополнительные сведения см. в статье [Использование Power BI с хранилищем данных SQL](sql-data-warehouse-get-started-visualize-with-power-bi.md) или в [документации по Power BI](https://powerbi.microsoft.com/blog/exploring-azure-sql-data-warehouse-with-power-bi/).

## <a name="azure-data-factory"></a>Фабрика данных Azure

Фабрика данных Azure предоставляет пользователям управляемую платформу для создания сложных конвейеров извлечения и загрузки. Интеграция пула SQL с фабрикой данных Azure включает в себя:

* **Хранимые процедуры**: координация выполнения хранимых процедур.
* **Копирование**: использование ADF для перемещения данных в пул SQL. В основе этой операции может использоваться стандартный механизм перемещения данных ADF или PolyBase.

Дополнительную информацию см. в статье [Визуализация данных с помощью Power BI](../../data-factory/load-azure-sql-data-warehouse.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json).

## <a name="azure-machine-learning"></a>Машинное обучение Azure

Служба "Машинное обучение Azure" — это полностью управляемая аналитическая служба, позволяющая пользователям создавать сложные модели с использованием крупного набора прогнозируемых инструментов. Пул SQL поддерживается в качестве источника и назначения для этих моделей и имеет следующие функциональные возможности.

* **Чтение данных:** Масштабирование моделей с помощью T-SQL для пула SQL.
* **Запись данных:** Зафиксируйте изменения из любой модели обратно в пул SQL.

Дополнительную информацию см. в статье [Анализ данных с помощью машинного обучения Azure](sql-data-warehouse-get-started-analyze-with-azure-machine-learning.md).

## <a name="azure-stream-analytics"></a>Azure Stream Analytics

Azure Stream Analytics — это сложная полностью управляемая инфраструктура для обработки и потребления данных о событиях, создаваемых концентратором событий Azure.  Интеграция с пулом SQL позволяет эффективно обрабатывать потоковые данные и хранить их вместе с реляционными данными, обеспечивая более глубокий и расширенный анализ.  

* **Выходные данные задания:** Отправка выходных данных заданий Stream Analytics непосредственно в пул SQL.

Дополнительные сведения см. в статье [Работа со службой Azure Stream Analytics и хранилищем данных SQL](sql-data-warehouse-integrate-azure-stream-analytics.md).
