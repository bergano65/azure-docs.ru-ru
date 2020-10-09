---
title: Создание интегрированных решений
description: Сведения о средствах и партнерах по созданию решений, поддерживающих интеграцию с пулом Synapse SQL.
services: synapse-analytics
author: mlee3gsd
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 04/17/2018
ms.author: martinle
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: 16cfdfb475ce21ed4b51dc9140e59df701363f27
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/09/2020
ms.locfileid: "85201010"
---
# <a name="integrate-other-services-with-a-synapse-sql-pool-data-warehouse"></a>Интеграция других служб с пулом Synapse SQL (хранилищем данных)

Возможность создания пула SQL в Azure Synapse Analytics позволяет интегрировать в Azure многие другие службы. С помощью Synapse SQL вы можете создать хранилище на основе ресурса пула SQL, что в свою очередь позволит использовать несколько дополнительных служб, например:

* Power BI
* Фабрика данных Azure
* Машинное обучение Azure
* Azure Stream Analytics

Дополнительные сведения об интеграции разных служб Azure см. в статье [Партнеры по интеграции данных в Azure Synapse Analytics](sql-data-warehouse-partner-data-integration.md).

## <a name="power-bi"></a>Power BI

Интеграция с Power BI позволяет совмещать вычислительные ресурсы хранилища данных с возможностями создания динамических отчетов и визуализации в Power BI. На данный момент интеграция с Power BI включает:

* **Прямое соединение**: расширенное подключение с логической опорой на хранилище данных, подготовленное с помощью пула SQL. Это обеспечивает более быстрый анализ в большем масштабе.
* **Открыть в Power BI**: кнопка "Открыть в Power BI" передает сведения об экземпляре в Power BI, упрощая подключение.

Дополнительные сведения см. в статье [Использование Power BI с хранилищем данных SQL](sql-data-warehouse-get-started-visualize-with-power-bi.md) или в [документации по Power BI](https://powerbi.microsoft.com/blog/exploring-azure-sql-data-warehouse-with-power-bi/).

## <a name="azure-data-factory"></a>Фабрика данных Azure

Фабрика данных Azure предоставляет пользователям управляемую платформу для создания сложных конвейеров извлечения и загрузки. Интеграция пула SQL с Фабрикой данных Azure распространяется на следующие элементы.

* **Хранимые процедуры.** Оркестрация выполнения хранимых процедур.
* **Действие копирования.** Использование ADF для перемещения данных в пул SQL. В основе этой операции может использоваться стандартный механизм перемещения данных ADF или PolyBase.

Дополнительную информацию см. в статье [Визуализация данных с помощью Power BI](../../data-factory/load-azure-sql-data-warehouse.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json).

## <a name="azure-machine-learning"></a>Машинное обучение Azure

Служба "Машинное обучение Azure" — это полностью управляемая аналитическая служба, позволяющая пользователям создавать сложные модели с использованием крупного набора прогнозируемых инструментов. Пул SQL поддерживается для этих моделей в качестве источника и предоставляет следующие возможности.

* **Чтение данных**. Использование моделей в большом масштабе с помощью T-SQL в пуле SQL.
* **Запись данных.** Фиксация изменений из любой модели в пул SQL.

Дополнительную информацию см. в статье [Анализ данных с помощью машинного обучения Azure](sql-data-warehouse-get-started-analyze-with-azure-machine-learning.md).

## <a name="azure-stream-analytics"></a>Azure Stream Analytics

Azure Stream Analytics — это сложная полностью управляемая инфраструктура для обработки и потребления данных о событиях, создаваемых концентратором событий Azure.  Интеграция с пулом SQL обеспечивает эффективную обработку и хранение не только реляционных, но и потоковых данных, позволяя проводить углубленный и более сложный анализ.  

* **Выходные данные заданий.** Отправка выходных данных заданий Stream Analytics напрямую в пул SQL.

Дополнительные сведения см. в статье [Работа со службой Azure Stream Analytics и хранилищем данных SQL](sql-data-warehouse-integrate-azure-stream-analytics.md).
