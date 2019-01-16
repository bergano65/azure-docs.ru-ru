---
title: Оптимизация кэша 2-го поколения | Документация Майкрософт
description: Узнайте, как выполнять мониторинг кэша 2-го поколения на портале Azure.
services: sql-data-warehouse
author: kevinvngo
manager: craigg
ms.service: sql-data-warehouse
ms.component: performance
ms.topic: how-to
ms.date: 09/06/2018
ms.author: kevin
ms.reviewer: igorstan
ms.openlocfilehash: 2a0504ae0e5c3dbf70ad84526176beae52f55870
ms.sourcegitcommit: 30d23a9d270e10bb87b6bfc13e789b9de300dc6b
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/08/2019
ms.locfileid: "54103134"
---
# <a name="how-to-monitor-the-gen2-cache"></a>Мониторинг кэша 2-го поколения
Архитектура хранилища 2-го поколения автоматически распределяет наиболее часто запрашиваемые сегменты columnstore в кэше, размещенные на твердотельных накопителях NVMe, разработанных для хранилищ данных 2-го поколения. Производительность повышается, когда запросы получают сегменты, размещенные в кэше. В этой статье описывается выполнение мониторинга и устранение неполадок с низкой производительностью запросов путем определения оптимального использования кэша 2-го поколения рабочей нагрузкой.  
## <a name="troubleshoot-using-the-azure-portal"></a>Устранение неполадок с помощью портала Azure
Вы можете использовать Azure Monitor для просмотра метрик кэша 2-го поколения, чтобы решить проблемы с производительностью запросов. Сначала перейдите на портал Azure и щелкните Monitor:

![Azure Monitor](./media/sql-data-warehouse-cache-portal/cache_0.png)

Нажмите кнопку "Метрики" и укажите **подписку**, **группу** **ресурсов**, **тип ресурса** и **имя ресурсов** своего хранилища данных.

Ключевые метрики для устранения неполадок с кэшем 2-го поколения — это **Cache hit percentage** (Процент попаданий в кэш) и **Cache used percentage** (Процент использования кэша). Настройте диаграмму метрики Azure для отображения этих двух метрик.

![Метрики кэша](./media/sql-data-warehouse-cache-portal/cache_1.png)


## <a name="cache-hit-and-used-percentage"></a>Процент попаданий и процент использования кэша
В матрице ниже описаны сценарии,основанные на значениях метрик кэша:

|                                | **Высокий процент попаданий в кэш** | **Низкий процент попаданий в кэш** |
| :----------------------------: | :---------------------------: | :--------------------------: |
| **Высокий процент использования кэша** |          Сценарий 1           |          Сценарий 2          |
| **Низкий процент использования кэша**  |          Сценарий 3           |          Сценарий 4          |

**Сценарий 1.** Вы оптимально используете кэш. [Устраните неполадки](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-manage-monitor) в других областях, которые могут замедлять выполнение запросов.

**Сценарий 2.** Текущий рабочий набор данных не помещается в кэш, что приводит к низкому проценту попаданий в кэш из-за физических операций чтения. Попробуйте увеличить уровень производительности и повторно запустите рабочую нагрузку для заполнения кэша.

**Сценарий 3.** Вполне вероятно, что запрос выполняется медленно из-за причин, которые не относятся к кэшу. [Устраните неполадки](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-manage-monitor) в других областях, которые могут замедлять выполнение запросов. Вы можете также рассмотреть [уменьшение масштаба экземпляра](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-manage-monitor), чтобы уменьшить размер кэша для сокращения затрат. 

**Сценарий 4.** У вас есть холодный кэш, который может быть причиной медленного выполнения запроса. Подумайте о том, чтобы повторно выполнить свой запрос, так как ваш рабочий набор данных теперь должен быть в режиме кэширования. 

**Внимание! Если процент попаданий в кэш или процент использования кэша не обновляются после повторного выполнения рабочей нагрузки, ваш рабочий набор может уже находиться в памяти. Обратите внимание, что кэшируются только таблицы с кластеризованными индексами columnstore.**

## <a name="next-steps"></a>Дополнительная информация
Дополнительные сведения о настройке производительности общих запросов см. в разделе [Наблюдение за выполнением запросов](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-manage-monitor#monitor-query-execution).


<!--Image references-->

<!--Article references-->
[SQL Data Warehouse best practices]: ./sql-data-warehouse-best-practices.md
[System views]: ./sql-data-warehouse-reference-tsql-system-views.md
[Table distribution]: ./sql-data-warehouse-tables-distribute.md
[Investigating queries waiting for resources]: ./sql-data-warehouse-manage-monitor.md#waiting

<!--MSDN references-->
[sys.dm_pdw_dms_workers]: http://msdn.microsoft.com/library/mt203878.aspx
[sys.dm_pdw_exec_requests]: http://msdn.microsoft.com/library/mt203887.aspx
[sys.dm_pdw_exec_sessions]: http://msdn.microsoft.com/library/mt203883.aspx
[sys.dm_pdw_request_steps]: http://msdn.microsoft.com/library/mt203913.aspx
[sys.dm_pdw_sql_requests]: http://msdn.microsoft.com/library/mt203889.aspx
[DBCC PDW_SHOWEXECUTIONPLAN]: http://msdn.microsoft.com/library/mt204017.aspx
[DBCC PDW_SHOWSPACEUSED]: http://msdn.microsoft.com/library/mt204028.aspx
[LABEL]: https://msdn.microsoft.com/library/ms190322.aspx
