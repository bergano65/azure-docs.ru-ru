---
title: Создавайте интегрированные решения
description: Инструменты решения и партнеры, которые интегрируются с пулом Synapse S'L.
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
ms.sourcegitcommit: d597800237783fc384875123ba47aab5671ceb88
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/03/2020
ms.locfileid: "80633139"
---
# <a name="integrate-other-services-with-a-sql-analytics-data-warehouse"></a>Интеграция других служб со складом данных S'L Analytics

Возможность анализа S'L в Azure Synapse Analytics позволяет пользователям интегрироваться со многими другими службами azure. Используя s'L Analytics, вы можете создать хранилище данных с помощью ресурса s-L Pool, который затем может использовать несколько дополнительных услуг, некоторые из которых включают в себя:

* Power BI
* Фабрика данных Azure
* Машинное обучение Azure
* Azure Stream Analytics

Для получения дополнительной информации об интеграционных службах в Azure просмотрите статью [партнеров по интеграции.](sql-data-warehouse-partner-data-integration.md)

## <a name="power-bi"></a>Power BI

Интеграция Power BI позволяет комбинировать вычислительную мощность хранилища данных с динамической отчетностью и визуализацией Power BI. На данный момент интеграция с Power BI включает:

* **Direct Connect**: Более продвинутое соединение с логическим pushdown против хранилища данных, подготовленного с помощью пула S'L. Это обеспечивает более быстрый анализ в большем масштабе.
* **Открыто в Power BI**: Кнопка "Открыть в Power BI" передает информацию о экземпляре в Power BI для упрощенного способа подключения.

Дополнительные сведения см. в статье [Использование Power BI с хранилищем данных SQL](sql-data-warehouse-get-started-visualize-with-power-bi.md) или в [документации по Power BI](https://powerbi.microsoft.com/blog/exploring-azure-sql-data-warehouse-with-power-bi/).

## <a name="azure-data-factory"></a>Фабрика данных Azure

Фабрика данных Azure предоставляет пользователям управляемую платформу для создания сложных конвейеров извлечения и загрузки. Интеграция пула с Azure Data Factory включает в себя:

* **Сохраненные процедуры**: Оркестр исполнения сохраненных процедур.
* **Копирование**: Используйте ADF для перемещения данных в пул S'L. В основе этой операции может использоваться стандартный механизм перемещения данных ADF или PolyBase.

Дополнительную информацию см. в статье [Визуализация данных с помощью Power BI](../../data-factory/load-azure-sql-data-warehouse.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json).

## <a name="azure-machine-learning"></a>Машинное обучение Azure

Служба "Машинное обучение Azure" — это полностью управляемая аналитическая служба, позволяющая пользователям создавать сложные модели с использованием крупного набора прогнозируемых инструментов. Пул s'L поддерживается как источник, так и пункт назначения для этих моделей и имеет следующую функциональность:

* **Читать данные:** Модели привода в масштабе с использованием T-S'L против пула S'L.
* **Напишите данные:** Зафиксировать изменения с любой модели обратно в пул S'L.

Дополнительную информацию см. в статье [Анализ данных с помощью машинного обучения Azure](sql-data-warehouse-get-started-analyze-with-azure-machine-learning.md).

## <a name="azure-stream-analytics"></a>Azure Stream Analytics

Azure Stream Analytics — это сложная полностью управляемая инфраструктура для обработки и потребления данных о событиях, создаваемых концентратором событий Azure.  Интеграция с пулом S'L позволяет эффективно обрабатывать и хранить потоковые данные наряду с реляционными данными, позволяющими более глубоко и продвинутый анализ.  

* **Выход задания:** Отправка выходных данных из заданий Stream Analytics непосредственно в пул S'L.

Дополнительные сведения см. в статье [Работа со службой Azure Stream Analytics и хранилищем данных SQL](sql-data-warehouse-integrate-azure-stream-analytics.md).
