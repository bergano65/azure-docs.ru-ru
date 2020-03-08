---
title: Устранение неполадок
description: Устранение неполадок Azure синапсе Analytics.
services: sql-data-warehouse
author: kevinvngo
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: manage
ms.date: 02/04/2019
ms.author: kevin
ms.reviewer: jrasnick
ms.custom: azure-synapse
ms.openlocfilehash: 085b907b6a848fb534df63b5465948864048cc19
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/05/2020
ms.locfileid: "78383757"
---
# <a name="troubleshooting-sql-analytics-in-azure-synapse"></a>Устранение неполадок аналитики SQL в Azure синапсе
В этой статье даны рекомендации по устранению распространенных неполадок.

## <a name="connecting"></a>Соединение
| Проблема                                                        | Решение                                                   |
| :----------------------------------------------------------- | :----------------------------------------------------------- |
| Ошибка входа пользователя "NT AUTHORITY\ANONYMOUS LOGON". (Microsoft SQL Server, ошибка: 18456) | Эта ошибка возникает, когда пользователь AAD пытается подключиться к базе данных master, в которой нет соответствующей учетной записи пользователя.  Чтобы устранить эту ошибку, либо укажите пул SQL, к которому вы хотите подключиться во время подключения, либо добавьте пользователя в базу данных master.  Дополнительные сведения см. в статье [Обзор безопасности](sql-data-warehouse-overview-manage-security.md) . |
| Субъект-сервер "MyUserName" не может получить доступ к базе данных master в текущем контексте безопасности. Невозможно открыть пользовательскую базу данных по умолчанию. Ошибка входа. Пользователю "MyUserName" не удалось войти в систему. (Microsoft SQL Server, ошибка: 916) | Эта ошибка возникает, когда пользователь AAD пытается подключиться к базе данных master, в которой нет соответствующей учетной записи пользователя.  Чтобы устранить эту ошибку, либо укажите пул SQL, к которому вы хотите подключиться во время подключения, либо добавьте пользователя в базу данных master.  Дополнительные сведения см. в статье [Обзор безопасности](sql-data-warehouse-overview-manage-security.md) . |
| Ошибка CTAIP                                                  | Эта ошибка может возникать при создании имени входа в базе данных master SQL Server, но не в базе данных SQL.  Если возникла эта ошибка, ознакомьтесь со статьей с [общими сведениями о безопасности](sql-data-warehouse-overview-manage-security.md) .  В этой статье объясняется, как создать имя для входа и пользователя в базе данных master, а затем создать в ней пользователя. |
| Заблокировано брандмауэром                                          | Пулы SQL защищаются брандмауэрами, чтобы обеспечить доступ к базе данных только известным IP-адресам. Брандмауэры являются безопасными по умолчанию. Это означает, что перед подключением необходимо прямо разрешить доступ для IP-адреса или диапазона IP-адресов.  Чтобы настроить брандмауэр для доступа, выполните действия, описанные в статье [Настройка доступа к брандмауэру сервера для IP-адреса клиента](sql-data-warehouse-get-started-provision.md) в [инструкциях по подготовке](sql-data-warehouse-get-started-provision.md). |
| Не удается подключиться с помощью средства или драйвера                           | SQL Analytics рекомендует использовать [SSMS](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms?view=sql-server-ver15), [SSDT для Visual Studio](sql-data-warehouse-install-visual-studio.md)или [sqlcmd](sql-data-warehouse-get-started-connect-sqlcmd.md) для запроса данных. Дополнительные сведения о драйверах и подключении к Azure синапсе см. в статье [драйверы для Azure синапсе](sql-data-warehouse-connection-strings.md) и [Подключение к статьям синапсе Azure](sql-data-warehouse-connect-overview.md) . |

## <a name="tools"></a>Инструменты
| Проблема                                                        | Решение                                                   |
| :----------------------------------------------------------- | :----------------------------------------------------------- |
| В обозревателе объектов Visual Studio отсутствуют пользователи AAD           | Это известная проблема  Сведения о пользователях можно просмотреть в файле [sys.database_principals](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-database-principals-transact-sql?view=sql-server-ver15).  Дополнительные сведения об использовании Azure Active Directory с SQL Analytics см. в статье [Проверка подлинности в Azure синапсе](sql-data-warehouse-authentication.md) . |
| Ручное создание сценариев, использование мастера создания скриптов или подключение через среду SSMS выполняются с высокой скоростью, не отвечают или выводят ошибки | Убедитесь, что пользователи были созданы в базе данных master. Коме того, убедитесь, что в параметрах создания сценариев заданы выпуск ядра "Выпуск Microsoft Azure SQL Data Warehouse Edition" и тип модуля "База данных SQL Microsoft Azure". |
| Создание скриптов в SSMS завершается ошибкой                               | Создание скрипта для SQL Analytics завершается ошибкой, если параметру "сформировать скрипт для зависимых объектов" задано значение "true". В качестве обходного решения пользователям необходимо вручную **выбрать сервис-> параметры-> Обозреватель объектов SQL Server-> создать скрипт для зависимых параметров и установить значение false** . |

## <a name="performance"></a>Производительность
| Проблема                                                        | Решение                                                   |
| :----------------------------------------------------------- | :----------------------------------------------------------- |
| Устранение проблем с производительностью запросов                            | Если вы пытаетесь устранить проблему конкретного запроса, для начала ознакомьтесь со статьей, [посвященной мониторингу запросов](../sql-data-warehouse/sql-data-warehouse-manage-monitor.md#monitor-query-execution). |
| Проблемы с пространством TempDB | [Мониторинг](../sql-data-warehouse/sql-data-warehouse-manage-monitor.md#monitor-tempdb) использования пространства tempdb.  Ниже приведены распространенные причины запуска пространства TempDB.<br>— Недостаточно ресурсов, выделенных для запроса, который вызывает сброс данных в TempDB.  См. раздел [Управление рабочей нагрузкой](resource-classes-for-workload-management.md) <br>-Статистика отсутствует или устарела, что приводит к чрезмерному перемещению данных.  Дополнительные сведения о создании статистики см. в разделе [обслуживание статистики таблиц](sql-data-warehouse-tables-statistics.md) .<br>— Пространство TempDB выделяется для каждого уровня обслуживания.  При [масштабировании пула SQL](../sql-data-warehouse/sql-data-warehouse-manage-compute-overview.md#scaling-compute) до более высокого значения DWU выделяется больше пространства tempdb.|
| Низкая производительность и неправильные планы запросов в результате отсутствия статистики | Самая распространенная причина низкой производительности — отсутствие статистики таблиц.  Сведения о том, как создать статистику и почему они важны для производительности, см. в разделе [обслуживание статистики таблиц](sql-data-warehouse-tables-statistics.md) . |
| Низкий уровень параллелизма и помещение запросов в очередь                             | Чтобы обеспечить выделение памяти с учетом параллелизма, важно понимать принципы [управления рабочими нагрузками](resource-classes-for-workload-management.md) . |
| Реализация рекомендаций                              | Лучше начать изучать способы улучшения производительности запросов в статье [рекомендации по SQL Analytics](sql-data-warehouse-best-practices.md) . |
| Повышение производительности за счет масштабирования                      | Иногда решение для повышения производительности заключается в том, чтобы увеличить производительность запросов путем [масштабирования пула SQL](sql-data-warehouse-manage-compute-overview.md). |
| Низкая производительность запросов как результат низкого качества индексов     | Иногда запросы могут замедлить работу из-за [низкого качества индекса columnstore](../sql-data-warehouse/sql-data-warehouse-tables-index.md#causes-of-poor-columnstore-index-quality).  Дополнительные сведения и [Перестроение индексов для улучшения качества сегментов](../sql-data-warehouse/sql-data-warehouse-tables-index.md#rebuilding-indexes-to-improve-segment-quality)см. в этой статье. |

## <a name="system-management"></a>Управление системой
| Проблема                                                        | Решение                                                   |
| :----------------------------------------------------------- | :----------------------------------------------------------- |
| Сообщение 40847: не удалось выполнить операцию, так как сервер достиг допустимой квоты в 45 000 единиц транзакций базы данных (DTU). | Либо Сократите [DWU](what-is-a-data-warehouse-unit-dwu-cdwu.md) базы данных, которую вы пытаетесь создать, либо [запросите увеличение квоты](sql-data-warehouse-get-started-create-support-ticket.md). |
| Анализ использования пространства                              | Сведения об использовании пространства в системе см. в разделе [размеры таблиц]( ../sql-data-warehouse/sql-data-warehouse-tables-overview.md#table-size-queries) . |
| Справка по управлению таблицами                                    | Дополнительные сведения об управлении таблицами см. в статье [Обзор таблиц](../sql-data-warehouse/sql-data-warehouse-tables-overview.md) .  В этой статье также содержатся ссылки на более подробные разделы, такие как [типы данных таблиц](sql-data-warehouse-tables-data-types.md), [распространение таблицы](sql-data-warehouse-tables-distribute.md), [индексирование таблицы](sql-data-warehouse-tables-index.md), [секционирование таблицы](sql-data-warehouse-tables-partition.md), [Ведение статистики таблицы](sql-data-warehouse-tables-statistics.md) и [временные таблицы](sql-data-warehouse-tables-temporary.md). |
| Индикатор выполнения прозрачного шифрования данных (TDE) не обновляется в портал Azure | Состояние прозрачного шифрования данных можно узнать с помощью [PowerShell](/powershell/module/az.sql/get-azsqldatabasetransparentdataencryption). |


## <a name="differences-from-sql-database"></a>Отличия от базы данных SQL
| Проблема                                 | Решение                                                   |
| :------------------------------------ | :----------------------------------------------------------- |
| Неподдерживаемые функции базы данных SQL     | Ознакомьтесь с разделом [Неподдерживаемые функции таблиц](../sql-data-warehouse/sql-data-warehouse-tables-overview.md#unsupported-table-features). |
| Неподдерживаемые типы данных базы данных SQL   | Ознакомьтесь с разделом [Неподдерживаемые типы данных](../sql-data-warehouse/sql-data-warehouse-tables-data-types.md#identify-unsupported-data-types).        |
| Ограничения относительно инструкций DELETE и UPDATE         | См. статью [обходные пути](../sql-data-warehouse/sql-data-warehouse-develop-ctas.md#ansi-join-replacement-for-update-statements), инструкции по [удалению](../sql-data-warehouse/sql-data-warehouse-develop-ctas.md#ansi-join-replacement-for-delete-statements) и [Использование CTAS для обхода неподдерживаемого синтаксиса Update и DELETE](sql-data-warehouse-develop-ctas.md). |
| Инструкция MERGE не поддерживается      | Ознакомьтесь с [обходными решениями для инструкции MERGE](../sql-data-warehouse/sql-data-warehouse-develop-ctas.md#replace-merge-statements).                  |
| Ограничения хранимых процедур          | Ознакомьтесь с [ограничениями хранимых процедур](../sql-data-warehouse/sql-data-warehouse-develop-stored-procedures.md#limitations) . |
| Определяемые пользователем функции не поддерживают инструкции SELECT | Это текущее ограничение определяемых пользователем функций.  Поддерживаемый синтаксис см. в разделе [Создание функции](https://docs.microsoft.com/sql/t-sql/statements/create-function-sql-data-warehouse?view=aps-pdw-2016-au7) . |

## <a name="next-steps"></a>Дальнейшие действия
Ниже перечислены некоторые ресурсы, которые можно использовать для поиска решения вашей проблемы.

* [Блоги](https://azure.microsoft.com/blog/tag/azure-sql-data-warehouse/)
* [Запросы функций](https://feedback.azure.com/forums/307516-sql-data-warehouse)
* [Видео](https://azure.microsoft.com/documentation/videos/index/?services=sql-data-warehouse)
* [Создание запроса в службу поддержки](sql-data-warehouse-get-started-create-support-ticket.md)
* [Форум MSDN](https://social.msdn.microsoft.com/Forums/home?forum=AzureSQLDataWarehouse)
* [Форум Stack Overflow](https://stackoverflow.com/questions/tagged/azure-sqldw)
* [Twitter](https://twitter.com/hashtag/SQLDW)
