---
title: Данные запросов из среды GA с использованием кода C - Azure Time Series Исследования (ru) Документы Майкрософт
description: Узнайте, как заставить данные из среды Azure Time Series Insights с помощью пользовательского приложения, написанного на C..
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
ms.sourcegitcommit: ea006cd8e62888271b2601d5ed4ec78fb40e8427
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/14/2020
ms.locfileid: "81383883"
---
# <a name="query-data-from-the-azure-time-series-insights-ga-environment-using-c"></a>Данные запросов из среды Azure Time Series Insights GA с использованием C #

В этом примере на примере C's показано, как использовать [AAP-запросы GA-запросов](https://docs.microsoft.com/rest/api/time-series-insights/ga-query) для запроса данных из сред Azure Time Series Insights GA.

> [!TIP]
> Посмотреть образцы кода [https://github.com/Azure-Samples/Azure-Time-Series-Insights](https://github.com/Azure-Samples/Azure-Time-Series-Insights/tree/master/csharp-tsi-ga-sample)GA C' на .

## <a name="summary"></a>Сводка

Приведенный ниже пример кода демонстрирует следующие особенности:

* Как приобрести токен доступа через Активный каталог Azure с помощью [Microsoft.IdentityModel.Customers.ActiveDirectory.](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory/)

* Как передать тот же токен, приобретенный доступ, в заголовке `Authorization` последующих запросов API запросов Запроса. 

* Выборка вызывает каждый из AIS запросов GA, демонстрирующих, как запросы HTTP делаются на:
    * [Получите API среды](https://docs.microsoft.com/rest/api/time-series-insights/ga-query-api#get-environments-api) для возврата сред, к которой пользователь имеет доступ
    * [Получить API доступности среды](https://docs.microsoft.com/rest/api/time-series-insights/ga-query-api#get-environment-availability-api)
    * [Получите API метаданных среды](https://docs.microsoft.com/rest/api/time-series-insights/ga-query-api#get-environment-metadata-api) для извлечения метаданных среды
    * [API событий среды](https://docs.microsoft.com/rest/api/time-series-insights/ga-query-api#get-environment-events-api)
    * [Получить агрегирует API среды](https://docs.microsoft.com/rest/api/time-series-insights/ga-query-api#get-environment-aggregates-api)
    
* Как взаимодействовать с AA-аПО запроса GA с помощью WSS для сообщения:

   * [Получите события окружающей среды потокового API](https://docs.microsoft.com/rest/api/time-series-insights/ga-query-api#get-environment-events-streamed-api)
   * [Получить окружающую среду агрегаты потокового API](https://docs.microsoft.com/rest/api/time-series-insights/ga-query-api#get-environment-aggregates-streamed-api)

## <a name="prerequisites-and-setup"></a>Предпосылки и настройки

Перед компиляцией и запуском примера кода выполните следующие шаги.

1. [Предоставление среды анализа временных рядов GA Azure.](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-get-started)
1. Наверсможно настройку среды Azure Time Series Insights для active Directory Azure, описанную в [аутентификации и авторизации.](time-series-insights-authentication-and-authorization.md) 
1. Установите требуемые зависимости проекта.
1. Изналивайте пример кода ниже, заменяя каждую **#DUMMY** с соответствующим идентификатором среды.
1. Выполните код внутри Visual Studio.

## <a name="project-dependencies"></a>Зависимости проектов

Рекомендуется использовать новейшую версию Visual Studio:

* [Визуальная студия 2019](https://visualstudio.microsoft.com/vs/) - Версия 16.4.2

Код выборки имеет две необходимые зависимости:

* [Microsoft.IdentityModel.Customers.ActiveDirectory](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory/) - 3.13.9 пакет.
* [Ньютонсофт.Джсон](https://www.nuget.org/packages/Newtonsoft.Json) - 9.0.1 пакет.

Загрузите пакеты в Visual Studio 2019, выбрав опцию **Build** > **Build Solution.**

Кроме того, добавьте пакеты с помощью [NuGet 2.12 "](https://www.nuget.org/)

* `dotnet add package Newtonsoft.Json --version 9.0.1`
* `dotnet add package Microsoft.IdentityModel.Clients.ActiveDirectory --version 3.13.9`

## <a name="c-sample-code"></a>Пример кода C#

[!code-csharp[csharpquery-example](~/samples-tsi/csharp-tsi-ga-sample/Program.cs)]

## <a name="next-steps"></a>Следующие шаги

- Чтобы узнать больше о запросе, прочитайте [ссылку API запроса](https://docs.microsoft.com/rest/api/time-series-insights/ga-query-api).

- Узнайте, как [подключить приложение JavaScript с помощью клиента SDK](https://github.com/microsoft/tsiclient) к Time Series Insights.
