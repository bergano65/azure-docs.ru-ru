---
title: Запрос данных из общедоступной среды службы "аналитика временных рядов C# Azure" с помощью кода | Документация Майкрософт
description: Из этой статьи вы узнаете, как запрашивать данные из среды службы "Аналитика временных рядов Azure", создав код для пользовательского приложения на C# (c-sharp) .NET.
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.devlang: csharp
ms.workload: big-data
ms.topic: conceptual
ms.date: 10/03/2019
ms.custom: seodec18
ms.openlocfilehash: 23e4ad5667019b5bc38f9ba11d14632d4d55d5b7
ms.sourcegitcommit: 92d42c04e0585a353668067910b1a6afaf07c709
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/28/2019
ms.locfileid: "72989978"
---
# <a name="query-data-from-the-azure-time-series-insights-ga-environment-using-c"></a>Запрос данных из общедоступной среды службы "аналитика временных рядов Azure" с помощьюC#

В C# этом примере показано, как запросить данные из среды "аналитика временных рядов Azure".

В нем показано несколько простых способов использования API запроса:

1. На этапе подготовки следует получить маркер доступа через API Azure Active Directory. Передавайте этот маркер в заголовке `Authorization` каждого запроса API запроса. Сведения о настройке неинтерактивных приложений см. в статье [Проверка подлинности и авторизация для API Azure Time Series Insights](time-series-insights-authentication-and-authorization.md). Кроме того, убедитесь, что все константы, определенные в начале примера, указаны правильно.
1. Запрашивается список сред, к которым у пользователя есть доступ. Выбирается одна из этих сред, и последующие данные запрашиваются для этой среды.
1. В качестве примера HTTPS-запроса для выбранной среды запрашиваются данные о доступности.
1. В качестве примера запроса веб-сокета для выбранной среды запрашиваются объединенные данные событий. Данные запрашиваются для всего диапазона времени доступности.

> [!NOTE]
> Пример кода доступен по адресу [https://github.com/Azure-Samples/Azure-Time-Series-Insights](https://github.com/Azure-Samples/Azure-Time-Series-Insights/tree/master/csharp-tsi-ga-sample).

## <a name="project-dependencies"></a>Зависимости проекта

Добавьте пакеты NuGet `Microsoft.IdentityModel.Clients.ActiveDirectory` и `Newtonsoft.Json`.

## <a name="c-example"></a>Пример C#

[!code-csharp[csharpquery-example](~/samples-tsi/csharp-tsi-ga-sample/Program.cs)]

## <a name="next-steps"></a>Дальнейшие действия

- Дополнительные сведения о запросах см. в [справочнике по API запросов](https://docs.microsoft.com/rest/api/time-series-insights/ga-query-api).

- Узнайте, как [подключить приложение JavaScript с помощью клиентского пакета SDK к службе](https://github.com/microsoft/tsiclient) "аналитика временных рядов".