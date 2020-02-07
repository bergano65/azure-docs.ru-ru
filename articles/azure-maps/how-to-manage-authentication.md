---
title: Управление проверкой подлинности | Карты Microsoft Azure
description: Используйте портал Azure для управления проверкой подлинности в картах Microsoft Azure.
author: walsehgal
ms.author: v-musehg
ms.date: 01/29/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.openlocfilehash: 30eb637e9117818758ed4ab0e3adef9db29cc698
ms.sourcegitcommit: db2d402883035150f4f89d94ef79219b1604c5ba
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/07/2020
ms.locfileid: "77057317"
---
# <a name="manage-authentication-in-azure-maps"></a>Управление аутентификацией в Azure Maps

После создания учетной записи Azure Maps создаются идентификатор клиента и ключи для поддержки проверки подлинности Azure Active Directory (Azure AD) и аутентификации с общим ключом.

## <a name="view-authentication-details"></a>Просмотр сведений об аутентификации

После создания учетной записи Azure Maps создаются первичный и вторичный ключи. Рекомендуется использовать первичный ключ в качестве ключа подписки при [использовании проверки подлинности с общим ключом для вызова Azure Maps](https://docs.microsoft.com/azure/azure-maps/azure-maps-authentication#shared-key-authentication). Вторичный ключ можно использовать в таких сценариях, как изменение ключевых изменений. Дополнительные сведения см. [в разделе Проверка подлинности в Azure Maps](https://aka.ms/amauth).

Сведения о проверке подлинности можно просмотреть в портал Azure. В учетной записи в меню **Параметры** выберите **Проверка подлинности**.

![Подробные сведения о проверке подлинности](./media/how-to-manage-authentication/how-to-view-auth.png)


## <a name="register-and-configure-an-azure-ad-app"></a>Регистрация и настройка приложения Azure AD

1. В портал Azure в списке служб Azure выберите **Azure Active Directory** > **Регистрация приложений** > **Новая регистрация**.  

    ![Регистрация приложения](./media/how-to-manage-authentication/app-registration.png)

1. Если вы уже зарегистрировали приложение, перейдите к следующему шагу. Если вы не зарегистрировали приложение, введите **имя**, выберите **тип учетной записи поддержки**и нажмите кнопку **зарегистрировать**.  

    ![Сведения о регистрации приложения](./media/how-to-manage-authentication/app-create.png)

1. Чтобы назначить разрешения делегированного API для Azure Maps, перейдите в приложение. Затем в разделе **Регистрация приложений**выберите **разрешения API** > **Добавить разрешение**. В разделе API, которые **использует Моя организация**, найдите и выберите **Azure Maps**.

    ![Добавить разрешения API приложения](./media/how-to-manage-authentication/app-permissions.png)

1. Установите флажок для **доступа к Azure Maps**, а затем выберите **Добавить разрешения**.

    ![Выбор разрешений API приложения](./media/how-to-manage-authentication/select-app-permissions.png)

1. Выполните одно из следующих действий в зависимости от метода проверки подлинности. 

    * Если приложение использует проверку подлинности на основе маркеров пользователя с помощью веб-пакета SDK Azure Maps, включите `oauth2AllowImplicitFlow`. Чтобы включить его, в разделе **манифеста** регистрации приложения установите `oauth2AllowImplicitFlow` в значение true. 
    
       ![Манифест приложения](./media/how-to-manage-authentication/app-manifest.png)

    * Если приложение использует проверку подлинности сервера или приложения, то на странице регистрации приложения перейдите в раздел **сертификаты & секреты**. Затем либо отправьте сертификат открытого ключа, либо создайте пароль, выбрав **новый секрет клиента**. 
    
       ![Создание секрета клиента](./media/how-to-manage-authentication/app-keys.png)

        Если вы создаете пароль, после нажатия на кнопку **Добавить**Скопируйте пароль и сохраните его в безопасном месте. Этот пароль будет использоваться для получения маркеров из Azure AD.

       ![Добавление секрета клиента](./media/how-to-manage-authentication/add-key.png)


## <a name="grant-role-based-access-control-to-azure-maps"></a>Предоставление Azure Maps управления доступом на основе ролей

После связывания учетной записи Azure Maps с клиентом Azure AD можно предоставить контроль доступа. Вы предоставляете *Управление доступом на основе ролей* (RBAC), назначив пользователю, группе или приложению одну или несколько Azure Maps ролей контроля доступа. 

1. Перейдите к **учетной записи Azure Maps**. Выберите **Управление доступом (IAM)**  > **назначение ролей**.

    ![Предоставление RBAC](./media/how-to-manage-authentication/how-to-grant-rbac.png)

1. На вкладке **назначения ролей** в разделе **роль**выберите **Azure Maps средство чтения даты (Предварительная версия)** . В поле **Назначение доступа к** выберите **Пользователь, группа или субъект-служба Azure AD**. Выберите пользователя или приложение. Затем нажмите кнопку **Save** (Сохранить).

    ![Добавление назначения роли](./media/how-to-manage-authentication/add-role-assignment.png)

## <a name="view-available-azure-maps-rbac-roles"></a>Просмотр доступных ролей RBAC Azure Maps

Чтобы просмотреть роли RBAC, доступные для Azure Maps, перейдите в раздел **Управление доступом (IAM)** . Выберите **роли**, а затем найдите роли, которые начинаются с *Azure Maps*. Эти Azure Maps роли являются ролями, к которым можно предоставить доступ.

![Просмотр доступных ролей](./media/how-to-manage-authentication/how-to-view-avail-roles.png)


## <a name="view-azure-maps-rbac"></a>Просмотр Azure Maps RBAC

RBAC обеспечивает детализированный контроль доступа.

Чтобы просмотреть пользователей и приложения, которым было предоставлено значение RBAC для Azure Maps, перейдите в раздел **Управление доступом (IAM)** . Выберите **назначения ролей**, а затем выполните фильтрацию по **Azure Maps**.

![Просмотр пользователей и приложений, которым было предоставлено разрешение RBAC](./media/how-to-manage-authentication/how-to-view-amrbac.png)


## <a name="request-tokens-for-azure-maps"></a>Запрос маркеров для Azure Maps

После регистрации приложения и связывания его с Azure Maps можно запросить маркеры доступа.

Если приложение использует проверку подлинности на основе маркеров пользователя с помощью веб-пакета SDK Azure Maps, настройте HTML-страницу, указав идентификатор клиента Azure Maps и идентификатор приложения Azure AD.

Если приложение использует проверку подлинности сервера или приложения, запросите маркер из конечной точки маркера Azure AD `https://login.microsoftonline.com`. В запросе используйте следующие сведения: 

* Идентификатор ресурса Azure AD `https://atlas.microsoft.com/`
* Идентификатор клиента Azure Maps
* Идентификатор приложения Azure AD
* Пароль или сертификат регистрации приложения Azure AD

| Среда Azure   | Конечная точка маркера Azure AD | Идентификатор ресурса Azure |
| --------------------|-------------------------|-------------------|
| Общедоступное облако Azure        | `https://login.microsoftonline.com` | `https://atlas.microsoft.com/` |
| Облако Azure для государственных организаций   | `https://login.microsoftonline.us`  | `https://atlas.microsoft.com/` | 

Дополнительные сведения о запросе маркеров доступа из Azure AD для пользователей и субъектов-служб см. в статье [сценарии проверки подлинности в Azure AD](https://docs.microsoft.com/azure/active-directory/develop/authentication-scenarios).


## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения см. в статье [Azure AD и Azure Maps веб-пакет SDK](https://docs.microsoft.com/azure/azure-maps/how-to-use-map-control).

Найдите метрики использования API для учетной записи Azure Maps:
> [!div class="nextstepaction"] 
> [Просмотр метрик использования](how-to-view-api-usage.md)

Просмотрите примеры, демонстрирующие интеграцию Azure AD с Azure Maps:

> [!div class="nextstepaction"]
> [Примеры проверки подлинности Azure AD](https://github.com/Azure-Samples/Azure-Maps-AzureAD-Samples)
