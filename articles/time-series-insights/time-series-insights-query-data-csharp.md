---
title: Запрос данных из общедоступной среды C# с помощью кода — служба "аналитика временных рядов Azure" | Документация Майкрософт
description: Узнайте, как запрашивать данные из среды службы "аналитика временных рядов Azure" с помощью пользовательского приложения C#, написанного на.
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.devlang: csharp
ms.workload: big-data
ms.topic: conceptual
ms.date: 02/03/2020
ms.custom: seodec18
ms.openlocfilehash: 9f7819974e3548baf5e10f0bf9a2d656d9412beb
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/04/2020
ms.locfileid: "76987977"
---
# <a name="query-data-from-the-azure-time-series-insights-ga-environment-using-c"></a>Запрос данных из общедоступной среды службы "аналитика временных рядов Azure" с помощьюC#

В C# этом примере показано, как использовать [API-интерфейсы](https://docs.microsoft.com/rest/api/time-series-insights/ga-query) общедоступных запросов для запроса данных из сред "аналитика временных рядов Azure".

> [!TIP]
> Просмотрите общедоступные примеры C# кода на [https://github.com/Azure-Samples/Azure-Time-Series-Insights](https://github.com/Azure-Samples/Azure-Time-Series-Insights/tree/master/csharp-tsi-ga-sample).

## <a name="summary"></a>Сводка

Приведенный ниже пример кода демонстрирует следующие возможности.

* Получение маркера доступа с помощью Azure Active Directory с помощью [Microsoft. IdentityModel. Clients. ActiveDirectory](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory/).

* Как передать полученный маркер доступа в заголовке `Authorization` последующих запросов API запросов. 

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

Скачайте пакеты в Visual Studio 2019, выбрав параметр **build** > **Build Solution** .

Кроме того, добавьте пакеты с помощью [NuGet 2.12 +](https://www.nuget.org/):

* `dotnet add package Newtonsoft.Json --version 9.0.1`
* `dotnet add package Microsoft.IdentityModel.Clients.ActiveDirectory --version 3.13.9`

## <a name="c-sample-code"></a>Пример кода C#

[!code-csharp[csharpquery-example](~/samples-tsi/csharp-tsi-ga-sample/Program.cs)]

## <a name="next-steps"></a>Дальнейшие действия

- Дополнительные сведения о запросах см. в [справочнике по API запросов](https://docs.microsoft.com/rest/api/time-series-insights/ga-query-api).

- Узнайте, как [подключить приложение JavaScript с помощью клиентского пакета SDK к службе](https://github.com/microsoft/tsiclient) "аналитика временных рядов".
