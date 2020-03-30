---
title: Управление аутентификацией Карты Microsoft Azure
description: Используйте портал Azure для управления аутентификацией в Microsoft Azure Maps.
author: philmea
ms.author: philmea
ms.date: 01/29/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.openlocfilehash: dfe73971f29ea362fdd0ddd654e705b622ab1866
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80335525"
---
# <a name="manage-authentication-in-azure-maps"></a>Управление аутентификацией в Azure Maps

После создания учетной записи Azure Maps для поддержки активации функционального каталога Azure Active Directory (Azure AD) и проверки подлинности общих ключей создаются идентификаторы клиента и ключи.

## <a name="view-authentication-details"></a>Просмотр сведений об аутентификации

После создания учетной записи Azure Maps создаются основные и вторичные ключи. Мы рекомендуем использовать основной ключ в качестве ключа подписки при [использовании проверки подлинности Shared Key для вызова Azure Maps.](https://docs.microsoft.com/azure/azure-maps/azure-maps-authentication#shared-key-authentication) Можно использовать вторичный ключ в таких сценариях, как изменение ключа. Для получения дополнительной информации смотрите [аутентификацию в Azure Maps](https://aka.ms/amauth).

Вы можете просматривать данные аутентификации на портале Azure. Там, в вашем аккаунте, в меню **«Настройки»,** выберите **аутентификацию.**

![Подробные сведения о проверке подлинности](./media/how-to-manage-authentication/how-to-view-auth.png)


## <a name="register-and-configure-an-azure-ad-app"></a>Регистрация и настройка приложения Azure AD

1. На портале Azure, в списке сервисов Azure, выберите **регистрацию приложения Azure Active Directory** > **App.** > **New registration**  

    ![Регистрация приложения](./media/how-to-manage-authentication/app-registration.png)

1. Если вы уже зарегистрировали приложение, продолжайте делать следующий шаг. Если вы еще не зарегистрировали приложение, введите **name,** выберите **тип учетной записи поддержки,** а затем выберите **Регистрацию.**  

    ![Регистрационные данные приложения](./media/how-to-manage-authentication/app-create.png)

1. Чтобы назначить делегированные разрешения API на Azure Maps, перейдите в приложение. Затем при **регистрации приложений**выберите **разрешения** > API**Добавление разрешения.** В **apIs моя организация использует,** поиск и выбор **Azure Maps**.

    ![Добавление разрешений API приложения](./media/how-to-manage-authentication/app-permissions.png)

1. Выберите флажок рядом с **картами доступа Azure,** а затем выберите **разрешения «Добавить».**

    ![Выберите разрешения API приложения](./media/how-to-manage-authentication/select-app-permissions.png)

1. Выполните один из следующих шагов, в зависимости от метода проверки подлинности. 

    * Если приложение использует проверку подлинности маркеров пользователей с помощью `oauth2AllowImplicitFlow`Web SDK Azure Maps, включите возможность включения. Чтобы включить его, в разделе **Манифест** регистрации приложения, установить `oauth2AllowImplicitFlow` на истину. 
    
       ![Манифест приложения](./media/how-to-manage-authentication/app-manifest.png)

    * Если приложение использует проверку подлинности сервера или приложения, то на странице регистрации приложения перейдите на **сертификаты & секреты.** Затем либо загрузить открытый сертификат ключа или создать пароль, выбрав **новый секрет клиента**. 
    
       ![Создание секрета клиента](./media/how-to-manage-authentication/app-keys.png)

        Если вы создаете пароль, то после того, как вы выберете **Добавить,** скопировать пароль и хранить его безопасно. Этот пароль можно использовать для получения токенов из Azure AD.

       ![Добавить секрет клиента](./media/how-to-manage-authentication/add-key.png)


## <a name="grant-role-based-access-control-to-azure-maps"></a>Управление доступом на основе роли в Azure Maps

После того, как вы свяжетесь с учетной записью Azure Maps с вашим арендатором Azure AD, вы можете предоставить элемент управления доступом. Вы предоставляете *элементанный контроль доступа* (RBAC), назначая пользователя, группу или приложение одной или нескольких ролей управления доступом Azure Maps. 

1. Перейдите к **учетной записи Azure Maps.** Выберите**назначение** **ролей управления доступом (IAM)** > .

    ![Предоставление RBAC](./media/how-to-manage-authentication/how-to-grant-rbac.png)

1. На **вкладке «Задания ролей»** под **«Роль»** выберите **«Разносторонний разчитыватель дат» (Preview)**— это средство для чтения с датаом Azure Maps . В поле **Назначение доступа к** выберите **Пользователь, группа или субъект-служба Azure AD**. Выберите пользователя или приложение. Затем выберите **Сохранить**.

    ![Добавление назначения роли](./media/how-to-manage-authentication/add-role-assignment.png)

## <a name="view-available-azure-maps-rbac-roles"></a>Просмотр доступных ролей RBAC Azure Maps

Для просмотра ролей RBAC, доступных для Azure Maps, перейдите на **управление доступом (IAM)**. Выберите **роли,** а затем ищите роли, которые начинаются с *Azure Maps.* Эти роли Azure Maps — это роли, к которым можно предоставить доступ.

![Просмотр доступных ролей](./media/how-to-manage-authentication/how-to-view-avail-roles.png)


## <a name="view-azure-maps-rbac"></a>Посмотреть Карты Лазурного RBAC

RBAC обеспечивает детальный контроль доступа.

Для просмотра пользователей и приложений, которые были предоставлены RBAC для Azure Maps, перейдите на **доступ к управлению доступом (IAM)**. Там выберите **назначения ролей,** а затем отфильтруйте по **Azure Maps.**

![Просмотр пользователей и приложений, которым предоставлен RBAC](./media/how-to-manage-authentication/how-to-view-amrbac.png)


## <a name="request-tokens-for-azure-maps"></a>Запрос маркеров для Azure Maps

После регистрации приложения и ассоциировать его с Azure Maps можно запросить токены доступа.

Если приложение использует проверку подлинности пользовательских токенов с помощью Web SDK Azure Maps, назначьте свою страницу HTML с идентификатором клиента Azure Maps и идентификатором приложения Azure AD.

Если приложение использует проверку подлинности сервера или приложения, запросите маркер `https://login.microsoftonline.com`из точки конечных токенов Azure AD. В своем запросе используйте следующие сведения: 

* Идентификатор ресурса Azure AD`https://atlas.microsoft.com/`
* Идентификатор клиента Azure Maps
* Идентификатор приложения Azure AD
* Пароль или сертификат регистрации приложения Azure AD

| Окружающая среда Azure   | Конечная точка маркера Azure AD | Идентификатор ресурса Azure |
| --------------------|-------------------------|-------------------|
| Общедоступное облако Azure        | `https://login.microsoftonline.com` | `https://atlas.microsoft.com/` |
| Облако правительства Azure   | `https://login.microsoftonline.us`  | `https://atlas.microsoft.com/` | 

Для получения дополнительной информации о запросе токенов доступа из Azure AD для пользователей и основ служб смотрите [сценарии аутентификации для Azure AD.](https://docs.microsoft.com/azure/active-directory/develop/authentication-scenarios)


## <a name="next-steps"></a>Дальнейшие действия

Для получения дополнительной информации смотрите [Azure AD и Azure Maps Web SDK](https://docs.microsoft.com/azure/azure-maps/how-to-use-map-control).

Найдите метрики использования API для учетной записи Azure Maps:
> [!div class="nextstepaction"] 
> [Просмотр метрик использования](how-to-view-api-usage.md)

Исследуйте образцы, которые показывают, как интегрировать Azure AD с Картами Azure:

> [!div class="nextstepaction"]
> [Образцы аутентификации Azure AD](https://github.com/Azure-Samples/Azure-Maps-AzureAD-Samples)
