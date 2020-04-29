---
title: Запрос данных из общедоступной среды с помощью кода C# — Azure Time Series Insights | Документация Майкрософт
description: Узнайте, как запрашивать данные из среды службы "аналитика временных рядов Azure" с помощью пользовательского приложения, написанного на языке C#.
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.devlang: csharp
ms.workload: big-data
ms.topic: conceptual
ms.date: 04/14/2020
ms.custom: seodec18
ms.openlocfilehash: 754d1b80236d138693987cccee7a218ccd96b16b
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2020
ms.locfileid: "81383883"
---
# <a name="query-data-from-the-azure-time-series-insights-ga-environment-using-c"></a>Запрос данных из общедоступной среды службы "аналитика временных рядов Azure" с помощью C #

В этом примере C# показано, как использовать [API-интерфейсы](https://docs.microsoft.com/rest/api/time-series-insights/ga-query) общедоступных запросов для запроса данных из общедоступных сред службы "аналитика временных рядов Azure".

> [!TIP]
> Просмотрите общедоступные примеры кода [https://github.com/Azure-Samples/Azure-Time-Series-Insights](https://github.com/Azure-Samples/Azure-Time-Series-Insights/tree/master/csharp-tsi-ga-sample)C# по адресу.

## <a name="summary"></a>Сводка

Приведенный ниже пример кода демонстрирует следующие возможности.

* Получение маркера доступа с помощью Azure Active Directory с помощью [Microsoft. IdentityModel. Clients. ActiveDirectory](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory/).

* Как передать полученный маркер доступа в `Authorization` заголовке последующих запросов API запросов. 

* В примере вызывается каждый из общедоступных интерфейсов API запроса, демонстрирующих, как выполняются HTTP-запросы к:
    * [Получение API сред](https://docs.microsoft.com/rest/api/time-series-insights/ga-query-api#get-environments-api) для возврата сред, к которым у пользователя есть доступ
    * [Получить API доступности среды](https://docs.microsoft.com/rest/api/time-series-insights/ga-query-api#get-environment-availability-api)
    * [Получение API метаданных среды](https://docs.microsoft.com/rest/api/time-series-insights/ga-query-api#get-environment-metadata-api) для получения метаданных среды
    * [Получение API событий среды](https://docs.microsoft.com/rest/api/time-series-insights/ga-query-api#get-environment-events-api)
    * [Получение API для агрегатов среды](https://docs.microsoft.com/rest/api/time-series-insights/ga-query-api#get-environment-aggregates-api)
    
* Как взаимодействовать с API-интерфейсами общедоступных запросов с помощью WSS для сообщения:

   * [Получение потоковых API событий среды](https://docs.microsoft.com/rest/api/time-series-insights/ga-query-api#get-environment-events-streamed-api)
   * [Получить статистические данные о среде потоковый API](https://docs.microsoft.com/rest/api/time-series-insights/ga-query-api#get-environment-aggregates-streamed-api)

## <a name="prerequisites-and-setup"></a>Предварительные требования и установка

Перед компиляцией и запуском примера кода выполните следующие шаги.

1. Подготавливает общедоступную среду службы " [аналитика временных рядов Azure](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-get-started) ".
1. Настройте среду службы "аналитика временных рядов Azure" для Azure Active Directory, как описано в статье [Проверка подлинности и авторизация](time-series-insights-authentication-and-authorization.md). 
1. Установите необходимые зависимости проекта.
1. Измените приведенный ниже пример кода, заменив каждый **#DUMMY #** на соответствующий идентификатор среды.
1. Выполните код в Visual Studio.

## <a name="project-dependencies"></a>Зависимости проектов

Рекомендуется использовать последнюю версию Visual Studio:

* [Visual Studio 2019](https://visualstudio.microsoft.com/vs/) — версия 16.4.2 +

Образец кода имеет две необходимые зависимости:

* Пакет [Microsoft. IdentityModel. Clients. ActiveDirectory](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory/) -3.13.9.
* [Newtonsoft. JSON](https://www.nuget.org/packages/Newtonsoft.Json) — пакет 9.0.1.

Скачайте пакеты в Visual Studio 2019, выбрав параметр **Build** > **Build Solution** .

Кроме того, добавьте пакеты с помощью [NuGet 2.12 +](https://www.nuget.org/):

* `dotnet add package Newtonsoft.Json --version 9.0.1`
* `dotnet add package Microsoft.IdentityModel.Clients.ActiveDirectory --version 3.13.9`

## <a name="c-sample-code"></a>Пример кода C#

[!code-csharp[csharpquery-example](~/samples-tsi/csharp-tsi-ga-sample/Program.cs)]

## <a name="next-steps"></a>Дальнейшие шаги

- Дополнительные сведения о запросах см. в [справочнике по API запросов](https://docs.microsoft.com/rest/api/time-series-insights/ga-query-api).

- Узнайте, как [подключить приложение JavaScript с помощью клиентского пакета SDK к службе](https://github.com/microsoft/tsiclient) "аналитика временных рядов".
