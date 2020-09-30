---
title: Хранимые процедуры управления — база данных Azure для MariaDB
description: Узнайте, какие хранимые процедуры в базе данных Azure для MariaDB полезны для настройки репликации данных, настройки часового пояса и завершения запросов.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: 453cb28b3053ee2fd2706a5537dc71b6cdca4174
ms.sourcegitcommit: f5580dd1d1799de15646e195f0120b9f9255617b
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/29/2020
ms.locfileid: "91539849"
---
# <a name="azure-database-for-mariadb-management-stored-procedures"></a>Хранимые процедуры управления базой данных Azure для MariaDB

Хранимые процедуры доступны на серверах базы данных Azure для MariaDB, чтобы помочь в управлении сервером MariaDB. Сюда входит управление подключениями сервера, запросами и настройка Репликация входных данных.  

## <a name="data-in-replication-stored-procedures"></a>Репликация входных данных хранимых процедур

Репликация входных данных позволяет синхронизировать данные сервера MariaDB, работающего локально, на виртуальных машинах или в службах баз данных, размещенных другими облачными поставщиками, со службой Базы данных Azure для MariaDB.

Следующие хранимые процедуры используются для установки или удаления Репликация входных данных между источником и репликой.

|**Имя хранимой процедуры**|**Входные параметры**|**Выходные параметры**|**Примечание об использовании**|
|-----|-----|-----|-----|
|*mysql.az_replication_change_master*|master_host<br/>master_user<br/>master_password<br/>master_port<br/>master_log_file<br/>master_log_pos<br/>master_ssl_ca|Н/Д|Чтобы передать данные в режиме SSL, передайте контекст сертификата ЦС в параметр master_ssl_ca. </br><br>Для передачи данных без использования SSL передайте пустую строку в параметр master_ssl_ca.|
|*mysql.az_replication _start*|Н/Д|Н/Д|Запускает георепликацию.|
|*mysql.az_replication _stop*|Н/Д|Н/Д|Останавливает георепликацию.|
|*mysql.az_replication _remove_master*|Н/Д|Н/Д|Удаляет отношение репликации между источником и репликой.|
|*mysql.az_replication_skip_counter*|Н/Д|Н/Д|Пропускает одну ошибку репликации.|

Сведения о настройке Репликация входных данных между источником и репликой в базе данных Azure для MariaDB см. в разделе [настройка репликация входных данных](howto-data-in-replication.md).

## <a name="other-stored-procedures"></a>Другие хранимые процедуры

Для управления сервером доступны следующие хранимые процедуры в базе данных Azure для MariaDB.

|**Имя хранимой процедуры**|**Входные параметры**|**Выходные параметры**|**Примечание об использовании**|
|-----|-----|-----|-----|
|*MySQL. az_kill*|processlist_id|Н/Д|Эквивалентно [`KILL CONNECTION`](https://dev.mysql.com/doc/refman/8.0/en/kill.html) команде. Будет завершать подключение, связанное с указанным processlist_id после завершения любой инструкции, когда соединение выполняется.|
|*MySQL. az_kill_query*|processlist_id|Н/Д|Эквивалентно [`KILL QUERY`](https://dev.mysql.com/doc/refman/8.0/en/kill.html) команде. Прекратит выполнение инструкции, в которой выполняется подключение. Оставляет подключение активным.|
|*MySQL. az_load_timezone*|Н/Д|Н/Д|Загружает таблицы часовых поясов, чтобы разрешить `time_zone` Присвоение параметру именованных значений (например, "США/тихоокеанское").|

## <a name="next-steps"></a>Дальнейшие действия
- Узнайте, как настроить [репликация входных данных](howto-data-in-replication.md)
- Сведения об использовании [таблиц часовых поясов](howto-server-parameters.md#working-with-the-time-zone-parameter)