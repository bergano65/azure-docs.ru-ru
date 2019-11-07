---
title: Создание интегрированных решений
description: Средства и партнеры с решениями, которые интегрируются с хранилищем данных SQL Azure.
services: sql-data-warehouse
author: mlee3gsd
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: integration
ms.date: 04/17/2018
ms.author: martinle
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: 6e159e0e254ae8b2515515dfaeb2c514e0f25e0b
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/06/2019
ms.locfileid: "73685638"
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

* **Прямое соединение**: расширенное соединение с логической опорой на хранилище данных SQL. Это обеспечивает более быстрый анализ в большем масштабе.
* **Открыть в Power BI**: кнопка "открыть в Power BI" передает сведения об экземпляре Power BI для упрощения подключения.

Дополнительные сведения см. в статье [Использование Power BI с хранилищем данных SQL](sql-data-warehouse-get-started-visualize-with-power-bi.md) или в [документации по Power BI](https://powerbi.microsoft.com/blog/exploring-azure-sql-data-warehouse-with-power-bi/).

## <a name="azure-data-factory"></a>Фабрика данных Azure
Фабрика данных Azure предоставляет пользователям управляемую платформу для создания сложных конвейеров извлечения и загрузки. Интеграция хранилища данных SQL с фабрикой данных Azure включает в себя следующее.

* **Хранимые процедуры**: управление выполнением хранимых процедур в хранилище данных SQL.
* **Действие копирования.** Используйте ADF для перемещения данных в хранилище данных SQL. В основе этой операции может использоваться стандартный механизм перемещения данных ADF или PolyBase. 

Дополнительную информацию см. в статье [Визуализация данных с помощью Power BI](https://docs.microsoft.com/azure/data-factory/load-azure-sql-data-warehouse?toc=/azure/sql-data-warehouse/toc.json).

## <a name="azure-machine-learning"></a>Машинное обучение Azure
Служба "Машинное обучение Azure" — это полностью управляемая аналитическая служба, позволяющая пользователям создавать сложные модели с использованием крупного набора прогнозируемых инструментов. Хранилище данных SQL поддерживается в качестве источника и цели для этих моделей со следующими возможностями.

* **Чтение данных:** масштабное использование моделей с помощью T-SQL в хранилище данных SQL.
* **Запись данных:** применение изменений любой модели в хранилище данных SQL.

Дополнительную информацию см. в статье [Анализ данных с помощью машинного обучения Azure](sql-data-warehouse-get-started-analyze-with-azure-machine-learning.md).

## <a name="azure-stream-analytics"></a>Azure Stream Analytics
Azure Stream Analytics — это сложная полностью управляемая инфраструктура для обработки и потребления данных о событиях, создаваемых концентратором событий Azure.  Интеграция с хранилищем данных SQL обеспечивает эффективную обработку и хранение потоковых данных наряду с реляционными данными, предоставляя более глубокий и расширенный анализ.  

* **Выходные данные заданий:** отправка выходных данных заданий Stream Analytics напрямую в хранилище данных SQL.

Дополнительные сведения см. в статье [Работа со службой Azure Stream Analytics и хранилищем данных SQL](sql-data-warehouse-integrate-azure-stream-analytics.md).


