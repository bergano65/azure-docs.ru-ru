---
title: Проверка подлинности и авторизация с помощью API в Azure Time Series Insights | Документация Майкрософт
description: В этой статье описывается настройка аутентификации и авторизации для пользовательского приложения, которое вызывает API "Аналитика временных рядов Azure".
ms.service: time-series-insights
services: time-series-insights
author: ashannon7
ms.author: dpalled
manager: cshankar
ms.reviewer: v-mamcge, jasonh, kfile
ms.devlang: csharp
ms.workload: big-data
ms.topic: conceptual
ms.date: 05/07/2019
ms.custom: seodec18
ms.openlocfilehash: 876f24581badb20e01271f88cb9b51b470718721
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/17/2019
ms.locfileid: "67164535"
---
# <a name="authentication-and-authorization-for-azure-time-series-insights-api"></a>Проверка подлинности и авторизация для API Azure Time Series Insights

В этой статье объясняется, как настроить аутентификацию и авторизацию для пользовательского приложения, которое вызывает API "Аналитика временных рядов Azure".

> [!TIP]
> Узнайте о [предоставление доступа к данным](./time-series-insights-data-access.md) в среду Time Series Insights в Azure Active Directory.

## <a name="service-principal"></a>Субъект-служба

В следующих разделах рассматривается настройка приложения для доступа к API Time Series Insights для приложения. Приложение можно будет запрашивать или публиковать эталонные данные в среде Time Series Insights с помощью учетных данных приложения, а не учетные данные пользователя.

## <a name="best-practices"></a>Рекомендации

При наличии приложения, которое необходимо доступа Time Series Insights:

1. Настройка приложения Azure Active Directory.
1. [Назначить политики доступа к данным](./time-series-insights-data-access.md) в среде Time Series Insights.

С помощью учетных данных приложения, а не свои учетные данные пользователя является предпочтительным, так как:

* Можно назначить разрешения для удостоверения приложения, отличаются от ваших разрешений. Как правило, приложение получает именно те разрешения, которые требуются для его работы. Например, можно разрешить приложению только читать данные в определенной среде Time Series Insights.
* Не требуется изменять учетные данные приложения в случае изменения ваших обязанностей.
* Чтобы автоматизировать аутентификацию при выполнении автоматического скрипта можно использовать сертификат или ключ приложения.

Ниже показано, как сделать с помощью портала Azure. В статье уделяется однотенантного приложения, где приложение должно выполняться в одной организации. Обычно однотенантная архитектура используется для создания бизнес-приложений в рамках организации.

## <a name="setup-summary"></a>Сводка установки

Процесс установки состоит из трех этапов:

1. Создайте приложение в Azure Active Directory.
1. Предоставьте этому приложению доступ к среде Time Series Insights.
1. Используйте идентификатор приложения и ключ, чтобы получить маркер из `https://api.timeseries.azure.com/`. Затем маркер может использоваться для вызова Time Series Insights API.

## <a name="detailed-setup"></a>Об установке

1. На портале Azure выберите **Azure Active Directory** > **Регистрация приложений** > **Регистрация нового приложения**.

   [![Регистрация нового приложения в Azure Active Directory](media/authentication-and-authorization/active-directory-new-application-registration.png)](media/authentication-and-authorization/active-directory-new-application-registration.png#lightbox)

1. Присвойте приложению имя, выберите тип быть **веб-приложение или API**, выберите любой допустимый URI для **URL-адрес входа**и выберите **создать**.

   [![Создайте приложение в Azure Active Directory](media/authentication-and-authorization/active-directory-create-web-api-application.png)](media/authentication-and-authorization/active-directory-create-web-api-application.png#lightbox)

1. Выберите созданное приложение и скопируйте его идентификатор в предпочитаемом текстовом редакторе.

   [![Скопируйте идентификатор приложения](media/authentication-and-authorization/active-directory-copy-application-id.png)](media/authentication-and-authorization/active-directory-copy-application-id.png#lightbox)

1. Выберите **ключи**, введите имя ключа, выберите срок действия и выберите **Сохранить**.

   [![Выбор ключей приложения](media/authentication-and-authorization/active-directory-application-keys.png)](media/authentication-and-authorization/active-directory-application-keys.png#lightbox)

   [![Введите имя ключа и истечения срока действия и нажмите Save](media/authentication-and-authorization/active-directory-application-keys-save.png)](media/authentication-and-authorization/active-directory-application-keys-save.png#lightbox)

1. Скопируйте ключ в предпочитаемый текстовый редактор.

   [![Скопируйте ключ приложения](media/authentication-and-authorization/active-directory-copy-application-key.png)](media/authentication-and-authorization/active-directory-copy-application-key.png#lightbox)

1. Для среды Time Series Insights, выберите **политики доступа к данным** и выберите **добавить**.

   [![Добавить новую политику доступа данных в среду Time Series Insights](media/authentication-and-authorization/time-series-insights-data-access-policies-add.png)](media/authentication-and-authorization/time-series-insights-data-access-policies-add.png#lightbox)

1. В **Выбор пользователя** диалоговое окно, вставьте имя приложения из шага 2 или идентификатор приложения из шага 3.

   [![Поиск приложения в диалоговом окне Выбор пользователей](media/authentication-and-authorization/time-series-insights-data-access-policies-select-user.png)](media/authentication-and-authorization/time-series-insights-data-access-policies-select-user.png#lightbox)

1. Выберите роль. Выберите **чтения** выполнять запросы к данным или **участник** для запроса данных и изменения эталонных данных. Нажмите кнопку **ОК**.

   [![Выбрать читателя или участника в диалоговом окне Выбор роли пользователя](media/authentication-and-authorization/time-series-insights-data-access-policies-select-role.png)](media/authentication-and-authorization/time-series-insights-data-access-policies-select-role.png#lightbox)

1. Сохранить политику, выбрав **ОК**.

1. Используйте идентификатор приложения из шага 3 и ключ приложения на шаге 5, чтобы получить маркер для приложения. Маркер затем может передаваться в `Authorization` заголовка, когда приложение вызывает Time Series Insights API.

    Если вы используете C#, можно использовать следующий код, чтобы получить маркер для приложения. Полный пример см. в статье [Запрос данных из среды Azure Time Series Insights с помощью C##](time-series-insights-query-data-csharp.md).

    ```csharp
    // Enter your Active Directory tenant domain name
    var tenant = "YOUR_AD_TENANT.onmicrosoft.com";
    var authenticationContext = new AuthenticationContext(
        $"https://login.microsoftonline.com/{tenant}",
        TokenCache.DefaultShared);

    AuthenticationResult token = await authenticationContext.AcquireTokenAsync(
        // Set the resource URI to the Azure Time Series Insights API
        resource: "https://api.timeseries.azure.com/",
        clientCredential: new ClientCredential(
            // Application ID of application registered in Azure Active Directory
            clientId: "YOUR_APPLICATION_ID",
            // Application key of the application that's registered in Azure Active Directory
            clientSecret: "YOUR_CLIENT_APPLICATION_KEY"));

    string accessToken = token.AccessToken;
    ```

Используйте **идентификатор приложения** и **ключ** в приложении для проверки подлинности с помощью Azure Time Series Insight.

## <a name="next-steps"></a>Дальнейшие действия

- Пример кода, который вызывает API Time Series Insights, см. в статье [Запрос данных из среды Azure Time Series Insights с помощью C##](time-series-insights-query-data-csharp.md).
- Справочные сведения об API см. в статье об [API запросов к службе "Аналитика временных рядов Azure"](/rest/api/time-series-insights/ga-query-api).
- Узнайте, как [создать субъект-службу](../active-directory/develop/howto-create-service-principal-portal.md).
