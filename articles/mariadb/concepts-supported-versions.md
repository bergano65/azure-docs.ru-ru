---
title: Поддерживаемые версии в службе "База данных Azure для MariaDB"
description: Описываются поддерживаемые версии в базе данных Azure для MariaDB.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: 69330e9d5a05fbcc892889f70a04f5eb4a4a2fb9
ms.sourcegitcommit: 71ee622bdba6e24db4d7ce92107b1ef1a4fa2600
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/17/2018
ms.locfileid: "53541427"
---
# <a name="supported-azure-database-for-mariadb-server-versions"></a>Поддерживаемые версии сервера базы данных Azure для MariaDB
База данных Azure для MariaDB была разработана с открытым исходным кодом [сервера MariaDB](https://downloads.mariadb.org/) с использованием подсистемы InnoDB. Сейчас база данных Azure для MySQL поддерживает перечисленные ниже версии:

## <a name="mariadb-version-10217"></a>Версия MariaDB 10.2.17
Сведения об улучшениях и исправлениях в MariaDB 10.2.17 см. в [документации MariaDB](https://downloads.mariadb.org/mariadb/10.2.17/).

> [!NOTE]
> В службе для перенаправления подключений к экземплярам сервера используется шлюз. После установки подключения в клиенте MySQL отображается версия MariaDB, установленная в шлюзе, а не фактическая версия, которая работает на вашем экземпляре сервера MariaDB. Чтобы определить версию MariaDB на экземпляре сервера, выполните команду `SELECT VERSION();` в командной строке MySQL.

## <a name="managing-updates-and-upgrades"></a>Управление обновлениями
Служба автоматически управляет установкой исправлений для обновления дополнительного номера версии.

## <a name="next-steps"></a>Дополнительная информация
Сведения о квотах и ограничениях для конкретных ресурсов с учетом вашего **уровня служб** представлены в статье [Уровни служб в базе данных Azure для MySQL](./concepts-pricing-tiers.md).
