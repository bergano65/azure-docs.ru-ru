---
title: Процедуры хранения управления - База данных Azure для MariaDB
description: Узнайте, какие сохраненные процедуры в базе данных Azure для MariaDB полезны для настройки репликации данных, настройки часового пояса и запросов об убийстве.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: 2f6d1e20db64cb0c2a64771ea26b971b22031fd9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79529996"
---
# <a name="azure-database-for-mariadb-management-stored-procedures"></a>База данных Azure для управления MariaDB хранит сярвые процедуры

Сохраненные процедуры доступны в базе данных Azure для серверов MariaDB, чтобы помочь управлять сервером MariaDB. Это включает в себя управление соединениями, запросами сервера и настройку репликации data-in.  

## <a name="data-in-replication-stored-procedures"></a>Процедуры репликации данных

Репликация входных данных позволяет синхронизировать данные сервера MariaDB, работающего локально, на виртуальных машинах или в службах баз данных, размещенных другими облачными поставщиками, со службой Базы данных Azure для MariaDB.

Следующие хранимые процедуры используются для установки или удаления репликации входных данных между главным экземпляром и репликой.

|**Имя хранимой процедуры**|**Параметры ввода**|**Выходные параметры**|**Примечание об использовании**|
|-----|-----|-----|-----|
|*mysql.az_replication_change_master*|master_host<br/>master_user<br/>master_password<br/>master_port<br/>master_log_file<br/>master_log_pos<br/>master_ssl_ca|Недоступно|Для передачи данных в режиме SSL передайте в контексте сертификата CA в master_ssl_ca параметр. </br><br>Для передачи данных без использования SSL передайте пустую строку в параметр master_ssl_ca.|
|*mysql.az_replication _start*|Недоступно|Недоступно|Запускает георепликацию.|
|*mysql.az_replication _stop*|Недоступно|Недоступно|Останавливает георепликацию.|
|*mysql.az_replication _remove_master*|Недоступно|Недоступно|Удаляет отношение репликации между главным экземпляром и репликой.|
|*mysql.az_replication_skip_counter*|Недоступно|Недоступно|Пропускает одну ошибку репликации.|

Чтобы настроить репликацию data-in между мастером и репликой в базе данных Azure для MariaDB, обратитесь к [тому, как настроить репликацию Data-in.](howto-data-in-replication.md)

## <a name="other-stored-procedures"></a>Другие хранимые процедуры

Следующие сохраненные процедуры доступны в базе данных Azure для Управления сервером MariaDB.

|**Имя хранимой процедуры**|**Параметры ввода**|**Выходные параметры**|**Примечание об использовании**|
|-----|-----|-----|-----|
|*mysql.az_kill*|processlist_id|Недоступно|Эквивалент [`KILL CONNECTION`](https://dev.mysql.com/doc/refman/8.0/en/kill.html) команды. Прекратит соединение, связанное с предоставленным processlist_id после прекращения выполнения любого оператора.|
|*mysql.az_kill_query*|processlist_id|Недоступно|Эквивалент [`KILL QUERY`](https://dev.mysql.com/doc/refman/8.0/en/kill.html) команды. Прекратит выписку, которая в настоящее время завершается соединением. Оставляет само соединение живым.|
|*mysql.az_load_timezone*|Недоступно|Недоступно|Загружает таблицы `time_zone` часового пояса, чтобы параметр был установлен на именованные значения (напротив. "США/Тихий океан").|

## <a name="next-steps"></a>Дальнейшие действия
- Узнайте, как настроить [репликацию data-in](howto-data-in-replication.md)
- Узнайте, как использовать [таблицы часового пояса](howto-server-parameters.md#working-with-the-time-zone-parameter)