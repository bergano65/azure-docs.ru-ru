---
title: Заметки о выпуске базы данных Azure SQL | Документация Майкрософт
description: Дополнительные сведения о новых функциях и улучшениях в службу базы данных SQL Azure и в документации по базе данных SQL Azure
services: sql-database
author: stevestein
manager: craigg
ms.service: sql-database
ms.subservice: service
ms.devlang: ''
ms.topic: conceptual
ms.date: 05/15/2019
ms.author: sstein
ms.openlocfilehash: be39aa63cf923e74dc7ed13ef78ba554ffd3b8a4
ms.sourcegitcommit: cf438e4b4e351b64fd0320bf17cc02489e61406a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/08/2019
ms.locfileid: "67658017"
---
# <a name="sql-database-release-notes"></a>Заметки о выпуске базы данных SQL

В этой статье перечислены функции базы данных SQL, используемые в данный момент в общедоступной предварительной версии. Для обновления базы данных SQL и усовершенствований, см. в разделе [обновления базы данных SQL](https://azure.microsoft.com/updates/?product=sql-database). Для обновлений и исправлений к другим службам Azure, см. в разделе [обновления службы](https://azure.microsoft.com/updates).

## <a name="features-in-public-preview"></a>Функции в общедоступной предварительной версии

| Компонент | Сведения |
| ---| --- |
| Восстановление ускоренной базы данных с помощью отдельных баз данных и пулов эластичных баз данных | Сведения см. в разделе [Ускорение восстановления базы данных](sql-database-accelerated-database-recovery.md).|
|Приблизительный подсчет различных объектов|Сведения см. в разделе [приблизительное Count Distinct](https://docs.microsoft.com/sql/relational-databases/performance/intelligent-query-processing#approximate-query-processing).|
|Пакетный режим Rowstore (при уровне совместимости 150)|Сведения см. в разделе [пакетный режим Rowstore](https://docs.microsoft.com/sql/relational-databases/performance/intelligent-query-processing#batch-mode-on-rowstore).|
| Обнаружение и классификация данных  |Сведения см. в разделе [базы данных SQL Azure и хранилище данных SQL обнаружение и классификация данных](sql-database-data-discovery-and-classification.md).|
| Задания обработки эластичных баз данных | Сведения см. в разделе [создания, настройки и управления масштабируемыми](elastic-jobs-overview.md). |
| Эластичные запросы | Сведения см. в разделе [Обзор эластичных запросов](sql-database-elastic-query-overview.md). |
| Эластичные транзакции | [Распределенные транзакции по облачным базам данных](sql-database-elastic-transactions-overview.md). |
|Обратной связи (режим строк) (на уровне совместимости 150)|Сведения см. в разделе [обратную связь с выделением памяти (в режиме строки)](https://docs.microsoft.com/sql/relational-databases/performance/intelligent-query-processing#row-mode-memory-grant-feedback).|
| Редактор запросов на портале Azure |Сведения см. в разделе [использование редактора запросов SQL на портале Azure для подключения и запроса данных](sql-database-connect-query-portal.md).|
| Службы R, / машинное обучение с помощью отдельных баз данных и пулов эластичных баз данных |Сведения см. в разделе [служб машинного обучения в базе данных SQL Azure](https://docs.microsoft.com/sql/advanced-analytics/what-s-new-in-sql-server-machine-learning-services?view=sql-server-2017#machine-learning-services-in-azure-sql-database).|
| Повторное создание баз данных с управляемых экземпляров |Сведения см. в разделе [повторного создания удалении баз данных в управляемый экземпляр SQL Azure](https://medium.com/azure-sqldb-managed-instance/re-create-dropped-databases-in-azure-sql-managed-instance-dc369ed60266).|
| Репликация с помощью управляемых экземпляров |Сведения см. в разделе [Настройка репликации в базе данных управляемого экземпляра базы данных SQL Azure](replication-with-sql-database-managed-instance.md).|
| Уровень бессерверных вычислений | Сведения см. в разделе [базы данных SQL без сервера (Предварительная версия)](sql-database-serverless.md).|
|Аналитика SQL|Сведения см. в разделе [аналитика SQL Azure](../azure-monitor/insights/azure-sql.md).|
|Таблица компиляции переменной отложенного (при уровне совместимости 150)|Сведения см. в разделе [переменной отложенной компиляции таблицы](https://docs.microsoft.com/sql/relational-databases/performance/intelligent-query-processing#table-variable-deferred-compilation).|
| Обнаружение угроз с помощью управляемых экземпляров |Сведения см. в разделе [Настройка обнаружения угроз для базы данных SQL управляемого экземпляра](sql-database-managed-instance-threat-detection.md).|
| Прозрачное шифрование данных (TDE) с перевести собственных ключей (BYOK) с помощью управляемых экземпляров |Сведения см. в разделе [Azure прозрачного шифрования данных SQL с помощью управляемых клиентом ключей в хранилище ключей Azure: Поддержка создания собственных ключей использования](transparent-data-encryption-byok-azure-sql.md).|
| &nbsp; |

## <a name="updates"></a>Обновления

Список обновлений базы данных SQL и улучшения, см. в разделе [обновления базы данных SQL](https://azure.microsoft.com/updates/?product=sql-database).

Для обновлений и исправлений для всех служб Azure, см. в разделе [обновления службы](https://azure.microsoft.com/updates).

## <a name="contribute-to-content"></a>Участия в создании содержимого

Редактирование документации базы данных SQL Azure, см. в разделе [Документация Майкрософт](https://docs.microsoft.com/contribute/).
