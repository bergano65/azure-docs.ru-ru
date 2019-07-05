---
title: Визуализация данных Azure обозреватель данных
description: Дополнительные сведения о различных способах, вы можете визуализировать данные обозреватель данных Azure
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: conceptual
ms.date: 06/30/2019
ms.openlocfilehash: d1c73d8eb65ed5d67d5250b4a3bca3b80450001e
ms.sourcegitcommit: 084630bb22ae4cf037794923a1ef602d84831c57
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/03/2019
ms.locfileid: "67536728"
---
# <a name="data-visualization-with-azure-data-explorer"></a>Визуализация данных с помощью обозревателя данных Azure 

Обозреватель данных Azure — это служба исследования высокомасштабируемые данных для данных журналов и данных телеметрии, который позволяет создавать сложные аналитические решения для больших объемов данных. Обозреватель данных Azure интегрируется с помощью различных средств визуализации, поэтому можно визуализировать данные и совместно использовать результаты в вашей организации. Эти данные могут быть преобразованы в ценную практическую действенные на ваш бизнес.

Визуализация данных и создания отчетов является важным шагом в процессе аналитики данных. Обозреватель данных Azure поддерживает многие службы бизнес-Аналитики, поэтому вы можете использовать тот, который лучше всего подходит для вашего сценария и бюджета.

## <a name="kusto-query-language-visualizations"></a>Визуализации языка запросов Kusto

Язык запросов Kusto [ `render operator` ](/azure/kusto/query/renderoperator) предлагает различные визуализации, таких как таблицы, круговые диаграммы и гистограммы для отображения результатов запроса. Запрос визуализации могут быть полезны в обнаружение аномалий и прогнозирование, машинное обучение и многое другое.

## <a name="power-bi"></a>Power BI

Обозреватель данных Azure предоставляет возможность подключения к [Power BI](https://powerbi.microsoft.com) с помощью различных методов: 

  * [Встроенные собственный соединитель Power BI](/azure/data-explorer/power-bi-connector)

  * [Импорт запроса с помощью обозревателя данных Azure в Power BI](/azure/data-explorer/power-bi-imported-query)
 
  * [SQL query](/azure/data-explorer/power-bi-sql-query)

## <a name="microsoft-excel"></a>Microsoft Excel

Обозреватель данных Azure предоставляет возможность подключения к [Microsoft Excel](https://products.office.com/excel) с помощью встроенных собственный соединитель Excel или импортировать запрос с помощью обозревателя данных Azure в Excel.

## <a name="grafana"></a>Grafana

[Grafana](https://grafana.com) предоставляет подключаемый модуль обозревателя данных Azure, которая позволяет визуализировать данные с помощью обозревателя данных Azure. Вы [настроить обозреватель данных Azure как источник данных для Grafana и последующей их визуализации](/azure/data-explorer/grafana). 

## <a name="odbc-connector"></a>Соединитель ODBC

Обозреватель данных Azure предоставляет [Open Database Connectivity (ODBC) соединителя](connect-odbc.md) , любое приложение, поддерживающее ODBC для подключения в обозреватель данных Azure.

## <a name="tableau"></a>Tableau

Обозреватель данных Azure предоставляет возможность подключения к [Tableau](https://www.tableau.com) с помощью [соединитель ODBC](/azure/data-explorer/connect-odbc) и затем [визуализировать данные в Tableau](tableau.md).

## <a name="qlik"></a>Qlik

Обозреватель данных Azure предоставляет возможность подключения к [Qlik](https://www.qlik.com) с помощью [соединитель ODBC](/azure/data-explorer/connect-odbc) Создание Qlik Sense панелей мониторинга и визуализации данных. Используя следующий видеоролик, узнать, как визуализировать данные обозреватель данных Azure с помощью Qlik. 

> [!VIDEO https://www.youtube.com/embed/nhWIiBwxjjU]  

## <a name="sisense"></a>Sisense

Обозреватель данных Azure предоставляет возможность подключения к [Sisense](https://www.sisense.com) с помощью соединителя JDBC. Вы [установить обозреватель данных Azure как источник данных для Sisense и последующей их визуализации](/azure/data-explorer/sisense).