---
title: Хранилище данных SQL Azure, заметки о выпуске за август 2018 г. | Документация Майкрософт
description: Заметки о выпуске для хранилища данных SQL Azure.
services: sql-data-warehouse
author: anumjs
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: ''
ms.date: 08/13/2018
ms.author: anjangsh
ms.reviewer: jrasnick
ms.openlocfilehash: f0840e9b91c81b8a99e8c736c3c5db082c92fe76
ms.sourcegitcommit: 67625c53d466c7b04993e995a0d5f87acf7da121
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/20/2019
ms.locfileid: "65912208"
---
# <a name="whats-new-in-azure-sql-data-warehouse-august-2018"></a>Что нового в Хранилище данных SQL Azure? Август 2018 г.
Хранилище данных SQL Azure постоянно совершенствуется. В этой статье описаны новые возможности и изменения, вступившие в силу с августа 2018 года.

## <a name="automatic-intelligent-insights"></a>Автоматический Intelligent Insights
Корпорация Майкрософт представила [автоматический Intelligent Insights](https://azure.microsoft.com/blog/automatic-intelligent-insights-to-optimize-performance-with-sql-data-warehouse/), чтобы выполнить обязательство по внедрению автоматизации в хранилище данных в облаке. Больше не нужно будет отслеживать хранилище данных на неравномерное распределение данных и неоптимальную статистику таблиц. Без дополнительной платы хранилище данных SQL предоставляет Intelligent Insights для всех экземпляров Gen2. Благодаря интеграции с [Помощником по Azure](https://docs.microsoft.com/azure/advisor/advisor-performance-recommendations) можно автоматически получать лучшие практические рекомендации по повышению производительности активных рабочих нагрузок. Хранилище данных SQL анализирует рабочие нагрузки и выводит рекомендации на основе вашего использования. Этот анализ проводится ежедневно и дает возможность отслеживать отчеты об использовании и рекомендации по улучшению рабочей нагрузки.

Рекомендации можно просмотреть на портале Помощника по Azure: ![Рекомендации на портале Помощника по Azure для Хранилища данных SQL Azure](https://azurecomcdn.azureedge.net/mediahandler/acomblog/media/Default/blog/4e205b6d-df04-48db-8eec-d591f2592cf4.png)

Вы также можете детальнее рассмотреть каждую категорию, чтобы просмотреть рекомендации по определенному оповещению: ![Сведения о рекомендации на портале Помощника по Azure для Хранилища данных SQL Azure](https://azurecomcdn.azureedge.net/mediahandler/acomblog/media/Default/blog/3c42426e-6969-46e3-9025-c34c0755a302.png)


## <a name="bug-fixes"></a>Исправления ошибок

| Название | Описание |
|:---|:---|
| **Потенциальные сбои запросов, когда счетчик разделителей превышает максимальный предел** |В случае превышения ограничения верхнего предела в 1 миллион файловых разделений необработанное исключение вызывает отказ SQL-сервера и все запросы дают сбой. Это исправление устранило проблему, правильно обрабатывая исключение и возвращая ошибку, не вызывая сбоев запросов. |
| **Увеличение значения по умолчанию ExternalMoveReadersPerNode для повышения производительности загрузки** |Эта проблема была вызвана тем, что параметр свойства ExternalMoveReadersPerNode не синхронизирован с параметром Service Fabric. Эта регрессия стала причиной низкой производительности загрузки 2-го поколения. Исправление возвращает производительность загрузки 2-го поколения с оптимизированными параметрами конструктора.|


## <a name="next-steps"></a>Дальнейшие действия
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
[Форум Stack Overflow]: https://stackoverflow.com/questions/tagged/azure-sqldw
[Twitter]: https://twitter.com/hashtag/SQLDW
[Видеоролики]: https://azure.microsoft.com/documentation/videos/index/?services=sql-data-warehouse
[create a SQL Data Warehouse]: ./create-data-warehouse-portal.md
[Azure glossary]: ../azure-glossary-cloud-terminology.md
