---
title: Ограничения емкости — Azure синапсе Analytics (прежнее название — SQL DW)
description: Максимальное значение, допустимое для различных компонентов аналитики SQL в Azure синапсе.
services: sql-data-warehouse
author: mlee3gsd
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: design
ms.date: 11/04/2019
ms.author: martinle
ms.reviewer: igorstan
ms.openlocfilehash: e3661797ea408f219a67a1862901fee7c27a1d58
ms.sourcegitcommit: 5a8c65d7420daee9667660d560be9d77fa93e9c9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/15/2019
ms.locfileid: "74123915"
---
# <a name="azure-synapse-analytics-formerly-sql-dw-capacity-limits"></a>Ограничения емкости Azure синапсе Analytics (ранее — хранилище данных SQL)

Максимальное значение, допустимое для различных компонентов Azure синапсе.

## <a name="workload-management"></a>Управление рабочей нагрузкой

| Категория | ОПИСАНИЕ | Максимальная |
|:--- |:--- |:--- |
| [Единицы использования хранилища данных (DWU)](what-is-a-data-warehouse-unit-dwu-cdwu.md) |Максимальный DWU для отдельной единицы пула SQL (хранилища данных) | Поколение 1: DW6000<br></br>Поколение 2: DW30000c |
| [Единицы использования хранилища данных (DWU)](what-is-a-data-warehouse-unit-dwu-cdwu.md) |Стандартное количество единиц передачи данных (DTU) для одного сервера |54 000<br></br>По умолчанию каждый SQL Server (например, myserver.database.windows.net) имеет квоту DTU 54 000, что позволяет до DW5000c. Эта квота является просто ограничением для безопасности. Вы можете увеличить квоту, [отправив запрос в службу поддержки](sql-data-warehouse-get-started-create-support-ticket.md) и указав *Квота* в качестве типа запроса.  Чтобы вычислить потребности в DTU, умножьте 7,5 на общее количество DWU или умножьте 9,5 на общее cDWU. Например,<br></br>6000 DWU x 7,5 = 45 000 DTU<br></br>DW5000c x 9,5 = 47 500 DTU.<br></br>Текущее потребление DTU можно просмотреть в настройке SQL Server на портале. В рамках квоты DTU учитываются как приостановленные, так и активные базы данных. |
| Подключение к базе данных |Максимальное число одновременных открытых сеансов |1024<br/><br/>Количество одновременных открытых сеансов будет меняться в зависимости от выбранного DWU. DWU600c и выше поддерживают максимум 1024 открытых сеансов. DWU500c и ниже поддерживают максимальное число одновременных открытых сеансов, равное 512. Обратите внимание: есть ограничения на количество запросов, которые могут выполняться одновременно. При превышении ограничения на число параллельных запросов запрос помещается во внутреннюю очередь для ожидания обработки. |
| Подключение к базе данных |Максимальный объем памяти для подготовленных инструкций |20 МБ |
| [Управление рабочей нагрузкой](resource-classes-for-workload-management.md) |Максимальное число одновременных запросов |128<br/><br/>  Будет выполняться не более 128 параллельных запросов, а оставшиеся запросы будут поставлены в очередь.<br/><br/>Количество одновременных запросов может уменьшиться, если пользователи назначены более высокому классу ресурсов или если параметр [единицы измерения хранилища данных](memory-concurrency-limits.md) будет снижен. Выполнение некоторых запросов, например запросов динамического административного представления, всегда разрешено и не влияет на лимит параллельных запросов. Дополнительные сведения о выполнении параллельных запросов см. в статье [Максимальные ограничения параллелизма](memory-concurrency-limits.md). |
| [tempdb](sql-data-warehouse-tables-temporary.md) |Максимальный объем в ГБ |399 ГБ для DW100. То есть для 1000 DWU размер tempdb составит 3,99 ТБ. |

## <a name="database-objects"></a>Объекты базы данных
| Категория | ОПИСАНИЕ | Максимальная |
|:--- |:--- |:--- |
| База данных |Максимальный размер | Поколение 1: 240 ТБ сжатых данных на диске Не зависит от пространства временной базы данных или журнала и предназначается исключительно для постоянных таблиц.  Коэффициент сжатия кластеризованного индекса columnstore примерно равен 5.  Сжатие позволяет базе данных увеличиваться в размере приблизительно до 1 ПБ, если все таблицы используют кластеризованный индекс columnstore (этот тип таблицы используется по умолчанию). <br/><br/> Поколение 2: 240 ТБ для таблиц rowstore и неограниченное пространство хранения для таблиц columnstore |
| таблица |Максимальный размер |60 ТБ сжатых данных на диске |
| таблица |Количество таблиц в базе данных | 100 000 |
| таблица |Количество столбцов в таблице |1024 столбца |
| таблица |Количество байт в столбце |Зависит от [типа данных](sql-data-warehouse-tables-data-types.md)столбца. Ограничение составляет 8000 для типов данных char, 4000 для nvarchar или 2 ГБ для типов данных MAX. |
| таблица |Количество байт в строке (определенный размер) |8060 байт<br/><br/>Количество байт в строке вычисляется так же, как и для SQL Server с включенным сжатием страниц. Как и SQL Server, поддерживается хранение с переполнением строк, что позволяет **столбцам переменной длины** быть отправлено за пределы строки. Когда столбцы переменной длины выходят за пределы строк, в главной записи сохраняется только 24-байтовый корень. Дополнительные сведения см. в статье [Превышающие размер страницы данные строки, превышающие 8 КБ](https://msdn.microsoft.com/library/ms186981.aspx). |
| таблица |Количество разделов в таблице |15 000<br/><br/>Для повышения производительности рекомендуем сократить количество разделов до минимума, который позволит выполнять ваши бизнес-задачи. С увеличением количества секций не только растут затраты на операции языка описания данных DDL и языка обработки данных DML, но и снижается производительность. |
| таблица |Количество символов в разделе (граничное значение) |4000 |
| Индекс |Количество некластеризованных индексов в таблице |50<br/><br/>Применимо только к таблицам rowstore. |
| Индекс |Количество кластеризованных индексов в таблице |1<br><br/>Применимо к таблицам rowstore и columnstore. |
| Индекс |Размер ключа индекса |900 байт<br/><br/>Применимо только к индексам rowstore.<br/><br/>В столбцах varchar могут создаваться индексы с максимальным размером более 900 байтов, если при создании такого индекса размер существующих данных в этих столбцах не превышает 900 байтов. Учтите, что при последующем выполнении в столбцах инструкций INSERT или UPDATE, которые приведут к превышению общего размера данных в 900 байт, соответствующие действия завершатся ошибкой. |
| Индекс |Количество ключевых столбцов в индексе |16<br/><br/>Применимо только к индексам rowstore. Кластеризованные индексы columnstore включают все столбцы. |
| Статистика |Размер значений объединенных столбцов |900 байт |
| Статистика |Количество столбцов в объекте статистики |32 |
| Статистика |Количество объектов статистики для столбцов в таблице |30 000 |
| Хранимые процедуры |Максимальное количество вложений |8 |
| Просмотр |Количество столбцов в представлении |1024 |

## <a name="loads"></a>Нагрузка
| Категория | ОПИСАНИЕ | Максимальная |
|:--- |:--- |:--- |
| Нагрузка Polybase |МБ в строке |1<br/><br/>Polybase загружает строки, размер которых меньше 1 МБ. Загрузка типов данных LOB в таблицы с кластеризованным индексом columnstore (CCI) не поддерживается.<br/><br/> |

## <a name="queries"></a>Запросы
| Категория | ОПИСАНИЕ | Максимальная |
|:--- |:--- |:--- |
| Запрос |Количество помещенных в очередь запросов к пользовательским таблицам |1000 |
| Запрос |Количество одновременных запросов к системным представлениям |100 |
| Запрос |Количество помещенных в очередь запросов к системным представлениям |1000 |
| Запрос |Максимальное количество параметров |2098 |
| Пакетная служба |Максимальный размер |65 536*4096 |
| Результаты SELECT |Количество столбцов в строке |4096<br/><br/>В каждой строке результата SELECT может содержаться не более 4096 столбцов. Нет никакой гарантии, что их количество всегда будет равно 4096. Если планом запроса предусмотрено использование временной таблицы, в такой таблице может быть не более 1024 столбцов. |
| SELECT |Вложенные запросы |32<br/><br/>Инструкция SELECT может содержать не более 32 вложенных запросов. Нет никакой гарантии, что их количество всегда будет равно 32. Например, с помощью инструкции JOIN можно включить в план запроса вложенный запрос. Количество вложенных запросов также может ограничиваться объемом доступной памяти. |
| SELECT |Количество столбцов в инструкции JOIN |1024 столбца<br/><br/>Инструкция JOIN может содержать не более 1024 столбцов. Нет никакой гарантии, что их количество всегда будет равно 1024. Если планом для инструкции JOIN предусмотрено использование временной таблицы с количеством столбцов, которое превышает результат JOIN, к такой временной таблице применяется ограничение в 1024 столбца. |
| SELECT |Количество байт в именах столбцов GROUP BY |8060<br/><br/>Размер имен столбцов в предложении GROUP BY не может превышать 8060 байтов. |
| SELECT |Количество байт в именах столбцов ORDER BY |8060 байт<br/><br/>Размер имен столбцов в предложении ORDER BY не может превышать 8060 байтов. |
| Идентификаторы в одной инструкции |Количество идентификаторов, на которые имеются ссылки. |65 535<br/><br/> Количество идентификаторов, которые могут содержаться в одном выражении запроса, ограничено. Дополнительные сведения см. в статье [Internal error: An expression services limit has been reached](https://support.microsoft.com/help/913050/error-message-when-you-run-a-query-in-sql-server-2005-internal-error-a)(Сообщение об ошибке при выполнении запроса в SQL Server 2005: "Внутренняя ошибка: достигнут предел служб выражений"). |
| Строковые литералы | Количество строковых литералов в инструкции | 20 000 <br/><br/>Число строковых констант в одном выражении запроса ограничено. Дополнительные сведения см.|

## <a name="metadata"></a>Метаданные
| Системное представление | Максимальное количество строк |
|:--- |:--- |
| sys.dm_pdw_component_health_alerts |10 000 |
| sys.dm_pdw_dms_cores |100 |
| sys.dm_pdw_dms_workers |Общее количество рабочих ролей DMS для последней 1000 запросов SQL. |
| sys.dm_pdw_errors |10 000 |
| sys.dm_pdw_exec_requests |10 000 |
| sys.dm_pdw_exec_sessions |10 000 |
| sys.dm_pdw_request_steps |Общее количество действий для последней 1000 запросов SQL, хранящихся в sys.dm_pdw_exec_requests. |
| sys.dm_pdw_os_event_logs |10 000 |
| sys.dm_pdw_sql_requests |Последняя 1000 запросов SQL, которые хранятся в sys.dm_pdw_exec_requests. |

## <a name="next-steps"></a>Дополнительная информация
Рекомендации по использованию Azure синапсе см. на [листе Памятка по](cheat-sheet.md).
