---
title: Выполнение запросов Apache Base в Azure HDInsight с помощью Apache Phoenix
description: Узнайте, как использовать Apache Zeppelin для выполнения запросов Apache Base с Phoenix.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 10/14/2019
ms.openlocfilehash: 28eeb446e55213f1ffa0a638878f6432fd15a05a
ms.sourcegitcommit: bb65043d5e49b8af94bba0e96c36796987f5a2be
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/16/2019
ms.locfileid: "72392245"
---
# <a name="use-apache-zeppelin-to-run-apache-phoenix-queries-over-apache-hbase-in-azure-hdinsight"></a>Использование Apache Zeppelin для запуска запросов Apache Phoenix через Apache HBase в Azure HDInsight

Apache Phoenix — это высокоуровневая реляционная база данных с открытым исходным кодом, построенная на базе HBase. Phoenix позволяет использовать запросы SQL вроде HBase. Phoenix использует драйверы JDBC под для создания, удаления, изменения таблиц, индексов, представлений и последовательностей SQL.  Кроме того, Phoenix можно использовать для обновления строк по отдельности и в небольшого объема. В Phoenix используется собственная компиляция NOSQL вместо MapReduce для компиляции запросов, что позволяет создавать приложения с низкой задержкой на основе HBase.

Apache Zeppelin — это веб-Записная книжка с открытым исходным кодом, которая позволяет создавать управляемые данными документы с поддержкой интерактивного анализа данных и таких языков, как SQL и Scala. Она помогает разработчикам данных & разработки, Организации, выполнения и совместного использования кода для обработки данных. Она позволяет визуализировать результаты, не обращаясь в командную строку или не требуя сведений о кластере.

Пользователи HDInsight могут использовать Apache Zeppelin для запроса таблиц Phoenix. Apache Zeppelin интегрирован с кластером HDInsight и не содержит дополнительных действий по его использованию. Просто создайте записную книжку Zeppelin с интерпретатором JDBC и начните писать запросы SQL в Phoenix.

## <a name="prerequisites"></a>Технические условия

Кластер Apache HBase в HDInsight. См. статью [Начало работы с Apache HBase](./apache-hbase-tutorial-get-started-linux.md).

## <a name="create-an-apache-zeppelin-note"></a>Создание заметки Apache Zeppelin

1. В URL-адресе `https://CLUSTERNAME.azurehdinsight.net/zeppelin` замените `CLUSTERNAME` именем своего кластера. В веб-браузере перейдите по этому URL-адресу. Введите имя пользователя и пароль для входа в кластер.

1. На странице Zeppelin выберите **создать новую заметку**.

    ![Заметка Zeppelin для кластера Interactive Query HDInsight](./media/apache-hbase-phoenix-zeppelin/hbase-zeppelin-create-note.png)

1. В диалоговом окне **создания заметки** введите или выберите следующие значения:

    - Имя примечания. Введите имя для примечания.
    - Интерпретатор по умолчанию: выберите **JDBC** из раскрывающегося списка.

    Затем выберите **создать заметку**.

1. Убедитесь, что в заголовке записной книжки отображается состояние подключено. Он обозначается зеленой точкой в правом верхнем углу.

    ![Состояние записной книжки Zeppelin](./media/apache-hbase-phoenix-zeppelin/hbase-zeppelin-connected.png "Состояния записной книжки Zeppelin")

1. Создайте таблицу HBase. Введите следующую команду и нажмите клавиши **SHIFT + ВВОД**:

    ```sql
    %jdbc(phoenix)
    CREATE TABLE Company (
        company_id INTEGER PRIMARY KEY,
        name VARCHAR(225)
    );
    ```

    Инструкция **% JDBC (Phoenix)** в первой строке указывает записной книжке использовать интерпретатор JDBC в Phoenix.

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

- [Apache Phoenix теперь поддерживает Zeppelin в Azure HDInsight](https://blogs.msdn.microsoft.com/ashish/2018/08/17/apache-phoenix-now-supports-zeppelin-in-azure-hdinsight/)
- [Грамматика Apache Phoenix](https://phoenix.apache.org/language/index.html)
