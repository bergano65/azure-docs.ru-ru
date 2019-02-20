---
title: Заметки о выпуске для Хранилища данных SQL Azure | Документация Майкрософт
description: Заметки о выпуске для хранилища данных SQL Azure.
services: sql-data-warehouse
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: manage
ms.date: 02/09/2019
author: mlee3gsd
ms.author: anumjs
ms.reviewer: jrasnick
manager: craigg
ms.openlocfilehash: 51932ebf7d5bdc6830098ce7136a3eee7255ffe1
ms.sourcegitcommit: b3d74ce0a4acea922eadd96abfb7710ae79356e0
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/14/2019
ms.locfileid: "56245513"
---
# <a name="azure-sql-data-warehouse-release-notes"></a>Заметки о выпуске для Хранилища данных SQL Azure
В этой статье перечислены новые функции и улучшения в последних выпусках [SQL Server на виртуальных машинах Azure](sql-data-warehouse-overview-what-is.md). В ней также указаны заметные обновления содержимого, которые не связаны напрямую с выпуском, но опубликованы в тот же интервал времени. Улучшения других служб Azure см. в статье [Обновления Azure](https://azure.microsoft.com/updates)

## <a name="sql-data-warehouse-version-100101060-january"></a>Версия 10.0.10106.0 (январь) Хранилища данных SQL

### <a name="service-improvements"></a>Улучшения службы

| Улучшения службы | Сведения |
| --- | --- |
| **Возможность перезапуска запроса — CTAS и INSERT/SELECT** | В редких случаях (то есть в случае периодических проблем с сетевым подключением, сбоев узлов) запрос, выполняемый в Хранилище данных SQL Azure, может завершиться сбоем. Более длительные инструкции, такие как [CREATE TABLE AS SELECT (CTAS)](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-develop-ctas) и операции INSERT-SELECT, больше подвержены этой потенциальной проблеме. В этом выпуске Хранилище данных SQL Azure реализует логику повтора для инструкций CTAS и INSERT-SELECT в дополнение к ранее объявленным инструкциям SELECT. Изменения позволяют системе прозрачно обрабатывать эти временные проблемы и предотвращать сбои запросов. Число повторных попыток и список обрабатываемых временных ошибок настраиваются системой.|
|**Оптимизация возврата результатов запросов по порядку**|В этом выпуске повышается производительность запросов SELECT...ORDER BY.   Теперь все вычислительные узлы отправляют свои результаты на один вычислительный узел, который объединяет и сортирует результаты, которые возвращаются пользователю через вычислительный узел.  Объединение через один вычислительный узел обеспечивает прирост производительности, если результирующий набор запросов содержит большое количество строк. Ранее подсистема выполнения запросов упорядочивала результаты на каждом вычислительном узле и передавала их потоком на управляющий узел, где результаты объединялись.|
|**Усовершенствования перемещения данных для PartitionMove и BroadcastMove**|В Хранилище данных SQL Azure 2-го поколения действия перемещения данных типа ShuffleMove используют методики мгновенного перемещения данных, описанные в блоге по улучшениям производительности [здесь](https://azure.microsoft.com/blog/lightning-fast-query-performance-with-azure-sql-data-warehouse/). В этом выпуске типы перемещения данных PartitionMove и BroadcastMove теперь основаны на тех же методах мгновенного перемещения данных. Запросы пользователей, которые используют эти типы действий перемещения данных, будут выполняться с повышенной производительностью. Чтобы воспользоваться преимуществами улучшений производительности, не нужно вносить изменения в код.|

### <a name="documentation-improvements"></a>Улучшения документации

| Улучшения документации | Сведения |
| --- | --- |
|Нет | |
| | |

## <a name="next-steps"></a>Дополнительная информация
[создать хранилище данных SQL](./create-data-warehouse-portal.md)

## <a name="more-information"></a>Дополнительные сведения
- [Хранилище данных Azure SQL](https://azure.microsoft.com/blog/tag/azure-sql-data-warehouse/)
- [Блоги группы консультирования клиентов](https://blogs.msdn.microsoft.com/sqlcat/tag/sql-dw/)
- [Истории успеха клиентов](https://azure.microsoft.com/case-studies/?service=sql-data-warehouse)
- [Форум Stack Overflow](http://stackoverflow.com/questions/tagged/azure-sqldw)
- [Twitter](https://twitter.com/hashtag/SQLDW)
- [Видеоролики](https://azure.microsoft.com/documentation/videos/index/?services=sql-data-warehouse)
- [глоссарий Azure Глоссарий](../azure-glossary-cloud-terminology.md)