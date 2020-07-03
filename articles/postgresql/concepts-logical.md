---
title: Логическое декодирование — база данных Azure для PostgreSQL — один сервер
description: Описывает логическое декодирование и wal2json для отслеживания измененных данных в базе данных Azure для PostgreSQL-Single Server.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 03/31/2020
ms.openlocfilehash: 1213b38f2b67e8fed179cfda4308943808893e1b
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2020
ms.locfileid: "80522151"
---
# <a name="logical-decoding"></a>Логическое декодирование
 
[Логическое декодирование в PostgreSQL](https://www.postgresql.org/docs/current/logicaldecoding.html) позволяет передавать изменения данных внешним потребителям. Логическое декодирование часто используется для потоковой передачи событий и сценариев отслеживания измененных данных.

При логическом декодировании используется подключаемый модуль вывода для преобразования журнала упреждающего ввода postgres (WAL) в доступный для чтения формат. База данных Azure для PostgreSQL предоставляет два подключаемых модуля выходных данных: [test_decoding](https://www.postgresql.org/docs/current/test-decoding.html) и [wal2json](https://github.com/eulerto/wal2json).
 

> [!NOTE]
> Логическая декодирование находится в общедоступной предварительной версии в базе данных Azure для PostgreSQL-Single Server.


## <a name="set-up-your-server"></a>Настройка сервера
Чтобы начать работу с логическим декодированием, включите сервер для сохранения и потоковой передачи WAL. 

1. Задайте Azure. replication_support для `logical` использования Azure CLI. 
   ```
   az postgres server configuration set --resource-group mygroup --server-name myserver --name azure.replication_support --value logical
   ```

   > [!NOTE]
   > Если вы используете реплики чтения, Azure. replication_support задать также `logical` , чтобы разрешить выполнение реплик. Если вы прекращаете использовать логическое декодирование, измените параметр обратно на `replica`. 


2. Чтобы изменения вступили в силу, перезагрузите сервер.
   ```
   az postgres server restart --resource-group mygroup --name myserver
   ```

## <a name="start-logical-decoding"></a>Начать логическое декодирование

Логическое декодирование можно использовать с помощью протокола потоковой передачи или интерфейса SQL. Оба метода используют [слоты репликации](https://www.postgresql.org/docs/current/logicaldecoding-explanation.html#LOGICALDECODING-REPLICATION-SLOTS). Слот представляет поток изменений из одной базы данных.

Для использования слота репликации требуются права репликации postgres. В настоящее время привилегия репликации доступна только для пользователя-администратора сервера. 

### <a name="streaming-protocol"></a>Протокол потоковой передачи
Часто рекомендуется использовать изменения с помощью протокола потоковой передачи. Вы можете создать собственный потребитель или соединитель или использовать такой инструмент, как [дебезиум](https://debezium.io/). 

Обратитесь к документации по wal2json, чтобы получить [Пример использования протокола потоковой передачи с pg_recvlogical](https://github.com/eulerto/wal2json#pg_recvlogical).


### <a name="sql-interface"></a>Интерфейс SQL
В приведенном ниже примере мы используем интерфейс SQL с подключаемым модулем wal2json.
 
1. Создайте слот.
   ```SQL
   SELECT * FROM pg_create_logical_replication_slot('test_slot', 'wal2json');
   ```
 
2. Выдавать команды SQL. Пример:
   ```SQL
   CREATE TABLE a_table (
      id varchar(40) NOT NULL,
      item varchar(40),
      PRIMARY KEY (id)
   );
   
   INSERT INTO a_table (id, item) VALUES ('id1', 'item1');
   DELETE FROM a_table WHERE id='id1';
   ```

3. Использование изменений.
   ```SQL
   SELECT data FROM pg_logical_slot_get_changes('test_slot', NULL, NULL, 'pretty-print', '1');
   ```

   Результат будет выглядеть следующим образом:
   ```
   {
         "change": [
         ]
   }
   {
         "change": [
                  {
                           "kind": "insert",
                           "schema": "public",
                           "table": "a_table",
                           "columnnames": ["id", "item"],
                           "columntypes": ["character varying(40)", "character varying(40)"],
                           "columnvalues": ["id1", "item1"]
                  }
         ]
   }
   {
         "change": [
                  {
                           "kind": "delete",
                           "schema": "public",
                           "table": "a_table",
                           "oldkeys": {
                                 "keynames": ["id"],
                                 "keytypes": ["character varying(40)"],
                                 "keyvalues": ["id1"]
                           }
                  }
         ]
   }
   ```

4. Удалите слот после завершения его использования.
   ```SQL
   SELECT pg_drop_replication_slot('test_slot'); 
   ```


## <a name="monitoring-slots"></a>Слоты мониторинга

Необходимо отслеживать логическое декодирование. Необходимо удалить любой неиспользуемый слот репликации. Слоты находятся в postgres WAL журналов и соответствующих системных каталогах, пока они не считаны потребителем. Если потребитель завершает работу или неправильно настроен, неиспользованные журналы будут занимать и заполнять хранилище. Кроме того, неиспользованные журналы увеличивают риск идентификатора транзакции врапараунд. В обоих случаях сервер может стать недоступным. Поэтому важно непрерывно потреблять логические слоты репликации. Если сегмент логической репликации больше не используется, немедленно удалите его.

Столбец "Active" в представлении pg_replication_slots будет указывать, есть ли потребитель, подключенный к слоту.
```SQL
SELECT * FROM pg_replication_slots;
```

[Настройте оповещения](howto-alert-on-metric.md) в *используемом хранилище* и *максимальную задержку* в метриках реплик, чтобы уведомить вас о том, что значения увеличиваются после обычных пороговых значений. 

> [!IMPORTANT]
> Необходимо удалить неиспользуемые слоты репликации. Если этого не сделать, это может привести к недоступности сервера.

## <a name="how-to-drop-a-slot"></a>Как удалить слот
Если вы не используете слот репликации, удалите его.

Удаление слота репликации с именем `test_slot` с помощью SQL:
```SQL
SELECT pg_drop_replication_slot('test_slot');
```

> [!IMPORTANT]
> Если вы перестаете использовать логическое декодирование, измените Azure. replication_support `replica` обратно `off`на или. Сведения о WAL, сохраняемые `logical` с помощью, являются более подробными и должны быть отключены, если не используется логическое декодирование. 

 
## <a name="next-steps"></a>Дальнейшие шаги

* Дополнительные [сведения о логическом декодировании](https://www.postgresql.org/docs/current/logicaldecoding-explanation.html)см. в документации по postgres.
* Если у вас возникли вопросы о логическом декодировании, обратитесь к [нашей команде](mailto:AskAzureDBforPostgreSQL@service.microsoft.com) .
* Дополнительные сведения о [репликах чтения](concepts-read-replicas.md).

