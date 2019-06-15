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
ms.devlang: na
ms.topic: article
ms.date: 01/16/2018
ms.author: apimpm
ms.openlocfilehash: d267ff3a43438d9fe6e4e21f0ac023cfa6675f19
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/13/2019
ms.locfileid: "65956306"
---
# <a name="authorize-developer-accounts-by-using-azure-active-directory-in-azure-api-management"></a>Авторизация учетных записей разработчиков с помощью Azure Active Directory в управлении API Azure

В этой статье показывается, как включить доступ к порталу разработчика для пользователей из каталога Active Directory Azure (Azure AD). В этом руководстве также показано, как управлять группами пользователей Azure AD путем добавления внешних групп, содержащих пользователей.

## <a name="prerequisites"></a>Технические условия

- Выполните инструкции из краткого руководства [Создание экземпляра службы управления API Azure](get-started-create-service-instance.md).
- Импортируйте и опубликуйте экземпляр службы управления API Azure. Дополнительные сведения см. в статье об [импорте и публикации](import-and-publish.md).

[!INCLUDE [premium-dev-standard.md](../../includes/api-management-availability-premium-dev-standard.md)]

## <a name="authorize-developer-accounts-by-using-azure-ad"></a>Авторизация учетных записей разработчиков с помощью Azure AD

1. Войдите на [портале Azure](https://portal.azure.com). 
2. Выберите пункт ![Стрелка](./media/api-management-howto-aad/arrow.png).
3. В поле поиска введите **api**.
4. Выберите **Службы управления API**.
5. Выберите экземпляр службы управления API.
6. В разделе **безопасности**выберите **удостоверения**.
7. Выберите **+ Добавить** в верхней части экрана.

    Справа отобразится область **Добавление поставщика удостоверений**.
8. В разделе **Тип поставщика** выберите **Azure Active Directory**.

    В области отобразятся элементы управления, необходимые для ввода других требуемых сведений. Элементы управления включают **идентификатор клиента** и **секрет клиента**. (Эти элементы управления описаны далее в этой статье.)
9. Запомните или запишите содержимое **URL-адрес перенаправления**.
    
   ![Шаги для добавления поставщика удостоверений на портале Azure](./media/api-management-howto-aad/api-management-with-aad001.png)  
10. В браузере откройте другую вкладку. 
11. Перейдите к [Azure портал регистрации приложений](https://go.microsoft.com/fwlink/?linkid=2083908) для регистрации приложения в Active Directory.
12. В разделе **управление**выберите **регистрация приложений**.
13. Выберите **Новая регистрация**. На **зарегистрировать приложение** странице, задайте значения следующим образом:
    
* Задайте **имя** значащее имя. например *портала разработчика*
* Задайте **поддерживаемые типы учетных записей** для **учетных записей в организации каталога только**. 
* Задайте **URI перенаправления** значение, полученное на шаге 9. 
* Выберите **зарегистрировать**. 

14.  После регистрации приложения, скопировать **идентификатор приложения (клиент)** из **Обзор** страницы. 
15. Вернитесь к экземпляру службы управления API. В **Добавление поставщика удостоверений** окне вставьте **идентификатор приложения (клиент)** значение в **идентификатор клиента** поле.
16. Вернитесь в конфигурацию Azure AD, выберите **сертификаты и секреты** под **управление**. Выберите **новый секрет клиента** кнопки. Введите значение в **описание**, выберите любой параметр для **Expires** и выберите **добавить**. Скопируйте значение секрета клиента до выхода со страницы. Они понадобятся вам на следующем шаге. 
17. В разделе **управление**выберите **проверки подлинности** , а затем выберите **маркеры Идентификации** под **неявное предоставление**
18. Вернитесь к экземпляру службы управления API, вставьте секрет в **секрет клиента** поле.

    > [!IMPORTANT]
    > Обязательно обновите **секрет клиента** до истечения срока действия ключа. 
    >  
    >

19. В окне **Добавление поставщика удостоверений** также содержится текстовое поле **Разрешенные клиенты**. Укажите домены экземпляров Azure AD, которым требуется предоставить доступ к API экземпляра службы управления API. Несколько доменов можно разделять символами начала новой строки, пробелами или запятыми.

> [!NOTE]
> В разделе **Разрешенные клиенты** можно указать несколько доменов. Прежде чем какой-либо пользователь сможет выполнить вход из домена, отличного от домена, в котором было зарегистрировано приложение, глобальный администратор этого другого домена должен предоставить разрешение для доступа приложения к данным каталога. Чтобы предоставить разрешение, глобальный администратор должен:. Перейдите на страницу `https://<URL of your developer portal>/aadadminconsent` (например, https://contoso.portal.azure-api.net/aadadminconsent).
> 2\. Ввести имя домена клиента Azure AD, к которому требуется предоставить доступ.
> c. Нажмите кнопку **Submit** (Отправить). 

20.  После указания требуемой конфигурации выберите **Добавить**.

После сохранения изменений пользователи в указанной службе Azure AD могут входить на портал разработчика, выполняя действия, которые описаны в разделе [Вход на портал разработчика с помощью учетной записи Azure Active Directory](#log_in_to_dev_portal).

## <a name="add-an-external-azure-ad-group"></a>Добавление внешней группы Azure AD

После включения доступа для пользователей в экземпляре Azure AD вы можете добавлять группы Azure AD в службе управления API, чтобы упростить управление связью разработчиков в такой группе с требуемыми продуктами.

 > [!IMPORTANT]
 > Чтобы добавить внешнюю группу Azure AD, необходимо сначала настроить экземпляр Azure AD на **удостоверения** вкладку, выполнив процедуру, описанную в предыдущем разделе. Кроме того, приложения должен быть предоставлен доступ к API Azure AD Graph с помощью `Directory.Read.All` разрешение. 

Внешние группы Azure AD добавляются с вкладки **Группы** в экземпляре службы управления API.

1. Выберите вкладку **Группы** .
2. Нажмите кнопку **Добавить группу AAD**.
   ![Кнопка "Добавить группу AAD"](./media/api-management-howto-aad/api-management-with-aad008.png)
3. Выберите группу, которую нужно добавить.
4. Нажмите кнопку **Выбрать**.

После добавления внешней группы Azure AD можно просмотреть и настроить ее свойства. Выберите имя группы на вкладке **Группы**. Здесь можно изменить **имя** и **описание** группы.
 
Пользователи из настроенного экземпляра Azure AD теперь могут входить на портал разработчика, просматривать любые группы, которые они могут видеть, и подписываться на них.

## <a name="a-idlogintodevportalsign-in-to-the-developer-portal-by-using-an-azure-ad-account"></a><a id="log_in_to_dev_portal"/>Вход на портал разработчика с помощью учетной записи Azure AD

Чтобы войти на портал разработчика с помощью учетной записи Azure AD, которая была настроена в предыдущих разделах:

1. Откройте новое окно браузера, используя URL-адрес входа из конфигурации приложения Active Directory, и выберите **Azure Active Directory**.

   ![Страница входа][api-management-dev-portal-signin]

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
