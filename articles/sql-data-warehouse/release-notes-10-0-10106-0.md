---
title: 'Хранилище данных SQL Azure: заметки о выпуске за декабрь 2018 г. | Документация Майкрософт'
description: Заметки о выпуске для хранилища данных SQL Azure.
services: sql-data-warehouse
author: twounder
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: manage
ms.date: 12/12/2018
ms.author: mausher
ms.reviewer: twounder
ms.openlocfilehash: 8e82e352ebea4634b1b99864245adcf606352657
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/31/2019
ms.locfileid: "55469349"
---
# <a name="whats-new-in-azure-sql-data-warehouse-version---100101060"></a>Новые возможности Хранилища данных SQL Azure версии 10.0.10106.0
Хранилище данных SQL Azure (Хранилище данных SQL) постоянно улучшается. В этой статье описаны новые возможности и изменения, представленные в Хранилище данных SQL версии 10.0.10106.0.

## <a name="query-restartability---ctas-and-insertselect"></a>Возможность перезапуска запроса — CTAS и INSERT/SELECT
В редких случаях (то есть в случае периодических проблем с сетевым подключением, сбоев узлов) запросы, выполняемые в Хранилище данных SQL Azure, могут завершаться сбоем. Более длительные инструкции, такие как [CREATE TABLE AS SELECT (CTAS)](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-develop-ctas) и операции INSERT-SELECT, больше подвержены этой потенциальной проблеме. В этом выпуске Хранилище данных SQL Azure реализует логику повтора для инструкций CTAS и INSERT-SELECT (в дополнение к ранее объявленным инструкциям SELECT), позволяя системе прозрачно обрабатывать эти временные проблемы и предотвращать сбои запросов. Число повторных попыток и список обрабатываемых временных ошибок настраиваются системой.

## <a name="return-order-by-optimization"></a>Оптимизация возврата результатов запросов по порядку
В этом выпуске повышается производительность запросов SELECT...ORDER BY.  Ранее подсистема выполнения запросов упорядочивала результаты на каждом вычислительном узле и передавала их потоком на управляющий узел, где результаты объединялись. Благодаря этому улучшению все вычислительные узлы отправляют результаты на один вычислительный узел, который затем объединяет их и возвращает отсортированные результаты пользователю через вычислительный узел.  Это обеспечивает прирост производительности, если результирующий набор запросов содержит большое количество строк.

## <a name="data-movement-enhancements-for-partitionmove-and-broadcastmove"></a>Усовершенствования перемещения данных для PartitionMove и BroadcastMove
В Хранилище данных SQL Azure 2-го поколения действия перемещения данных типа ShuffleMove используют методики мгновенного перемещения данных, описанные в [блоге по улучшениям производительности](https://azure.microsoft.com/blog/lightning-fast-query-performance-with-azure-sql-data-warehouse/).  В этом выпуске типы перемещения данных PartitionMove и BroadcastMove теперь основаны на тех же методах мгновенного перемещения данных.  Запросы пользователей, которые используют эти типы действий перемещения данных, также будут выполняться быстрее.  Чтобы воспользоваться преимуществами повышения производительности, не нужно вносить изменения в код.

## <a name="next-steps"></a>Дополнительная информация
Теперь, когда вам уже известны некоторые сведения о хранилище данных SQL, узнайте о том, как его [создать][create a SQL Data Warehouse]. Если вы раньше не работали с Azure, используйте [глоссарий Azure][Azure glossary], чтобы узнать значение новых терминов. Или ознакомьтесь со следующими ресурсами, посвященными хранилищу данных SQL.  

* [Истории успеха клиентов]
* [Блоги]
* [Запросы функций]
* [Видеоролики]
* [Блоги группы консультирования клиентов]
* [Форум Stack Overflow]
* [Twitter]


[Блоги]: https://azure.microsoft.com/blog/tag/azure-sql-data-warehouse/
[Блоги группы консультирования клиентов]: https://blogs.msdn.microsoft.com/sqlcat/tag/sql-dw/
[Истории успеха клиентов]: https://azure.microsoft.com/case-studies/?service=sql-data-warehouse
[Запросы функций]: https://feedback.azure.com/forums/307516-sql-data-warehouse
[Форум Stack Overflow]: http://stackoverflow.com/questions/tagged/azure-sqldw
[Twitter]: https://twitter.com/hashtag/SQLDW
[Видеоролики]: https://azure.microsoft.com/documentation/videos/index/?services=sql-data-warehouse
[create a SQL Data Warehouse]: ./create-data-warehouse-portal.md
[Azure glossary]: ../azure-glossary-cloud-terminology.md
