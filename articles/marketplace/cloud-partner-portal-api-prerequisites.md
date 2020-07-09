---
title: Необходимые условия для API — Azure Marketplace
description: Необходимые условия для использования API-интерфейсов Портал Cloud Partner.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/08/2020
ms.openlocfilehash: c932db1b86cecae4d90b0d6acffc4a0cde49e369
ms.sourcegitcommit: d7008edadc9993df960817ad4c5521efa69ffa9f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/08/2020
ms.locfileid: "86115746"
---
<a name="api-prerequisites"></a>Предварительные требования для API
================

> [!NOTE]
> Программные интерфейсы Портала Cloud Partner интегрированы с Центром партнеров и продолжат работать после миграции предложений в Центр партнеров. Интеграция включает небольшие изменения. Ознакомьтесь с изменениями, приведенными в [справочнике по портал Cloud Partner API](./cloud-partner-portal-api-overview.md) , чтобы убедиться, что код будет продолжать работать после перехода в центр партнеров.

Существует два программных ресурса, которые необходимы, чтобы использовать API Портала Cloud Partner: субъект-служба и маркер доступа Azure Active Directory (Azure AD).


<a name="create-a-service-principal-in-your-azure-active-directory-tenant"></a>Создание субъекта-службы в клиенте Azure Active Directory
----------------------------------------------------------------

Сначала необходимо создать субъект-службу в клиенте Azure Active Directory. Этому клиенту будет назначен собственный набор разрешений на Портале Cloud Partner. Ваш код будет вызывать эти API, используя этот клиент вместо персональных учетных данных.  Полное описание создания субъекта-службы см. в статье [как использовать портал для создания приложения Azure AD и субъекта-службы, которые имеют доступ к ресурсам](../active-directory/develop/howto-create-service-principal-portal.md).


<a name="add-the-service-principal-to-your-account"></a>Добавление субъекта-службы в учетную запись
-----------------------------------------

Теперь, когда вы создали субъект-службу в клиенте, его можно добавить в качестве пользователя в учетную запись Портала Cloud Partner. Как и пользователь, субъект-служба может быть владельцем или участником на портале.

Для добавления субъекта-службы выполните приведенные ниже действия:

1. Войдите на Портал Cloud Partner. 
2. В строке меню слева щелкните **Пользователи** и выберите **Добавить пользователя**.

   ![Добавление пользователя на портале](./media/cloud-partner-portal-api-prerequisites/add-user.jpg)

3. В раскрывающемся списке **Тип** выберите **Субъект-служба** и добавьте следующие данные:

-   **Понятное имя** для субъекта-службы, например `spAccount`.
-   **Идентификатор приложения**. Чтобы найти этот идентификатор, перейдите в [портал Azure](https://portal.azure.com), щелкните **Azure Active Directory**, выберите **Регистрация приложений**и щелкните свое приложение.
-   **Идентификатор клиента**, также известный как **идентификатор каталога**, для клиента Azure AD. Этот идентификатор можно найти на странице Azure Active Directory [портала Azure](https://portal.azure.com) в разделе **Свойства**.
-   **Идентификатор объекта** субъекта-службы. Этот идентификатор можно получить на портале Azure. Перейдите к **Azure Active Directory**, щелкните **Регистрация приложений**, выберите свое приложение и щелкните имя приложения в разделе **Управляемое приложение в локальном каталоге**. Перейдите на страницу **Свойства**, чтобы найти идентификатор объекта. Убедитесь, что вы копируете не начальный идентификатор объекта, который находится в приложении, а идентификатор объекта в управляемом приложении.
-   **Роль**, связанная с учетной записью, которая будет использоваться для RBAC.

     ![Добавление управляемого приложения на портале](./media/cloud-partner-portal-api-prerequisites/managed-app.png)

1. Щелкните **Добавить**, чтобы добавить субъект-службу в учетную запись.

   ![Добавление субъекта-службы](./media/cloud-partner-portal-api-prerequisites/add-service-principal.jpg)


<a name="get-an-azure-ad-access-token"></a>Получение токена доступа Azure AD
----------------------------

Во время проверки подлинности программные интерфейсы Портала Cloud Partner используют следующие ресурсы:

- маркер носителя JSON Web Token (JWT) для запроса доступа к ресурсам;
- протокол [OpenID Connect](https://openid.net/connect/) (OIDC) для проверки удостоверения;
- [Azure Active Directory (Azure AD)](../active-directory/fundamentals/active-directory-whatis.md) в качестве поставщика удостоверений.

Существует два принципиальных подхода к программному получению маркера JWT:

- Использование библиотеки проверки подлинности Майкрософт для .NET ([MSAL.NET](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet)).  Это подход более высокого уровня, который рекомендуется для разработчиков .NET. 
- Отправьте запрос **HTTP POST** к конечной точке **маркера** аутентификации Azure AD, который принимает такую форму:

``` HTTP
POST https://login.microsoftonline.com/<tenant-id>/oauth2/token
    client_id: <application-id>
    client_secret:<application-secret>
    grant_type: client_credentials
    resource: https://cloudpartner.azure.com
```

Теперь этот маркер можно передать как часть заголовка авторизации для запросов API.

``` HTTP
GET https://cloudpartner.azure.com/api/offerTypes?api-version=2016-08-01-preview 
    Accept: application/json
    Authorization: Bearer <access-token>
```
> [!NOTE]
> Во всех API в этом справочнике предполагается, что заголовок авторизации всегда передается, поэтому об этом не упоминается явно.

В случае возникновения ошибок проверки подлинности в запросе ознакомьтесь со статьей [Troubleshooting common authentication errors](./cloud-partner-portal-api-troubleshooting-authentication-errors.md) (Устранение распространенных ошибок проверок подлинности).
