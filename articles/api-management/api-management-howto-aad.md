---
title: Авторизация учетных записей разработчиков с помощью Azure Active Directory в службе управления API Azure | Документация Майкрософт
description: Узнайте, как авторизовать пользователей с помощью Azure Active Directory в службе управления API.
services: api-management
documentationcenter: API Management
author: miaojiang
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 11/04/2019
ms.author: apimpm
ms.openlocfilehash: b0c3487bb77f32483d6d65cd0a4b1f637267eabf
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/17/2019
ms.locfileid: "74144358"
---
# <a name="authorize-developer-accounts-by-using-azure-active-directory-in-azure-api-management"></a>Авторизация учетных записей разработчиков с помощью Azure Active Directory в управлении API Azure

В этой статье показывается, как включить доступ к порталу разработчика для пользователей из каталога Active Directory Azure (Azure AD). В этом руководстве также показано, как управлять группами пользователей Azure AD путем добавления внешних групп, содержащих пользователей.

## <a name="prerequisites"></a>предварительным требованиям

- Выполните задачи в кратком руководстве по [созданию экземпляра службы управления API Azure](get-started-create-service-instance.md).
- Импортируйте и опубликуйте экземпляр службы управления API Azure. Дополнительные сведения см. в статье об [импорте и публикации](import-and-publish.md).

[!INCLUDE [premium-dev-standard.md](../../includes/api-management-availability-premium-dev-standard.md)]

## <a name="authorize-developer-accounts-by-using-azure-ad"></a>Авторизация учетных записей разработчиков с помощью Azure AD

1. Войдите на [портале Azure](https://portal.azure.com). 
2. Выберите ![стрелка](./media/api-management-howto-aad/arrow.png).
3. В поле поиска введите **api**.
4. Выберите **Службы управления API**.
5. Выберите экземпляр службы управления API.
6. В разделе **Безопасность**выберите **удостоверение**.
7. Выберите **+ Добавить** в верхней части экрана.

    Справа отобразится область **Добавление поставщика удостоверений**.
8. В разделе **Тип поставщика** выберите **Azure Active Directory**.

    В области отобразятся элементы управления, необходимые для ввода других требуемых сведений. Элементы управления включают **идентификатор клиента** и **секрет клиента**. (Эти элементы управления описаны далее в этой статье.)
9. Запишите содержимое **URL-адреса перенаправления**.
    
   ![Шаги для добавления поставщика удостоверений на портале Azure](./media/api-management-howto-aad/api-management-with-aad001.png)  
10. В браузере откройте другую вкладку. 
11. Перейдите к [портал Azure-регистрация приложений](https://go.microsoft.com/fwlink/?linkid=2083908) , чтобы зарегистрировать приложение в Active Directory.
12. В разделе **Управление**выберите **Регистрация приложений**.
13. Выберите **Новая регистрация**. На странице **Регистрация приложения** задайте следующие значения:
    
* Задайте **понятное имя.** Например, *Developer — портал*
* Установите для **поддерживаемых типов учетных** записей **учетные записи только в этом каталоге Организации**. 
* Задайте для **URI перенаправления** значение, полученное на шаге 9. 
* Выберите **зарегистрировать**. 

14.  После регистрации приложения скопируйте **идентификатор приложения (Client)** на странице **Обзор** . 
15. Вернитесь к экземпляру управления API. В окне **Добавление поставщика удостоверений** вставьте значение **Application (Client) ID** в поле **Client ID (идентификатор клиента** ).
16. Вернитесь к конфигурации Azure AD, выберите **сертификаты & секреты** в разделе **Управление**. Нажмите кнопку **создать секрет клиента** . Введите значение в окне **Описание**, выберите любой вариант для параметра **срок действия** и нажмите кнопку **добавить**. Скопируйте значение секрета клиента, прежде чем покинуть страницу. Они понадобятся вам на следующем шаге. 
17. В разделе **Управление**выберите **Проверка подлинности** , а затем выберите **токены идентификации** в разделе **неявное предоставление** .
18. Вернитесь к экземпляру управления API, вставьте секрет в поле **секрет клиента** .

    > [!IMPORTANT]
    > Обязательно обновите **секрет клиента** до истечения срока действия ключа. 
    >  
    >

19. В окне **Добавление поставщика удостоверений** также содержится текстовое поле **Разрешенные клиенты**. Укажите домены экземпляров Azure AD, которым требуется предоставить доступ к API экземпляра службы управления API. Несколько доменов можно разделять символами начала новой строки, пробелами или запятыми.

> [!NOTE]
> В разделе **Разрешенные клиенты** можно указать несколько доменов. Прежде чем какой-либо пользователь сможет выполнить вход из домена, отличного от домена, в котором было зарегистрировано приложение, глобальный администратор этого другого домена должен предоставить разрешение для доступа приложения к данным каталога. Чтобы предоставить разрешение, глобальный администратор должен: a. Перейдите на страницу `https://<URL of your developer portal>/aadadminconsent` (например, https://contoso.portal.azure-api.net/aadadminconsent).
> b. Ввести имя домена клиента Azure AD, к которому требуется предоставить доступ.
> c. Нажмите кнопку **Submit** (Отправить). 

20.  После указания требуемой конфигурации выберите **Добавить**.

После сохранения изменений пользователи в указанной службе Azure AD могут входить на портал разработчика, выполняя действия, которые описаны в разделе [Вход на портал разработчика с помощью учетной записи Azure Active Directory](#log_in_to_dev_portal).

## <a name="add-an-external-azure-ad-group"></a>Добавление внешней группы Azure AD

После включения доступа для пользователей в клиенте Azure AD можно добавить группы Azure AD в службу управления API. В результате вы можете управлять видимостью продуктов с помощью групп Azure AD.

Чтобы добавить внешнюю группу Azure AD в APIM, сначала необходимо выполнить действия, описанные в предыдущем разделе. Кроме того, зарегистрированному приложению необходимо предоставить доступ к Azure Active Directory API Graph с разрешением `Directory.ReadAll` следующим образом: 

1. Вернитесь к регистрации приложения, созданному в предыдущем разделе.
2. Перейдите на вкладку **разрешения API** , а затем нажмите кнопку **+ Добавить разрешение** . 
3. В области **разрешения API запроса** выберите вкладку API- **интерфейсы Майкрософт** и прокрутите вниз, чтобы найти плитку **Azure Active Directory графа** в разделе Поддерживаемые устаревшие API-интерфейсы, и щелкните ее. Затем нажмите кнопку **разрешения приложения** и выберите разрешение **Directory. ReadAll** , а затем добавьте это разрешение с помощью кнопки внизу. 
4. Нажмите кнопку **предоставить согласие администратора для {tenantname}** , чтобы предоставить доступ всем пользователям в этом каталоге. 

Теперь можно добавить внешние группы Azure AD на вкладке **группы** экземпляра службы управления API.

1. Выберите вкладку **Группы** .
2. Нажмите кнопку **Добавить группу AAD**.
   ![Кнопка "Добавить группу AAD"](./media/api-management-howto-aad/api-management-with-aad008.png)
3. Выберите группу, которую нужно добавить.
4. Нажмите кнопку **Выбрать**.

После добавления внешней группы Azure AD можно просмотреть и настроить ее свойства. Выберите имя группы на вкладке **группы** . Здесь можно изменить **имя** и **Описание** для группы.
 
Пользователи из настроенного экземпляра Azure AD теперь могут входить на портал разработчика, просматривать любые группы, которые они могут видеть, и подписываться на них.

## <a name="a-idlog_in_to_dev_portal-developer-portal---add-azure-ad-account-authentication"></a>портал разработчика <a id="log_in_to_dev_portal"/>. Добавление проверки подлинности учетной записи Azure AD

На портале разработчика вход с помощью AAD возможен с мини-приложением **кнопок OAuth** . Мини-приложение уже включено на странице входа в содержимое портала разработчика по умолчанию.

![Мини-приложение кнопок AAD](./media/api-management-howto-aad/portal-oauth-widget.png)

Хотя новая учетная запись будет автоматически создана всякий раз, когда новый пользователь входит в состав AAD, вы можете добавить то же мини-приложение на страницу регистрации.

## <a name="legacy-developer-portal---how-to-sign-in-with-azure-ad"></a>Устаревший портал разработчика. как войти с помощью Azure AD

[!INCLUDE [api-management-portal-legacy.md](../../includes/api-management-portal-legacy.md)]

Чтобы войти на портал разработчика с помощью учетной записи Azure AD, которая была настроена в предыдущих разделах:

1. Откройте новое окно браузера, используя URL-адрес входа из конфигурации приложения Active Directory, и выберите **Azure Active Directory**.

   ![страница входа][api-management-dev-portal-signin]

1. Введите учетные данные одного из пользователей в Azure AD и нажмите кнопку **Вход**.

   ![Вход с использованием имени пользователя и пароля][api-management-aad-signin]

1. Может появиться запрос с регистрационной формой, если требуются какие-либо дополнительные сведения. Заполните эту регистрационную форму и выберите **Регистрация**.

   ![Кнопка "Регистрация" в форме регистрации][api-management-complete-registration]

Теперь этот пользователь вошел на портал разработчика вашего экземпляра службы управления API.

![Портал разработчика после завершения регистрации][api-management-registration-complete]

[api-management-dev-portal-signin]: ./media/api-management-howto-aad/api-management-dev-portal-signin.png
[api-management-aad-signin]: ./media/api-management-howto-aad/api-management-aad-signin.png
[api-management-complete-registration]: ./media/api-management-howto-aad/api-management-complete-registration.png
[api-management-registration-complete]: ./media/api-management-howto-aad/api-management-registration-complete.png

[How to add operations to an API]: api-management-howto-add-operations.md
[How to add and publish a product]: api-management-howto-add-products.md
[Monitoring and analytics]: api-management-monitoring.md
[Add APIs to a product]: api-management-howto-add-products.md#add-apis
[Publish a product]: api-management-howto-add-products.md#publish-product
[Get started with Azure API Management]: get-started-create-service-instance.md
[API Management policy reference]: api-management-policy-reference.md
[Caching policies]: api-management-policy-reference.md#caching-policies
[Create an API Management service instance]: get-started-create-service-instance.md

[https://oauth.net/2/]: https://oauth.net/2/
[WebApp-GraphAPI-DotNet]: https://github.com/AzureADSamples/WebApp-GraphAPI-DotNet
[Accessing the Graph API]: https://msdn.microsoft.com/library/azure/dn132599.aspx#BKMK_Graph

[Prerequisites]: #prerequisites
[Configure an OAuth 2.0 authorization server in API Management]: #step1
[Configure an API to use OAuth 2.0 user authorization]: #step2
[Test the OAuth 2.0 user authorization in the Developer Portal]: #step3
[Next steps]: #next-steps

[Sign in to the developer portal by using an Azure AD account]: #Sign-in-to-the-developer-portal-by-using-an-Azure-AD-account
