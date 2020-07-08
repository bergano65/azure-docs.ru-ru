---
title: Проверка подлинности и авторизация API — Аналитика временных рядов Azure | Документация Майкрософт
description: В этой статье описывается настройка аутентификации и авторизации для пользовательского приложения, которое вызывает API "Аналитика временных рядов Azure".
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: shresha
manager: dpalled
ms.reviewer: v-mamcge, jasonh, kfile
ms.devlang: csharp
ms.workload: big-data
ms.topic: conceptual
ms.date: 06/18/2020
ms.custom: seodec18, has-adal-ref
ms.openlocfilehash: 94fef951bf1c5c9d69a9b49cd9465d7d248c74a7
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/02/2020
ms.locfileid: "85099222"
---
# <a name="authentication-and-authorization-for-azure-time-series-insights-api"></a>Проверка подлинности и авторизация для API Azure Time Series Insights

В этом документе описывается порядок регистрации приложения в Azure Active Directory с помощью новой колонки Azure Active Directory. Приложения, зарегистрированные в Azure Active Directory, позволяют пользователям проходить проверку подлинности и иметь право использовать API Аналитики временных рядов Azure, связанный с средой Аналитики временных рядов.

## <a name="service-principal"></a>Субъект-служба

В разделах ниже описывается порядок настройки приложения для доступа к API Аналитики временных рядов от имени приложения. Приложение может запрашивать или публиковать эталонные данные в среде Аналитики временных рядов с помощью собственных учетных данных приложения с помощью Azure Active Directory.

## <a name="summary-and-best-practices"></a>Сводка и рекомендации

Процесс регистрации приложения Azure Active Directory состоит из трех основных этапов.

1. [Регистрация приложения](#azure-active-directory-app-registration) в Azure Active Directory.
1. Авторизация приложения для [доступа к данным в среде Аналитики временных рядов](#granting-data-access).
1. Использование **идентификатора приложения** и **секрета клиента** для получения маркера от `https://api.timeseries.azure.com/` в [клиентском приложении](#client-app-initialization). С помощью маркера безопасности можно вызывать API Time Series Insights.

В **шаге 3** разделение учетных данных приложения и пользователя позволяет выполнить следующее:

* Назначить удостоверению приложения разрешения, которые отличаются от ваших разрешений. Как правило, приложение получает именно те разрешения, которые требуются для его работы. Например, можно разрешить приложению читать данные только в определенной среде Аналитике временных рядов Azure.
* Изолировать безопасность приложения от создания учетных данных для проверки подлинности пользователя, используя **секрет клиента** или сертификат безопасности. В результате учетные данные приложения не зависят от учетных данных конкретного пользователя. При изменении роли пользователя приложению не обязательно потребуются новые учетные данные или дальнейшая настройка. Если пользователь изменит пароль, для доступа к приложению не требуются новые учетные данные или ключи.
* Запустить автоматический сценарий, используя **секрет клиента** или сертификат безопасности, а не учетные данные конкретного пользователя (требование их наличия).
* Используйте сертификат безопасности, а не пароль для защиты доступа к API Аналитики временных рядов Azure.

> [!IMPORTANT]
> Следуйте принципу **разделения проблем** (описанных выше в этом сценарии) при настройке политики безопасности Аналитики временных рядов Azure

> [!NOTE]
> * В статье рассматривается однотенантное приложение — решение, используемое в пределах одной организации.
> * Обычно однотенантная архитектура используется для создания бизнес-приложений в рамках организации.

## <a name="detailed-setup"></a>Подробная настройка

### <a name="azure-active-directory-app-registration"></a>Регистрация приложения в Azure Active Directory

[!INCLUDE [Azure Active Directory app registration](../../includes/time-series-insights-aad-registration.md)]

### <a name="granting-data-access"></a>Предоставление доступа к данным

1. Для среды Аналитики временных рядов выберите **Политики доступа к данным** и нажмите **Добавить**.

   [![Добавление новой политики доступа к данным в среде Аналитики временных рядов](media/authentication-and-authorization/time-series-insights-data-access-policies-add.png)](media/authentication-and-authorization/time-series-insights-data-access-policies-add.png#lightbox)

1. В диалоговом окне **Выбор пользователя** вставьте **имя приложения** или **идентификатор приложения**, скопированные в разделе регистрации приложения Azure Active Directory.

   [![Поиск приложения в диалоговом окне "Выбор пользователя"](media/authentication-and-authorization/time-series-insights-data-access-policies-select-user.png)](media/authentication-and-authorization/time-series-insights-data-access-policies-select-user.png#lightbox)

1. Выберите роль. Выберите **Читатель**, чтобы предоставить разрешение на запрос данных, или **Участник**, чтобы предоставить разрешение на запрос данных и изменение эталонных данных. Щелкните **ОК**.

   [![Выбор роли "Читатель" или "Участник" в диалоговом окне "Выбор роли"](media/authentication-and-authorization/time-series-insights-data-access-policies-select-role.png)](media/authentication-and-authorization/time-series-insights-data-access-policies-select-role.png#lightbox)

1. Сохраните политику, нажав кнопку **ОК**.

   > [!TIP]
   > Сведения о дополнительных параметрах доступа к данным см. в разделе, посвященном [предоставлению доступа к данным](./time-series-insights-data-access.md).

### <a name="client-app-initialization"></a>Инициализация клиентского приложения

* Разработчики могут использовать [библиотеку проверки подлинности Майкрософт (MSAL) для проверки подлинности с помощью службы "аналитика временных рядов Azure".

* Проверка подлинности с помощью ADAL:

   1. Используйте **идентификатор приложения** и **секрет клиента** (ключ приложения) из раздела регистрации приложения Azure Active Directory, чтобы получить маркер от имени приложения.

   1. В C# маркер безопасности от имени приложения можно получить с помощью следующего кода. Полный пример см. в статье [Запрос данных из среды Аналитики временных рядов Azure с помощью C#](time-series-insights-query-data-csharp.md).

        [!code-csharp[csharpquery-example](~/samples-tsi/csharp-tsi-ga-sample/Program.cs?range=170-199)]

   1. Затем маркер безопасности можно передать в заголовок `Authorization`, когда приложение вызывает API Time Series Insights.

> [!IMPORTANT]
> Если вы используете [библиотеку Azure Active Directory для проверки подлинности (ADAL)](https://docs.microsoft.com/azure/active-directory/azuread-dev/active-directory-authentication-libraries) , см. статью о [миграции на MSAL](https://docs.microsoft.com/azure/active-directory/develop/msal-net-migration).

    See our [Manage GA reference data for an Azure Time Series Insights environment using C#](time-series-insights-manage-reference-data-csharp.md) article to learn more.

## <a name="common-headers-and-parameters"></a>Общие заголовки и параметры

В этом разделе описаны общие заголовки HTTP-запросов и параметры, используемые для выполнения запросов к интерфейсам API Аналитики временных рядов. Требования к конкретному API подробно описаны в [справочной документации по REST API Аналитики временных рядов Azure](https://docs.microsoft.com/rest/api/time-series-insights/).

> [!TIP]
> Дополнительные сведения об использовании интерфейсов REST API, а также о HTTP-запросах и обработке HTTP-ответов см. в [справочнике по Azure REST API](https://docs.microsoft.com/rest/api/azure/)

### <a name="authentication"></a>Аутентификация

Для выполнения запросов с проверкой подлинности по [REST API Аналитики временных рядов](https://docs.microsoft.com/rest/api/time-series-insights/) необходимо передать допустимый маркер носителя OAuth 2.0 в [заголовок авторизации](/rest/api/apimanagement/2019-12-01/authorizationserver/createorupdate) с помощью выбранного клиента (POST, JavaScript C#).

> [!TIP]
> Ознакомьтесь со статьей, посвященной [примеру визуализации пакета SDK клиента](https://tsiclientsample.azurewebsites.net/), размещенному в службе Аналитики временных рядов Azure, чтобы узнать, как выполнять проверку подлинности с помощью API Аналитики временных рядов программным способом с помощью [пакета клиента SDK клиента для JavaScript](https://github.com/microsoft/tsiclient/blob/master/docs/API.md) (с диаграммами и графиками).

### <a name="http-headers"></a>HTTP-заголовки

Обязательные заголовки запроса описаны ниже.

| Обязательный заголовок запроса | Описание |
| --- | --- |
| Авторизация | Для проверки подлинности с помощью Аналитики временных рядов необходимо передать допустимый маркер носителя OAuth 2.0 в заголовок **Authorization**. |

> [!IMPORTANT]
> Маркер должен быть выдан ресурсу `https://api.timeseries.azure.com/` (известному как "аудитория" маркера).
> * Результирующий **AuthURL** [Postman](https://www.getpostman.com/) будет следующим: `https://login.microsoftonline.com/microsoft.onmicrosoft.com/oauth2/authorize?scope=https://api.timeseries.azure.com/.default`
> * `https://api.timeseries.azure.com/` является действительным адресом, а `https://api.timeseries.azure.com` — нет.

Дополнительные заголовки запроса описаны ниже.

| Дополнительный заголовок запроса. | Описание |
| --- | --- |
| Content-type | Поддерживается только `application/json`. |
| x-ms-client-request-id | Идентификатор запроса клиента Служба записывает это значение. Позволяет службе выполнять трассировку операций между службами. |
| x-ms-client-session-id | Идентификатор сеанса клиента. Служба записывает это значение. Позволяет службе выполнять трассировку группы связанных операций между службами. |
| x-ms-client-application-name | Имя приложения, создавшего этот запрос. Служба записывает это значение. |

Необязательные, но рекомендуемые заголовки ответа описаны ниже.

| Заголовок ответа | Описание |
| --- | --- |
| Content-type | Поддерживается только `application/json`. |
| x-ms-request-id | Идентификатор запроса, сформированный сервером. Можно использовать для обращения в корпорацию Майкрософт, чтобы исследовать запрос. |
| x-ms-property-not-found-behavior | Необязательный заголовок ответа API. Возможные значения: `ThrowError` (по умолчанию) или `UseNull`. |

### <a name="http-parameters"></a>Параметры HTTP

> [!TIP]
> Дополнительные сведения об обязательных и необязательных запросах см. в [справочной документации](https://docs.microsoft.com/rest/api/time-series-insights/).

Обязательные параметры строки запроса URL-адреса зависят от версии API.

| Release | Возможные значения версии API |
| --- |  --- |
| Общедоступная версия | `api-version=2016-12-12`|
| Preview (Предварительный просмотр) | `api-version=2018-11-01-preview` |
| Preview (Предварительный просмотр) | `api-version=2018-08-15-preview` |

Необязательные параметры строки запроса URL-адреса включают установку времени ожидания для времени выполнения HTTP-запроса.

| Необязательный параметр запроса | Описание | Версия |
| --- |  --- | --- |
| `timeout=<timeout>` | Время ожидания на стороне сервера для выполнения HTTP-запроса. Применяется только к API [получения событий среды](https://docs.microsoft.com/rest/api/time-series-insights/ga-query-api#get-environment-events-api) и [получения агрегатов среды](https://docs.microsoft.com/rest/api/time-series-insights/ga-query-api#get-environment-aggregates-api). Значение времени ожидания должно быть в формате длительности ISO 8601 (например, `"PT20S"`) и должно находиться в диапазоне `1-30 s`. Значение по умолчанию: `30 s`. | GA |
| `storeType=<storeType>` | Для сред предварительного просмотра с включенным теплым хранилищем запрос можно выполнить либо в `WarmStore`, либо в `ColdStore`. Этот параметр в запросе определяет, в каком хранилище должен выполняться запрос. Если этот параметр не определен, запрос будет выполнен в холодном хранилище. Чтобы запросить теплое хранилище, параметру **storeType** следует присвоить значение `WarmStore`. Если этот параметр не определен, запрос будет выполнен для холодного хранилища. | Preview (Предварительный просмотр) |

## <a name="next-steps"></a>Дальнейшие действия

* Пример кода, который вызывает API Аналитики временных рядов, см. в статье [Запрос данных с помощью C#](./time-series-insights-query-data-csharp.md).

* Примеры кода API Аналитики временных рядов Azure (предварительная версия) см. в статье [Данные предварительного просмотра запроса с помощью C#](./time-series-insights-update-query-data-csharp.md).

* Справочные сведения об API см. в [справочной документации по API запроса](https://docs.microsoft.com/rest/api/time-series-insights/ga-query-api).

* Узнайте подробнее о [создании субъекта-службы](../active-directory/develop/howto-create-service-principal-portal.md).
