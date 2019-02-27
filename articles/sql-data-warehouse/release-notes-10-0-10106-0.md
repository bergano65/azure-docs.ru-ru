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
ms.openlocfilehash: 2de7c335e56117f2a99db5150575ed94616467e9
ms.sourcegitcommit: 75fef8147209a1dcdc7573c4a6a90f0151a12e17
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/20/2019
ms.locfileid: "56455591"
---
# <a name="azure-sql-data-warehouse-release-notes"></a>Заметки о выпуске для Хранилища данных SQL Azure
В этой статье перечислены новые функции и улучшения в последних выпусках [Хранилища данных SQL Azure](sql-data-warehouse-overview-what-is.md). В ней также указаны заметные обновления содержимого, которые не связаны напрямую с выпуском, но опубликованы в тот же интервал времени. Улучшения других служб Azure см. в статье [Обновления Azure](https://azure.microsoft.com/updates)

## <a name="sql-data-warehouse-version-100101060-january"></a>Версия 10.0.10106.0 (январь) Хранилища данных SQL

### <a name="service-improvements"></a>Улучшения службы

| Улучшения службы | Сведения |
| --- | --- |
|**Оптимизация возврата результатов запросов по порядку**|В этом выпуске повышается производительность запросов SELECT...ORDER BY.   Теперь все вычислительные узлы отправляют свои результаты на один вычислительный узел, который объединяет и сортирует результаты, которые возвращаются пользователю через вычислительный узел.  Объединение через один вычислительный узел обеспечивает прирост производительности, если результирующий набор запросов содержит большое количество строк. Ранее подсистема выполнения запросов упорядочивала результаты на каждом вычислительном узле и передавала их потоком на управляющий узел, где результаты объединялись.|
|**Усовершенствования перемещения данных для PartitionMove и BroadcastMove**|В Хранилище данных SQL Azure 2-го поколения действия перемещения данных типа ShuffleMove используют методики мгновенного перемещения данных, описанные в блоге по улучшениям производительности [здесь](https://azure.microsoft.com/blog/lightning-fast-query-performance-with-azure-sql-data-warehouse/). В этом выпуске типы перемещения данных PartitionMove и BroadcastMove теперь основаны на тех же методах мгновенного перемещения данных. Запросы пользователей, которые используют эти типы действий перемещения данных, будут выполняться с повышенной производительностью. Чтобы воспользоваться преимуществами улучшений производительности, не нужно вносить изменения в код.|
|**Важные ошибки**|Неправильная версия Хранилища данных SQL Azure. SELECT @@VERSION может вернуть неправильную версию 10.0.9999.0. Правильная версия текущего выпуска — 10.0.10106.0. Эта ошибка нам известна и находится на рассмотрении.

### <a name="documentation-improvements"></a>Улучшения документации

| Улучшения документации | Сведения |
| --- | --- |
|Нет | |
| | |

## <a name="next-steps"></a>Дополнительная информация
- [создать хранилище данных SQL](./create-data-warehouse-portal.md)

## <a name="more-information"></a>Дополнительные сведения
- [Хранилище данных Azure SQL](https://azure.microsoft.com/blog/tag/azure-sql-data-warehouse/)
- [Блоги группы консультирования клиентов](https://blogs.msdn.microsoft.com/sqlcat/tag/sql-dw/)
- [Истории успеха клиентов](https://azure.microsoft.com/case-studies/?service=sql-data-warehouse)
- [Форум Stack Overflow](http://stackoverflow.com/questions/tagged/azure-sqldw)
- [Twitter](https://twitter.com/hashtag/SQLDW)
- [Видеоролики](https://azure.microsoft.com/documentation/videos/index/?services=sql-data-warehouse)
- [глоссарий Azure Глоссарий](../azure-glossary-cloud-terminology.md)
