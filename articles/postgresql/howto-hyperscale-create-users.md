---
title: Создание пользователей - Hyperscale (Citus) - База данных Azure для PostgreS'L
description: В этой статье описывается, как можно создавать новые учетные записи пользователей для взаимодействия с базой данных Azure для PostgreS-L - Hyperscale (Citus).
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.topic: conceptual
ms.date: 1/8/2019
ms.openlocfilehash: 674fd4372bdf7c3782d18aaf04b48eb0067a9b2e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77484933"
---
# <a name="create-users-in-azure-database-for-postgresql---hyperscale-citus"></a>Создание пользователей в базе данных Azure для PostgreS-L - Hyperscale (Citus)

> [!NOTE]
> Термин "пользователи" относится к пользователям в группе серверов Hyperscale (Citus). Вместо этого, чтобы узнать о пользователях подписки Azure и их привилегиях, посетите [статью управления доступом на основе роли Azure (RBAC)](../role-based-access-control/built-in-roles.md) или просмотрите, [как настроить роли.](../role-based-access-control/custom-roles.md)

## <a name="the-server-admin-account"></a>Учетная запись администратора сервера

В движке PostgreS'L [используются роли](https://www.postgresql.org/docs/current/sql-createrole.html) для управления доступом к объектам базы данных, а недавно созданная группа серверов Hyperscale (Citus) поставляется с несколькими ролями, заранее определенными:

* Роли [PostgreS'L по умолчанию](https://www.postgresql.org/docs/current/default-roles.html)
* `azure_pg_admin`
* `postgres`
* `citus`

Поскольку Hyperscale является управляемой службой PaaS, `postgres` только Microsoft может войти в систему с супер ролью пользователя. Для ограниченного административного доступа `citus` Hyperscale предоставляет эту роль.

Разрешения на `citus` роль:

* Прочитайте все переменные конфигурации, даже переменные, обычно видимые только суперпользователям.
* Прочитайте\_все\_ \* представления pg stat и используйте различные расширения, связанные со статистикой, даже представления или расширения, обычно видимые только суперпользователям.
* Выполняйте функции мониторинга, которые могут принимать блокировки ACCESS SHARE на столах, потенциально в течение длительного времени.
* [Создавайте расширения PostgreS'L](concepts-hyperscale-extensions.md) (потому `azure_pg_admin`что роль является членом).

Примечательно, что `citus` роль имеет некоторые ограничения:

* Не успомяно создать роли
* Не успомяно создать базы данных

## <a name="how-to-create-additional-user-roles"></a>Как создать дополнительные роли пользователя

Как уже `citus` упоминалось, учетная запись админа не имеет разрешения на создание дополнительных пользователей. Чтобы добавить пользователя, используйте интерфейс портала Azure.

1. Перейдите на страницу **Роли** для группы серверов Hyperscale и нажмите **кнопку ::**

   ![Страница ролей](media/howto-hyperscale-create-users/1-role-page.png)

2. Введите имя роли и пароль. Нажмите **Сохранить**.

   ![Добавление роли](media/howto-hyperscale-create-users/2-add-user-fields.png)

Пользователь будет создан на удее координатора группы серверов и распространяется на все рабочие узлы. Роли, созданные через портал `LOGIN` Azure, имеют атрибут, что означает, что они являются истинными пользователями, которые могут войти в базу данных.

## <a name="how-to-modify-privileges-for-user-role"></a>Как изменить привилегии для роли пользователя

Новые роли пользователей обычно используются для обеспечения доступа к базе данных с ограниченными привилегиями. Чтобы изменить пользовательские привилегии, используйте стандартные команды PostgreS'L, используя такой инструмент, как PgAdmin или psql. (См. [подключение с psql](quickstart-create-hyperscale-portal.md#connect-to-the-database-using-psql) в Hyperscale (Citus) quickstart.)

Например, чтобы `db_user` разрешить `mytable`читать, предоставить разрешение:

```sql
GRANT SELECT ON mytable TO db_user;
```

Hyperscale (Citus) распространяет одностоловые заявления GRANT по всему кластеру, применяя их на всех рабочих узлах. Однако GRANTs, которые являются общесистемными (например, для всех таблиц в схеме), должны быть запущены на каждом узеле даты.  Используйте `run_command_on_workers()` функцию помощника:

```sql
-- applies to the coordinator node
GRANT SELECT ON ALL TABLES IN SCHEMA public TO db_user;

-- make it apply to workers as well
SELECT run_command_on_workers(
  'GRANT SELECT ON ALL TABLES IN SCHEMA public TO db_user;'
);
```

## <a name="how-to-delete-a-user-role-or-change-their-password"></a>Как удалить роль пользователя или изменить его пароль

Чтобы обновить пользователя, посетите страницу **Роли** для вашей группы серверов Hyperscale и нажмите эллипсы **...** Эллипсы откроют меню для удаления пользователя или сбросить пароль.

   ![Отобразить роль](media/howto-hyperscale-create-users/edit-role.png)

Роль `citus` является привилегированной и не может быть удалена.

## <a name="next-steps"></a>Дальнейшие действия

Откройте брандмауэр для IP-адресов новых компьютеров пользователей, чтобы они могли подключиться: [Создайте и управляйте правилами гипермасштаба (Citus) с помощью портала Azure.](howto-hyperscale-manage-firewall-using-portal.md)

Для получения дополнительной информации об управлении учетной записью пользователей баз данных см.

* [Роли и привилегии базы данных](https://www.postgresql.org/docs/current/static/user-manag.html)
* [ГРАНТ Синтаксис](https://www.postgresql.org/docs/current/static/sql-grant.html)
* [Привилегии](https://www.postgresql.org/docs/current/static/ddl-priv.html)
