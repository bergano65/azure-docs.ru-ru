---
title: Визуализация данных обозреватель данных Azure
description: Узнайте о различных способах визуализации данных Azure обозреватель данных
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: gabil
ms.service: data-explorer
ms.topic: conceptual
ms.date: 01/30/2020
ms.openlocfilehash: 32ccc5ad236c87d3609798a8432db14ee440d067
ms.sourcegitcommit: db2d402883035150f4f89d94ef79219b1604c5ba
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/07/2020
ms.locfileid: "77064569"
---
# <a name="data-visualization-with-azure-data-explorer"></a>Визуализация данных с помощью Azure обозреватель данных 

Azure обозреватель данных — это быстрая и Высокомасштабируемая служба просмотра данных для данных журналов и телеметрии, которая используется для создания сложных аналитических решений для больших объемов данных. Azure обозреватель данных интегрируется с различными средствами визуализации, позволяя визуализировать данные и совместно использовать результаты в Организации. Эти данные можно преобразовать в ценную информацию, чтобы повлиять на бизнес.

Визуализация данных и отчетность — это важный шаг в процессе анализа данных. Azure обозреватель данных поддерживает множество служб бизнес-аналитики, что позволяет использовать ту, которая лучше подходит для вашего сценария и бюджета.

## <a name="kusto-query-language-visualizations"></a>Визуализации языка запросов Kusto

[`render operator`](/azure/kusto/query/renderoperator) языка запросов Kusto предлагает различные визуализации, такие как таблицы, круговые диаграммы и линейчатые диаграммы для отображения результатов запроса. Визуализации запросов полезны при обнаружении аномалий и прогнозировании, машинном обучении и т. д.

## <a name="power-bi"></a>Power BI

Azure обозреватель данных предоставляет возможность подключения к [Power BI](https://powerbi.microsoft.com) с помощью различных методов: 

  * [Встроенный соединитель Native Power BI](/azure/data-explorer/power-bi-connector)

  * [Импорт запросов из обозреватель данных Azure в Power BI](/azure/data-explorer/power-bi-imported-query)
 
  * [SQL query](/azure/data-explorer/power-bi-sql-query)

## <a name="microsoft-excel"></a>Microsoft Excel

Azure обозреватель данных предоставляет возможность подключения к [Microsoft Excel](https://products.office.com/excel) с помощью [встроенного собственного соединителя Excel](excel-connector.md)или [импорта запроса](excel-blank-query.md) из обозреватель данных Azure в Excel.

## <a name="grafana"></a>Grafana

[Grafana](https://grafana.com) предоставляет подключаемый модуль Azure обозреватель данных, который позволяет визуализировать данные из обозреватель данных Azure. Вы [настраиваете обозреватель данных Azure в качестве источника данных для Grafana, а затем визуализируйте данные](/azure/data-explorer/grafana). 

## <a name="odbc-connector"></a>Соединитель ODBC

Azure обозреватель данных предоставляет [Соединитель ODBC](connect-odbc.md) , так что любое приложение, которое поддерживает ODBC, может подключаться к Azure обозреватель данных.

## <a name="tableau"></a>Tableau

Azure обозреватель данных предоставляет возможность подключения к [Tableau](https://www.tableau.com) с помощью [соединителя ODBC](/azure/data-explorer/connect-odbc) и [визуализации данных в Tableau](tableau.md).

## <a name="qlik"></a>Qlik

Azure обозреватель данных предоставляет возможность подключения к [Qlik Sense](https://www.qlik.com) с помощью [соединителя ODBC](/azure/data-explorer/connect-odbc) , а затем создает панели мониторинга Qlik senseного датчика и визуализируют данные. С помощью следующего видео вы узнаете, как визуализировать данные обозреватель данных Azure с помощью Qlik Sense. 

> [!VIDEO https://www.youtube.com/embed/nhWIiBwxjjU]  

## <a name="sisense"></a>Sisense

Azure обозреватель данных предоставляет возможность подключения к [Sisense](https://www.sisense.com) с помощью соединителя JDBC. Вы [настраиваете обозреватель данных Azure в качестве источника данных для Sisense, а затем визуализируйте данные](/azure/data-explorer/sisense).

## <a name="redash"></a>Redash

Вы можете использовать [перетире](https://redash.io/) для создания панелей мониторинга и визуализации данных. [Настройте обозреватель данных Azure в качестве источника данных для перештриха, а затем визуализируйте данные](/azure/data-explorer/redash).