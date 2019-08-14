---
title: Проверка подлинности и авторизация с помощью API в службе "аналитика временных рядов Azure" | Документация Майкрософт
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
ms.date: 08/08/2019
ms.custom: seodec18
ms.openlocfilehash: 602623d48457498963cb5928081d24c1d1132ad4
ms.sourcegitcommit: 13a289ba57cfae728831e6d38b7f82dae165e59d
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/09/2019
ms.locfileid: "68935239"
---
# <a name="authentication-and-authorization-for-azure-time-series-insights-api"></a>Проверка подлинности и авторизация для API Azure Time Series Insights

В этом документе описывается регистрация приложения в Azure Active Directory с помощью новой колонки Azure Active Directory. Приложения, зарегистрированные в Azure Active Directory позволяют пользователям проходить проверку подлинности и иметь право использовать API анализа временных рядов Azure, связанный с средой "аналитика временных рядов".

## <a name="service-principal"></a>Субъект-служба

В следующих разделах описано, как настроить приложение для доступа к API "аналитика временных рядов" от имени приложения. Приложение может запрашивать или публиковать эталонные данные в среде Time Series Insights с помощью собственных учетных данных приложения с помощью Azure Active Directory.

## <a name="summary-and-best-practices"></a>Сводка и рекомендации

Процесс регистрации Azure Active Directory приложения состоит из трех основных этапов.

1. [Зарегистрируйте приложение](#azure-active-directory-app-registration) в Azure Active Directory.
1. Авторизация приложения для [доступа к данным в среде "аналитика временных рядов"](#granting-data-access).
1. Используйте **идентификатор приложения** и **секрет клиента** , чтобы получить маркер из `https://api.timeseries.azure.com/` в клиентском [приложении](#client-app-initialization). С помощью маркера безопасности можно вызывать API Time Series Insights.

На **шаге 3**разделение приложения и учетные данные пользователя позволяют:

* Назначьте разрешения удостоверению приложения, отличающиеся от собственных разрешений. Как правило, приложение получает именно те разрешения, которые требуются для его работы. Например, можно разрешить приложению считывать данные только из определенной среды "аналитика временных рядов".
* Изолируйте безопасность приложения от создания учетных данных для проверки подлинности пользователя с помощью **секрета клиента** или сертификата безопасности. В результате учетные данные приложения не зависят от учетных данных конкретного пользователя. При изменении роли пользователя приложение не обязательно потребует новых учетных данных или дальнейшей настройки. Если пользователь изменяет пароль, для доступа к приложению не требуются новые учетные данные или ключи.
* Запустите автоматический сценарий, используя **секрет клиента** или сертификат безопасности, а не учетные данные конкретного пользователя (требуется их присутствие).
* Используйте сертификат безопасности, а не пароль для защиты доступа к API службы "аналитика временных рядов Azure".

> [!IMPORTANT]
> Следуйте принципу **разделения проблем** (описанных выше) при настройке политики безопасности "аналитика временных рядов Azure".

> [!NOTE]
> * В этой статье основное внимание уделяется приложению с одним клиентом, в котором приложение предназначено только для работы в одной организации.
> * Обычно для бизнес-приложений, выполняемых в Организации, используются приложения с одним клиентом.

## <a name="detailed-setup"></a>Подробная настройка

### <a name="azure-active-directory-app-registration"></a>Регистрация приложения Azure Active Directory

[!INCLUDE [Azure Active Directory app registration](../../includes/time-series-insights-aad-registration.md)]

### <a name="granting-data-access"></a>Предоставление доступа к данным

1. Для среды "аналитика временных рядов" выберите **политики доступа к данным** и нажмите кнопку **Добавить**.

   [![Добавление новой политики доступа к данным в среду "аналитика временных рядов"](media/authentication-and-authorization/time-series-insights-data-access-policies-add.png)](media/authentication-and-authorization/time-series-insights-data-access-policies-add.png#lightbox)

1. В диалоговом окне **Выбор пользователя** вставьте **имя приложения** или **идентификатор приложения** из раздела регистрация приложения Azure Active Directory.

   [![Поиск приложения в диалоговом окне "Выбор пользователя"](media/authentication-and-authorization/time-series-insights-data-access-policies-select-user.png)](media/authentication-and-authorization/time-series-insights-data-access-policies-select-user.png#lightbox)

1. Выберите роль. Выберите **читатель** , чтобы запросить данные или **участника** для запроса данных и изменения ссылочных данных. Нажмите кнопку **ОК**.

   [![Выбор читателя или участника в диалоговом окне "Выбор роли пользователя"](media/authentication-and-authorization/time-series-insights-data-access-policies-select-role.png)](media/authentication-and-authorization/time-series-insights-data-access-policies-select-role.png#lightbox)

1. Сохраните политику, нажав **кнопку ОК**.

   > [!TIP]
   > Узнайте, как [предоставить доступ к данным](./time-series-insights-data-access.md) в среду "аналитика временных рядов" в Azure Active Directory.

### <a name="client-app-initialization"></a>Инициализация клиентского приложения

1. Используйте **идентификатор приложения** и **секрет клиента** (ключ приложения) из раздела регистрации приложения Azure Active Directory, чтобы получить маркер от имени приложения.

    В C#следующий код может получить маркер от имени приложения. Полный пример см. в статье [Запрос данных из среды Azure Time Series Insights с помощью C##](time-series-insights-query-data-csharp.md).

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

## <a name="next-steps"></a>Следующие шаги

- Пример кода, который вызывает API-интерфейс "аналитика временных рядов", см. в разделе [запрос данных с помощью C# ](./time-series-insights-query-data-csharp.md).

- Примеры кода API для аналитики временных рядов см. в статье [Предварительный просмотр данных запросов C#с помощью ](./time-series-insights-update-query-data-csharp.md).

- Справочные сведения об API см. в статье об [API запросов к службе "Аналитика временных рядов Azure"](https://docs.microsoft.com/rest/api/time-series-insights/ga-query-api).

- Узнайте, как [создать субъект-службу](../active-directory/develop/howto-create-service-principal-portal.md).