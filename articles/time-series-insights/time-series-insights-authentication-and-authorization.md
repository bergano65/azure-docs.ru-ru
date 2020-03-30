---
title: Аутентификация и авторизация API - Исследования серии времени Azure Документы Майкрософт
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
ms.date: 02/03/2020
ms.custom: seodec18
ms.openlocfilehash: ff5f7a80e2dcedb1795bae14ee9140c2842303a5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76984602"
---
# <a name="authentication-and-authorization-for-azure-time-series-insights-api"></a>Проверка подлинности и авторизация для API Azure Time Series Insights

В этом документе описывается, как зарегистрировать приложение в Active Directory Azure с помощью нового лезвия Active Directory Azure. Приложения, зарегистрированные в Active Directory Azure, позволяют пользователям аутентифицировать и иметь право использовать API Анализа Времени Azure, связанный с средой Исследования временных рядов.

> [!IMPORTANT]
> Azure Time Series Insights поддерживает обе следующие библиотеки аутентификации:
> * Более поздняя [библиотека аутентификации Майкрософт (MSAL)](https://docs.microsoft.com/azure/active-directory/develop/msal-overview)
> * [Библиотека подлинности активных каталогов Azure (ADAL)](https://docs.microsoft.com/azure/active-directory/develop/active-directory-authentication-libraries)

## <a name="service-principal"></a>Субъект-служба

В следующих разделах описывается, как настроить приложение для доступа к API Time Series Insights от имени приложения. Затем приложение может задавать запрос или публиковать справочные данные в среде Time Series Insights, используя собственные учетные данные приложений через Azure Active Directory.

## <a name="summary-and-best-practices"></a>Резюме и лучшие практики

Поток регистрации приложений Azure Active Directory включает в себя три основных шага.

1. [Зарегистрируйте приложение](#azure-active-directory-app-registration) в active-каталоге Azure.
1. Разрешить приложению иметь [доступ к данным к среде Time Series Insights.](#granting-data-access)
1. Используйте **идентификатор приложения** и `https://api.timeseries.azure.com/` секрет **клиента,** чтобы приобрести токен в [клиентском приложении.](#client-app-initialization) С помощью маркера безопасности можно вызывать API Time Series Insights.

В **шаге 3**, разделение приложения и учетных данных пользователей позволяет:

* Назначайте разрешения на идентификацию приложения, которые отличаются от ваших собственных разрешений. Как правило, приложение получает именно те разрешения, которые требуются для его работы. Например, можно разрешить приложению считывать данные только из определенной среды Time Series Insights.
* Изолировать безопасность приложения от создания учетных данных пользователя, используя секрет **клиента** или сертификат безопасности. В результате учетные данные приложения не зависят от учетных данных конкретного пользователя. Если роль пользователя изменяется, приложение не обязательно требует новых учетных данных или дальнейшей конфигурации. Если пользователь меняет свой пароль, весь доступ к приложению не требует новых учетных данных или ключей.
* Выполнить без присмотра скрипт с помощью сертификата **клиента секрет** или сертификат безопасности, а не учетных данных конкретного пользователя (требуя их присутствия).
* Используйте сертификат безопасности, а не пароль, чтобы обеспечить доступ к API Azure Time Series Insights.

> [!IMPORTANT]
> Следуйте принципу **разделения проблем** (описано для этого сценария выше) при настройке политики безопасности Azure Time Series Insights.

> [!NOTE]
> * Статья посвящена приложению с одним арендатором, в котором приложение предназначено для запуска только в одной организации.
> * Обычно приложения с одним арендатором используются для приложений, запускаемых в вашей организации.

## <a name="detailed-setup"></a>Детальная настройка

### <a name="azure-active-directory-app-registration"></a>Регистрация приложения Active Directory Azure

[!INCLUDE [Azure Active Directory app registration](../../includes/time-series-insights-aad-registration.md)]

### <a name="granting-data-access"></a>Предоставление доступа к данным

1. Для среды Исследования временных рядов выберите **политики доступа к данным** и **переберите Добавить.**

   [![Добавление новой политики доступа к данным в среде Time Series Insights](media/authentication-and-authorization/time-series-insights-data-access-policies-add.png)](media/authentication-and-authorization/time-series-insights-data-access-policies-add.png#lightbox)

1. В диалоговом поле **Select User** вставьте либо **имя приложения,** либо **идентификатор приложения** из раздела регистрации приложений Azure Active Directory.

   [![Поиск приложения в диалоговом окне "Выбор пользователя"](media/authentication-and-authorization/time-series-insights-data-access-policies-select-user.png)](media/authentication-and-authorization/time-series-insights-data-access-policies-select-user.png#lightbox)

1. Выберите роль. Выберите **Reader** для запроса данных или **вкладчика** в запрос данных и изменение справочных данных. Нажмите кнопку **ОК**.

   [![Выберите reader или Contributor в диалоговом окне роли пользователя](media/authentication-and-authorization/time-series-insights-data-access-policies-select-role.png)](media/authentication-and-authorization/time-series-insights-data-access-policies-select-role.png#lightbox)

1. Сохранить политику, выбрав **OK.**

   > [!TIP]
   > Для продвинутых вариантов доступа к данным прочитайте [предоставление доступа к данным.](./time-series-insights-data-access.md)

### <a name="client-app-initialization"></a>Инициализация клиентского приложения

* Разработчики могут использовать [библиотеку подлинности Майкрософт (MSAL)](https://docs.microsoft.com/azure/active-directory/develop/msal-overview) или [Библиотеку активных каталогов Azure (ADAL)](https://docs.microsoft.com/azure/active-directory/develop/active-directory-authentication-libraries) для проверки подлинности с помощью Azure Time Series Insights.

* Например, для проверки подлинности с помощью ADAL:

   1. Используйте **идентификатор приложения** и **секрет клиента** (Application Key) из раздела регистрации приложений Azure Active Directory, чтобы приобрести токен от имени приложения.

   1. В C, следующий код может приобрести токен от имени приложения. Для получения полной выборки прочитайте [данные запроса с помощью C .](time-series-insights-query-data-csharp.md)

        [!code-csharp[csharpquery-example](~/samples-tsi/csharp-tsi-ga-sample/Program.cs?range=170-199)]

   1. Затем маркер безопасности можно передать в заголовок `Authorization`, когда приложение вызывает API Time Series Insights.

* Кроме того, разработчики могут выбрать аутентификации с помощью MSAL. Прочитайте о переходе в [MSAL](https://docs.microsoft.com/azure/active-directory/develop/msal-net-migration) и ознакомьтесь с нашими [справочниками Manage GA для среды Azure Time Series Insights, используя](time-series-insights-manage-reference-data-csharp.md) статью C', чтобы узнать больше. 

## <a name="common-headers-and-parameters"></a>Общие заголовки и параметры

В этом разделе описаны общие заголовки запросов HTTP и параметры, используемые для выполнения запросов в отношении AIS Time Series Insights GA и Preview AIS. Требования, предъявляемые к API, более подробно описаны в [справочной документации Time Series Insights REST API.](https://docs.microsoft.com/rest/api/time-series-insights/)

> [!TIP]
> Прочитайте [ссылку на API Azure REST,](https://docs.microsoft.com/rest/api/azure/) чтобы узнать больше о том, как использовать APPi REST, сделать запросы HTTP и обрабатывать ответы HTTP.

### <a name="authentication"></a>Проверка подлинности

Для выполнения аутентифицированных запросов в отношении [AI Time Series Insights REST,](https://docs.microsoft.com/rest/api/time-series-insights/)действительный маркер предъявителя OAuth 2.0 должен быть передан в [заголовке авторизации](/rest/api/apimanagement/2019-01-01/authorizationserver/createorupdate) с помощью клиента REST по вашему выбору (Postman, JavaScript, C). 

> [!TIP]
> Ознакомьте размещенную визуализацию [образца sDK клиента SDK,](https://tsiclientsample.azurewebsites.net/) чтобы ознакомиться с визуализацией, чтобы узнать, как проверить подлинность с помощью AA AIS серии времени, программно используя [SDK клиента JavaScript](https://github.com/microsoft/tsiclient/blob/master/docs/API.md) вместе с диаграммами и графиками.

### <a name="http-headers"></a>HTTP-заголовки

Необходимые заголовки запросов описаны ниже.

| Требуемый заголовок запроса | Описание |
| --- | --- |
| Авторизация | Для проверки подлинности с помощью Time Series Insights в заголовке **авторизации** необходимо передать действительный токен OAuth 2.0 Bearer. | 

> [!IMPORTANT]
> Токен должен быть выдан `https://api.timeseries.azure.com/` именно ресурсу (также известному как «аудитория» токена).
> * Ваш [почтальон](https://www.getpostman.com/) **AuthURL** поэтому будет:`https://login.microsoftonline.com/microsoft.onmicrosoft.com/oauth2/authorize?resource=https://api.timeseries.azure.com/`
> * `https://api.timeseries.azure.com/`действителен, `https://api.timeseries.azure.com` но не является.

Дополнительные заголовки запросов описаны ниже.

| Дополнительный заголовок запроса. | Описание |
| --- | --- |
| Content-type | поддерживается только. `application/json` |
| x-ms-client-request-id | Идентификатор запроса клиента. Служба записывает это значение. Позволяет службе отслеживать работу между службами. |
| x-ms-client-session-id | Идентификатор сеанса клиента. Служба записывает это значение. Позволяет службе отслеживать группу связанных операций между службами. |
| x-ms-client-application-name | Название приложения, которое создало этот запрос. Служба записывает это значение. |

Дополнительные, но рекомендуемые заголовки ответов описаны ниже.

| Заголовок ответа | Описание |
| --- | --- |
| Content-type | Поддерживается только `application/json`. |
| x-ms-request-id | Идентификатор запроса, генерируемого сервером. Можно использовать для обращения в корпорацию Майкрософт для расследования запроса. |
| x-ms-property-не-найдено-поведение | GA API необязательный заголовок ответа. Возможные `ThrowError` значения (по `UseNull`умолчанию) или . |

### <a name="http-parameters"></a>Параметры HTTP

> [!TIP]
> Более подробную информацию об обязательной и дополнительной информации о запросе можно найти в [справочной документации.](https://docs.microsoft.com/rest/api/time-series-insights/)

Требуемые параметры строки запроса URL-адреса зависят от версии API.

| Release | Возможные значения версии API |
| --- |  --- |
| Общедоступная версия | `api-version=2016-12-12`|
| Preview (Предварительный просмотр) | `api-version=2018-11-01-preview` |
| Preview (Предварительный просмотр) | `api-version=2018-08-15-preview` |

Дополнительные параметры строки запроса URL включают установку тайм-аута для времени выполнения запроса HTTP.

| Дополнительный параметр запроса | Описание | Версия |
| --- |  --- | --- |
| `timeout=<timeout>` | Тайм-аут на стороне сервера для выполнения запроса HTTP. Применяется только к [событиям Get Environment](https://docs.microsoft.com/rest/api/time-series-insights/ga-query-api#get-environment-events-api) и Get Environment [Aggregates](https://docs.microsoft.com/rest/api/time-series-insights/ga-query-api#get-environment-aggregates-api) AA. Значение тайм-аута должно быть в формате Продолжительности ISO 8601, например, `"PT20S"` и должно быть в диапазоне `1-30 s`. Значение по умолчанию: `30 s`. | GA |
| `storeType=<storeType>` | Для сред предварительного просмотра с включенным теплым магазином `WarmStore` `ColdStore`запрос может быть выполнен либо на . Этот параметр в запросе определяет, на каком хранении должен быть выполнен запрос. Если не определено, запрос будет выполнен в холодном магазине. Чтобы задать запрос в теплом магазине, **storeType** должен быть установлен на `WarmStore`. Если не определено, запрос будет выполнен против холодного магазина. | Preview (Предварительный просмотр) |

## <a name="next-steps"></a>Дальнейшие действия

- Для примера кода, который вызывает GA Time Series Insights API, прочитайте [данные запроса с помощью C .](./time-series-insights-query-data-csharp.md)

- Для предварительных версий API API-кода Серии Предварительного просмотра прочитайте [данные для предварительного просмотра запросов с помощью C .](./time-series-insights-update-query-data-csharp.md)

- Для справочной информации API прочитайте справочную документацию [API запроса.](https://docs.microsoft.com/rest/api/time-series-insights/ga-query-api)

- Узнайте, как [создать основной сервис.](../active-directory/develop/howto-create-service-principal-portal.md)
