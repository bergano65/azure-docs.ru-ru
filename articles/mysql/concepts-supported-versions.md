---
title: Поддерживаемые версии в службе "База данных Azure для MySQL"
description: Описываются поддерживаемые версии в базе данных Azure для MySQL.
services: mysql
author: ajlam
ms.author: andrela
manager: kfile
editor: jasonwhowell
ms.service: mysql
ms.topic: article
ms.date: 10/10/2018
ms.openlocfilehash: f2a9348e267ad6a5929fda64cc08dcd837243e71
ms.sourcegitcommit: 4047b262cf2a1441a7ae82f8ac7a80ec148c40c4
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/11/2018
ms.locfileid: "49093346"
---
# <a name="supported-azure-database-for-mysql-server-versions"></a>Поддерживаемые версии сервера базы данных Azure для MySQL
Служба "База данных Azure для MySQL" разработана на базе [MySQL Community Edition](https://www.mysql.com/products/community/) с использованием подсистемы InnoDB. Сейчас база данных Azure для MySQL поддерживает перечисленные ниже версии.

## <a name="mysql-version-5639"></a>MySQL версии 5.6.39
Сведения об улучшениях и исправлениях в MySQL 5.6.39 см. в [документации](https://dev.mysql.com/doc/relnotes/mysql/5.6/en/news-5-6-39.html) по MySQL.

## <a name="mysql-version-5721"></a>MySQL версии 5.7.21
Сведения об улучшениях и исправлениях в MySQL 5.7.21 см. в [документации](https://dev.mysql.com/doc/relnotes/mysql/5.7/en/news-5-7-21.html) по MySQL.

> [!NOTE]
> В службе для перенаправления подключений к экземплярам сервера используется шлюз. После установки подключения в клиенте MySQL отображается версия MySQL, установленная в шлюзе, а не фактическая версия, которая работает на вашем экземпляре сервера MySQL. Чтобы определить версию MySQL на экземпляре сервера, выполните команду `SELECT VERSION();` в командной строке MySQL.

## <a name="managing-updates-and-upgrades"></a>Управление обновлениями
Служба автоматически управляет установкой исправлений для обновления версии исправлений. Сейчас обновление до дополнительной версии не поддерживается. Например, обновление с версии MySQL 5.6 до MySQL 5.7 не поддерживается. Чтобы выполнить обновление до следующей дополнительной версии, создайте [дамп и восстановите его](./concepts-migrate-dump-restore.md) на сервере, который был создан с новой версией ядра.

## <a name="next-steps"></a>Дополнительная информация

Сведения о квотах и ограничениях для конкретных ресурсов с учетом вашего **уровня служб** представлены в статье [Уровни служб в базе данных Azure для MySQL](./concepts-pricing-tiers.md).
