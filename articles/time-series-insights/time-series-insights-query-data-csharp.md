---
title: Запрос данных из среды Gen1 с помощью кода C# — Azure Time Series Insights Gen1 | Документация Майкрософт
description: Узнайте, как запрашивать данные из среды Gen1 временных рядов Azure с помощью пользовательского приложения, написанного на языке C#.
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: diviso
ms.devlang: csharp
ms.workload: big-data
ms.topic: conceptual
ms.date: 09/30/2020
ms.custom: seodec18
ms.openlocfilehash: a7dd67387a541ff62bfa85a7afc2f1bbf31c6180
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/09/2020
ms.locfileid: "91596438"
---
# <a name="query-data-from-the-azure-time-series-insights-gen1-environment-using-c-sharp"></a>Запрос данных из среды Gen1 "аналитика временных рядов Azure" с помощью языка C диезом

> [!CAUTION]
> Эта статья посвящена службе "Аналитика временных рядов Azure" 1-го поколения.

В этом примере C# демонстрируется использование [API-интерфейсов Gen1 запросов](https://docs.microsoft.com/rest/api/time-series-insights/gen1-query) для запроса данных из сред Gen1 "аналитика временных рядов Azure".

> [!TIP]
> Просмотрите примеры кода C# Gen1 по адресу [https://github.com/Azure-Samples/Azure-Time-Series-Insights](https://github.com/Azure-Samples/Azure-Time-Series-Insights/tree/master/gen1-sample/csharp-tsi-gen1-sample) .

## <a name="summary"></a>Сводка

Приведенный ниже пример кода демонстрирует следующие возможности.

* Получение маркера доступа с помощью Azure Active Directory с использованием [Microsoft.IdentityModel.Clients.ActiveDirectory](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory/).

* Как передать полученный маркер доступа в `Authorization` заголовке последующих запросов API запросов.

* В примере вызывается каждый из интерфейсов API запроса Gen1, демонстрирующих, как выполняются HTTP-запросы к:
  * [Получение API сред](https://docs.microsoft.com/rest/api/time-series-insights/gen1-query-api#get-environments-api) для возврата сред, к которым у пользователя есть доступ
  * [Получить API доступности среды](https://docs.microsoft.com/rest/api/time-series-insights/gen1-query-api#get-environment-availability-api)
  * [Получение API метаданных среды](https://docs.microsoft.com/rest/api/time-series-insights/gen1-query-api#get-environment-metadata-api) для получения метаданных среды
  * [Получение API событий среды](https://docs.microsoft.com/rest/api/time-series-insights/gen1-query-api#get-environment-events-api)
  * [Получение API для агрегатов среды](https://docs.microsoft.com/rest/api/time-series-insights/gen1-query-api#get-environment-aggregates-api)

* Как взаимодействовать с API запросов Gen1 с помощью WSS для сообщения:

  * [Получение потоковых API событий среды](https://docs.microsoft.com/rest/api/time-series-insights/gen1-query-api#get-environment-events-streamed-api)
  * [Получить статистические данные о среде потоковый API](https://docs.microsoft.com/rest/api/time-series-insights/gen1-query-api#get-environment-aggregates-streamed-api)

## <a name="prerequisites-and-setup"></a>Предварительные требования и настройка

Перед компиляцией и запуском примера кода выполните следующие шаги.

1. [Подготавливает среду Gen1 "аналитика временных рядов Azure](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-get-started) ".
1. Настройте среду службы Аналитики временных рядов Azure для Azure Active Directory, как описано в разделе [Проверка подлинности и авторизация](time-series-insights-authentication-and-authorization.md).
1. Установите необходимые зависимости проекта.
1. Измените приведенный ниже пример кода, заменив каждый **#DUMMY #** на соответствующий идентификатор среды.
1. Выполните код в Visual Studio.

## <a name="project-dependencies"></a>Зависимости проектов

Рекомендуется использовать последнюю версию Visual Studio:

* [Visual Studio 2019](https://visualstudio.microsoft.com/vs/) — версия 16.4.2 или более поздняя

Пример кода имеет две необходимые зависимости:

* Пакет [Microsoft. IdentityModel. Clients. ActiveDirectory](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory/) -3.13.9.
* [Newtonsoft.Js](https://www.nuget.org/packages/Newtonsoft.Json) пакет 9.0.1.

Скачайте пакеты в Visual Studio 2019, выбрав параметр **Сборка** > **Собрать решение**.

Кроме того, добавьте пакеты с помощью [NuGet 2.12 +](https://www.nuget.org/):

* `dotnet add package Newtonsoft.Json --version 9.0.1`
* `dotnet add package Microsoft.IdentityModel.Clients.ActiveDirectory --version 3.13.9`

## <a name="c-sample-code"></a>Пример кода C#

Чтобы получить доступ к образцу кода C#, обратитесь к репозиторию " [аналитика временных рядов Azure](https://github.com/Azure-Samples/Azure-Time-Series-Insights/blob/master/gen1-sample/csharp-tsi-gen1-sample/Program.cs)".

## <a name="next-steps"></a>Дальнейшие действия

* Дополнительные сведения о запросах см. в [справочнике по API запросов](https://docs.microsoft.com/rest/api/time-series-insights/gen1-query-api).

* Узнайте, как [подключить приложение JavaScript с помощью клиентского пакета SDK](https://github.com/microsoft/tsiclient) к службе Аналитики временных рядов Azure.
Azure-Samples/Azure-Time-Series-Insights/Gen1-Sample/CSharp-TSI-Gen1-Sample/Program. CS
