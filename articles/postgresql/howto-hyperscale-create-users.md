---
title: Создание пользователей в базе данных Azure для PostgreSQL — масштабирование (Цитус)
description: В этой статье описывается создание новых учетных записей пользователей для взаимодействия с базой данных Azure для PostgreSQL-Scale (Цитус).
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 7187135b29f0a9a790c032330c73bcb1ae27229b
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/04/2019
ms.locfileid: "73515945"
---
# <a name="create-users-in-azure-database-for-postgresql---hyperscale-citus"></a>Создание пользователей в базе данных Azure для PostgreSQL — масштабирование (Цитус)

В этой статье описывается, как создавать пользователей в группе серверов с горизонтальным масштабированием (Цитус). Чтобы узнать больше о пользователях подписки Azure и их привилегиях, ознакомьтесь со [статьей управление доступом на основе ролей (RBAC) в Azure](../role-based-access-control/built-in-roles.md) или Узнайте, [как настроить роли](../role-based-access-control/custom-roles.md).

## <a name="the-server-admin-account"></a>Учетная запись администратора сервера

Недавно созданная группа серверов Цитус с горизонтальным масштабированием имеет несколько предварительно определенных ролей:

* [Роли PostgreSQL по умолчанию](https://www.postgresql.org/docs/current/default-roles.html)
* *azure_pg_admin*
* *postgres*
* *Цитус*

Пользователь с правами администратора сервера *Цитус*является членом роли *azure_pg_admin* .
Однако он не является частью роли *postgres* (супер User).  Так как в качестве масштаба используется управляемая служба PaaS, только корпорация Майкрософт является частью роли суперпользователя.

Подсистема PostgreSQL использует права для управления доступом к объектам базы данных, как описано в [документации по продукту PostgreSQL](https://www.postgresql.org/docs/current/static/sql-createrole.html).
В службе "База данных Azure для PostgreSQL" пользователю-администратору сервера предоставляются следующие права: LOGIN, NOSUPERUSER, INHERIT, CREATEDB, CREATEROLE, NOREPLICATION

## <a name="how-to-create-additional-users"></a>Создание дополнительных пользователей

Учетная запись администратора *Цитус* не имеет разрешения на создание дополнительных пользователей. Чтобы добавить пользователя, используйте вместо этого портал Azure.

1. Перейдите на страницу **роли** для группы масштабируемых серверов и нажмите кнопку **+ Добавить**:

   ![Страница «роли»](media/howto-hyperscale-create-users/1-role-page.png)

2. Введите имя и пароль роли. Щелкните **Сохранить**.

   ![Добавление роли](media/howto-hyperscale-create-users/2-add-user-fields.png)

Пользователь будет создан на узле координатора группы серверов и распространен на все рабочие узлы.

## <a name="how-to-delete-a-user-or-change-their-password"></a>Как удалить пользователя или изменить пароль

Перейдите на страницу **роли** для группы серверов с горизонтальным масштабированием и нажмите кнопку с многоточием **...** рядом с пользователем. С многоточием откроется меню для удаления пользователя или сброса пароля.

   ![Изменение роли](media/howto-hyperscale-create-users/edit-role.png)

Роль *Цитус* является привилегированной и не может быть удалена.

## <a name="how-to-modify-privileges-for-role"></a>Изменение привилегий для роли

Новые роли обычно используются для предоставления доступа к базе данных с ограниченными правами. Чтобы изменить привилегии пользователя, используйте стандартные команды PostgreSQL, используя такие средства, как PgAdmin или psql. (См. раздел " [подключение с помощью psql](quickstart-create-hyperscale-portal.md#connect-to-the-database-using-psql) " в кратком руководстве по масштабированию (Цитус).)

Например, чтобы разрешить *DB_USER* чтение *MyTable*, предоставьте разрешение:

```sql
GRANT SELECT ON mytable TO db_user;
```

Масштабирование (Цитус) распространяет инструкции GRANT с одной таблицы на весь кластер, применяя их ко всем рабочим узлам. Однако разрешения, доступные для всей системы (например, для всех таблиц в схеме), должны выполняться на каждом узле даты.  Используйте вспомогательную функцию *run_command_on_workers ()* :

```sql
-- applies to the coordinator node
GRANT SELECT ON ALL TABLES IN SCHEMA public TO db_user;

-- make it apply to workers as well
SELECT run_command_on_workers(
  'GRANT SELECT ON ALL TABLES IN SCHEMA public TO db_user;'
);
```

## <a name="next-steps"></a>Дальнейшие действия

Откройте брандмауэр для IP-адресов компьютеров новых пользователей, чтобы разрешить им подключение: [Создание правил брандмауэра Цитус и управление ими с помощью портал Azure](howto-hyperscale-manage-firewall-using-portal.md).

Дополнительные сведения об управлении учетными записями пользователей баз данных см. в разделе Документация по продукту PostgreSQL:

* [Роли и привилегии базы данных](https://www.postgresql.org/docs/current/static/user-manag.html)
* [Синтаксис предоставления](https://www.postgresql.org/docs/current/static/sql-grant.html)
* [Права](https://www.postgresql.org/docs/current/static/ddl-priv.html)
