---
title: Заметки о выпуске для Хранилища данных SQL Azure | Документация Майкрософт
description: Заметки о выпуске для хранилища данных SQL Azure.
services: sql-data-warehouse
author: mlee3gsd
ms.author: anumjs
ms.reviewer: jrasnick
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: manage
ms.date: 02/09/2019
ms.openlocfilehash: 2ac60287c9d92ab6230e1dd6777504036e54492d
ms.sourcegitcommit: b3d74ce0a4acea922eadd96abfb7710ae79356e0
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/14/2019
ms.locfileid: "56244680"
---
# <a name="azure-sql-data-warehouse-release-notes-and-documentation-updates"></a>Заметки о выпуске и обновление документации по Хранилищу данных SQL Azure

Хранилище данных SQL Azure (SQL DW) — это облачное корпоративное хранилище данных, использующее массовую параллельную обработку (MPP) для быстрого выполнения сложных запросов к данным, объем которых исчисляется петабайтами. Используйте хранилище данных SQL как ключевой компонент решения для больших данных. Импортируйте большие данные в хранилище данных SQL с помощью простых запросов T-SQL PolyBase и используйте возможности MPP для выполнения высокопроизводительной аналитики. Во время интеграции и анализа хранилище данных станет единственным оптимальным и надежным вариантом получения аналитических сведений для вашей компании.

Щелкните ссылки ниже, чтобы подробнее узнать о новых функциях и улучшениях в последней версии Хранилища данных SQL Azure. Получение этих служебных обновлений можно ожидать в течение указанного периода обслуживания.

- [Версия 10.0.10106.0 (январь) Хранилища данных SQL](./release-notes-10-0-10106-0.md)
- [Декабрь 2018 г.](./release-notes-december-2018.md)
- [Октябрь 2018 г.](./release-notes-october-2018.md)
- [Сентябрь 2018 г.](./release-notes-september-2018.md)
- [Август 2018 г.](./release-notes-august-2018.md)
- [Июль 2018 г.](./release-notes-july-2018.md)
- [Июнь 2018 г.](./release-notes-june-2018.md)
- [Май 2018 г.](./release-notes-may-2018.md)

## <a name="checking-the-code-version-that-has-been-applied-to-your-data-warehouse"></a>Проверка версии кода хранилища данных

Чтобы проверить, какой выпуск применялся к хранилищу данных, подключитесь к хранилищу данных с помощью SSMS и запустите следующий код для возврата текущей версии Хранилища данных SQL.

```sql
SELECT @@VERSION AS 'SQL Data Warehouse';
```

Выходные данные примера: ![Версия Хранилища данных SQL](./media/release-notes/dw-version.png)

Используйте полученную версию, чтобы проверить, какой выпуск применялся к Хранилищу данных SQL Azure. 


## <a name="next-steps"></a>Дополнительная информация
- Дополнительные сведения о просмотре расписания обслуживания см. в [этой статье](https://docs.microsoft.com/azure/sql-data-warehouse/viewing-maintenance-schedule). 
- Дополнительные сведения об изменении расписания обслуживания см. в [этой статье](https://docs.microsoft.com/azure/sql-data-warehouse/changing-maintenance-schedule).
- Дополнительные сведения о создании, просмотре оповещений и управлении ими с помощью Azure Monitor см. в [этой статье](https://docs.microsoft.com/azure/monitoring-and-diagnostics/alert-metric).
- Дополнительные сведения о действиях веб-перехватчика для правил генерации оповещений журнала см. в [этой статье](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitor-alerts-unified-log-webhook).
- Дополнительные сведения о службе "Работоспособность служб Azure" см. в [этой статье](https://docs.microsoft.com/azure/service-health/service-health-overview).