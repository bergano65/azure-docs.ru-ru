---
title: Устранение неполадок с Synapse SQL
description: Устранение неполадок с Synapse SQL в Azure Synapse Analytics.
services: synapse-analytics
author: kevinvngo
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 02/04/2019
ms.author: kevin
ms.reviewer: jrasnick
ms.custom: azure-synapse
ms.openlocfilehash: bce5e14db133ad55a2035c0c8074486ed1b8a6ec
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/09/2020
ms.locfileid: "89460513"
---
# <a name="troubleshooting-synapse-sql-in-azure-synapse-analytics"></a>Устранение неполадок с Synapse SQL в Azure Synapse Analytics

В этой статье рассматриваются типичные проблемы в области устранения неполадок с Synapse SQL.

## <a name="connecting"></a>Соединение

| Проблема                                                        | Решение                                                   |
| :----------------------------------------------------------- | :----------------------------------------------------------- |
| Ошибка входа пользователя "NT AUTHORITY\ANONYMOUS LOGON". (Microsoft SQL Server, ошибка: 18456) | Эта ошибка возникает, когда пользователь Azure AD пытается подключиться к базе данных master, в которой нет соответствующей учетной записи пользователя.  Для устранения этой проблемы либо укажите пул SQL, к которому необходимо подключиться во время подключения, либо добавьте учетную запись пользователя в базу данных master.  См. дополнительные сведения в статье [Обзор безопасности](sql-data-warehouse-overview-manage-security.md). |
| Субъект-сервер "MyUserName" не может получить доступ к базе данных master в текущем контексте безопасности. Невозможно открыть пользовательскую базу данных по умолчанию. Ошибка входа. Пользователю "MyUserName" не удалось войти в систему. (Microsoft SQL Server, ошибка: 916) | Эта ошибка возникает, когда пользователь Azure AD пытается подключиться к базе данных master, в которой нет соответствующей учетной записи пользователя.  Для устранения этой проблемы либо укажите пул SQL, к которому необходимо подключиться во время подключения, либо добавьте учетную запись пользователя в базу данных master.  См. дополнительные сведения в статье [Обзор безопасности](sql-data-warehouse-overview-manage-security.md). |
| Ошибка CTAIP                                                  | Эта ошибка может возникать при создании имени входа в базе данных master базы данных SQL, но не в конкретной базе данных SQL.  Если возникла эта ошибка, ознакомьтесь со статьей с [общими сведениями о безопасности](sql-data-warehouse-overview-manage-security.md) .  В этой статье объясняется, как создать имя входа и пользователя в базе данных master, а затем создать пользователя в базе данных SQL. |
| Заблокировано брандмауэром                                          | Пулы SQL защищены брандмауэрами. Это гарантирует, что доступ к базе данных возможен только с известных IP-адресов. Брандмауэры являются безопасными по умолчанию. Это означает, что перед подключением необходимо прямо разрешить доступ для IP-адреса или диапазона IP-адресов.  Чтобы настроить брандмауэр для предоставления доступа, выполните указания в разделе [Настройка доступа через брандмауэр сервера для IP-адреса вашего клиента](create-data-warehouse-portal.md) в статье [Инструкции по подготовке](create-data-warehouse-portal.md). |
| Не удается подключиться с помощью средства или драйвера                           | Пул Synapse SQL рекомендует использовать [SSMS](/sql/ssms/download-sql-server-management-studio-ssms?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest), [SSDT для Visual Studio](sql-data-warehouse-install-visual-studio.md) или [sqlcmd](sql-data-warehouse-get-started-connect-sqlcmd.md) для запроса данных. Для получения дополнительной информации о драйверах и подключении к Azure Synapse обратитесь к статьям [Драйверы для Azure Synapse](sql-data-warehouse-connection-strings.md) и [Подключение к Azure Synapse](sql-data-warehouse-connect-overview.md). |

## <a name="tools"></a>Инструменты

| Проблема                                                        | Решение                                                   |
| :----------------------------------------------------------- | :----------------------------------------------------------- |
| В обозревателе объектов Visual Studio отсутствуют пользователи Azure AD           | Это известная проблема  Сведения о пользователях можно просмотреть в файле [sys.database_principals](/sql/relational-databases/system-catalog-views/sys-database-principals-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest).  Дополнительные сведения об использовании Azure Active Directory с пулом Synapse SQL приводятся в статье [Аутентификация в Azure Synapse](sql-data-warehouse-authentication.md). |
| Создание сценариев вручную, использование мастера создания сценариев или подключение с помощью SSMS происходит медленно, не отвечает или с ошибками. | Убедитесь, что пользователи были созданы в базе данных master. В параметрах создания скриптов также убедитесь, что для выпуска Engine задано значение "Microsoft Azure синапсе Analytics Edition", а тип модуля — "База данных SQL Microsoft Azure". |
| Создание скриптов в SSMS завершается ошибкой                               | Если для параметра "Сформировать скрипт для зависимых объектов" задано значение True, создание скрипта для пула Synapse SQL завершается ошибкой. Чтобы устранить проблему, выберите **Сервис -> Параметры -> Обозреватель объектов SQL Server -> Создать скрипт для зависимых параметров и задайте значение False** |

## <a name="performance"></a>Производительность

| Проблема                                                        | Решение                                                   |
| :----------------------------------------------------------- | :----------------------------------------------------------- |
| Устранение проблем с производительностью запросов                            | Если вы пытаетесь устранить проблему конкретного запроса, для начала ознакомьтесь со статьей, [посвященной мониторингу запросов](sql-data-warehouse-manage-monitor.md#monitor-query-execution). |
| Проблемы с пространством TempDB | Использование пространства [Мониторинг TempDB](sql-data-warehouse-manage-monitor.md#monitor-tempdb).  Ниже приводятся распространенные причины нехватки пространства TempDB.<br>Запросу выделяется недостаточно ресурсов, что вызывает сброс данных в TempDB.  См. раздел [Управление рабочими нагрузками](resource-classes-for-workload-management.md). <br>Статистика отсутствует или устарела, что приводит к перемещению чрезмерного объема данных.  См. подробные сведения о составлении статистики в разделе [Ведение статистики таблиц](sql-data-warehouse-tables-statistics.md).<br>Пространство TempDB выделяется на уровне служб.  В результате [увеличения числа единиц использования хранилища данных в пуле SQL](sql-data-warehouse-manage-compute-overview.md#scaling-compute) будет выделено больше пространства TempDB.|
| Низкая производительность и неправильные планы запросов в результате отсутствия статистики | Самая распространенная причина низкой производительности — отсутствие статистики таблиц.  Дополнительные сведения о создании статистики и о том, почему она очень важна для производительности, см. в статье [Ведение статистики таблиц](sql-data-warehouse-tables-statistics.md). |
| Низкий уровень параллелизма и помещение запросов в очередь                             | Чтобы обеспечить выделение памяти с учетом параллелизма, важно понимать принципы [управления рабочими нагрузками](resource-classes-for-workload-management.md) . |
| Реализация рекомендаций                              | Лучше всего начать изучение способов повышения производительности запросов со статьи [Рекомендации по использованию пула Synapse SQL](sql-data-warehouse-best-practices.md). |
| Повышение производительности за счет масштабирования                      | Иногда повысить производительность запросов позволяет простое увеличение вычислительной мощности, выделяемой для выполнения запросов, за счет [масштабирования своего пула SQL](sql-data-warehouse-manage-compute-overview.md). |
| Низкая производительность запросов как результат низкого качества индексов     | Иногда выполнение запросов может замедляться из-за [низкого качества индекса columnstore](sql-data-warehouse-tables-index.md#causes-of-poor-columnstore-index-quality).  Дополнительные сведения см. в статье об индексировании таблиц в разделе, посвященном [повышению качества сегментов за счет перестроения индексов](sql-data-warehouse-tables-index.md#rebuilding-indexes-to-improve-segment-quality). |

## <a name="system-management"></a>Управление системой

| Проблема                                                        | Решение                                                   |
| :----------------------------------------------------------- | :----------------------------------------------------------- |
| Сообщение 40847: "Не удалось выполнить операцию, так как сервер достиг допустимой квоты в 45 000 единиц транзакций базы данных (DTU)". | Либо уменьшите значение [DWU](what-is-a-data-warehouse-unit-dwu-cdwu.md) создаваемой базы данных, либо [запросите увеличение квоты](sql-data-warehouse-get-started-create-support-ticket.md). |
| Анализ использования пространства                              | Сведения об использовании пространства в системе см. в разделе о [размерах таблиц](sql-data-warehouse-tables-overview.md#table-size-queries). |
| Справка по управлению таблицами                                    | Справочную информацию об управлении таблицами см. в статье [Обзор таблиц](sql-data-warehouse-tables-overview.md).  Эта статья также содержит ссылки на более подробные разборы следующих тем: [Типы табличных данных](sql-data-warehouse-tables-data-types.md), [Распределение таблицы](sql-data-warehouse-tables-distribute.md), [Индексирование таблицы](sql-data-warehouse-tables-index.md), [Разделение таблицы на разделы](sql-data-warehouse-tables-partition.md), [Ведение статистики таблицы](sql-data-warehouse-tables-statistics.md) и [Временные таблицы](sql-data-warehouse-tables-temporary.md). |
| На портале Azure не обновляется индикатор хода прозрачного шифрования данных (TDE) | Состояние TDE можно просмотреть с помощью [PowerShell](/powershell/module/az.sql/get-azsqldatabasetransparentdataencryption?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json). |

## <a name="differences-from-sql-database"></a>Отличия от базы данных SQL

| Проблема                                 | Решение                                                   |
| :------------------------------------ | :----------------------------------------------------------- |
| Неподдерживаемые функции базы данных SQL     | Ознакомьтесь с разделом [Неподдерживаемые функции таблиц](sql-data-warehouse-tables-overview.md#unsupported-table-features). |
| Неподдерживаемые типы данных базы данных SQL   | Ознакомьтесь с разделом [Неподдерживаемые типы данных](sql-data-warehouse-tables-data-types.md#identify-unsupported-data-types).        |
| Ограничения относительно инструкций DELETE и UPDATE         | Ознакомьтесь с обходными решениями для инструкций [UPDATE](sql-data-warehouse-develop-ctas.md#ansi-join-replacement-for-update-statements) и [DELETE](sql-data-warehouse-develop-ctas.md#ansi-join-replacement-for-delete-statements), а также с [использованием инструкции CTAS для обхода неподдерживаемого синтаксиса UPDATE и DELETE](sql-data-warehouse-develop-ctas.md). |
| Инструкция MERGE не поддерживается      | Ознакомьтесь с [обходными решениями для инструкции MERGE](sql-data-warehouse-develop-ctas.md#replace-merge-statements).                  |
| Ограничения хранимых процедур          | Ознакомьтесь с [ограничениями хранимых процедур](sql-data-warehouse-develop-stored-procedures.md#limitations) . |
| Определяемые пользователем функции не поддерживают инструкции SELECT | Это текущее ограничение определяемых пользователем функций.  Сведения о поддерживаемом синтаксисе см. в статье, посвященной инструкции [CREATE FUNCTION](/sql/t-sql/statements/create-function-sql-data-warehouse?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest). |

## <a name="next-steps"></a>Дальнейшие действия

Ниже перечислены некоторые ресурсы, которые можно использовать для поиска решения вашей проблемы.

* [Блоги](https://azure.microsoft.com/blog/tag/azure-sql-data-warehouse/)
* [Запросы функций](https://feedback.azure.com/forums/307516-sql-data-warehouse)
* [Видео](https://azure.microsoft.com/documentation/videos/index/?services=sql-data-warehouse)
* [Создание запроса в службу поддержки](sql-data-warehouse-get-started-create-support-ticket.md)
* [Страница вопросов и ответов Microsoft](https://docs.microsoft.com/answers/topics/azure-synapse-analytics.html)
* [Форум Stack Overflow](https://stackoverflow.com/questions/tagged/azure-sqldw)
* [Twitter](https://twitter.com/hashtag/SQLDW)
