---
title: Поддерживаемые версии в службе "База данных Azure для MariaDB"
description: Описываются поддерживаемые версии в базе данных Azure для MariaDB.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 09/12/2019
ms.openlocfilehash: e5d1dbc8c212d4cdefb12fb740a454324d3adfa1
ms.sourcegitcommit: dd69b3cda2d722b7aecce5b9bd3eb9b7fbf9dc0a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/12/2019
ms.locfileid: "70962923"
---
# <a name="supported-azure-database-for-mariadb-server-versions"></a>Поддерживаемые версии сервера базы данных Azure для MariaDB

База данных Azure для MariaDB была разработана с [сервера MariaDB](https://downloads.mariadb.org/)с открытым исходным кодом с помощью подсистемы InnoDB. 

MariaDB использует схему именования X. Y. Z. X — основной номер версии, Y — дополнительный номер версии, а Z — версия исправления.

> [!NOTE]
> В службе для перенаправления подключений к экземплярам сервера используется шлюз. После установки подключения в клиенте MySQL отображается версия MariaDB, установленная в шлюзе, а не фактическая версия, которая работает на вашем экземпляре сервера MariaDB. Чтобы определить версию экземпляра сервера MariaDB, используйте `SELECT VERSION();` команду.

Сейчас база данных Azure для MySQL поддерживает перечисленные ниже версии:

## <a name="mariadb-version-102"></a>MariaDB версии 10,2

Версия исправления: 10.2.25

Дополнительные сведения об улучшениях и исправлениях в этой версии см. в [документации по MariaDB](https://mariadb.com/kb/en/library/mariadb-10225-release-notes/) .

## <a name="mariadb-version-103"></a>MariaDB версии 10,3

Версия исправления: 10.3.16

Дополнительные сведения об улучшениях и исправлениях в этой версии см. в [документации по MariaDB](https://mariadb.com/kb/en/library/mariadb-10316-release-notes/) .

## <a name="managing-updates-and-upgrades"></a>Управление обновлениями
Служба автоматически управляет обновлениями для обновлений исправлений. Например, 10.2.21 в 10.2.23.  

Сейчас обновления основного и дополнительного номера версии не поддерживаются. Например, обновление с MariaDB 10,2 до MariaDB 10,3 не поддерживается. Если вы хотите выполнить обновление с 10,2 до 10,3, сделайте [дамп и восстановите](./howto-migrate-dump-restore.md) его на сервере, который был создан с использованием новой версии модуля.

## <a name="next-steps"></a>Следующие шаги

- Сведения о квотах и ограничениях для конкретных ресурсов с учетом вашего **уровня служб** представлены в статье [Параметры и производительность базы данных Azure для PostgreSQL: возможности, доступные в каждой ценовой категории](./concepts-pricing-tiers.md).
