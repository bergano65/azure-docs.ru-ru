---
title: Настройка подключения к базе данных SQL Azure с помощью управляемого удостоверения (Предварительная версия)
titleSuffix: Azure Cognitive Search
description: Узнайте, как настроить подключение индексатора к базе данных SQL Azure с помощью управляемого удостоверения (Предварительная версия)
manager: luisca
author: markheff
ms.author: maheff
ms.devlang: rest-api
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 05/18/2020
ms.openlocfilehash: d0933f5305007bc4a8238adb2b6b949ab0c11edf
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/02/2020
ms.locfileid: "85559941"
---
# <a name="set-up-an-indexer-connection-to-azure-sql-database-using-a-managed-identity-preview"></a>Настройка подключения индексатора к базе данных SQL Azure с помощью управляемого удостоверения (Предварительная версия)

> [!IMPORTANT] 
> Поддержка подключения к источнику данных с использованием управляемого удостоверения в настоящее время предоставляется в режиме общедоступной предварительной версии. Для предварительной версии функции соглашение об уровне обслуживания не предусмотрено. Мы не рекомендуем использовать ее в рабочей среде.
> Чтобы запросить доступ к предварительной версии этой функции, заполните [эту форму](https://aka.ms/azure-cognitive-search/mi-preview-request).

На этой странице описывается, как настроить подключение индексатора к базе данных SQL Azure с помощью управляемого удостоверения вместо предоставления учетных данных в строке подключения к объекту источника данных.

Перед изучением этой функции желательно иметь представление о том, что такое индексатор и как настроить индексатор для источника данных. См. подробнее в следующих статьях:

* [Обзор индексатора](search-indexer-overview.md)
* [Индексатор SQL Azure](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md)

## <a name="set-up-a-connection-using-a-managed-identity"></a>Настройка подключения с помощью управляемого удостоверения

### <a name="1---turn-on-system-assigned-managed-identity"></a>1\. Включение управляемого удостоверения, назначаемого системой

Если включено управляемое удостоверение, назначаемое системой, Azure самостоятельно создаст удостоверение для службы поиска, которое можно использовать для проверки подлинности в других службах Azure для того же клиента и той же подписки. Это удостоверение вы можете использовать в назначениях для управления доступом на основе ролей (RBAC), чтобы разрешить доступ к данным на время индексирования.

![Включение управляемого удостоверения, назначаемого системой](./media/search-managed-identities/turn-on-system-assigned-identity.png "Включение управляемого удостоверения, назначаемого системой")

Нажав **Сохранить**, вы увидите идентификатор объекта, назначенный вашей службе поиска.

![Идентификатор объекта](./media/search-managed-identities/system-assigned-identity-object-id.png "Идентификатор объекта.")

### <a name="2---provision-azure-active-directory-admin-for-sql-server"></a>2\. Подготовка администратора Azure Active Directory для SQL Server

При подключении к базе данных на следующем шаге вам потребуется учетная запись Azure Active Directory (Azure AD) с правами администратора на доступ к базе данных, чтобы вы могли предоставить службе поиска разрешение на доступ к базе данных.

Выполните инструкции [здесь](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication-configure?tabs=azure-powershell#provision-an-azure-active-directory-administrator-for-your-azure-sql-database-server), чтобы предоставить администратору учетной записи Azure AD доступ к базе данных.

### <a name="3---assign-the-search-service-permissions"></a>3\. Назначение разрешений службе поиска

Чтобы назначить службе поиска разрешение на чтение базы данных, выполните указанные ниже действия.

1. Подключитесь к Visual Studio.

    ![Подключение к Visual Studio](./media/search-managed-identities/connect-with-visual-studio.png "Подключитесь к Visual Studio.")

2. Пройдите проверку подлинности с учетной записью Azure AD.

    ![Проверка подлинности](./media/search-managed-identities/visual-studio-authentication.png "Authenticate")

3. Выполните следующие команды:

    Заключите имя службы поиска в скобки.
    
    ```
    CREATE USER [your search service name here] FROM EXTERNAL PROVIDER;
    EXEC sp_addrolemember 'db_datareader', [your search service name here];
    ```

    ![Новый запрос](./media/search-managed-identities/visual-studio-new-query.png "Создать запрос")

    ![Выполнение запроса](./media/search-managed-identities/visual-studio-execute-query.png "Выполнение запроса")

>[!NOTE]
> Если после завершения этого шага удостоверение службы поиска из шага 1 изменилось, необходимо удалить членство в роли и удалить пользователя из базы данных SQL, а затем снова добавить разрешения, выполнив шаг 3 еще раз.
> Чтобы удалить членство в роли и пользователя, выполните следующие команды.
> ```
> sp_droprolemember 'db_datareader', [your search service name];
> DROP USER IF EXISTS [your search service name];
> ```

### <a name="4---add-a-role-assignment"></a>4\. Добавление назначения роли

На этом шаге вы предоставите службе "Когнитивный поиск Azure" разрешение на чтение данных из SQL Server.

1. На портале Azure перейдите к учетной записи Azure SQL Server.
2. Выберите **Управление доступом (IAM)**
3. Щелкните **Добавить**, а затем **Добавить назначение роли**.

    ![Добавление назначения роли](./media/search-managed-identities/add-role-assignment-sql-server.png "Добавление назначения роли")

4. Выберите соответствующую роль **читателя**.
5. В поле **Назначение доступа к** сохраните выбранное значение **Пользователь, группа или субъект-служба Azure AD**.
6. Найдите службу поиска, выберите ее и щелкните **Сохранить**.

    ![Добавление назначения роли читателя](./media/search-managed-identities/add-role-assignment-sql-server-reader-role.png "Добавление назначения роли читателя")

### <a name="5---create-the-data-source"></a>5\. Создание источника данных

Если индексация выполняется по базе данных SQL, источник данных должен иметь следующие обязательные свойства.

* Свойство **name** — уникальное имя источника данных в службе поиска.
* **type** — `azuresql`
* **credentials**
    * Если для проверки подлинности используется управляемое удостоверение, формат **учетных данных** отличается от того, который используется без управляемого удостоверения. Здесь вы будете предоставлять имя начального каталога или базы данных и ResourceId без ключа учетной записи или пароля. ResourceId должен включать идентификатор подписки базы данных SQL Azure, группу ресурсов базы данных SQL и имя базы данных SQL. 
    * Формат строки подключения для управляемого удостоверения:
        * *Initial Catalog|Database=**имя базы данных**;ResourceId=/subscriptions/**ИД подписки**/resourceGroups/**имя группы ресурсов**/providers/Microsoft.Sql/servers/**имя сервера SQL Server**/;Connection Timeout=**время ожидания подключения**;*
* **container** указывает имя таблицы или представления, которые необходимо индексировать.

Пример создания объекта SQL Azure в качестве источника данных с помощью [REST API](https://docs.microsoft.com/rest/api/searchservice/create-data-source):

```
POST https://[service name].search.windows.net/datasources?api-version=2020-06-30
Content-Type: application/json
api-key: [admin key]

{
    "name" : "sql-datasource",
    "type" : "azuresql",
    "credentials" : { "connectionString" : "Database=sql-database;ResourceId=/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/azure-sql-resource-group/providers/Microsoft.Sql/servers/sql-server-search-demo;Connection Timeout=30;" },
    "container" : { "name" : "my-table" }
} 
```

Портал Azure и [.NET SDK](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.datasource?view=azure-dotnet) также поддерживают строку подключения с управляемым удостоверением. Для портала Azure требуется флаг компонента, который будет предоставлен после регистрации для использования предварительной версии, которую можно выполнить по ссылке в начале этой страницы. 

### <a name="6---create-the-index"></a>6\. Создание индекса

Индекс задает поля в документе, атрибуты, и другие компоненты, которые определяют процедуру поиска.

Создать индекс с полем `booktitle`, доступным для поиска, можно следующим образом.   

```
POST https://[service name].search.windows.net/indexes?api-version=2020-06-30
Content-Type: application/json
api-key: [admin key]

{
    "name" : "my-target-index",
    "fields": [
    { "name": "id", "type": "Edm.String", "key": true, "searchable": false },
    { "name": "booktitle", "type": "Edm.String", "searchable": true, "filterable": false, "sortable": false, "facetable": false }
    ]
}
```

Дополнительные сведения о создании индексов см. в статье [Create Index](https://docs.microsoft.com/rest/api/searchservice/create-index) (Создание индекса).

### <a name="7---create-the-indexer"></a>7\. Создание индексатора

Индексатор соединяет источник данных с целевым индексом поиска и предоставляет расписание для автоматизации обновления данных.

Когда индекс и источник данных уже созданы, можно создать индексатор.

Пример определения индексатора для SQL Azure:

```
POST https://[service name].search.windows.net/indexers?api-version=2020-06-30
Content-Type: application/json
api-key: [admin key]

{
    "name" : "sql-indexer",
    "dataSourceName" : "sql-datasource",
    "targetIndexName" : "my-target-index",
    "schedule" : { "interval" : "PT2H" }
}
```    

Этот индексатор будет выполняться каждые два часа (интервал расписания имеет значение "PT2H"). Чтобы запускать индексатор каждые 30 минут, задайте интервал "PT30M". Самый короткий интервал, который можно задать, составляет 5 минут. Расписание является необязательным. Если оно не указано, то индексатор выполняется только один раз при его создании. Однако индексатор можно запустить по запросу в любое время.   

Дополнительные сведения об API создания индексатора см. в разделе [Создание индексатора](https://docs.microsoft.com/rest/api/searchservice/create-indexer).

Дополнительные сведения об определении расписаний индексаторов для Когнитивного поиска Azure см. [здесь](search-howto-schedule-indexers.md).

## <a name="troubleshooting"></a>Устранение неполадок

Если при попытке индексатора подключиться к источнику данных появляется сообщение о том, что клиенту не разрешен доступ к серверу, ознакомьтесь с [общими ошибками индексатора](https://docs.microsoft.com/azure/search/search-indexer-troubleshooting).

## <a name="see-also"></a>См. также раздел

Дополнительные сведения об индексаторе SQL Azure:
* [Индексатор SQL Azure](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md)
