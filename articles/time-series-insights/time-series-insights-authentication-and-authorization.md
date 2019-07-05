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
ms.date: 06/29/2019
ms.custom: seodec18
ms.openlocfilehash: 899bcffaf3a54bd541d488f99c35ec6721d751ca
ms.sourcegitcommit: 5bdd50e769a4d50ccb89e135cfd38b788ade594d
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/03/2019
ms.locfileid: "67543880"
---
# <a name="authentication-and-authorization-for-azure-time-series-insights-api"></a>Проверка подлинности и авторизация для API Azure Time Series Insights

В этом документе описывается, как зарегистрировать приложение в Azure Active Directory с помощью новой колонки Azure Active Directory. Приложения, зарегистрированные в Azure Active Directory позволяют пользователям проходить проверку подлинности и авторизацию для использования API Azure Time Series Insight сопоставленный среды Time Series Insights.

## <a name="service-principal"></a>Субъект-служба

В следующих разделах рассматривается настройка приложения для доступа к Time Series Insights API от имени приложения. Приложение может затем запросить или публиковать эталонные данные в среде Time Series Insights, с помощью собственных учетных данных приложения через Azure Active Directory.

## <a name="summary-and-best-practices"></a>Сводка и рекомендации

В процесс регистрации приложения Azure Active Directory включает три основных действия.

1. [Регистрация приложения](#azure-active-directory-app-registration) в Azure Active Directory.
1. Разрешает приложению иметь [доступ к данным для среды Time Series Insights](#granting-data-access).
1. Используйте **идентификатор приложения** и **секрет клиента** , чтобы получить маркер из `https://api.timeseries.azure.com/` в вашей [клиентское приложение](#client-app-initialization). С помощью маркера безопасности можно вызывать API Time Series Insights.

На **шаг 3**, разделение учетных данных пользователя и приложения позволяет:

* Назначьте разрешения для удостоверения приложения, отличаются от ваших разрешений. Как правило, приложение получает именно те разрешения, которые требуются для его работы. Например можно разрешить приложению считывать данные только из определенной среде Time Series Insights.
* Выявление безопасность приложения от учетных данных проверки подлинности, создание пользователя с помощью **секрет клиента** или сертификата безопасности. Таким образом учетные данные приложения не зависят от определенных учетных данных пользователя. При изменении роли пользователя, приложение не требует обязательного новые учетные данные или дальнейшей настройки. Если пользователь меняет свой пароль, весь доступ к приложению не требуется новые учетные данные или ключи.
* Запуск сценария автоматической установки с помощью **секрет клиента** или сертификата безопасности вместо того, чтобы учетные данные определенного пользователя (требуя их наличия).
* Используйте сертификат безопасности, а не пароль для защиты доступа к вашей API Azure Time Series Insights.

> [!IMPORTANT]
> Следуйте принципу **разделения задач** (см. в этом сценарии выше) при настройке политики безопасности Azure Time Series Insights.

> [!NOTE]
> * В статье уделяется однотенантного приложения, где приложение должно выполняться в одной организации.
> * Операция обычно используется приложениями с одним клиентом для бизнес-приложений, работающих в вашей организации.

## <a name="detailed-setup"></a>Об установке

### <a name="azure-active-directory-app-registration"></a>Регистрация приложения Azure Active Directory

[!INCLUDE [Azure Active Directory app registration](../../includes/time-series-insights-aad-registration.md)]

### <a name="granting-data-access"></a>Предоставление доступа к данным

1. Для среды Time Series Insights, выберите **политики доступа к данным** и выберите **добавить**.

   [![Добавить новую политику доступа данных в среду Time Series Insights](media/authentication-and-authorization/time-series-insights-data-access-policies-add.png)](media/authentication-and-authorization/time-series-insights-data-access-policies-add.png#lightbox)

1. В **Выбор пользователя** диалоговом окне вставьте либо **имя_приложения** или **идентификатор приложения** из раздела регистрации приложения Azure Active Directory.

   [![Поиск приложения в диалоговом окне Выбор пользователей](media/authentication-and-authorization/time-series-insights-data-access-policies-select-user.png)](media/authentication-and-authorization/time-series-insights-data-access-policies-select-user.png#lightbox)

1. Выберите роль. Выберите **чтения** выполнять запросы к данным или **участник** для запроса данных и изменения эталонных данных. Нажмите кнопку **ОК**.

   [![Выбрать читателя или участника в диалоговом окне Выбор роли пользователя](media/authentication-and-authorization/time-series-insights-data-access-policies-select-role.png)](media/authentication-and-authorization/time-series-insights-data-access-policies-select-role.png#lightbox)

1. Сохранить политику, выбрав **ОК**.

   > [!TIP]
   > Узнайте о [предоставление доступа к данным](./time-series-insights-data-access.md) в среду Time Series Insights в Azure Active Directory.

### <a name="client-app-initialization"></a>Инициализации клиентского приложения

1. Используйте **идентификатор приложения** и **секрет клиента** (ключ приложения) в разделе регистрации приложения Azure Active Directory, чтобы получить маркер от имени приложения.

    В C#, следующий код может получить маркер от имени приложения. Полный пример см. в статье [Запрос данных из среды Azure Time Series Insights с помощью C##](time-series-insights-query-data-csharp.md).

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

1. Затем маркер безопасности можно передать в заголовок `Authorization`, когда приложение вызывает API Time Series Insights.

## <a name="next-steps"></a>Дальнейшие действия

- Пример кода, который вызывает API Time Series Insights общедоступной версии, см. в разделе [запрос данных с помощью C# ](./time-series-insights-query-data-csharp.md).

- Примеры кода для предварительной версии API Time Series Insights, см. в разделе [предварительного просмотра запроса данных с помощью C# ](./time-series-insights-update-query-data-csharp.md).

- Справочные сведения об API см. в статье об [API запросов к службе "Аналитика временных рядов Azure"](/rest/api/time-series-insights/ga-query-api).

- Узнайте, как [создать субъект-службу](../active-directory/develop/howto-create-service-principal-portal.md).