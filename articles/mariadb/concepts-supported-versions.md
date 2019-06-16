---
title: Поддерживаемые версии в службе "База данных Azure для MariaDB"
description: Описываются поддерживаемые версии в базе данных Azure для MariaDB.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 06/11/2019
ms.openlocfilehash: 760cb39ea1f3980faba348c7aa1de68a66b20a8d
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/13/2019
ms.locfileid: "67065725"
---
# <a name="supported-azure-database-for-mariadb-server-versions"></a>Поддерживаемые версии сервера базы данных Azure для MariaDB

База данных Azure для MariaDB была разработана с открытым исходным кодом [сервера MariaDB](https://downloads.mariadb.org/) с использованием подсистемы InnoDB. Сейчас база данных Azure для MySQL поддерживает перечисленные ниже версии:

## <a name="mariadb-version-102"></a>MariaDB версии 10.2

Ссылаться на [MariaDB документации](https://mariadb.com/kb/en/library/mariadb-10223-release-notes/) Дополнительные сведения об улучшениях и исправлениях в MariaDB 10.2.23.

## <a name="mariadb-version-103"></a>Версия MariaDB 10.3

Ссылаться на [MariaDB документации](https://mariadb.com/kb/en/library/mariadb-10314-release-notes/) Дополнительные сведения об улучшениях и исправлениях в MariaDB 10.3.14.

> [!NOTE]
> В службе для перенаправления подключений к экземплярам сервера используется шлюз. После установки подключения в клиенте MySQL отображается версия MariaDB, установленная в шлюзе, а не фактическая версия, которая работает на вашем экземпляре сервера MariaDB. Чтобы определить версию MariaDB на экземпляре сервера, выполните команду `SELECT VERSION();` в командной строке MySQL.

## <a name="managing-updates-and-upgrades"></a>Управление обновлениями

Служба автоматически управляет установкой исправлений для обновления дополнительного номера версии.

## <a name="next-steps"></a>Дальнейшие действия

- Сведения о квотах и ограничениях для конкретных ресурсов с учетом вашего **уровня служб** представлены в статье [Параметры и производительность базы данных Azure для PostgreSQL: возможности, доступные в каждой ценовой категории](./concepts-pricing-tiers.md).
