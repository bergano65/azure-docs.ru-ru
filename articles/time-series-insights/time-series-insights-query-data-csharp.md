---
title: Запрос данных из среды Azure время Series Insights GA с помощью C# кода | Документация Майкрософт
description: Из этой статьи вы узнаете, как запрашивать данные из среды службы "Аналитика временных рядов Azure", создав код для пользовательского приложения на C# (c-sharp) .NET.
ms.service: time-series-insights
services: time-series-insights
author: ashannon7
ms.author: anshan
manager: cshankar
reviewer: jasonwhowell, kfile, tsidocs
ms.devlang: csharp
ms.workload: big-data
ms.topic: conceptual
ms.date: 05/09/2019
ms.custom: seodec18
ms.openlocfilehash: 5e8b8d47b04d7d0b93bc699064ee414bf4429c4a
ms.sourcegitcommit: 8fc5f676285020379304e3869f01de0653e39466
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/09/2019
ms.locfileid: "65510200"
---
# <a name="query-data-from-the-azure-time-series-insights-ga-environment-using-c"></a>Запрос данных с помощью среды Azure время Series Insights GAC#

Это C# примере показано, как запрашивать данные из среды времени серии Azure Insights-общедоступной версии. 

В нем показано несколько простых способов использования API запроса:

1. На этапе подготовки следует получить маркер доступа через API Azure Active Directory. Передавайте этот маркер в заголовке `Authorization` каждого запроса API запроса. Сведения о настройке неинтерактивных приложений см. в статье [Проверка подлинности и авторизация для API Azure Time Series Insights](time-series-insights-authentication-and-authorization.md). Кроме того, убедитесь, что все константы, определенные в начале примера, указаны правильно.
1. Запрашивается список сред, к которым у пользователя есть доступ. Выбирается одна из этих сред, и последующие данные запрашиваются для этой среды.
1. В качестве примера HTTPS-запроса для выбранной среды запрашиваются данные о доступности.
1. В качестве примера запроса веб-сокета для выбранной среды запрашиваются объединенные данные событий. Данные запрашиваются для всего диапазона времени доступности.

> [!NOTE]
> Пример кода доступен на [ https://github.com/Azure-Samples/Azure-Time-Series-Insights ](https://github.com/Azure-Samples/Azure-Time-Series-Insights/tree/master/csharp-ga-preview-sample).

## <a name="project-dependencies"></a>Зависимости проекта

Добавьте пакеты NuGet `Microsoft.IdentityModel.Clients.ActiveDirectory` и `Newtonsoft.Json`.

## <a name="c-example"></a>Пример C#

[!code-csharp[csharpquery-example](~/samples-tsi/csharp-tsi-ga-sample/Program.cs)]

## <a name="next-steps"></a>Дальнейшие действия

- Дополнительные сведения о запросах к [Справочник по API запроса](/rest/api/time-series-insights/ga-query-api).

- Узнайте, как для [подключение одностраничного приложения JavaScript](tutorial-create-tsi-sample-spa.md) для Time Series Insights.