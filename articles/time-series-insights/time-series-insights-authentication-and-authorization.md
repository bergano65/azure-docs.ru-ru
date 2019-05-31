---
title: Аутентификация и авторизация с помощью API в службе "Аналитика временных рядов Azure" | Документация Майкрософт
description: В этой статье описывается настройка аутентификации и авторизации для пользовательского приложения, которое вызывает API "Аналитика временных рядов Azure".
ms.service: time-series-insights
services: time-series-insights
author: ashannon7
ms.author: dpalled
manager: cshankar
ms.reviewer: v-mamcge, jasonh, kfile, anshan
ms.devlang: csharp
ms.workload: big-data
ms.topic: conceptual
ms.date: 05/07/2019
ms.custom: seodec18
ms.openlocfilehash: 9b6cd993e9f6c6dbf173c161de638c6c4a8b18d3
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/27/2019
ms.locfileid: "66237053"
---
# <a name="authentication-and-authorization-for-azure-time-series-insights-api"></a>Проверка подлинности и авторизация для API Azure Time Series Insights

В этой статье объясняется, как настроить аутентификацию и авторизацию для пользовательского приложения, которое вызывает API "Аналитика временных рядов Azure".

> [!TIP]
> Узнайте о [предоставление доступа к данным](./time-series-insights-data-access.md) в среду Time Series Insights в Azure Active Directory.

## <a name="service-principal"></a>Субъект-служба

Это и следующих разделах описывается настройка приложения для доступа к Time Series Insights API от имени приложения. Приложение можно будет запрашивать или публиковать эталонные данные в среде Time Series Insights с помощью учетных данных приложения, а не учетные данные пользователя.

## <a name="best-practices"></a>Рекомендации

При наличии приложения, которое необходимо доступа Time Series Insights:

1. Настройка приложения Azure Active Directory.
1. [Назначить политики доступа к данным](./time-series-insights-data-access.md) в среде Time Series Insights.

С помощью приложения, а не свои учетные данные пользователя желательно с момента:

* Можно назначить разрешения для удостоверения приложения, отличаются от ваших разрешений. Как правило, приложение получает именно те разрешения, которые требуются для его работы. Например, можно разрешить приложению только читать данные в определенной среде Time Series Insights.
* У вас нет приложения учетные данные, если изменения ваших обязанностей.
* Вы можете использовать сертификат или ключ приложения, чтобы автоматизировать проверку подлинности при выполнении автоматического скрипта.

Ниже показано, как сделать с помощью портала Azure. В статье уделяется однотенантного приложения, где приложение должно выполняться в одной организации. Операция обычно используется приложениями с одним клиентом для бизнес-приложений, работающих в вашей организации.

## <a name="set-up-summary"></a>Настройка Сводка

Процесс установки состоит из трех этапов:

1. Создайте приложение в Azure Active Directory.
1. Предоставьте этому приложению доступ к среде Time Series Insights.
1. Используйте идентификатор приложения и ключ, чтобы получить маркер из `https://api.timeseries.azure.com/`. С помощью маркера безопасности можно вызывать API Time Series Insights.

## <a name="detailed-setup"></a>Об установке

1. На портале Azure выберите **Azure Active Directory** > **Регистрация приложений** > **Регистрация нового приложения**.

   [![Регистрация нового приложения в Azure Active Directory](media/authentication-and-authorization/active-directory-new-application-registration.png)](media/authentication-and-authorization/active-directory-new-application-registration.png#lightbox)

1. Присвойте приложению имя, выберите тип **Веб-приложение или API**, выберите любой допустимый универсальный код ресурса (URI) в поле **URL-адрес входа** и нажмите кнопку **Создать**.

   [![Создайте приложение в Azure Active Directory](media/authentication-and-authorization/active-directory-create-web-api-application.png)](media/authentication-and-authorization/active-directory-create-web-api-application.png#lightbox)

1. Выберите только что созданное приложение и скопируйте его идентификатор в привычный текстовый редактор.

   [![Скопируйте идентификатор приложения](media/authentication-and-authorization/active-directory-copy-application-id.png)](media/authentication-and-authorization/active-directory-copy-application-id.png#lightbox)

1. Выберите **Ключи**, введите имя ключа, выберите дату истечения срока действия и нажмите кнопку **Сохранить**.

   [![Выбор ключей приложения](media/authentication-and-authorization/active-directory-application-keys.png)](media/authentication-and-authorization/active-directory-application-keys.png#lightbox)

   [![Введите имя ключа и истечения срока действия и щелкните "Сохранить"](media/authentication-and-authorization/active-directory-application-keys-save.png)](media/authentication-and-authorization/active-directory-application-keys-save.png#lightbox)

1. Скопируйте ключ в предпочитаемый текстовый редактор.

   [![Скопируйте ключ приложения](media/authentication-and-authorization/active-directory-copy-application-key.png)](media/authentication-and-authorization/active-directory-copy-application-key.png#lightbox)

1. Для среды Time Series Insights выберите **Политики доступа к данным** и щелкните **Добавить**.

   [![Добавить новую политику доступа данных в среду Time Series Insights](media/authentication-and-authorization/time-series-insights-data-access-policies-add.png)](media/authentication-and-authorization/time-series-insights-data-access-policies-add.png#lightbox)

1. В **Выбор пользователя** диалоговом окне вставьте имя приложения (из **шаг 2**) или идентификатор приложения (из **шаг 3**).

   [![Поиск приложения в диалоговом окне Выбор пользователей](media/authentication-and-authorization/time-series-insights-data-access-policies-select-user.png)](media/authentication-and-authorization/time-series-insights-data-access-policies-select-user.png#lightbox)

1. Выберите роль (**чтения** для запроса данных, **участник** для запроса данных и изменения эталонных данных) и нажмите кнопку **ОК**.

   [![Выберите в диалоговом окне выберите роль читателя или участника](media/authentication-and-authorization/time-series-insights-data-access-policies-select-role.png)](media/authentication-and-authorization/time-series-insights-data-access-policies-select-role.png#lightbox)

1. Сохранить политику, щелкнув **ОК**.

1. Используйте идентификатор приложения (из **шаг 3**) и ключ приложения (из **шаг 5**), чтобы получить маркер от имени приложения. Затем маркер безопасности можно передать в заголовок `Authorization`, когда приложение вызывает API Time Series Insights.

    При использовании C# с помощью следующего кода можно получить маркер безопасности от имени приложения. Полный пример см. в статье [Запрос данных из среды Azure Time Series Insights с помощью C##](time-series-insights-query-data-csharp.md).

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
