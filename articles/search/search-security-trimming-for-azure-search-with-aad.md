---
title: Фильтры безопасности для усечения результатов с помощью Active Directory
titleSuffix: Azure Cognitive Search
description: Управление доступом к содержимому Когнитивный поиск Azure с помощью фильтров безопасности и удостоверений Azure Active Directory (AAD).
manager: nitinme
author: brjohnstmsft
ms.author: brjohnst
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 01280b6ee9dda15af3c0fc707a385501580c624c
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/23/2019
ms.locfileid: "72794299"
---
# <a name="security-filters-for-trimming-azure-cognitive-search-results-using-active-directory-identities"></a>Фильтры безопасности для усечения результатов Когнитивный поиск Azure с использованием удостоверений Active Directory

В этой статье показано, как использовать удостоверения безопасности Azure Active Directory (AAD) вместе с фильтрами в Когнитивный поиск Azure, чтобы обрезать результаты поиска на основе членства в группе пользователей.

В этой статье рассматриваются следующие задачи:
> [!div class="checklist"]
> - создание групп и пользователей AAD;
> - связывание пользователя с созданной группой;
> - кэширование новых групп;
> - индексация документов со связанными группами;
> - отправка запроса на поиск с помощью фильтра идентификаторов групп.
> 
> [!NOTE]
> Примеры фрагментов когда в этой статье написаны языке C#. Полный исходный код можно найти на сайте [GitHub](https://aka.ms/search-dotnet-howto). 

## <a name="prerequisites"></a>Технические условия

Индекс в Когнитивный поиске Azure должен иметь [поле безопасности](search-security-trimming-for-azure-search.md) для хранения списка удостоверений групп, имеющих доступ на чтение документа. Этот вариант использования предполагает прямое соответствие между защищаемым элементом (например, заявление на вступление в колледж конкретного человека) и полем безопасности, в котором указывается, кто имеет доступ к этому элементу (персонал приемной комиссии).

В этом пошаговом руководстве для создания пользователей, групп и ассоциаций в AAD необходимо иметь разрешения администратора AAD.

Ваша заявка также должна быть зарегистрирована в AAD, как описано в следующей процедуре.

### <a name="register-your-application-with-aad"></a>Регистрация приложения с помощью AAD

На этом шаге приложение интегрируется с AAD для приема входа с использованием учетных записей пользователя и группы. Если вы не являетесь администратором AAD в своей организации, для выполнения следующих действий может потребоваться [создать клиент](https://docs.microsoft.com/azure/active-directory/develop/active-directory-howto-tenant).

1. Перейдите на [**портал регистрации приложений**](https://apps.dev.microsoft.com) и выберите  >  **Converged app** (Конвергированное приложение)  > **Добавить приложение**.
2. Введите имя приложения, а затем щелкните **Создать**. 
3. Выберите только что зарегистрированное приложение на странице "Мои приложения".
4. На странице регистрации приложения выберите **Платформы** > **Добавление платформы** и **Веб-API**.
5. Оставаясь на странице регистрации приложения выберите **Разрешения Microsoft Graph** > **Добавить**.
6. В разделе "Выберите разрешения" добавьте следующие делегированные разрешения, а затем щелкните **ОК**:

   + **Directory.ReadWrite.All**;
   + **Group.ReadWrite.All**;
   + **User.ReadWrite.All**.

Microsoft Graph предоставляет API, который обеспечивает программный доступ к AAD через REST API. Пример кода в этом пошаговом руководстве использует разрешения для вызова API Microsoft Graph, чтобы создавать группы, пользователей и ассоциации. API также используются для кэширования идентификаторов групп для более быстрой фильтрации.

## <a name="create-users-and-groups"></a>Создание пользователей и групп

При добавлении поиска в установленное приложение у вас могут быть идентификаторы пользователей и групп в AAD. В этом случае можно пропустить следующие три шага. 

Однако, если у вас нет имеющихся пользователей, можно использовать API Microsoft Graph для создания субъектов безопасности. В следующих фрагментах кода показано, как создавать идентификаторы, которые становятся значениями данных для поля безопасности в индексе Azure Когнитивный поиск. В нашем гипотетическом приложении для приема в колледж это будут идентификаторы безопасности для сотрудников приемной комиссии.

Назначение пользователей и членство в группе может быть очень гибким, особенно в крупных организациях. Код, который создает удостоверения пользователей и групп, должен выполняться достаточно часто для учета изменений в членстве организации. Аналогично, для индекса Когнитивный поиск Azure требуется аналогичное расписание обновления, отражающее текущее состояние разрешенных пользователей и ресурсов.

### <a name="step-1-create-aad-grouphttpsdocsmicrosoftcomgraphapigroup-post-groupsviewgraph-rest-10"></a>Шаг 1. Создание [группы AAD](https://docs.microsoft.com/graph/api/group-post-groups?view=graph-rest-1.0) 
```csharp
// Instantiate graph client 
GraphServiceClient graph = new GraphServiceClient(new DelegateAuthenticationProvider(...));
Group group = new Group()
{
    DisplayName = "My First Prog Group",
    SecurityEnabled = true,
    MailEnabled = false,
    MailNickname = "group1"
}; 
Group newGroup = await graph.Groups.Request().AddAsync(group);
```
   
### <a name="step-2-create-aad-userhttpsdocsmicrosoftcomgraphapiuser-post-usersviewgraph-rest-10"></a>Шаг 2. Создание [пользователя AAD](https://docs.microsoft.com/graph/api/user-post-users?view=graph-rest-1.0)
```csharp
User user = new User()
{
    GivenName = "First User",
    Surname = "User1",
    MailNickname = "User1",
    DisplayName = "First User",
    UserPrincipalName = "User1@FirstUser.com",
    PasswordProfile = new PasswordProfile() { Password = "********" },
    AccountEnabled = true
};
User newUser = await graph.Users.Request().AddAsync(user);
```

### <a name="step-3-associate-user-and-group"></a>Шаг 3. Связывание пользователя и групп
```csharp
await graph.Groups[newGroup.Id].Members.References.Request().AddAsync(newUser);
```

### <a name="step-4-cache-the-groups-identifiers"></a>Шаг 4. Кэширование идентификаторов групп
Чтобы уменьшить задержки в сети, при необходимости можно кэшировать ассоциации пользователей и групп, чтобы при выдаче запроса на поиск группы возвращались из кэша и не приходилось обращаться в AAD. Для отправки единого HTTP-запроса с несколькими пользователями и создания кэша можно использовать [API пакетной службы AAD](https://developer.microsoft.com/graph/docs/concepts/json_batching).

Microsoft Graph может обрабатывать большое число запросов. Если возникает огромное количество запросов, Microsoft Graph отклоняет запрос с кодом состояния HTTP 429. Дополнительные сведения см. в статье [Руководство по регулированию Microsoft Graph](https://developer.microsoft.com/graph/docs/concepts/throttling).

## <a name="index-document-with-their-permitted-groups"></a>Индексирование документа с разрешенными группами

Операции запроса в Azure Когнитивный поиск выполняются через индекс Когнитивный поиск Azure. На этом шаге операция индексирования импортирует данные с возможностью поиска в индекс, включая идентификаторы, используемые в качестве фильтров безопасности. 

Когнитивный поиск Azure не проверяет подлинность удостоверений пользователей или предоставляет логику для определения содержимого, которое у пользователя есть разрешение на просмотр. Вариант использования фильтров безопасности предполагает обеспечение связи между конфиденциальным документом и идентификатором группы, имеющим доступ к этому документу, импортированным без изменений в индекс поиска. 

В гипотетическом примере тело запроса размещения в индексе Azure Когнитивный поиск будет включать в себя очерк или запись о соискательх, а также идентификатор группы, имеющий разрешение на просмотр этого содержимого. 

В универсальном примере, используемом в примере кода для этого пошагового руководства, действие индекса может выглядеть следующим образом:

```csharp
var actions = new IndexAction<SecuredFiles>[]
              {
                  IndexAction.Upload(
                  new SecuredFiles()
                  {
                      FileId = "1",
                      Name = "secured_file_a",
                      GroupIds = new[] { groups[0] }
                  }),
              ...
             };

var batch = IndexBatch.New(actions);

_indexClient.Documents.Index(batch);  
```

## <a name="issue-a-search-request"></a>Отправка поискового запроса

В целях применения фильтров безопасности значения в поле безопасности в индексе являются статическими значениями, используемыми для включения или исключения документов в результатах поиска. Например, если идентификатором группы для приемов является "A11B22C33D44-E55F66G77-H88I99JKK", все документы в индексе Azure Когнитивный поиск, в которых в списке безопасности будут включены (или исключены), включаются в результаты поиска, отправляемые запрашивающей стороне.

Чтобы фильтровать документы, возвращаемые в результатах поиска, по группам для пользователя, выполняющего запрос, сделайте следующее.

### <a name="step-1-retrieve-users-group-identifiers"></a>Шаг 1. Получение идентификаторов группы пользователя

Если группы пользователя не кэшированы или истек срок действия кэша, отправьте запрос [групп](https://docs.microsoft.com/graph/api/directoryobject-getmembergroups?view=graph-rest-1.0).
```csharp
private static void RefreshCacheIfRequired(string user)
{
    if (!_groupsCache.ContainsKey(user))
    {
        var groups = GetGroupIdsForUser(user).Result;
        _groupsCache[user] = groups;
    }
}

private static async Task<List<string>> GetGroupIdsForUser(string userPrincipalName)
{
    List<string> groups = new List<string>();
    var allUserGroupsRequest = graph.Users[userPrincipalName].GetMemberGroups(true).Request();

    while (allUserGroupsRequest != null) 
    {
        IDirectoryObjectGetMemberGroupsRequestBuilder allUserGroups = await allUserGroupsRequest.PostAsync();
        groups = allUserGroups.ToList();
        allUserGroupsRequest = allUserGroups.NextPageRequest;
    }
    return groups;
}
``` 

### <a name="step-2-compose-the-search-request"></a>Шаг 2. Составление поискового запроса

Предположим, что у вас есть членство в группах пользователей. Это позволяет отправить поисковой запрос с соответствующими значениями фильтра.

```csharp
string filter = String.Format("groupIds/any(p:search.in(p, '{0}'))", string.Join(",", groups.Select(g => g.ToString())));
SearchParameters parameters = new SearchParameters()
             {
                 Filter = filter,
                 Select = new[] { "application essays" }
             };

DocumentSearchResult<SecuredFiles> results = _indexClient.Documents.Search<SecuredFiles>("*", parameters);
```
### <a name="step-3-handle-the-results"></a>Шаг 3. Обработка результатов

Ответ включает отфильтрованный список, состоящий из тех документов, для которых у пользователя есть разрешение на просмотр. В зависимости от структуры страницы результатов поиска можно включить визуальные подсказки для отражения отфильтрованного набора результатов.

## <a name="conclusion"></a>Заключение

В этом пошаговом руководстве вы узнали, как использовать входы AAD для фильтрации документов в Azure Когнитивный поиск результаты, обрезая результаты документов, которые не соответствуют фильтру, указанному в запросе.

## <a name="see-also"></a>Дополнительные материалы

+ [Управление доступом на основе удостоверений с помощью фильтров Когнитивный поиск Azure](search-security-trimming-for-azure-search.md)
+ [Фильтры в Когнитивный поиск Azure](search-filters.md)
+ [Безопасность данных и управление доступом в Azure Когнитивный поиск операции](search-security-overview.md)
