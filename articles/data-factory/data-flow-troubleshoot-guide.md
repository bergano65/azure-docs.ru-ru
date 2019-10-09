---
title: Устранение неполадок с потоками данных фабрики данных Azure | Документация Майкрософт
description: Узнайте, как устранять неполадки потока данных в фабрике данных Azure.
services: data-factory
author: kromerm
ms.service: data-factory
ms.topic: troubleshooting
ms.date: 10/08/2019
ms.author: makromer
ms.openlocfilehash: 5eafa0cc6284df5c969f63e2ab3fac4113fab417
ms.sourcegitcommit: 42748f80351b336b7a5b6335786096da49febf6a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/09/2019
ms.locfileid: "72178622"
---
# <a name="troubleshoot-azure-data-factory-data-flows"></a>Устранение неполадок в потоках данных фабрики данных Azure

В этой статье рассматриваются распространенные методы устранения неполадок для потоков данных в фабрике данных Azure.

## <a name="error-message-df-sys-01-shadeddatabricksorgapachehadoopfsazureazureexception-commicrosoftazurestoragestorageexception-the-specified-container-does-not-exist"></a>Сообщение об ошибке: DF-SYS-01: затененные. кирпичи. org. Apache. Hadoop. FS. Azure. Азуриксцептион: com. Microsoft. Azure. Storage. StorageException: Указанный контейнер не существует.

- **Проблема.** Не удается выполнить предварительный просмотр данных, отладка и поток данных конвейера, так как контейнер не существует

- **Причина.** Когда набор данных содержит контейнер, который не существует в хранилище

- **Решение**. Убедитесь, что контейнер, на который вы ссылаетесь, существует в наборе данных.

## <a name="error-message-df-sys-01-javalangassertionerror-assertion-failed-conflicting-directory-structures-detected-suspicious-paths"></a>Сообщение об ошибке: DF-SYS-01: Java. lang. Ассертионеррор: сбой утверждения: Обнаружены конфликтующие структуры каталогов. Подозрительные пути

- **Проблема.** Использование подстановочных знаков в преобразовании «источник» с Parquet-файлами

- **Причина.** Неверный или недопустимый синтаксис с подстановочными знаками

- **Решение**. Проверьте синтаксис с подстановочными знаками, который используется в параметрах преобразования источника.

## <a name="error-message-df-src-002-container-container-name-is-required"></a>Сообщение об ошибке: DF-SRC-002: "контейнер" (имя контейнера) является обязательным

- **Проблема.** Не удается выполнить предварительный просмотр данных, отладка и поток данных конвейера, так как контейнер не существует

- **Причина.** Когда набор данных содержит контейнер, который не существует в хранилище

- **Решение**. Убедитесь, что контейнер, на который вы ссылаетесь, существует в наборе данных.

## <a name="error-message-df-uni-001-primarykeyvalue-has-incompatible-types-integertype-and-stringtype"></a>Сообщение об ошибке: DF-UNI-001: Примарикэйвалуе имеет несовместимые типы IntegerType и Стрингтипе

- **Проблема.** Не удается выполнить предварительный просмотр данных, отладка и поток данных конвейера, так как контейнер не существует

- **Причина.** Происходит при попытке вставить неправильный тип первичного ключа в приемники базы данных

- **Решение**. Используйте производный столбец для приведения столбца, используемого для первичного ключа в потоке данных, в соответствии с типом данных целевой базы данных.

## <a name="error-message-df-sys-01-commicrosoftsqlserverjdbcsqlserverexception-the-tcpip-connection-to-the-host-xxxxxdatabasewindowsnet-port-1433-has-failed-error-xxxxdatabasewindowsnet-verify-the-connection-properties-make-sure-that-an-instance-of-sql-server-is-running-on-the-host-and-accepting-tcpip-connections-at-the-port-make-sure-that-tcp-connections-to-the-port-are-not-blocked-by-a-firewall"></a>Сообщение об ошибке: DF-SYS-01: com. Microsoft. SqlServer. JDBC. SQLServerException: Сбой подключения TCP/IP к узлу xxxxx.database.windows.net порт 1433. Ошибка: "xxxx.database.windows.net. Проверьте свойства соединения. Убедитесь, что экземпляр SQL Server работает на узле и принимает подключения TCP/IP через порт. Убедитесь, что TCP-подключения к порту не заблокированы брандмауэром.

- **Проблема.** Не удается предварительно просмотреть данные или выполнить конвейер с источником или приемником базы данных

- **Причина.** База данных защищена брандмауэром

- **Решение**. Открытие доступа брандмауэра к базе данных

## <a name="error-message-df-sys-01-commicrosoftsqlserverjdbcsqlserverexception-there-is-already-an-object-named-xxxxxx-in-the-database"></a>Сообщение об ошибке: DF-SYS-01: com. Microsoft. SqlServer. JDBC. SQLServerException: В базе данных уже имеется объект с именем "XXXXXX".

- **Проблема.** Приемнику не удалось создать таблицу

- **Причина.** В целевой базе данных уже существует имя таблицы с тем же именем, определенным в источнике или в наборе данных

- **Решение**. Изменение имени таблицы, которую вы пытаетесь создать



## <a name="next-steps"></a>Следующие шаги

Для получения дополнительных сведений об устранении неполадок воспользуйтесь следующими ресурсами:

*  [Блог фабрики данных](https://azure.microsoft.com/blog/tag/azure-data-factory/)
*  [Запросы функций фабрики данных](https://feedback.azure.com/forums/270578-data-factory)
*  [Видеоролики по Azure](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
*  [Форум MSDN](https://social.msdn.microsoft.com/Forums/home?sort=relevancedesc&brandIgnore=True&searchTerm=data+factory)
*  [Форум Stack Overflow для фабрики данных](https://stackoverflow.com/questions/tagged/azure-data-factory)
*  [Сведения о фабрике данных в Twitter](https://twitter.com/hashtag/DataFactory)
