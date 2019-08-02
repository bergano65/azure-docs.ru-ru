---
title: Устранение неполадок хранилища данных SQL Azure | Документация Майкрософт
description: Диагностика и устранение неполадок хранилища данных SQL Azure.
services: sql-data-warehouse
author: kevinvngo
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: manage
ms.date: 7/29/2019
ms.author: kevin
ms.reviewer: igorstan
ms.openlocfilehash: 04d63b2c1583228a274c0ba21c87df08886f5cdb
ms.sourcegitcommit: 08d3a5827065d04a2dc62371e605d4d89cf6564f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/29/2019
ms.locfileid: "68619071"
---
# <a name="troubleshooting-azure-sql-data-warehouse"></a>Устранение неполадок хранилища данных SQL Azure
В этой статье даны рекомендации по устранению распространенных неполадок.

## <a name="connecting"></a>Соединение
| Проблемы                                                        | Разрешение                                                   |
| :----------------------------------------------------------- | :----------------------------------------------------------- |
| Ошибка входа пользователя "NT AUTHORITY\ANONYMOUS LOGON". (Microsoft SQL Server, ошибка: 18456) | Эта ошибка возникает, когда пользователь AAD пытается подключиться к базе данных master, в которой нет соответствующей учетной записи пользователя.  Для устранения этой проблемы либо укажите хранилище данных SQL, к которому необходимо подключиться, во время подключения, либо добавьте учетную запись пользователя в базу данных master.  Дополнительные сведения см. в статье [Обзор безопасности][Security overview] . |
| Субъект-сервер "MyUserName" не может получить доступ к базе данных master в текущем контексте безопасности. Не удается открыть пользовательскую базу данных по умолчанию. Не удалось выполнить вход. Пользователю "MyUserName" не удалось войти в систему. (Microsoft SQL Server, ошибка: 916) | Эта ошибка возникает, когда пользователь AAD пытается подключиться к базе данных master, в которой нет соответствующей учетной записи пользователя.  Для устранения этой проблемы либо укажите хранилище данных SQL, к которому необходимо подключиться, во время подключения, либо добавьте учетную запись пользователя в базу данных master.  Дополнительные сведения см. в статье [Обзор безопасности][Security overview] . |
| Ошибка CTAIP                                                  | Эта ошибка может возникнуть, когда имя для входа создано в базе данных master SQL Server, но не создано в базе данных хранилища данных SQL.  Если возникла эта ошибка, ознакомьтесь со статьей с [общими сведениями о безопасности][Security overview] .  В этой статье объясняется, как создать имя для входа и пользователя в базе данных master, а затем создать пользователя в базе данных хранилища данных SQL. |
| Заблокировано брандмауэром                                          | Базы данных SQL Azure защищены брандмауэрами на уровне сервера и базы данных. Это гарантирует, что доступ к базам данных возможен только с известных IP-адресов. Брандмауэры являются безопасными по умолчанию. Это означает, что перед подключением необходимо прямо разрешить доступ для IP-адреса или диапазона IP-адресов.  Чтобы настроить брандмауэр для доступа, выполните действия, описанные в статье [Настройка доступа к брандмауэру сервера для IP-адреса клиента][Configure server firewall access for your client IP] в инструкциях по [подготовке][Provisioning instructions]. |
| Не удается подключиться с помощью средства или драйвера                           | Хранилище данных SQL рекомендует использовать [SSMS][SSMS], [SSDT для Visual Studio][SSDT for Visual Studio]или [sqlcmd][sqlcmd] для запроса данных. Дополнительные сведения о драйверах и подключении к хранилищу данных SQL см. в статье [драйверы для хранилища данных SQL Azure][Drivers for Azure SQL Data Warehouse] и [Подключение к статьям хранилища данных SQL Azure][Connect to Azure SQL Data Warehouse] . |

## <a name="tools"></a>Сервис
| Проблемы                                                        | Разрешение                                                   |
| :----------------------------------------------------------- | :----------------------------------------------------------- |
| В обозревателе объектов Visual Studio отсутствуют пользователи AAD           | Это известная проблема.  Сведения о пользователях можно просмотреть в файле [sys.database_principals][sys.database_principals].  Дополнительные сведения об использовании Azure Active Directory с хранилищем данных SQL см. в статье [Проверка подлинности в хранилище данных SQL Azure][Authentication to Azure SQL Data Warehouse] . |
| Ручное создание сценариев, использование мастера создания скриптов или подключение через среду SSMS выполняются с высокой скоростью, не отвечают или выводят ошибки | Убедитесь, что пользователи были созданы в базе данных master. Коме того, убедитесь, что в параметрах создания сценариев заданы выпуск ядра "Выпуск Microsoft Azure SQL Data Warehouse Edition" и тип модуля "База данных SQL Microsoft Azure". |
| Создание скриптов в SSMS завершается ошибкой                               | Если для параметра "Сформировать скрипт для зависимых объектов" задано значение True, создание скрипта для хранилища данных SQL завершается ошибкой. Чтобы устранить проблему, выберите "Средства -> Параметры -> Обозреватель объектов SQL Server -> Создать скрипт для зависимых параметров" и задайте значение False. |

## <a name="performance"></a>Производительность
| Проблемы                                                        | Разрешение                                                   |
| :----------------------------------------------------------- | :----------------------------------------------------------- |
| Устранение проблем с производительностью запросов                            | Если вы пытаетесь устранить проблему конкретного запроса, для начала ознакомьтесь со статьей, [посвященной мониторингу запросов][Learning how to monitor your queries]. |
| Низкая производительность и неправильные планы запросов в результате отсутствия статистики | Самая распространенная причина низкой производительности — отсутствие статистики таблиц.  Сведения о том, как создать статистику и почему они важны для производительности, см. в разделе [обслуживание статистики таблиц][Statistics] . |
| Низкий уровень параллелизма и помещение запросов в очередь                             | Чтобы обеспечить выделение памяти с учетом параллелизма, важно понимать принципы [управления рабочими нагрузками][Workload management] . |
| Реализация рекомендаций                              | Лучше всего начать изучение способов повышения производительности запросов со статьи [Рекомендации по использованию хранилища данных SQL Azure][SQL Data Warehouse best practices] . |
| Повышение производительности за счет масштабирования                      | Иногда повысить производительность запросов позволяет простое увеличение вычислительной мощности, выделяемой для выполнения запросов, за счет [масштабирования хранилища данных SQL][Scaling your SQL Data Warehouse]. |
| Низкая производительность запросов как результат низкого качества индексов     | Иногда запросы могут замедлить работу из-за [низкого качества индекса columnstore][Poor columnstore index quality].  Дополнительные сведения и [Перестроение индексов для улучшения качества сегментов][Rebuild indexes to improve segment quality]см. в этой статье. |

## <a name="system-management"></a>Управление системой
| Проблемы                                                        | Разрешение                                                   |
| :----------------------------------------------------------- | :----------------------------------------------------------- |
| Сообщение 40847: "Не удалось выполнить операцию, так как сервер достиг допустимой квоты в 45 000 единиц транзакций базы данных (DTU)". | Либо Сократите [DWU][DWU] базы данных, которую вы пытаетесь создать, либо [запросите увеличение квоты][request a quota increase]. |
| Анализ использования пространства                              | Сведения об использовании пространства в системе см. в разделе [размеры таблиц][Table sizes] . |
| Справка по управлению таблицами                                    | Дополнительные сведения об управлении таблицами см. в статье [Обзор таблиц][Overview] .  В этой статье также содержатся ссылки на более подробные разделы, такие как [типы данных таблиц][Data types], [распространение таблицы][Distribute], [индексирование таблицы][Index], [секционирование таблицы][Partition], [Ведение статистики таблицы][Statistics] и [временные таблицы][Temporary]. |
| Индикатор выполнения прозрачного шифрования данных (TDE) не обновляется в портал Azure | Состояние прозрачного шифрования данных можно узнать с помощью [PowerShell](/powershell/module/az.sql/get-azsqldatabasetransparentdataencryption). |


## <a name="differences-from-sql-database"></a>Отличия от базы данных SQL
| Проблемы                                 | Разрешение                                                   |
| :------------------------------------ | :----------------------------------------------------------- |
| Неподдерживаемые функции базы данных SQL     | Ознакомьтесь с разделом [Неподдерживаемые функции таблиц][Unsupported table features]. |
| Неподдерживаемые типы данных базы данных SQL   | Ознакомьтесь с разделом [Неподдерживаемые типы данных][Unsupported data types].        |
| Ограничения относительно инструкций DELETE и UPDATE         | См. статью [обходные пути][UPDATE workarounds], инструкции по [удалению][DELETE workarounds] и [Использование CTAS для обхода неподдерживаемого синтаксиса Update и DELETE][Using CTAS to work around unsupported UPDATE and DELETE syntax]. |
| Инструкция MERGE не поддерживается      | Ознакомьтесь с [обходными решениями для инструкции MERGE][MERGE workarounds].                  |
| Ограничения хранимых процедур          | Ознакомьтесь с [ограничениями хранимых процедур][Stored procedure limitations] . |
| Определяемые пользователем функции не поддерживают инструкции SELECT | Это текущее ограничение определяемых пользователем функций.  Поддерживаемый синтаксис см. в разделе [Создание функции][CREATE FUNCTION] . |

## <a name="next-steps"></a>Следующие шаги
Ниже перечислены некоторые ресурсы, которые можно использовать для поиска решения вашей проблемы.

* [Блоги]
* [Запросы функций]
* [Видеоролики]
* [Блоги группы CAT]
* [Создание запроса в службу поддержки]
* [Форум MSDN]
* [Форум Stack Overflow]
* [Twitter]

<!--Image references-->

<!--Article references-->
[Security overview]: sql-data-warehouse-overview-manage-security.md
[SSMS]: /sql/ssms/download-sql-server-management-studio-ssms
[SSDT for Visual Studio]: sql-data-warehouse-install-visual-studio.md
[Drivers for Azure SQL Data Warehouse]: sql-data-warehouse-connection-strings.md
[Connect to Azure SQL Data Warehouse]: sql-data-warehouse-connect-overview.md
[Создание запроса в службу поддержки]: sql-data-warehouse-get-started-create-support-ticket.md
[Scaling your SQL Data Warehouse]: sql-data-warehouse-manage-compute-overview.md
[DWU]: sql-data-warehouse-overview-what-is.md
[request a quota increase]: sql-data-warehouse-get-started-create-support-ticket.md
[Learning how to monitor your queries]: sql-data-warehouse-manage-monitor.md
[Provisioning instructions]: sql-data-warehouse-get-started-provision.md
[Configure server firewall access for your client IP]: sql-data-warehouse-get-started-provision.md
[SQL Data Warehouse best practices]: sql-data-warehouse-best-practices.md
[Table sizes]: sql-data-warehouse-tables-overview.md#table-size-queries
[Unsupported table features]: sql-data-warehouse-tables-overview.md#unsupported-table-features
[Unsupported data types]: sql-data-warehouse-tables-data-types.md#unsupported-data-types
[Overview]: sql-data-warehouse-tables-overview.md
[Data types]: sql-data-warehouse-tables-data-types.md
[Distribute]: sql-data-warehouse-tables-distribute.md
[Index]: sql-data-warehouse-tables-index.md
[Partition]: sql-data-warehouse-tables-partition.md
[Statistics]: sql-data-warehouse-tables-statistics.md
[Temporary]: sql-data-warehouse-tables-temporary.md
[Poor columnstore index quality]: sql-data-warehouse-tables-index.md#causes-of-poor-columnstore-index-quality
[Rebuild indexes to improve segment quality]: sql-data-warehouse-tables-index.md#rebuilding-indexes-to-improve-segment-quality
[Workload management]: resource-classes-for-workload-management.md
[Using CTAS to work around unsupported UPDATE and DELETE syntax]: sql-data-warehouse-develop-ctas.md
[UPDATE workarounds]: sql-data-warehouse-develop-ctas.md#ansi-join-replacement-for-update-statements
[DELETE workarounds]: sql-data-warehouse-develop-ctas.md#ansi-join-replacement-for-delete-statements
[MERGE workarounds]: sql-data-warehouse-develop-ctas.md#replace-merge-statements
[Stored procedure limitations]: sql-data-warehouse-develop-stored-procedures.md#limitations
[Authentication to Azure SQL Data Warehouse]: sql-data-warehouse-authentication.md


<!--MSDN references-->
[sys.database_principals]: /sql/relational-databases/system-catalog-views/sys-database-principals-transact-sql
[CREATE FUNCTION]: /sql/t-sql/statements/create-function-sql-data-warehouse
[sqlcmd]: sql-data-warehouse-get-started-connect-sqlcmd.md

<!--Other Web references-->
[Блоги]: https://azure.microsoft.com/blog/tag/azure-sql-data-warehouse/
[Блоги группы CAT]: https://blogs.msdn.microsoft.com/sqlcat/tag/sql-dw/
[Запросы функций]: https://feedback.azure.com/forums/307516-sql-data-warehouse
[Форум MSDN]: https://social.msdn.microsoft.com/Forums/home?forum=AzureSQLDataWarehouse
[Форум Stack Overflow]: https://stackoverflow.com/questions/tagged/azure-sqldw
[Twitter]: https://twitter.com/hashtag/SQLDW
[Видеоролики]: https://azure.microsoft.com/documentation/videos/index/?services=sql-data-warehouse
[Databricks]: https://docs.microsoft.com/azure/azure-databricks/databricks-extract-load-sql-data-warehouse#load-data-into-azure-sql-data-warehouse
