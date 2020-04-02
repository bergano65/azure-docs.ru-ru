---
title: Логическая расшифровка - База данных Azure для PostgreS'L - Единый сервер
description: Описывает логическую расшифровку и wal2json для захвата данных из изменений в базе данных Azure для PostgreS-L - Единый сервер
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 03/31/2020
ms.openlocfilehash: 1213b38f2b67e8fed179cfda4308943808893e1b
ms.sourcegitcommit: b0ff9c9d760a0426fd1226b909ab943e13ade330
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/01/2020
ms.locfileid: "80522151"
---
# <a name="logical-decoding"></a>Логическая расшифровка
 
[Логическая расшифровка в PostgreS'L](https://www.postgresql.org/docs/current/logicaldecoding.html) позволяет передавать изменения данных внешним потребителям. Логическая расшифровка широко используется для потоковой передачи событий и изменения сценариев захвата данных.

Логическая расшифровка использует выходный плагин для преобразования журнала Postgres заранее (WAL) в читаемый формат. База данных Azure для PostgreS'L предоставляет два выходных плагина: [test_decoding](https://www.postgresql.org/docs/current/test-decoding.html) и [wal2json](https://github.com/eulerto/wal2json).
 

> [!NOTE]
> Логическая расшифровка находится в открытом доступе на базе данных Azure для PostgreS-L - Single Server.


## <a name="set-up-your-server"></a>Настройка сервера
Чтобы начать использовать логическую расшифровку, включите сервер для сохранения и потоковой передачи WAL. 

1. Установите azure.replication_support с `logical` помощью Azure CLI. 
   ```
   az postgres server configuration set --resource-group mygroup --server-name myserver --name azure.replication_support --value logical
   ```

   > [!NOTE]
   > Если вы используете прочитанные реплики, `logical` azure.replication_support установить также позволяет реплики для запуска. Если вы прекратите использовать логическую расшифровку, измените настройку обратно в `replica`. 


2. Чтобы изменения вступили в силу, перезагрузите сервер.
   ```
   az postgres server restart --resource-group mygroup --name myserver
   ```

## <a name="start-logical-decoding"></a>Начало логической расшифровки

Логическая расшифровка может быть использована с помощью потокового протокола или интерфейса S'L. Оба метода используют [слоты репликации.](https://www.postgresql.org/docs/current/logicaldecoding-explanation.html#LOGICALDECODING-REPLICATION-SLOTS) Слот представляет собой поток изменений из одной базы данных.

Использование слота репликации требует привилегий репликации Postgres. В настоящее время привилегия репликации доступна только для пользователя администратора сервера. 

### <a name="streaming-protocol"></a>Протокол потоковой передачи
Потребление изменений с помощью потокового протокола часто предпочтительнее. Вы можете создать свой собственный потребитель / разъем, или использовать инструмент, как [Debezium](https://debezium.io/). 

Посетите документацию wal2json для [примера, используя протокол потоковой передачи с pg_recvlogical.](https://github.com/eulerto/wal2json#pg_recvlogical)


### <a name="sql-interface"></a>Интерфейс СЗЛ
В приведенном ниже примере мы используем интерфейс S'L с плагином wal2json.
 
1. Создайте слот.
   ```SQL
   SELECT * FROM pg_create_logical_replication_slot('test_slot', 'wal2json');
   ```
 
2. Выпуск команд S'L. Пример:
   ```SQL
   CREATE TABLE a_table (
      id varchar(40) NOT NULL,
      item varchar(40),
      PRIMARY KEY (id)
   );
   
   INSERT INTO a_table (id, item) VALUES ('id1', 'item1');
   DELETE FROM a_table WHERE id='id1';
   ```

3. Потребляйте изменения.
   ```SQL
   SELECT data FROM pg_logical_slot_get_changes('test_slot', NULL, NULL, 'pretty-print', '1');
   ```

   Выход будет выглядеть следующим:
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

4. Бросьте слот, как только вы закончите использовать его.
   ```SQL
   SELECT pg_drop_replication_slot('test_slot'); 
   ```


## <a name="monitoring-slots"></a>Слоты мониторинга

Вы должны контролировать логическое декодирование. Любой неиспользованный слот репликации должен быть удален. Слоты удерживают журналы Postgres WAL и соответствующие системные каталоги до тех пор, пока потребитель не зачитает изменения. Если ваш потребитель не удается или не был должным образом настроен, непотребленные журналы будут накапливаться и заполнить ваше хранилище. Кроме того, непотребленных журналов увеличивают риск обертывания идентификатора транзакций. Обе ситуации могут привести к тому, что сервер станет недоступен. Поэтому крайне важно, чтобы логические слоты репликации потреблялись непрерывно. Если слот репликации логических больше не используется, немедленно отбросьте его.

В "активном" столбце в представлении pg_replication_slots будет указано, есть ли потребитель, подключенный к слоту.
```SQL
SELECT * FROM pg_replication_slots;
```

[Установите оповещения](howto-alert-on-metric.md) о *используемом хранилище* и *Max отстают от* метрик реплик, чтобы уведомить вас о том, что значения увеличиваются после обычных пороговых значений. 

> [!IMPORTANT]
> Вы должны отказаться от неиспользованных слотов репликации. Невыполнение этого правила может привести к недоступности сервера.

## <a name="how-to-drop-a-slot"></a>Как упасть слот
Если вы не активно потребляете слот репликации, вы должны отказаться от него.

Чтобы упасть слот репликации, который называется `test_slot` с помощью S'L:
```SQL
SELECT pg_drop_replication_slot('test_slot');
```

> [!IMPORTANT]
> Если вы прекратите использовать логическую расшифровку, измените azure.replication_support обратно `replica` или `off`. Сохраненные данные `logical` WAL более многословны и должны быть отключены, когда не используется логическая расшифровка. 

 
## <a name="next-steps"></a>Дальнейшие действия

* Посетите документацию Postgres, чтобы [узнать больше о логической расшифровке.](https://www.postgresql.org/docs/current/logicaldecoding-explanation.html)
* Опрокитесь на [нашу команду,](mailto:AskAzureDBforPostgreSQL@service.microsoft.com) если у вас есть вопросы о логической расшифровке.
* Подробнее о [прочитайте реплики](concepts-read-replicas.md).

