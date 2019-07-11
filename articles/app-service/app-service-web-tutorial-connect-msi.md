---
title: Безопасное подключение к Базе данных SQL с использованием управляемого удостоверения — Служба приложений Azure | Документация Майкрософт
description: Узнайте, как сделать подключение к базе данных более безопасным с использованием управляемого удостоверения, а также как применить это к другим службам Azure.
services: app-service\web
documentationcenter: dotnet
author: cephalin
manager: syntaxc4
editor: ''
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: tutorial
ms.date: 06/21/2019
ms.author: cephalin
ms.custom: mvc
ms.openlocfilehash: 31535642526c608ad0ae29e5c0e3c93368e184ad
ms.sourcegitcommit: 9b80d1e560b02f74d2237489fa1c6eb7eca5ee10
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/01/2019
ms.locfileid: "67481016"
---
# <a name="tutorial-secure-azure-sql-database-connection-from-app-service-using-a-managed-identity"></a>Руководство. Безопасное подключение к Базе данных SQL Azure из службы приложений с использованием управляемого удостоверения

[Служба приложений](overview.md) — это служба веб-размещения с самостоятельной установкой исправлений и высоким уровнем масштабируемости в Azure. Она также предоставляет [управляемое удостоверение](overview-managed-identity.md) для вашего приложения, которое является готовым решением для защиты доступа к [Базе данных SQL Azure](/azure/sql-database/) и другим службам Azure. Управляемые удостоверения в службе приложений делают ваше приложение более безопасным, устраняя из него секреты, такие как учетные данные в строках подключения. В этом руководстве вы добавите управляемое удостоверение в пример веб-приложения ASP.NET, которое вы создали при работе с [руководством по созданию приложения ASP.NET в Azure с подключением к базе данных SQL](app-service-web-tutorial-dotnet-sqldatabase.md) Когда вы закончите, ваш пример приложения будет безопасно подключаться к базе данных SQL без необходимости использования имен пользователей и паролей.

> [!NOTE]
> Этот сценарий сейчас поддерживается только в .NET Framework версии 4.7.2 и новее. [.NET Core 2.2](https://www.microsoft.com/net/download/dotnet-core/2.2) поддерживает этот сценарий, но без включения в стандартные образы в Службе приложений. 
>

Вы узнаете, как выполнять следующие задачи:

> [!div class="checklist"]
> * Включение управляемых удостоверений
> * Предоставление управляемому удостоверению доступа к Базе данных SQL
> * Настройте Entity Framework для использования аутентификации Azure AD с базой данных SQL
> * Подключитесь к базе данных SQL из Visual Studio с использованием аутентификации Azure AD

> [!NOTE]
>Аутентификация Azure AD _отличается_ от [интегрированной аутентификации Windows](/previous-versions/windows/it-pro/windows-server-2003/cc758557(v=ws.10)) в локальном экземпляре Active Directory (AD DS). Доменные службы Active Directory и Azure AD используют разные протоколы аутентификации. Дополнительные сведения см. на странице [Документация по доменным службам Azure AD](https://docs.microsoft.com/azure/active-directory-domain-services/).

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Предварительные требования

Эта статья является продолжением статьи [Руководство. Создание приложения ASP.NET в Azure с подключением к базе данных SQL](app-service-web-tutorial-dotnet-sqldatabase.md). Если вы еще этого не сделали, сначала ознакомьтесь с этим руководством. Кроме того, вы можете адаптировать шаги для своего собственного приложения ASP.NET с базой данных SQL.

Чтобы отладить приложение, используя базу данных SQL в качестве серверного компонента, убедитесь, что вы [разрешили клиентские подключения со своего компьютера](app-service-web-tutorial-dotnet-sqldatabase.md#allow-client-connection-from-your-computer).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="grant-azure-ad-user-access-to-database"></a>Предоставьте пользователю Azure AD доступ к базе данных

Сначала включите аутентификацию Azure AD в базе данных SQL, назначив пользователя Azure AD администратором Active Directory сервера базы данных SQL. Этот пользователь отличается от учетной записи Майкрософт, которую вы использовали для регистрации на подписку Azure. Это должен быть пользователь, которого вы создали, импортировали, синхронизировали или пригласили в Azure AD. Дополнительные сведения о разрешенных пользователях Azure AD см. в разделе [Возможности и ограничения Azure AD в базе данных SQL](../sql-database/sql-database-aad-authentication.md#azure-ad-features-and-limitations). 

Найдите ИД объекта пользователя Azure AD с помощью [`az ad user list`](/cli/azure/ad/user?view=azure-cli-latest#az-ad-user-list) и замените *\<user-principal-name>* . Результат сохраняется в переменной.

```azurecli-interactive
azureaduser=$(az ad user list --filter "userPrincipalName eq '<user-principal-name>'" --query [].objectId --output tsv)
```
> [!TIP]
> Чтобы просмотреть список всех имен участников-пользователей в Azure AD, запустите `az ad user list --query [].userPrincipalName`.
>

Добавьте этого пользователя Azure AD в качестве администратора Active Directory с помощью команды [`az sql server ad-admin create`](/cli/azure/sql/server/ad-admin?view=azure-cli-latest#az-sql-server-ad-admin-create) в Cloud Shell. В следующей команде замените *\<server-name>* .

```azurecli-interactive
az sql server ad-admin create --resource-group myResourceGroup --server-name <server-name> --display-name ADMIN --object-id $azureaduser
```

Для получения дополнительных сведений о добавлении администратора Active Directory см. раздел [Подготовка администратора Azure Active Directory для сервера базы данных SQL Azure](../sql-database/sql-database-aad-authentication-configure.md#provision-an-azure-active-directory-administrator-for-your-azure-sql-database-server)

## <a name="set-up-visual-studio"></a>Настройка Visual Studio

Чтобы включить разработку и отладку в Visual Studio, добавьте своего пользователя Azure AD в Visual Studio, выбрав в меню **Файл** > **Параметры учетной записи** и нажмите **Добавить учетную запись**.

Чтобы настроить пользователя Azure AD для службы аутентификации Azure, выберите в меню **Инструменты** > **Параметры**, а затем выберите **Служба аутентификация Azure** > **Выбор учетной записи**. Выберите добавленного пользователя Azure AD и нажмите **ОК**.

Теперь вы готовы разрабатывать и отлаживать свое приложение с базой данных SQL в качестве серверной части, используя аутентификацию Azure AD.

## <a name="modify-aspnet-project"></a>Изменение проекта ASP.NET

В Visual Studio откройте консоль диспетчера пакетов и добавьте пакет NuGet [Microsoft.Azure.Services.AppAuthentication](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication):

```powershell
Install-Package Microsoft.Azure.Services.AppAuthentication -Version 1.2.0
```

Работая в верхней части файла в *Web.config*, внесите следующие изменения:

- В `<configSections>` добавьте следующее объявление разделов в

    ```xml
    <section name="SqlAuthenticationProviders" type="System.Data.SqlClient.SqlAuthenticationProviderConfigurationSection, System.Data, Version=4.0.0.0, Culture=neutral, PublicKeyToken=b77a5c561934e089" />
    ```

- ниже закрывающийся тег `</configSections>`, а также добавьте следующий код XML для `<SqlAuthenticationProviders>`.

    ```xml
    <SqlAuthenticationProviders>
      <providers>
        <add name="Active Directory Interactive" type="Microsoft.Azure.Services.AppAuthentication.SqlAppAuthenticationProvider, Microsoft.Azure.Services.AppAuthentication" />
      </providers>
    </SqlAuthenticationProviders>
    ```    

- Найдите строку подключения с именем `MyDbConnection` и замените ее значение `connectionString` на `"server=tcp:<server-name>.database.windows.net;database=<db-name>;UID=AnyString;Authentication=Active Directory Interactive"`. Замените _\<server-name>_ и  _\<db-name>_ на имя вашего сервера и имя базы данных.

Введите `Ctrl+F5`, чтобы снова запустить приложение. То же приложение CRUD в вашем браузере теперь подключается к базе данных SQL Azure напрямую, используя аутентификацию Azure AD. Эта настройка позволяет запускать перенос базы данных. Позже, когда вы развернете свои изменения в службе приложений, те же настройки будут работать с соединением удостоверения приложения.

## <a name="use-managed-identity-connectivity"></a>Использование соединения управляемого удостоверения

Затем настройте приложение службы приложений для подключения к базе данных SQL с назначенным системой управляемым удостоверением.

### <a name="enable-managed-identity-on-app"></a>Включение управляемого удостоверения в приложении

Чтобы включить управляемое удостоверение для приложения Azure, используйте команду [az webapp identity assign](/cli/azure/webapp/identity?view=azure-cli-latest#az-webapp-identity-assign) в Cloud Shell. В следующей команде замените *\<app-name>* .

```azurecli-interactive
az webapp identity assign --resource-group myResourceGroup --name <app-name>
```

Ниже приведен пример выхода.

```json
{
  "additionalProperties": {},
  "principalId": "21dfa71c-9e6f-4d17-9e90-1d28801c9735",
  "tenantId": "72f988bf-86f1-41af-91ab-2d7cd011db47",
  "type": "SystemAssigned"
}
```

### <a name="add-managed-identity-to-an-azure-ad-group"></a>Добавление управляемого удостоверения в группу Azure AD

Чтобы предоставить идентификацию и доступ к базе данных SQL, необходимо добавить его в [группу Azure AD](../active-directory/fundamentals/active-directory-manage-groups.md). В Cloud Shell добавьте его в новую группу с именем _myAzureSQLDBAccessGroup_, показанную в следующем сценарии:

```azurecli-interactive
groupid=$(az ad group create --display-name myAzureSQLDBAccessGroup --mail-nickname myAzureSQLDBAccessGroup --query objectId --output tsv)
msiobjectid=$(az webapp identity show --resource-group myResourceGroup --name <app-name> --query principalId --output tsv)
az ad group member add --group $groupid --member-id $msiobjectid
az ad group member list -g $groupid
```

Если вы хотите увидеть полные выходные данные JSON для каждой команды, удалите параметры `--query objectId --output tsv`.

### <a name="grant-permissions-to-azure-ad-group"></a>Предоставление разрешений в группе Azure AD

В Cloud Shell войдите в базу данных SQL с помощью команды SQLCMD. Замените _\<server-name>_ именем сервера базы данных SQL, _\<db-name>_ , а _\<aad-user-name>_ и _\<aad-password>_  — учетными данными пользователя Azure AD.

```azurecli-interactive
sqlcmd -S <server-name>.database.windows.net -d <db-name> -U <aad-user-name> -P "<aad-password>" -G -l 30
```

В командной строке SQL нужной базы данных выполните следующие команды, чтобы добавить группу Azure AD и предоставить приложению требуемые разрешения. Например, 

```sql
CREATE USER [myAzureSQLDBAccessGroup] FROM EXTERNAL PROVIDER;
ALTER ROLE db_datareader ADD MEMBER [myAzureSQLDBAccessGroup];
ALTER ROLE db_datawriter ADD MEMBER [myAzureSQLDBAccessGroup];
ALTER ROLE db_ddladmin ADD MEMBER [myAzureSQLDBAccessGroup];
GO
```

Введите `EXIT`, чтобы вернуться в командную строку Cloud Shell.

### <a name="modify-connection-string"></a>Изменение строки подключения

Помните, что те же изменения, которые вы внесли в `Web.config`, работают с управляемым удостоверением, поэтому единственное, что нужно сделать, — это удалить существующую строку подключения в вашем приложении, которую было создано Visual Studio во время первого развертывая вашего приложения. Используйте в следующих командах, но замените *\<app-name>* именем вашего приложения.

```azurecli-interactive
az webapp config connection-string delete --resource-group myResourceGroup --name <app-name> --setting-names MyDbConnection
```

## <a name="publish-your-changes"></a>Публикация изменений

Теперь требуется опубликовать изменения в Azure.

В **обозревателе решений** щелкните правой кнопкой мыши проект **DotNetAppSqlDb** и выберите **Опубликовать**.

![Публикация в обозревателе решений](./media/app-service-web-tutorial-dotnet-sqldatabase/solution-explorer-publish.png)

На странице публикации щелкните **Опубликовать**. Когда на новой веб-странице отображается список дел, ваше приложение подключается к базе данных с управляемым удостоверением.

![Приложение Azure после включения Code First Migration](./media/app-service-web-tutorial-dotnet-sqldatabase/this-one-is-done.png)

Теперь вы должны иметь возможность редактировать список дел по-прежнему.

[!INCLUDE [cli-samples-clean-up](../../includes/cli-samples-clean-up.md)]

## <a name="next-steps"></a>Дополнительная информация

Вы научились выполнять следующие задачи:

> [!div class="checklist"]
> * Включение управляемых удостоверений
> * Предоставление управляемому удостоверению доступа к Базе данных SQL
> * Настройте Entity Framework для использования аутентификации Azure AD с базой данных SQL
> * Подключитесь к базе данных SQL из Visual Studio с использованием аутентификации Azure AD

Перейдите к следующему руководству, чтобы научиться сопоставлять пользовательские DNS-имена с веб-приложением.

> [!div class="nextstepaction"]
> [Сопоставление существующего настраиваемого DNS-имени со Службой приложений Azure](app-service-web-tutorial-custom-domain.md)
