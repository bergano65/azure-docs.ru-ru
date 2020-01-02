---
title: Устранение неполадок в потоках данных
description: Узнайте, как устранять неполадки потока данных в фабрике данных Azure.
services: data-factory
ms.author: makromer
author: kromerm
manager: anandsub
ms.service: data-factory
ms.topic: troubleshooting
ms.custom: seo-lt-2019
ms.date: 12/06/2019
ms.openlocfilehash: b972bbeac419d88afdd257a7fd19587dbaedf0d9
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/08/2019
ms.locfileid: "74930173"
---
# <a name="troubleshoot-azure-data-factory-data-flows"></a>Устранение неполадок в потоках данных фабрики данных Azure

В этой статье рассматриваются распространенные методы устранения неполадок для потоков данных в фабрике данных Azure.

## <a name="common-errors-and-messages"></a>Распространенные ошибки и сообщения

### <a name="error-message-df-sys-01-shadeddatabricksorgapachehadoopfsazureazureexception-commicrosoftazurestoragestorageexception-the-specified-container-does-not-exist"></a>Сообщение об ошибке: DF-SYS-01: теньd. кирпичs. org. Apache. Hadoop. FS. Azure. Азуриксцептион: com. Microsoft. Azure. Storage. StorageException: указанный контейнер не существует.

- **Симптомы**: не удается выполнить предварительный просмотр данных, отладка и поток данных конвейера, так как контейнер не существует

- **Причина**: Если набор данных содержит контейнер, который не существует в хранилище

- **Решение**. Убедитесь, что контейнер, на который вы ссылаетесь, существует в наборе данных.

### <a name="error-message-df-sys-01-javalangassertionerror-assertion-failed-conflicting-directory-structures-detected-suspicious-paths"></a>Сообщение об ошибке: DF-SYS-01: Java. lang. Ассертионеррор: сбой утверждения: обнаружены конфликтующие структуры каталогов. Подозрительные пути

- **Симптомы**: при использовании подстановочных знаков в преобразовании «источник» с Parquet-файлами

- **Причина**: неправильный или недопустимый синтаксис подстановочных знаков

- **Решение**. Проверьте синтаксис подстановочных знаков, который используется в параметрах преобразования источника.

### <a name="error-message-df-src-002-container-container-name-is-required"></a>Сообщение об ошибке: DF-SRC-002: "контейнер" (имя контейнера) является обязательным

- **Симптомы**: не удается выполнить предварительный просмотр данных, отладка и поток данных конвейера, так как контейнер не существует

- **Причина**: Если набор данных содержит контейнер, который не существует в хранилище

- **Решение**. Убедитесь, что контейнер, на который вы ссылаетесь, существует в наборе данных.

### <a name="error-message-df-uni-001-primarykeyvalue-has-incompatible-types-integertype-and-stringtype"></a>Сообщение об ошибке: DF-UNI-001: Примарикэйвалуе имеет несовместимые типы IntegerType и Стрингтипе

- **Симптомы**: не удается выполнить предварительный просмотр данных, отладка и поток данных конвейера, так как контейнер не существует

- **Причина**: происходит при попытке вставить неправильный тип первичного ключа в приемники базы данных

- **Решение**. Используйте производный столбец для приведения столбца, используемого для первичного ключа в потоке данных, в соответствии с типом данных целевой базы данных.

### <a name="error-message-df-sys-01-commicrosoftsqlserverjdbcsqlserverexception-the-tcpip-connection-to-the-host-xxxxxdatabasewindowsnet-port-1433-has-failed-error-xxxxdatabasewindowsnet-verify-the-connection-properties-make-sure-that-an-instance-of-sql-server-is-running-on-the-host-and-accepting-tcpip-connections-at-the-port-make-sure-that-tcp-connections-to-the-port-are-not-blocked-by-a-firewall"></a>Сообщение об ошибке: DF-SYS-01: com. Microsoft. SqlServer. JDBC. SQLServerException: сбой подключения TCP/IP к узлу xxxxx.database.windows.net порта 1433. Ошибка: "xxxx.database.windows.net. Проверьте свойства соединения. Убедитесь, что экземпляр SQL Server работает на узле и принимает подключения TCP/IP через порт. Убедитесь, что TCP-подключения к порту не заблокированы брандмауэром.

- **Симптомы**: не удается предварительно просмотреть данные или выполнить конвейер с источником или приемником базы данных

- **Причина**: база данных защищена брандмауэром

- **Решение**. Откройте доступ к базе данных через брандмауэр.

### <a name="error-message-df-sys-01-commicrosoftsqlserverjdbcsqlserverexception-there-is-already-an-object-named-xxxxxx-in-the-database"></a>Сообщение об ошибке: DF-SYS-01: com. Microsoft. SqlServer. JDBC. SQLServerException: в базе данных уже есть объект с именем XXXXXX.

- **Симптомы**: не удалось создать таблицу приемника

- **Причина**: в целевой базе данных уже существует имя таблицы с тем же именем, определенным в источнике или в наборе данных

- **Решение**. Измените имя таблицы, которую вы пытаетесь создать.

### <a name="error-message-df-sys-01-commicrosoftsqlserverjdbcsqlserverexception-string-or-binary-data-would-be-truncated"></a>Сообщение об ошибке: DF-SYS-01: com. Microsoft. SqlServer. JDBC. SQLServerException: строковые или двоичные данные будут обрезаны. 

- **Симптомы**. при записи данных в приемник SQL происходит сбой потока данных при выполнении конвейера с возможной ошибкой усечения.

- **Причина**: поле из потока данных сопоставляется со столбцом в базе данных SQL недостаточно для хранения значения, в результате чего драйвер SQL выдает эту ошибку.

- **Решение**. можно уменьшить длину данных для строковых столбцов с помощью ```left()``` в производном столбце или реализовать [шаблон "строка ошибки".](how-to-data-flow-error-rows.md)

### <a name="error-message-since-spark-23-the-queries-from-raw-jsoncsv-files-are-disallowed-when-the-referenced-columns-only-include-the-internal-corrupt-record-column"></a>Сообщение об ошибке: поскольку Spark 2,3, запросы из необработанных файлов JSON/CSV запрещены, если ссылочные столбцы содержат только внутренний поврежденный столбец записи. 

- **Симптомы**: ошибка чтения из источника JSON

- **Причина**. при чтении из источника JSON с одним документом во многих вложенных строках в ADF-файле с помощью Spark не удается определить, где начинается новый документ и заканчивается предыдущий документ.

- **Решение**. в преобразовании «источник», использующем набор данных JSON, разверните узел «Параметры JSON» и включите «один документ».

### <a name="error-message-duplicate-columns-found-in-join"></a>Сообщение об ошибке: в соединении обнаружены дублирующиеся столбцы

- **Симптомы**: преобразование «соединение» привело к тому, что столбцы из левой и правой части содержат дублирующиеся имена столбцов.

- **Причина**: соединяемые потоки имеют общие имена столбцов

- **Решение**. Добавьте трансфорамтион SELECT после объединения и выберите "удалить дублирующиеся столбцы" как для входных, так и для выходных данных.


## <a name="general-troubleshooting-guidance"></a>Общие рекомендации по устранению неполадок

1. Проверьте состояние подключений к набору данных. В каждом преобразовании источника и приемника посетите связанную службу для каждого набора данных, который вы используете, и проверьте соединения.
2. Проверьте состояние соединения файла и таблицы в конструкторе потока данных. Чтобы убедиться, что вы можете получить доступ к данным, переключитесь на отладку и щелкните Предварительный просмотр данных в исходных преобразованиях.
3. Если все выглядит хорошо в предварительной версии данных, перейдите в конструктор конвейера и разместите поток данных в действии конвейера. Отладка конвейера для сквозного тестирования.

## <a name="next-steps"></a>Дальнейшие действия

Для получения дополнительных сведений об устранении неполадок воспользуйтесь следующими ресурсами:

*  [Блог фабрики данных](https://azure.microsoft.com/blog/tag/azure-data-factory/)
*  [Запросы функций фабрики данных](https://feedback.azure.com/forums/270578-data-factory)
*  [Видеоролики по Azure](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
*  [Форум MSDN](https://social.msdn.microsoft.com/Forums/home?sort=relevancedesc&brandIgnore=True&searchTerm=data+factory)
*  [Форум Stack Overflow для фабрики данных](https://stackoverflow.com/questions/tagged/azure-data-factory)
*  [Сведения о фабрике данных в Twitter](https://twitter.com/hashtag/DataFactory)
*  [Пошаговое руководств по производительности потоков данных сопоставления ADF](concepts-data-flow-performance.md)
