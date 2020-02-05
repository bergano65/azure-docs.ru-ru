---
title: Управление проверкой подлинности | Карты Microsoft Azure
description: Вы можете использовать портал Azure для управления проверкой подлинности в картах Microsoft Azure.
author: walsehgal
ms.author: v-musehg
ms.date: 01/29/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.openlocfilehash: f856aebe5e3acaca142e460d18ec8c6498b18787
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/04/2020
ms.locfileid: "76989310"
---
# <a name="manage-authentication-in-azure-maps"></a>Управление аутентификацией в Azure Maps

После создания учетной записи Azure Maps создаются идентификатор и ключи клиента для поддержки Azure Active Directory (Azure AD) и проверки подлинности с общим ключом.

## <a name="view-authentication-details"></a>Просмотр сведений об аутентификации

После создания учетной записи Azure Maps создаются первичный и вторичный ключи. При вызове Azure Maps с использованием [проверки подлинности с общим ключом](https://docs.microsoft.com/azure/azure-maps/azure-maps-authentication#shared-key-authentication)рекомендуется использовать первичный ключ в качестве ключа подписки. Вторичный ключ можно использовать в таких сценариях, как изменение ключевых изменений. Дополнительные сведения см. в статье [Проверка подлинности с помощью Azure Maps](https://aka.ms/amauth).

Сведения о проверке подлинности можно просмотреть на портал Azure. Перейдите к своей учетной записи и выберите **Проверка подлинности** в меню **Параметры** .

![Подробные сведения о проверке подлинности](./media/how-to-manage-authentication/how-to-view-auth.png)


## <a name="configure-azure-ad-app-registration"></a>Настройка регистрации Azure AD App

1. Выберите **Azure Active Directory** из списка служб Azure в портал Azure.  Выберите **Регистрация приложений** и щелкните **Новая регистрация**.  Далее Введите **имя**, выберите **тип учетной записи поддержки**и нажмите кнопку **зарегистрировать**.  Если у вас уже есть зарегистрированное приложение, перейдите к шагу 2. 

    ![Регистрация приложения](./media/how-to-manage-authentication/app-registration.png)

    ![Сведения о регистрации приложения](./media/how-to-manage-authentication/app-create.png)

2. Чтобы назначить разрешения делегированного API для Azure Maps, перейдите в приложение в разделе **Регистрация приложений**. Затем выберите **разрешения API**, а затем — **Добавить разрешение**. Найдите и выберите **Azure Maps** в разделе **API, используемые моей организацией**.

    ![Разрешения API приложений](./media/how-to-manage-authentication/app-permissions.png)

3. Проверьте **Azure Maps доступа** и нажмите кнопку **Добавить разрешения**.

    ![Выбор разрешений API приложения](./media/how-to-manage-authentication/select-app-permissions.png)

4. Выполните шаг a или b в зависимости от метода проверки подлинности. 

    1. Если приложение использует проверку подлинности на основе маркеров пользователя с помощью веб-пакета SDK Azure Maps, включите `oauth2AllowImplicitFlow`. Чтобы включить `oauth2AllowImplicitFlow`, задайте для него значение true в разделе манифеста регистрации приложения. 
    
       ![Манифест приложения](./media/how-to-manage-authentication/app-manifest.png)

    2. Если приложение использует проверку подлинности сервера или приложения, перейдите в колонку **сертификаты & секреты** на странице регистрации приложения и либо создайте пароль, щелкнув **новый секрет клиента** , либо отправьте сертификат открытого ключа в регистрацию приложения. Если вы создаете пароль, после нажатия кнопки **Добавить**Скопируйте пароль в дальнейшем и сохраните его в безопасном месте. Этот пароль будет использоваться для получения маркеров из Azure AD.

       ![Ключи приложения](./media/how-to-manage-authentication/app-keys.png)

       ![Добавить ключ](./media/how-to-manage-authentication/add-key.png)


## <a name="grant-role-based-access-control-rbac-to-azure-maps"></a>Предоставление управления доступом на основе ролей (RBAC) для Azure Maps

После связывания учетной записи Azure Maps с клиентом Azure AD можно предоставить контроль доступа. Управление доступом предоставляется путем назначения пользователю, группе или приложению одной или нескольких Azure Maps ролей контроля доступа.

1. Перейдите к **учетной записи Azure Maps**. Выберите **Управление доступом (IAM)** , а затем выберите **назначение ролей**.

    ![Предоставление RBAC](./media/how-to-manage-authentication/how-to-grant-rbac.png)

2. В окне **назначение ролей** в разделе **роль**выберите **Azure Maps средство чтения даты (Предварительная версия)** . В разделе **назначение доступа для** выбора **пользователя, группы или участника службы Azure AD**. Выберите пользователя или приложение. Щелкните **Сохранить**.

    ![Добавление назначения роли](./media/how-to-manage-authentication/add-role-assignment.png)

## <a name="view-available-azure-maps-rbac-roles"></a>Просмотр доступных ролей RBAC Azure Maps

Чтобы просмотреть роли управления доступом на основе ролей (RBAC), доступные для Azure Maps, перейдите в раздел **Управление доступом (IAM)** , выберите **роли**, а затем найдите роли, начиная с **Azure Maps**. Эти роли являются ролями, к которым можно предоставить доступ.

![Просмотр доступных ролей](./media/how-to-manage-authentication/how-to-view-avail-roles.png)


## <a name="view-azure-maps-rbac"></a>Просмотр Azure Maps RBAC

RBAC обеспечивает детализированный контроль доступа.

Чтобы просмотреть пользователей и приложения, которым было предоставлено значение RBAC для Azure Maps, перейдите в раздел **Управление доступом (IAM)** , выберите **назначения ролей**, а затем отфильтруйте по **Azure Maps**.

![Просмотр пользователей и приложений, которым предоставлено разрешение RBAC](./media/how-to-manage-authentication/how-to-view-amrbac.png)


## <a name="request-tokens-for-azure-maps"></a>Запрос маркеров для Azure Maps

После регистрации приложения и его связывания с Azure Maps можно запросить маркеры доступа.

* Если приложение использует проверку подлинности на основе маркеров пользователя с помощью веб-пакета SDK Azure Maps, необходимо настроить HTML-страницу, указав идентификатор клиента Azure Maps и идентификатор приложения Azure AD.

* Если приложение использует проверку подлинности сервера или приложения, необходимо запросить маркер из конечной точки маркера Azure AD, `https://login.microsoftonline.com` с ИДЕНТИФИКАТОРом ресурса Azure AD `https://atlas.microsoft.com/`, ИДЕНТИФИКАТОРом Azure Maps клиента, ИДЕНТИФИКАТОРом приложения Azure AD, паролем регистрации приложения Azure AD или сертификатом.

| Среда Azure   | Конечная точка маркера Azure AD | Идентификатор ресурса Azure |
| --------------------|-------------------------|-------------------|
| Azure Public        | https://login.microsoftonline.com | https://atlas.microsoft.com/ |
| Azure для государственных организаций    | https://login.microsoftonline.us  | https://atlas.microsoft.com/ | 

Дополнительные сведения о запросе маркеров доступа из Azure AD для пользователей и субъектов-служб см. в статье [сценарии проверки подлинности в Azure AD](https://docs.microsoft.com/azure/active-directory/develop/authentication-scenarios).


## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения об аутентификации Azure AD и веб-пакете SDK Azure Maps см. в статье [Использование библиотеки Map Control в службе Azure Maps](https://docs.microsoft.com/azure/azure-maps/how-to-use-map-control).

Узнайте, как просматривать метрики использования API для учетной записи Azure Maps:
> [!div class="nextstepaction"] 
> [Просмотр метрик использования](how-to-view-api-usage.md)

Список примеров, демонстрирующих интеграцию Azure Active Directory (AAD) с Azure Maps, см. в следующих статьях:

> [!div class="nextstepaction"]
> [Примеры проверки подлинности Azure AD](https://github.com/Azure-Samples/Azure-Maps-AzureAD-Samples)
