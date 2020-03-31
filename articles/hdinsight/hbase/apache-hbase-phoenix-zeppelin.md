---
title: Выполнить запросы базы Apache в Azure HDInsight с Apache Phoenix
description: Узнайте, как использовать Apache Цеппелин для запуска запросов базы Apache с Phoenix.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 10/14/2019
ms.openlocfilehash: 28eeb446e55213f1ffa0a638878f6432fd15a05a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "72392245"
---
# <a name="use-apache-zeppelin-to-run-apache-phoenix-queries-over-apache-hbase-in-azure-hdinsight"></a>Используйте Apache Цеппелин для запуска запросов Apache Phoenix над Apache HBase в Azure HDInsight

Apache Phoenix — это реляционная база данных на основе HBase с открытым кодом и высоким уровнем параллелизма. Phoenix позволяет использовать S'L, как запросы по HBase. Phoenix использует драйверы JDBC внизу, чтобы позволить вам создавать, удалять, изменять таблицы, индексы, представления и последовательности.  Вы также можете использовать Phoenix для обновления строк индивидуально и оптом. Phoenix использует нативную компиляцию NOS'L вместо того, чтобы использовать MapReduce для компиляции запросов, что позволяет создавать приложения с низкой задержкой поверх HBase.

Apache Цеппелин — это веб-блокнот с открытым исходным кодом, позволяющий создавать совместные документы, основанные на данных, с использованием интерактивной аналитики данных и таких языков, как S'L и Scala. Это помогает разработчикам данных & данных ученым разрабатывать, организовывать, выполнять и обмениваться кодом для обработки данных. Это позволяет визуализировать результаты, не ссылаясь на командную строку или не нуждаясь в деталях кластера.

Пользователи HDInsight могут использовать Apache Цеппелин для запроса таблиц Phoenix. Апач Цеппелин интегрирован с кластером HDInsight, и нет никаких дополнительных шагов, чтобы использовать его. Просто создайте блокнот Цеппелин с переводчиком JDBC и начните писать свои запросы Phoenix S'L

## <a name="prerequisites"></a>Предварительные требования

Кластер Apache HBase на HDInsight. Смотрите [Начало работы с Apache HBase](./apache-hbase-tutorial-get-started-linux.md).

## <a name="create-an-apache-zeppelin-note"></a>Создание заметки Apache Zeppelin

1. В URL-адресе `https://CLUSTERNAME.azurehdinsight.net/zeppelin` замените `CLUSTERNAME` именем своего кластера. В веб-браузере перейдите по этому URL-адресу. Введите имя пользователя и пароль для входа в кластер.

1. Со страницы Цеппелина выберите **Создать новую заметку.**

    ![Заметка Zeppelin для кластера Interactive Query HDInsight](./media/apache-hbase-phoenix-zeppelin/hbase-zeppelin-create-note.png)

1. В диалоговом окне **создания заметки** введите или выберите следующие значения:

    - Имя примечания: Введите имя для заметки.
    - Переводчик по умолчанию: Выберите **jdbc** из списка выпадающих.

    Затем выберите **Создать Примечание**.

1. Убедитесь, что заголовок ноутбука показывает подключенный статус. Это обозначается зеленой точкой в правом верхнем углу.

    ![Состояния записной книжки Zeppelin](./media/apache-hbase-phoenix-zeppelin/hbase-zeppelin-connected.png "Состояния записной книжки Zeppelin")

1. Создайте таблицу HBase. Введите следующую команду, а затем нажмите **Shift и введите:**

    ```sql
    %jdbc(phoenix)
    CREATE TABLE Company (
        company_id INTEGER PRIMARY KEY,
        name VARCHAR(225)
    );
    ```

    Заявление **%jdbc (phoenix)** в первой строке говорит блокноту использовать переводчика Phoenix JDBC.

1. Просмотр созданных таблиц.

    ```sql
    %jdbc(phoenix)
    SELECT DISTINCT table_name
    FROM SYSTEM.CATALOG
    WHERE table_schem is null or table_schem <> 'SYSTEM';
    ```

1. Вставьте значения в таблицу.

    ```sql
    %jdbc(phoenix)
    UPSERT INTO dbo.Company VALUES(1, 'Microsoft');
    UPSERT INTO dbo.Company (name, company_id) VALUES('Apache', 2);
    ```

1. Отправьте запрос к таблице.

    ```sql
    %jdbc(phoenix)
    SELECT * FROM dbo.Company;
    ```

1. Удалите запись.

    ```sql
    %jdbc(phoenix)
    DELETE FROM dbo.Company WHERE COMPANY_ID=1;
    ```

1. Удалите таблицу.

    ```sql
    %jdbc(phoenix)
    DROP TABLE dbo.Company;
    ```

## <a name="next-steps"></a>Дальнейшие действия

- [Apache Phoenix теперь поддерживает Цеппелин в Azure HDInsight](https://blogs.msdn.microsoft.com/ashish/2018/08/17/apache-phoenix-now-supports-zeppelin-in-azure-hdinsight/)
- [Грамматика Апачи Феникс](https://phoenix.apache.org/language/index.html)
