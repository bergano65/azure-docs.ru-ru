---
title: Управление доступом к рабочим областям, данным и конвейерам
description: Узнайте, как управлять доступом к рабочим областям, данным и конвейерам в рабочей области Azure Synapse Analytics (предварительной версии).
services: synapse-analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: ''
ms.date: 04/15/2020
ms.author: v-stazar
ms.reviewer: jrasnick
ms.openlocfilehash: 89d2105ab080309639c4341072c3f5f36608dfce
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81421108"
---
# <a name="manage-access-to-workspaces-data-and-pipelines"></a>Управление доступом к рабочим областям, данным и конвейерам

Узнайте, как управлять доступом к рабочим областям, данным и конвейерам в рабочей области Azure Synapse Analytics (предварительной версии).

> [!NOTE]
> Общедоступная версия RBAC будет улучшена благодаря появлению ролей RBAC Azure для Synapse.

## <a name="access-control-for-workspace"></a>Контроль доступа к рабочей области

Для рабочего развертывания в рабочей области Azure Synapse мы рекомендуем организовать среду, чтобы упростить подготовку пользователей и администраторов.

> [!NOTE]
> Этот подход состоит в том, чтобы создать несколько групп безопасности, а затем настроить рабочую область для согласованного использования. После настройки групп администратору нужно только управлять членством в группах безопасности.

### <a name="step-1-set-up-security-groups-with-names-following-this-pattern"></a>Шаг 1. Настройка групп безопасности с именами по шаблону

1. Создайте группу безопасности с именем `Synapse_WORKSPACENAME_Users`.
2. Создайте группу безопасности с именем `Synapse_WORKSPACENAME_Admins`.
3. Добавлен параметр `Synapse_WORKSPACENAME_Admins` для `ProjectSynapse_WORKSPACENAME_Users`.

### <a name="step-2-prepare-the-default-adls-gen2-account"></a>Шаг 2. Подготовка учетной записи ADLS 2-го поколения по умолчанию

После подготовки рабочей области необходимо выбрать учетную запись ADLS 2-го поколения и контейнер файловой системы для используемой рабочей области.

1. Откройте [портал Azure](https://portal.azure.com).
2. Перейдите к учетной записи ADLS 2-го поколения.
3. Перейдите к контейнеру (файловой системы), выбранному для рабочей области Azure Synapse.
4. Щелкните **Управление доступом (IAM)** .
5. Назначьте указанные ниже роли:
   1. роль **Читатель** — `Synapse_WORKSPACENAME_Users`;
   2. роль **Владелец данных BLOB-объектов хранилища** — `Synapse_WORKSPACENAME_Admins`;
   3. роль **Участник данных BLOB-объектов хранилища** — `Synapse_WORKSPACENAME_Users`;
   4. роль **Владелец данных BLOB-объектов хранилища** — `WORKSPACENAME`.
  
### <a name="step-3-configure-the-workspace-admin-list"></a>Шаг 3. Настройка списка администраторов рабочей области

1. Перейдите к [**пользовательскому веб-интерфейсу Azure Synapse**](https://web.azuresynapse.net).
2. Последовательно выберите **Управление**  > **Безопасность** > **Управление доступом**.
3. Щелкните **Добавить администратора** и выберите `Synapse_WORKSPACENAME_Admins`.

### <a name="step-4-configure-sql-admin-access-for-the-workspace"></a>Шаг 4. Настройка доступа администратора SQL для рабочей области

1. Перейдите на [портал Azure](https://portal.azure.com).
2. Перейдите к рабочей области.
3. Последовательно выберите **Параметры** > **Администратор Active Directory**.
4. Щелкните **Задать администратора**.
5. Выберите `Synapse_WORKSPACENAME_Admins`
6. Щелкните **Выбрать**.
7. Нажмите кнопку **Сохранить**.

### <a name="step-5-add-and-remove-users-and-admins-to-security-groups"></a>Шаг 5. Добавление и удаление пользователей и администраторов в группах безопасности

1. Добавьте пользователей, которым требуется административный доступ в `Synapse_WORKSPACENAME_Admins`.
2. Добавьте всех остальных пользователей в `Synapse_WORKSPACENAME_Users`.

## <a name="access-control-to-data"></a>Управление доступом к данным

Управление доступом к базовым данным разделяется на три части:

- доступ плоскости данных к учетной записи хранения (уже настроенной ранее на шаге 2);
- доступ плоскости данных к Базам данных SQL (как для пулов SQL, так и для SQL по запросу);
- создание учетных данных для баз данных SQL по запросу через учетную запись хранения.

## <a name="access-control-to-sql-databases"></a>Управление доступом к Базам данных SQL

> [!TIP]
> Необходимо выполнить следующие шаги для **каждой** базы данных SQL, чтобы предоставить пользователю доступ ко всем базам данных SQL.

### <a name="sql-on-demand"></a>SQL по запросу

Чтобы предоставить пользователю доступ к **отдельной** базе данных SQL по запросу, выполните действия, описанные в примере ниже.

1. Создайте имя для входа:

    ```sql
    use master
    go
    CREATE LOGIN [John.Thomas@microsoft.com] FROM EXTERNAL PROVIDER;
    go
    ```

2. Создайте пользователя:

    ```sql
    use yourdb -- Use your DB name
    go
    CREATE USER john FROM LOGIN [John.Thomas@microsoft.com];
    ```

3. Добавьте пользователя к членам указанной роли:

    ```sql
    use yourdb -- Use your DB name
    go
    alter role db_owner Add member john -- Type USER name from step 2
    ```

### <a name="sql-pools"></a>Пулы SQL

Чтобы предоставить пользователю доступ к **отдельной** Базе данных SQL, выполните следующие действия:

1. Создайте пользователя в базе данных, выполнив следующую команду, предназначенную для требуемой базы данных в средстве выбора контекста (раскрывающемся списке для выбора баз данных):

    ```sql
    --Create user in SQL DB
    CREATE USER [<alias@domain.com>] FROM EXTERNAL PROVIDER;
    ```

2. Предоставьте пользователю роль для доступа к базе данных:

    ```sql
    --Create user in SQL DB
    EXEC sp_addrolemember 'db_owner', '<alias@domain.com>';
    ```

> [!IMPORTANT]
> *db_datareader* и *db_datawriter* могут работать в качестве разрешений на чтение и запись, если нежелательно предоставлять разрешение *db_owner*.
> Для чтения и записи напрямую из Spark в пул SQL или из него пользователю Spark требуется разрешение *db_owner*.

Создав пользователей, проверьте, может ли SQL по запросу запрашивать учетную запись хранения.

- Выполните следующую команду, предназначенную для базы данных **master** SQL по запросу:

    ```sql
    CREATE CREDENTIAL [https://<storageaccountname>.dfs.core.windows.net]
    WITH IDENTITY='User Identity';
    ```

## <a name="access-control-to-workspace-pipeline-runs"></a>Управление доступом к запускам конвейеров рабочей области

### <a name="workspace-managed-identity"></a>Управляемое удостоверение рабочей области

> [!IMPORTANT]
> Удостоверению рабочей области необходимо предоставить доступ к пулу SQL напрямую для успешного выполнения конвейеров, включающих в себя наборы данных или действия, которые ссылаются на пул SQL.

Выполните следующие команды в каждом пуле SQL, чтобы разрешить управляемому удостоверению рабочей области выполнять конвейеры в базе данных пула SQL:

```sql
--Create user in DB
CREATE USER [<workspacename>] FROM EXTERNAL PROVIDER;

--Granting permission to the identity
GRANT CONTROL ON DATABASE::<SQLpoolname> TO <workspacename>;
```

Это разрешение можно удалить, выполнив следующий скрипт в том же пуле SQL:

```sql
--Revoking permission to the identity
REVOKE CONTROL ON DATABASE::<SQLpoolname> TO <workspacename>;

--Deleting the user in the DB
DROP USER [<workspacename>];
```

## <a name="next-steps"></a>Дальнейшие действия

Общие сведения о доступе к SQL Synapse и управлении им см. в разделе [Управление доступом к SQL Synapse](../sql/access-control.md). Подробные сведения о субъектах базы данных см. в [этой статье](https://msdn.microsoft.com/library/ms181127.aspx). Дополнительные сведения о ролях базы данных см. [здесь](https://msdn.microsoft.com/library/ms189121.aspx).
