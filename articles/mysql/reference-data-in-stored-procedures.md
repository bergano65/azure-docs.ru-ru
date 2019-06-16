---
title: Хранимые процедуры репликации входных данных базы данных Azure для MySQL
description: В этой статье описаны все хранимые процедуры, используемые для репликации входных данных.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 08/31/2018
ms.openlocfilehash: a3c88953eea95871529e8ab257f52b694db443a9
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/13/2019
ms.locfileid: "61244316"
---
# <a name="azure-database-for-mysql-data-in-replication-stored-procedures"></a>Хранимые процедуры репликации входных данных базы данных Azure для MySQL

Репликация входных данных позволяет синхронизировать данные работающего локально сервера MySQL в виртуальных машинах или службах баз данных, размещенных другими облачными поставщиками, в службу базы данных Azure для MySQL.

Следующие хранимые процедуры используются для установки или удаления репликации входных данных между главным экземпляром и репликой.

|**Имя хранимой процедуры**|**Входные параметры**|**Выходные параметры**|**Примечание об использовании**|
|-----|-----|-----|-----|
|*mysql.az_replication_change_master*|master_host<br/>master_user<br/>master_password<br/>master_port<br/>master_log_file<br/>master_log_pos<br/>master_ssl_ca|Н/Д|Для передачи данных в режиме SSL передайте контекст сертификата ЦС в параметр master_ssl_ca. </br><br>Для передачи данных без использования SSL передайте пустую строку в параметр master_ssl_ca.|
|*mysql.az_replication _start*|Н/Д|Н/Д|Запускает георепликацию.|
|*mysql.az_replication _stop*|Н/Д|Н/Д|Останавливает георепликацию.|
|*mysql.az_replication _remove_master*|Н/Д|Н/Д|Удаляет отношение репликации между главным экземпляром и репликой.|
|*mysql.az_replication_skip_counter*|Н/Д|Н/Д|Пропускает одну ошибку репликации.|

Чтобы настроить репликацию входных данных между главным экземпляром и репликой базы данных Azure для MySQL, ознакомьтесь со статьей [Настройка Базы данных Azure для MySQL для репликации входных данных](howto-data-in-replication.md).
