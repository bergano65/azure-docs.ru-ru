---
title: Заметки о выпуске базы данных Azure SQL | Документация Майкрософт
description: Дополнительные сведения о новых функциях и улучшениях в службу базы данных SQL Azure и в документации по базе данных SQL Azure
services: sql-database
author: CarlRabeler
manager: craigg
ms.service: sql-database
ms.subservice: service
ms.devlang: ''
ms.topic: conceptual
ms.date: 04/10/2019
ms.author: carlrab
ms.openlocfilehash: 9b961436c81282381f963d16c6c6dd5f289d1259
ms.sourcegitcommit: 1a19a5845ae5d9f5752b4c905a43bf959a60eb9d
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/11/2019
ms.locfileid: "59495111"
---
# <a name="sql-database-release-notes"></a>Заметки о выпуске базы данных SQL

В этой статье перечислены новые функции и усовершенствования в службу базы данных SQL и в документации по базе данных SQL. Улучшения для служб базы данных SQL, см. в разделе также [обновления базы данных SQL](https://azure.microsoft.com/updates/?product=sql-database). Усовершенствования к другим службам Azure, см. в разделе [обновления службы](https://azure.microsoft.com/updates).

## <a name="features-in-public-preview"></a>Функции в общедоступной предварительной версии

| Функция | Сведения |
| ---| --- |
| Задания обработки эластичных баз данных | Сведения см. в разделе [создавать, настраивать и администрировать задания обработки эластичных БД](elastic-jobs-overview.md) |
| Эластичные транзакции | [Распределенные транзакции по облачным базам данных](sql-database-elastic-transactions-overview.md) |
| Эластичные запросы | Сведения см. в разделе [Обзор эластичных запросов](sql-database-elastic-query-overview.md) |
| Репликация с помощью управляемых экземпляров |Сведения см. в разделе [Настройка репликации в базе данных управляемого экземпляра базы данных SQL Azure](replication-with-sql-database-managed-instance.md)|
| Параметры сортировки экземпляра с помощью управляемых экземпляров |Сведения см. в разделе [использование PowerShell с помощью шаблона Azure Resource Manager для создания управляемого экземпляра базы данных SQL Azure](./scripts/sql-managed-instance-create-powershell-azure-resource-manager-template.md)|
| Службы R, / машинное обучение с помощью отдельных баз данных и пулов эластичных баз данных |Сведения см. в разделе [служб машинного обучения в базе данных SQL Azure](https://docs.microsoft.com/sql/advanced-analytics/what-s-new-in-sql-server-machine-learning-services?view=sql-server-2017#machine-learning-services-in-azure-sql-database)|
| Восстановление ускоренной базы данных с помощью отдельных баз данных и пулов эластичных баз данных | Сведения см. в разделе [Ускорение восстановления базы данных](sql-database-accelerated-database-recovery.md)|
| Обнаружение и классификация данных  |Сведения см. в разделе [базы данных SQL Azure и хранилище данных SQL обнаружение и классификация данных](sql-database-data-discovery-and-classification.md)|
| Прозрачное шифрование данных (TDE) с перевести собственных ключей (BYOK) с помощью управляемых экземпляров |Сведения см. в разделе [Azure прозрачного шифрования данных SQL с помощью управляемых клиентом ключей в хранилище ключей Azure: Поддержка создания собственных ключей использования](transparent-data-encryption-byok-azure-sql.md)|
| Повторное создание баз данных с управляемых экземпляров |Сведения см. в разделе [повторного создания удалении баз данных в управляемый экземпляр SQL Azure](https://medium.com/azure-sqldb-managed-instance/re-create-dropped-databases-in-azure-sql-managed-instance-dc369ed60266)|
| Обнаружение угроз с помощью управляемых экземпляров |Сведения см. в разделе [Настройка обнаружения угроз для базы данных SQL управляемого экземпляра](sql-database-managed-instance-threat-detection.md)|
| Гипермасштабируемый уровни служб для отдельных баз данных |Сведения см. в разделе [Гипермасштабируемого уровня службы для до 100 ТБ](sql-database-service-tier-hyperscale.md)|
| Редактор запросов на портале Azure |Сведения см. в разделе [использование редактора запросов SQL на портале Azure для подключения и запроса данных](sql-database-connect-query-portal.md)|
|Приблизительный подсчет различных объектов|Сведения см. в разделе [приблизительное Count Distinct](https://docs.microsoft.com/sql/relational-databases/performance/intelligent-query-processing#approximate-query-processing)|
|Пакетный режим Rowstore (при уровне совместимости 150)|Сведения см. в разделе [пакетный режим Rowstore](https://docs.microsoft.com/sql/relational-databases/performance/intelligent-query-processing#batch-mode-on-rowstore)|
|Обратной связи (режим строк) (на уровне совместимости 150)|Сведения см. в разделе [обратную связь с выделением памяти (в режиме строки)](https://docs.microsoft.com/sql/relational-databases/performance/intelligent-query-processing#row-mode-memory-grant-feedback)|
|Таблица компиляции переменной отложенного (при уровне совместимости 150)|Сведения см. в разделе [переменной отложенной компиляции таблицы](https://docs.microsoft.com/sql/relational-databases/performance/intelligent-query-processing#table-variable-deferred-compilation)|
|Аналитика SQL|Сведения см. в разделе [аналитика SQL Azure](../azure-monitor/insights/azure-sql.md)|
| Поддержку часовых поясов для управляемых экземпляров|Дополнительные сведения см. в разделе [часовой пояс в базе данных управляемого экземпляра SQL Azure](sql-database-managed-instance-timezone.md)|
|||

## <a name="march-2019"></a>Марта 2019 г.

### <a name="service-improvements"></a>Улучшения службы

| Улучшения службы | Сведения |
| --- | --- |
| Общая доступность: поддержка горизонтального масштабирования для чтения для службы "База данных SQL Azure" | Дополнительные сведения см. в разделе [масштабирование для чтения](sql-database-read-scale-out.md)|
| &nbsp; |

### <a name="documentation-improvements"></a>Улучшения документации

| Улучшения документации | Сведения |
| --- | --- |
| Поддержку часовых поясов для управляемых экземпляров|Дополнительные сведения см. в разделе [часовой пояс в базе данных управляемого экземпляра SQL Azure](sql-database-managed-instance-timezone.md)|
| Добавлен журнал ограничения для отдельных баз данных|Дополнительные сведения см. в разделе [единый ограничения ресурсов базы данных виртуальное](sql-database-vcore-resource-limits-single-databases.md).|
| Добавлен журнал ограничения для эластичных пулов и баз данных в составе пула|Дополнительные сведения см. в разделе [эластичных пулов ограничения ресурсов виртуальное](sql-database-vcore-resource-limits-elastic-pools.md).|
| Добавлена управления скорость журнала транзакций| Добавить новое содержимое для [управления скорость журнала транзакций](sql-database-resource-limits-database-server.md#transaction-log-rate-governance).|
| Обновленные образцы PowerShell для отдельных баз данных и эластичных пулов для использования модуля az.sql | Дополнительные сведения см. в разделе [примеры PowerShell для отдельных баз данных и эластичных пулов](sql-database-powershell-samples.md#single-database-and-elastic-pools).|
| &nbsp; |

## <a name="february-2019"></a>Февраль 2019 г.

### <a name="service-improvements"></a>Улучшения службы

| Улучшения службы | Сведения |
| --- | --- |
|Создание возобновляемого индекса в сети теперь общедоступна| Дополнительные сведения см. в разделе [Create Index](https://docs.microsoft.com/sql/t-sql/statements/create-index-transact-sql).|
|Поддержка управляемого экземпляра улучшенные таблицы маршрутов| Дополнительные сведения см. в разделе [требований к сети](sql-database-managed-instance-connectivity-architecture.md#network-requirements).|
|Переименование базы данных поддерживается в управляемом экземпляре | Дополнительные сведения см. в разделе [ALTER DATABASE](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql?view=azuresqldb-mi-current) и [sp_rename](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-rename-transact-sql) синтаксис.|
|База данных SQL как источник ссылочных данных для Stream Analytics. | Дополнительные сведения см. в разделе [Stream Analytics](https://azure.microsoft.com/services/stream-analytics/).|
|Data Migration Assistance добавляет поддержку для управляемого экземпляра. |Дополнительные сведения см. в разделе [новые возможности в DMA](https://docs.microsoft.com/sql/dma/dma-whatsnew).|
|SQL Server Migration Assistant добавляет поддержку для оценки готовности целевой для управляемого экземпляра. | Дополнительные сведения см. в разделе [SQL Server Migration Assistant](https://docs.microsoft.com/sql/ssma/sql-server-migration-assistant).
|Служба миграции данных поддерживает перенос из Amazon служб удаленных рабочих СТОЛОВ в управляемый экземпляр | Дополнительные сведения см. в статье [Руководство. Перенос служб удаленных рабочих СТОЛОВ SQL Server в базу данных SQL Azure или базы данных SQL управляемого экземпляра через Интернет с помощью DMS](../dms/tutorial-rds-sql-server-azure-sql-and-managed-instance-online.md).|
| &nbsp; |

### <a name="documentation-improvements"></a>Улучшения документации

| Улучшения документации | Сведения |
| --- | --- |
|Добавление управляемых уточнения параметр развертывания экземпляра|Обновить множество статей для уточнения применимости для отдельной базы данных пула эластичных баз данных и параметры развертывания управляемого экземпляра. |
|Размеры обновленной базы данных tempdb для модели приобретения на основе DTU | Дополнительные сведения см. в разделе [базы данных Tempdb в базе данных SQL](https://docs.microsoft.com/sql/relational-databases/databases/tempdb-database#tempdb-database-in-sql-database).|
|Обновленные импорта и экспорта с помощью bacpac-файл для поддержки управляемого экземпляра| Дополнительные сведения см. в разделе [Импорт из bacpac-файла](sql-database-import.md) и [Экспорт в bacpac-файла](sql-database-export.md). |
| &nbsp; |


## <a name="january-2019"></a>Январь 2019 г.

### <a name="service-improvements"></a>Улучшения службы

| Улучшения службы | Сведения |
| --- | --- |
| Параметры дополнительной детализации для вычислительных ресурсов | Общего назначения и критически важный для бизнеса службы уровней для [отдельных баз данных](sql-database-vcore-resource-limits-single-databases.md) и [эластичных пулов](sql-database-vcore-resource-limits-elastic-pools.md) теперь имеют дополнительные параметры точного вычисления.|
| Просмотр записей аудита для управляемого экземпляра на портале Azure | Просмотр [записи для управляемых экземпляров](sql-database-managed-instance-auditing.md) в Azure теперь поддерживается портала.|
| Функцию обнаружения угроз ADVANCE, переименована в повышенной безопасности данных | Функцию обнаружения угроз Advance переименован в [повышенной безопасности данных](sql-advanced-threat-protection.md) для отдельных баз данных, эластичных пулов и управляемых экземпляров. |
| &nbsp; |

### <a name="documentation-improvements"></a>Улучшения документации

| Улучшения документации | Сведения |
| --- | --- |
| Управляемые экземпляры и репликации транзакций | Добавлена статья об использовании [репликация транзакций с управляемых экземпляров](replication-with-sql-database-managed-instance.md) |
| Добавлена Azure AD с руководством управляемого экземпляра | Это [Azure AD с управляемым экземпляром](sql-database-managed-instance-aad-security-tutorial.md) руководства вы узнаете необходимо настроить и проверить управляемый экземпляр безопасности с помощью имен входа Azure AD. |
| Обновленное содержимое для задания службы автоматизации с помощью скриптов Transact-SQL | Обновлен и уточнен содержимое с помощью [задания службы автоматизации с помощью скриптов Transact-SQL](sql-database-job-automation-overview.md) для отдельных баз данных, эластичных пулов и управляемых экземпляров |
| Содержимое безопасности для управляемых экземпляров обновлены | Обновлен и уточнен содержимое для [модель безопасности для управляемых экземпляров](sql-database-security-overview.md)и различия с помощью модели безопасности для отдельных баз данных и эластичных пулов |
| Обновить все краткие руководства и учебники | Все краткие руководства и учебники в [документации](https://docs.microsoft.com/azure/sql-database) были обновлены и обновляются в соответствии с изменениями на портале Azure |
| Общие сведения о добавленных краткие руководства | Обзор руководства для добавления [отдельных баз данных](sql-database-quickstart-guide.md) и [управляемые экземпляры](sql-database-managed-instance-quickstart-guide.md) |
| Добавлена база данных SQL. словарь терминов | Это [глоссарий терминов](sql-database-glossary-terms.md) статья содержит список терминах баз данных SQL, а также ссылки на основной странице концептуальной, поясняет термин в контексте. |
| &nbsp; |

## <a name="contribute-to-content-improvement"></a>Contribute содержимого улучшение

Набор документации по Azure SQL является открытым исходным кодом. Работа с открытыми обеспечивает ряд преимуществ:

- Позволяет получать отзывы о больше всего нужны какие-либо документы планирования репозиториев с открытым исходным кодом.
- Просмотра репозиториев с открытым исходным кодом такими текстами позволяет публиковать наиболее полезное содержимое при первом выпуске.
- Обновления открытым исходным кодом репозиториев с такими текстами позволяет упростить непрерывный процесс улучшения содержимого.

Для пополнения содержимого документации по базе данных SQL Azure, см. в разделе [обзор руководства участника Майкрософт](https://docs.microsoft.com/contribute/). Взаимодействие с пользователем на [docs.microsoft.com](https://docs.microsoft.com/) интегрируется [GitHub](https://github.com/) рабочих процессов непосредственно к стало еще проще. Начните с [редактирования документа, вы просматриваете](https://docs.microsoft.com/contribute/#quick-edits-to-existing-documents). Или справке по [просматривать новые разделы](https://docs.microsoft.com/contribute/#review-open-prs), или [создать проблемы с качеством](https://docs.microsoft.com/contribute/#create-quality-issues).
