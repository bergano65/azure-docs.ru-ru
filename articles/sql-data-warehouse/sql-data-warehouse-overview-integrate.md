---
title: Создание интегрированных решений с помощью хранилища данных SQL | Документация Майкрософт
description: 'Инструменты и партнерские решения, которые интегрируются с хранилищем данных SQL '
services: sql-data-warehouse
author: KavithaJonnakuti
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: consume
ms.date: 04/17/2018
ms.author: kavithaj
ms.reviewer: igorstan
ms.openlocfilehash: d2fcdd4aa28a7d3b1a8e50629e5be1d88046a12b
ms.sourcegitcommit: f8c592ebaad4a5fc45710dadc0e5c4480d122d6f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/29/2019
ms.locfileid: "58621215"
---
# <a name="integrate-other-services-with-sql-data-warehouse"></a>Интеграция других служб с хранилищем данных SQL
Помимо базовых возможностей, хранилище данных SQL позволяет пользователям интегрировать многие другие службы в Azure. К этим службам относятся следующие:

* Power BI
* Фабрика данных Azure
* Машинное обучение Azure
* Azure Stream Analytics

Хранилище данных SQL по-прежнему обеспечивает интеграцию с дополнительными службами в Azure и поддерживает больше [партнеров по интеграции](sql-data-warehouse-partner-data-integration.md).

## <a name="power-bi"></a>Power BI
Интеграция с Power BI позволяет совмещать вычислительные ресурсы хранилища данных SQL с возможностями создания динамических отчетов и визуализацией Power BI. На данный момент интеграция с Power BI включает:

* **Прямое соединение**: расширенное подключение с логической опорой на Хранилище данных SQL. Это обеспечивает более быстрый анализ в большем масштабе.
* **Открыть в Power BI**: кнопка "Открыть в Power BI" передает сведения об экземпляре в Power BI, упрощая подключение.

Дополнительные сведения см. в статье [Использование Power BI с хранилищем данных SQL](sql-data-warehouse-get-started-visualize-with-power-bi.md) или в [документации по Power BI](https://powerbi.microsoft.com/blog/exploring-azure-sql-data-warehouse-with-power-bi/).

## <a name="azure-data-factory"></a>Фабрика данных Azure
Фабрика данных Azure предоставляет пользователям управляемую платформу для создания сложных конвейеров извлечения и загрузки. Интеграция хранилища данных SQL с фабрикой данных Azure включает в себя следующее.

* **Хранимые процедуры.** Управление выполнением хранимых процедур в Хранилище данных SQL.
* **Действие копирования.** Используйте ADF для перемещения данных в Хранилище данных SQL. В основе этой операции может использоваться стандартный механизм перемещения данных ADF или PolyBase. 

Дополнительную информацию см. в статье [Визуализация данных с помощью Power BI](https://docs.microsoft.com/azure/data-factory/load-azure-sql-data-warehouse?toc=/azure/sql-data-warehouse/toc.json).

## <a name="azure-machine-learning"></a>Машинное обучение Azure
Служба "Машинное обучение Azure" — это полностью управляемая аналитическая служба, позволяющая пользователям создавать сложные модели с использованием крупного набора прогнозируемых инструментов. Хранилище данных SQL поддерживается в качестве источника и цели для этих моделей со следующими возможностями.

* **Чтение данных**. Масштабное использование моделей с помощью T-SQL в Хранилище данных SQL.
* **Запись данных.** Применение изменений любой модели в Хранилище данных SQL.

Дополнительную информацию см. в статье [Анализ данных с помощью машинного обучения Azure](sql-data-warehouse-get-started-analyze-with-azure-machine-learning.md).

## <a name="azure-stream-analytics"></a>Azure Stream Analytics
Azure Stream Analytics — это сложная полностью управляемая инфраструктура для обработки и потребления данных о событиях, создаваемых концентратором событий Azure.  Интеграция с хранилищем данных SQL обеспечивает эффективную обработку и хранение потоковых данных наряду с реляционными данными, предоставляя более глубокий и расширенный анализ.  

* **Выходные данные заданий.** Отправка выходных данных заданий Stream Analytics напрямую в Хранилище данных SQL.

Дополнительные сведения см. в статье [Работа со службой Azure Stream Analytics и хранилищем данных SQL](sql-data-warehouse-integrate-azure-stream-analytics.md).


