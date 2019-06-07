---
title: Визуализация данных Azure обозреватель данных
description: Дополнительные сведения о различных способах, вы можете визуализировать данные обозреватель данных Azure
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: conceptual
ms.date: 06/03/2019
ms.openlocfilehash: 85c37b6d626fc9942f5df956e738431d2727d282
ms.sourcegitcommit: cababb51721f6ab6b61dda6d18345514f074fb2e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/04/2019
ms.locfileid: "66481839"
---
# <a name="data-visualization-with-azure-data-explorer"></a>Визуализация данных с помощью обозревателя данных Azure 

Обозреватель данных Azure — это служба исследования высокомасштабируемые данных для данных журналов и данных телеметрии, который позволяет создавать сложные аналитические решения для больших объемов данных. Обозреватель данных Azure интегрируется с помощью различных средств визуализации, поэтому можно визуализировать данные и совместно использовать результаты в вашей организации. Эти данные могут быть преобразованы в ценную практическую действенные на ваш бизнес.

Визуализация данных и создания отчетов является важным шагом в процессе аналитики данных. Обозреватель данных Azure поддерживает многие службы бизнес-Аналитики, поэтому вы можете использовать тот, который лучше всего подходит для вашего сценария и бюджета.

* Azure обозреватель данных визуализации: С помощью языка запросов Kusto [ `render operator` ](/azure/kusto/query/renderoperator) предлагает различные типы визуализаций для отображения результатов запроса. Запрос визуализации могут быть полезны в обнаружение аномалий и прогнозирование, машинное обучение и многое другое.

* [Power BI](https://powerbi.microsoft.com). Обозреватель данных Azure предоставляет возможность подключения к Power BI с помощью различных методов: 

  * [Встроенные собственный соединитель Power BI](/azure/data-explorer/power-bi-connector)

  * [Импорт запроса с помощью обозревателя данных Azure в Power BI](/azure/data-explorer/power-bi-imported-query)
 
  * [SQL-запрос](/azure/data-explorer/power-bi-sql-query).

* [Microsoft Excel](https://products.office.com/excel): Обозреватель данных Azure предоставляет возможность подключения к Excel, используя собственный встроенный соединитель Excel, или импортировать запрос с помощью обозревателя данных Azure в Excel.

* [Grafana](https://grafana.com): Grafana предоставляет подключаемый модуль обозревателя данных Azure, которая позволяет визуализировать данные с помощью обозревателя данных Azure. Вы [настроить обозреватель данных Azure как источник данных для Grafana и последующей их визуализации](/azure/data-explorer/grafana)

* [Sisense](https://www.sisense.com): Обозреватель данных Azure предоставляет возможность подключения к Sisense с помощью соединителя JDBC. Вы [установить обозреватель данных Azure как источник данных для Sisense и последующей их визуализации](/azure/data-explorer/sisense).

* [Tableau](https://www.tableau.com): Обозреватель данных Azure предоставляет возможность подключения с помощью Tableau [соединитель ODBC и представить их в Tableau](/azure/data-explorer/connect-odbc).

* [Qlik](https://www.qlik.com): Обозреватель данных Azure предоставляет возможность подключения с помощью Qlik [соединитель ODBC](/azure/data-explorer/connect-odbc).