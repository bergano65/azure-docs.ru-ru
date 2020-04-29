---
title: Устранение неполадок
description: Устранение неполадок Azure синапсе Analytics.
services: synapse-analytics
author: kevinvngo
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 02/04/2019
ms.author: kevin
ms.reviewer: jrasnick
ms.custom: azure-synapse
ms.openlocfilehash: 973d2339db1e55f2cca45025f2d678e5126f4317
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2020
ms.locfileid: "80743668"
---
# <a name="troubleshooting-sql-analytics-in-azure-synapse"></a>Устранение неполадок аналитики SQL в Azure синапсе

В этой статье даны рекомендации по устранению распространенных неполадок.

## <a name="connecting"></a>Соединение

| Проблема                                                        | Решение                                                   |
| :----------------------------------------------------------- | :----------------------------------------------------------- |
| Ошибка входа пользователя "NT AUTHORITY\ANONYMOUS LOGON". (Microsoft SQL Server, ошибка: 18456) | Эта ошибка возникает, когда пользователь Azure AD пытается подключиться к базе данных master, но не имеет пользователя в Master.  Чтобы устранить эту ошибку, либо укажите пул SQL, к которому вы хотите подключиться во время подключения, либо добавьте пользователя в базу данных master.  Дополнительные сведения см. в статье [Обзор безопасности](sql-data-warehouse-overview-manage-security.md) . |
| Субъект-сервер "MyUserName" не может получить доступ к базе данных master в текущем контексте безопасности. Невозможно открыть пользовательскую базу данных по умолчанию. Ошибка входа. Пользователю "MyUserName" не удалось войти в систему. (Microsoft SQL Server, ошибка: 916) | Эта ошибка возникает, когда пользователь Azure AD пытается подключиться к базе данных master, но не имеет пользователя в Master.  Чтобы устранить эту ошибку, либо укажите пул SQL, к которому вы хотите подключиться во время подключения, либо добавьте пользователя в базу данных master.  Дополнительные сведения см. в статье [Обзор безопасности](sql-data-warehouse-overview-manage-security.md) . |
| Ошибка CTAIP                                                  | Эта ошибка может возникать при создании имени входа в базе данных master SQL Server, но не в базе данных SQL.  Если возникла эта ошибка, ознакомьтесь со статьей с [общими сведениями о безопасности](sql-data-warehouse-overview-manage-security.md) .  В этой статье объясняется, как создать имя для входа и пользователя в базе данных master, а затем создать в ней пользователя. |
| Заблокировано брандмауэром                                          | Пулы SQL защищаются брандмауэрами, чтобы обеспечить доступ к базе данных только известным IP-адресам. Брандмауэры являются безопасными по умолчанию. Это означает, что перед подключением необходимо прямо разрешить доступ для IP-адреса или диапазона IP-адресов.  Чтобы настроить брандмауэр для предоставления доступа, выполните указания в разделе [Создание правила брандмауэра на уровне сервера с помощью портала Azure](create-data-warehouse-portal.md) статьи [Создание хранилища данных SQL Azure](create-data-warehouse-portal.md). |
| Не удается подключиться с помощью средства или драйвера                           | Пул синапсе SQL рекомендует использовать [SSMS](/sql/ssms/download-sql-server-management-studio-ssms?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest), [SSDT для Visual Studio](sql-data-warehouse-install-visual-studio.md)или [sqlcmd](sql-data-warehouse-get-started-connect-sqlcmd.md) для запроса данных. Дополнительные сведения о драйверах и подключении к Azure синапсе см. в статье [драйверы для Azure синапсе](sql-data-warehouse-connection-strings.md) и [Подключение к статьям синапсе Azure](sql-data-warehouse-connect-overview.md) . |

## <a name="tools"></a>Инструменты

| Проблема                                                        | Решение                                                   |
| :----------------------------------------------------------- | :----------------------------------------------------------- |
| В обозревателе объектов Visual Studio отсутствуют пользователи Azure AD           | Это известная проблема.  Сведения о пользователях можно просмотреть в файле [sys.database_principals](/sql/relational-databases/system-catalog-views/sys-database-principals-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest).  Дополнительные сведения об использовании Azure Active Directory с пулом синапсе SQL см. в статье [Аутентификация в Azure синапсе](sql-data-warehouse-authentication.md) . |
| Ручное создание сценариев, использование мастера создания скриптов или подключение через среду SSMS выполняются с высокой скоростью, не отвечают или выводят ошибки | Убедитесь, что пользователи были созданы в базе данных master. В параметрах создания скриптов также убедитесь, что для выпуска Engine задано значение "выпуск Хранилища данных SQL Microsoft Azure", а тип подсистемы — "База данных SQL Microsoft Azure". |
| Создание скриптов в SSMS завершается ошибкой                               | Создание скрипта для пула SQL синапсе завершается ошибкой, если параметр "сформировать скрипт для зависимых объектов" имеет значение "true". В качестве обходного решения пользователям необходимо вручную **выбрать сервис-> параметры->обозреватель объектов SQL Server-> создать скрипт для зависимых параметров и установить значение false** . |

## <a name="performance"></a>Тестирование

| Проблема                                                        | Решение                                                   |
| :----------------------------------------------------------- | :----------------------------------------------------------- |
| Устранение проблем с производительностью запросов                            | Если вы пытаетесь устранить проблему конкретного запроса, для начала ознакомьтесь со статьей [Мониторинг рабочей нагрузки с помощью динамических административных представлений](sql-data-warehouse-manage-monitor.md#monitor-query-execution). |
| Проблемы с пространством TempDB | [Мониторинг](sql-data-warehouse-manage-monitor.md#monitor-tempdb) использования пространства tempdb.  Ниже приведены распространенные причины запуска пространства TempDB.<br>— Недостаточно ресурсов, выделенных для запроса, который вызывает сброс данных в TempDB.  См. раздел [Управление рабочей нагрузкой](resource-classes-for-workload-management.md) <br>-Статистика отсутствует или устарела, что приводит к чрезмерному перемещению данных.  Дополнительные сведения о создании статистики см. в разделе [обслуживание статистики таблиц](sql-data-warehouse-tables-statistics.md) .<br>— Пространство TempDB выделяется для каждого уровня обслуживания.  При [масштабировании пула SQL](sql-data-warehouse-manage-compute-overview.md#scaling-compute) до более высокого значения DWU выделяется больше пространства tempdb.|
| Низкая производительность и неправильные планы запросов в результате отсутствия статистики | Самая распространенная причина низкой производительности — отсутствие статистики таблиц.  Дополнительные сведения о создании статистики и о том, почему она очень важна для производительности, см. в статье [Управление статистикой таблиц в хранилище данных SQL](sql-data-warehouse-tables-statistics.md). |
| Низкий уровень параллелизма и помещение запросов в очередь                             | Чтобы обеспечить выделение памяти с учетом параллелизма, важно понимать принципы [управления рабочими нагрузками](resource-classes-for-workload-management.md). |
| Реализация рекомендаций                              | Лучше узнать о способах повышения производительности запросов — синапсе в статье рекомендации по [использованию пула SQL](sql-data-warehouse-best-practices.md) . |
| Повышение производительности за счет масштабирования                      | Иногда решение для повышения производительности заключается в том, чтобы увеличить производительность запросов путем [масштабирования пула SQL](sql-data-warehouse-manage-compute-overview.md). |
| Низкая производительность запросов как результат низкого качества индексов     | Иногда выполнение запросов может замедляться из-за [низкого качества индекса columnstore](sql-data-warehouse-tables-index.md#causes-of-poor-columnstore-index-quality).  Дополнительные сведения см. в статье об индексировании таблиц в разделе, посвященном [повышению качества сегментов за счет перестроения индексов](sql-data-warehouse-tables-index.md#rebuilding-indexes-to-improve-segment-quality). |

## <a name="system-management"></a>Управление системой

| Проблема                                                        | Решение                                                   |
| :----------------------------------------------------------- | :----------------------------------------------------------- |
| Сообщение 40847: не удалось выполнить операцию, так как сервер достиг допустимой квоты в 45 000 единиц транзакций базы данных (DTU). | Либо уменьшите значение [DWU](what-is-a-data-warehouse-unit-dwu-cdwu.md) создаваемой базы данных, либо [запросите увеличение квоты](sql-data-warehouse-get-started-create-support-ticket.md). |
| Анализ использования пространства                              | Сведения об использовании пространства в системе см. в разделе о [запросах размера таблицы](sql-data-warehouse-tables-overview.md#table-size-queries). |
| Справка по управлению таблицами                                    | Справочную информацию об управлении таблицами см. в статье [Общие сведения о таблицах в хранилище данных SQL](sql-data-warehouse-tables-overview.md).  Дополнительные сведения см. в статьях, посвященных [типам данных таблиц](sql-data-warehouse-tables-data-types.md), [распределению](sql-data-warehouse-tables-distribute.md), [индексированию](sql-data-warehouse-tables-index.md), [секционированию](sql-data-warehouse-tables-partition.md), [управлению статистикой таблиц](sql-data-warehouse-tables-statistics.md) и [временным таблицам](sql-data-warehouse-tables-temporary.md). |
| Индикатор выполнения прозрачного шифрования данных (TDE) не обновляется в портал Azure | Состояние прозрачного шифрования данных можно узнать с помощью [PowerShell](/powershell/module/az.sql/get-azsqldatabasetransparentdataencryption?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json). |

## <a name="differences-from-sql-database"></a>Отличия от базы данных SQL

| Проблема                                 | Решение                                                   |
| :------------------------------------ | :----------------------------------------------------------- |
| Неподдерживаемые функции базы данных SQL     | Ознакомьтесь с разделом [Неподдерживаемые функции таблиц](sql-data-warehouse-tables-overview.md#unsupported-table-features). |
| Неподдерживаемые типы данных базы данных SQL   | Ознакомьтесь с разделом [Неподдерживаемые типы данных](sql-data-warehouse-tables-data-types.md#identify-unsupported-data-types).        |
| Ограничения относительно инструкций DELETE и UPDATE         | Ознакомьтесь с обходными решениями для инструкций [UPDATE](sql-data-warehouse-develop-ctas.md#ansi-join-replacement-for-update-statements) и [DELETE](sql-data-warehouse-develop-ctas.md#ansi-join-replacement-for-delete-statements), а также с [использованием инструкции CTAS для обхода неподдерживаемого синтаксиса UPDATE и DELETE](sql-data-warehouse-develop-ctas.md). |
| Инструкция MERGE не поддерживается      | Ознакомьтесь с [обходными решениями для инструкции MERGE](sql-data-warehouse-develop-ctas.md#replace-merge-statements).                  |
| Ограничения хранимых процедур          | Ознакомьтесь с [ограничениями хранимых процедур](sql-data-warehouse-develop-stored-procedures.md#limitations). |
| Определяемые пользователем функции не поддерживают инструкции SELECT | Это текущее ограничение определяемых пользователем функций.  Сведения о поддерживаемом синтаксисе см. в статье, посвященной инструкции [CREATE FUNCTION](/sql/t-sql/statements/create-function-sql-data-warehouse?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest). |

## <a name="next-steps"></a>Дальнейшие шаги

Ниже перечислены некоторые ресурсы, которые можно использовать для поиска решения вашей проблемы.

* [Блоги](https://azure.microsoft.com/blog/tag/azure-sql-data-warehouse/)
* [Запросы функций](https://feedback.azure.com/forums/307516-sql-data-warehouse)
* [Видео](https://azure.microsoft.com/documentation/videos/index/?services=sql-data-warehouse)
* [Создание запроса в службу поддержки](sql-data-warehouse-get-started-create-support-ticket.md)
* [Форум MSDN](https://social.msdn.microsoft.com/Forums/home?forum=AzureSQLDataWarehouse)
* [Форум Stack Overflow](https://stackoverflow.com/questions/tagged/azure-sqldw)
* [Twitter](https://twitter.com/hashtag/SQLDW)
