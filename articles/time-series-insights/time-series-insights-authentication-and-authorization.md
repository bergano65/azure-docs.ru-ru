---
title: Проверка подлинности и авторизация API в Azure Time Series Insights | Документация Майкрософт
description: В этой статье описывается настройка аутентификации и авторизации для пользовательского приложения, которое вызывает API "Аналитика временных рядов Azure".
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.reviewer: v-mamcge, jasonh, kfile
ms.devlang: csharp
ms.workload: big-data
ms.topic: conceptual
ms.date: 04/14/2020
ms.custom: seodec18, has-adal-ref
ms.openlocfilehash: bf959a7ac8c1038c4306a45ba4519374c5d85f29
ms.sourcegitcommit: 50ef5c2798da04cf746181fbfa3253fca366feaa
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/30/2020
ms.locfileid: "82612288"
---
# <a name="authentication-and-authorization-for-azure-time-series-insights-api"></a>Проверка подлинности и авторизация для API Azure Time Series Insights

В этом документе описывается регистрация приложения в Azure Active Directory с помощью новой колонки Azure Active Directory. Приложения, зарегистрированные в Azure Active Directory позволяют пользователям проходить проверку подлинности и иметь право использовать API анализа временных рядов Azure, связанный с средой "аналитика временных рядов".

> [!IMPORTANT]
> Служба "аналитика временных рядов Azure" поддерживает обе следующие библиотеки проверки подлинности:
> * Более свежая [Библиотека проверки подлинности Майкрософт (MSAL)](https://docs.microsoft.com/azure/active-directory/develop/msal-overview)
> * [Библиотека проверки Подлинности Azure Active Directory (ADAL)](https://docs.microsoft.com/azure/active-directory/develop/active-directory-authentication-libraries)

## <a name="service-principal"></a>Субъект-служба

В следующих разделах описано, как настроить приложение для доступа к API "аналитика временных рядов" от имени приложения. Приложение может запрашивать или публиковать эталонные данные в среде Time Series Insights с помощью собственных учетных данных приложения с помощью Azure Active Directory.

## <a name="summary-and-best-practices"></a>Сводка и рекомендации

Процесс регистрации Azure Active Directory приложения состоит из трех основных этапов.

1. [Зарегистрируйте приложение](#azure-active-directory-app-registration) в Azure Active Directory.
1. Авторизация приложения для [доступа к данным в среде "аналитика временных рядов"](#granting-data-access).
1. Используйте **идентификатор приложения** и **секрет клиента** , чтобы получить маркер из `https://api.timeseries.azure.com/` в [клиентском приложении](#client-app-initialization). С помощью маркера безопасности можно вызывать API Time Series Insights.

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

   [![Добавление новой политики доступа к данным в среде Time Series Insights](media/authentication-and-authorization/time-series-insights-data-access-policies-add.png)](media/authentication-and-authorization/time-series-insights-data-access-policies-add.png#lightbox)

1. В диалоговом окне **Выбор пользователя** вставьте **имя приложения** или **идентификатор приложения** из раздела регистрация приложения Azure Active Directory.

   [![Поиск приложения в диалоговом окне "Выбор пользователя"](media/authentication-and-authorization/time-series-insights-data-access-policies-select-user.png)](media/authentication-and-authorization/time-series-insights-data-access-policies-select-user.png#lightbox)

1. Выберите роль. Выберите **читатель** , чтобы запросить данные или **участника** для запроса данных и изменения ссылочных данных. Щелкните **ОК**.

   [![Выбор читателя или участника в диалоговом окне "Выбор роли пользователя"](media/authentication-and-authorization/time-series-insights-data-access-policies-select-role.png)](media/authentication-and-authorization/time-series-insights-data-access-policies-select-role.png#lightbox)

1. Сохраните политику, нажав **кнопку ОК**.

   > [!TIP]
   > Дополнительные параметры доступа к данным см. в статье [предоставление доступа к данным](./time-series-insights-data-access.md).

### <a name="client-app-initialization"></a>Инициализация клиентского приложения

* Разработчики могут использовать [библиотеку проверки подлинности Майкрософт (MSAL)](https://docs.microsoft.com/azure/active-directory/develop/msal-overview) или [библиотеку проверки подлинности (ADAL) Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-authentication-libraries) для проверки подлинности с помощью службы "аналитика временных рядов Azure".

* Например, для проверки подлинности с помощью ADAL:

   1. Используйте **идентификатор приложения** и **секрет клиента** (ключ приложения) из раздела регистрации приложения Azure Active Directory, чтобы получить маркер от имени приложения.

   1. В C# следующий код может получить маркер от имени приложения. Полный пример см. в статье [запрос данных с помощью C#](time-series-insights-query-data-csharp.md).

        [!code-csharp[csharpquery-example](~/samples-tsi/csharp-tsi-ga-sample/Program.cs?range=170-199)]

   1. Затем маркер безопасности можно передать в заголовок `Authorization`, когда приложение вызывает API Time Series Insights.

* Кроме того, разработчики могут использовать проверку подлинности с помощью MSAL. Дополнительные сведения о [миграции в MSAL](https://docs.microsoft.com/azure/active-directory/develop/msal-net-migration) см. в статье Управление общедоступными [справочными данными для среды Azure Time Series Insights с помощью C#](time-series-insights-manage-reference-data-csharp.md) .

## <a name="common-headers-and-parameters"></a>Общие заголовки и параметры

В этом разделе описаны распространенные заголовки HTTP-запросов и параметры, используемые для выполнения запросов к интерфейсам API "аналитика временных рядов". Требования к конкретному API подробно описаны в [справочной документации "аналитика временных рядов" REST API](https://docs.microsoft.com/rest/api/time-series-insights/).

> [!TIP]
> Ознакомьтесь со [справочником по REST API Azure](https://docs.microsoft.com/rest/api/azure/) , чтобы узнать больше о том, как использовать интерфейсы API, выполнять HTTP-запросы и обрабатывать HTTP-ответы.

### <a name="authentication"></a>Аутентификация

Для выполнения запросов с проверкой подлинности к [API-интерфейсам "аналитика временных рядов](https://docs.microsoft.com/rest/api/time-series-insights/)" необходимо передать допустимый токен носителя OAuth 2,0 в [заголовок авторизации](/rest/api/apimanagement/2019-12-01/authorizationserver/createorupdate) с помощью клиента произвольного выбора (POST, JavaScript, C#).

> [!TIP]
> Ознакомьтесь с [примером визуализации клиентского пакета SDK](https://tsiclientsample.azurewebsites.net/) для службы "аналитика временных рядов Azure", чтобы узнать, как программным способом проверить подлинность с помощью API-интерфейсов "аналитика временных рядов", используя [клиентский пакет SDK для JavaScript](https://github.com/microsoft/tsiclient/blob/master/docs/API.md) и диаграммы и графики.

### <a name="http-headers"></a>Заголовки HTTP

Обязательные заголовки запроса описаны ниже.

| Заголовок требуемого запроса | Описание |
| --- | --- |
| Авторизация | Для проверки подлинности с помощью Time Series Insights в заголовке **авторизации** должен быть передан допустимый токен носителя OAuth 2,0. |

> [!IMPORTANT]
> Маркер должен быть полностью выдан `https://api.timeseries.azure.com/` ресурсу (также известен как "аудитория" маркера).
> * Таким [Postman](https://www.getpostman.com/) образом, **аусурл** будет:`https://login.microsoftonline.com/microsoft.onmicrosoft.com/oauth2/authorize?resource=https://api.timeseries.azure.com/`
> * `https://api.timeseries.azure.com/`является допустимым `https://api.timeseries.azure.com` , но не имеет.

Дополнительные заголовки запроса описаны ниже.

| Дополнительный заголовок запроса. | Описание |
| --- | --- |
| Content-type | поддерживается `application/json` только. |
| x-ms-client-request-id | Идентификатор запроса клиента. Служба записывает это значение. Позволяет службе выполнять трассировку операций между службами. |
| x-MS-Client-Session-ID | Идентификатор сеанса клиента. Служба записывает это значение. Позволяет службе выполнять трассировку группы связанных операций между службами. |
| x-MS-Client-Application-Name | Имя приложения, создавшего этот запрос. Служба записывает это значение. |

Необязательные, но Рекомендуемые заголовки ответа описаны ниже.

| Заголовок ответа | Описание |
| --- | --- |
| Content-type | Поддерживается только `application/json`. |
| x-ms-request-id | Идентификатор запроса, сформированный сервером. Можно использовать для обращения в корпорацию Майкрософт, чтобы исследовать запрос. |
| x-MS-Property-не найдено-поведение | Необязательный заголовок ответа API. Возможные значения: `ThrowError` (по умолчанию `UseNull`) или. |

### <a name="http-parameters"></a>Параметры HTTP

> [!TIP]
> Дополнительные сведения о обязательных и необязательных запросах см. в [справочной документации](https://docs.microsoft.com/rest/api/time-series-insights/).

Требуемые параметры строки запроса URL-адреса зависят от версии API.

| Release | Возможные значения версии API |
| --- |  --- |
| Общедоступная версия | `api-version=2016-12-12`|
| Предварительный просмотр | `api-version=2018-11-01-preview` |
| Предварительный просмотр | `api-version=2018-08-15-preview` |

Необязательные параметры строки запроса URL-адреса включают установку времени ожидания для времени выполнения HTTP-запроса.

| Необязательный параметр запроса | Описание | Версия |
| --- |  --- | --- |
| `timeout=<timeout>` | Время ожидания на стороне сервера для выполнения HTTP-запроса. Применяется только к [событиям "получить среду](https://docs.microsoft.com/rest/api/time-series-insights/ga-query-api#get-environment-events-api) " и " [получить агрегаты среды](https://docs.microsoft.com/rest/api/time-series-insights/ga-query-api#get-environment-aggregates-api) ". Значение времени ожидания должно быть в формате длительности ISO 8601, `"PT20S"` например и должно находиться в диапазоне `1-30 s`. Значение по умолчанию — `30 s`. | GA |
| `storeType=<storeType>` | Для сред предварительного просмотра с включенным горячим сохранением запрос можно выполнить либо в `WarmStore` , `ColdStore`либо в. Этот параметр в запросе определяет, в каком хранилище должен выполняться запрос. Если этот параметр не определен, запрос будет выполнен в холодном хранилище. Чтобы запросить горячий магазин, **storeType** необходимо установить в `WarmStore`значение. Если этот параметр не определен, запрос будет выполнен для холодного хранилища. | Предварительный просмотр |

## <a name="next-steps"></a>Дальнейшие действия

- Пример кода, который вызывает API-интерфейс "аналитика временных рядов", считывает [данные запросов с помощью C#](./time-series-insights-query-data-csharp.md).

- Примеры кода API для аналитики временных рядов см. в статье [Предварительный просмотр данных в C#](./time-series-insights-update-query-data-csharp.md).

- Сведения о справочнике по API см. в [справочной документации по API запроса](https://docs.microsoft.com/rest/api/time-series-insights/ga-query-api) .

- Узнайте, как [создать субъект-службу](../active-directory/develop/howto-create-service-principal-portal.md).
