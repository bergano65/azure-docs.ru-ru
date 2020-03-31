---
title: Авторизовать учетные записи разработчиков с помощью active-каталога Azure
titleSuffix: Azure API Management
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
ms.openlocfilehash: 6102b1e1d6ddbac01033b9cecfeba96a7eb33777
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79473546"
---
# <a name="authorize-developer-accounts-by-using-azure-active-directory-in-azure-api-management"></a>Авторизация учетных записей разработчиков с помощью Azure Active Directory в управлении API Azure

В этой статье показывается, как включить доступ к порталу разработчика для пользователей из каталога Active Directory Azure (Azure AD). В этом руководстве также показано, как управлять группами пользователей Azure AD путем добавления внешних групп, содержащих пользователей.

## <a name="prerequisites"></a>Предварительные требования

- Выполните задачи в кратком руководстве по [созданию экземпляра службы управления API Azure](get-started-create-service-instance.md).
- Импортируйте и опубликуйте экземпляр службы управления API Azure. Дополнительные сведения см. в статье об [импорте и публикации](import-and-publish.md).

[!INCLUDE [premium-dev-standard.md](../../includes/api-management-availability-premium-dev-standard.md)]

## <a name="authorize-developer-accounts-by-using-azure-ad"></a>Авторизация учетных записей разработчиков с помощью Azure AD

1. Войдите на [портал Azure](https://portal.azure.com). 
2. Выберите пункт ![Стрелка](./media/api-management-howto-aad/arrow.png).
3. В поле поиска введите **api**.
4. Выберите **услуги управления API**.
5. Выберите экземпляр службы управления API.
6. Под **безопасностью**выберите **идентификационные данные.**
7. Выберите **+ Добавить** в верхней части экрана.

    Справа отобразится область **Добавление поставщика удостоверений**.
8. В разделе **Тип поставщика** выберите **Azure Active Directory**.

    В области отобразятся элементы управления, необходимые для ввода других требуемых сведений. Элементы управления включают **идентификатор клиента** и **секрет клиента**. (Эти элементы управления описаны далее в этой статье.)
9. Сделать заметку о содержании **URL-адреса Redirect**.
    
   ![Шаги для добавления поставщика удостоверений на портале Azure](./media/api-management-howto-aad/api-management-with-aad001.png)  
10. В браузере откройте другую вкладку. 
11. Перейдите на [портал Azure - Регистрация приложений](https://go.microsoft.com/fwlink/?linkid=2083908) для регистрации приложения в Active Directory.
12. Под **управлением**, выберите **регистрации приложений**.
13. Выберите **Новая регистрация**. На странице **заявки Регистра** установить значения следующим образом:
    
    * Установите **имя** к значимому имени. например, *разработчик-портал*
    * Установите **типы поддерживаемых учетных записей** только **в этом каталоге организации.** 
    * Установите **перенаправить URI** к значению, вы получили от шага 9. 
    * Выберите **Регистрацию**. 

14.  После регистрации приложения скопируйте **идентификатор приложения (клиента)** со страницы **"Обзор".** 
15. Вернитесь к экземпляру Управления API. В окне **поставщика идентификационных** данных Add вставьте значение **идентификатора приложения (клиента)** в поле **идентификации клиента.**
16. Вернитесь к конфигурации Azure AD, выберите **сертификаты & секреты** под **управлением.** Нажмите кнопку **New client secret** (Новый секрет клиента). Введите значение в поле **Описание**, выберите любой параметр в области **Срок действия истекает** и щелкните **Добавить**. Копируйте секретное значение клиента перед выходом из страницы. Они понадобятся вам на следующем шаге. 
17. Под **управлением**, выберите **аутентификацию,** а затем выберите **токены ID** под **неявной грант**
18. Вернитесь к своему экземпляру API Management, вставьте секрет в **клиентскую тайную** коробку.

    > [!IMPORTANT]
    > Обязательно обновите **секрет клиента** до истечения срока действия ключа. 
    >  
    >

19. В окне **Добавление поставщика удостоверений** также содержится текстовое поле **Разрешенные клиенты**. Укажите домены экземпляров Azure AD, которым требуется предоставить доступ к API экземпляра службы управления API. Несколько доменов можно разделять символами начала новой строки, пробелами или запятыми.

    > [!NOTE]
    > В разделе **Разрешенные клиенты** можно указать несколько доменов. Прежде чем какой-либо пользователь сможет выполнить вход из домена, отличного от домена, в котором было зарегистрировано приложение, глобальный администратор этого другого домена должен предоставить разрешение для доступа приложения к данным каталога. Чтобы предоставить разрешение, глобальный администратор должен: a. Перейдите на страницу `https://<URL of your developer portal>/aadadminconsent` (например, https://contoso.portal.azure-api.net/aadadminconsent).
    > b. Ввести имя домена клиента Azure AD, к которому требуется предоставить доступ.
    > c. Нажмите кнопку **Отправить**. 

20.  После указания требуемой конфигурации выберите **Добавить**.

После сохранения изменений пользователи в указанной службе Azure AD могут входить на портал разработчика, выполняя действия, которые описаны в разделе [Вход на портал разработчика с помощью учетной записи Azure Active Directory](#log_in_to_dev_portal).

## <a name="add-an-external-azure-ad-group"></a>Добавление внешней группы Azure AD

После включения доступа для пользователей в адеп-арендаторе Azure AD можно добавить группы Azure AD в управление API. В результате можно контролировать видимость продукта с помощью групп Azure AD.

Чтобы добавить внешнюю группу Azure AD в APIM, необходимо сначала завершить предыдущий раздел. Кроме того, зарегистрированное приложение должно получить доступ к `Directory.Read.All` API Microsoft Graph с разрешением, выдвивая следующие действия: 

1. Вернитесь к регистрации приложений, которая была создана в предыдущем разделе.
2. Выберите **разрешения API,** а затем нажмите **кнопку «Добавить разрешение**». 
3. В панели **разрешений API Запроса** выберите вкладку **Microsoft API,** а затем выберите плитку **Microsoft Graph.** Выберите **разрешения приложения,** ищите **каталог,** а затем выберите **разрешение Directory.Read.All.** 
4. Нажмите **Накнопка разрешений добавить** в нижней части панели, а затем нажмите **Грант админ согласия на "tenantname",** так что вы предоставляете доступ для всех пользователей в этом каталоге. 

Теперь можно добавить внешние группы Azure AD из вкладки **группы** экземпляра Управления API.

1. Выберите вкладку **Группы** .
2. Нажмите кнопку **Добавить группу AAD**.
    ![Кнопка "Добавить группу AAD"](./media/api-management-howto-aad/api-management-with-aad008.png)
3. Выберите группу, которую нужно добавить.
4. Нажмите кнопку **Выбрать**.

После добавления внешней группы Azure AD можно просмотреть и настроить ее свойства. Выберите название группы из вкладки **Группы.** Отсюда вы можете отсюда отодвинет информацию об **имени** и **описании** для группы.
 
Пользователи из настроенного экземпляра Azure AD теперь могут входить на портал разработчика, просматривать любые группы, которые они могут видеть, и подписываться на них.

## <a name="developer-portal---add-azure-ad-account-authentication"></a><a id="log_in_to_dev_portal"/>Портал разработчиков - добавьте аутентификацию учетной записи Azure AD

На портале разработчиков, входе в систему с AAD можно с **кнопкой вввоза: Виджет OAuth.** Виджет уже включен на странице ввне страницы содержимого портала разработчика по умолчанию.

Хотя новая учетная запись будет автоматически создаваться всякий раз, когда новый пользователь впишется в AAD, вы можете добавить тот же виджет на страницу регистрации.

**Форма регистрации: виджет OAuth** представляет форму, используемую для регистрации с OAuth.

> [!IMPORTANT]
> Необходимо [переиздать портал](api-management-howto-developer-portal-customize.md#publish) для встыковок изменений AAD.

## <a name="legacy-developer-portal---how-to-sign-in-with-azure-ad"></a>Портал разработчиков Legacy - как войти в систему с Azure AD

[!INCLUDE [api-management-portal-legacy.md](../../includes/api-management-portal-legacy.md)]

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

[Prerequisites]: #prerequisites
[Configure an OAuth 2.0 authorization server in API Management]: #step1
[Configure an API to use OAuth 2.0 user authorization]: #step2
[Test the OAuth 2.0 user authorization in the Developer Portal]: #step3
[Next steps]: #next-steps

[Sign in to the developer portal by using an Azure AD account]: #Sign-in-to-the-developer-portal-by-using-an-Azure-AD-account
