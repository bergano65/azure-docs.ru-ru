---
title: Запрос данных из среды предварительной версии службы "аналитика временных рядов C# Azure" с помощью кода | Документация Майкрософт
description: Из этой статьи вы узнаете, как запрашивать данные из среды службы "Аналитика временных рядов Azure", создав код для пользовательского приложения на C# (c-sharp) .NET.
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.devlang: csharp
ms.workload: big-data
ms.topic: conceptual
ms.date: 10/08/2019
ms.custom: seodec18
ms.openlocfilehash: 46ade3ed6e8712a074974c81e51b2dd6c834db26
ms.sourcegitcommit: 92d42c04e0585a353668067910b1a6afaf07c709
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/28/2019
ms.locfileid: "72989661"
---
# <a name="query-data-from-the-azure-time-series-insights-preview-environment-using-c"></a>Запрос данных из среды предварительной версии службы "аналитика временных рядов Azure" с помощьюC#

В C# этом примере показано, как запросить данные из среды предварительной версии службы "аналитика временных рядов Azure".

В нем показано несколько простых способов использования API запроса:

1. На этапе подготовки следует получить маркер доступа через API Azure Active Directory. Передавайте этот маркер в заголовке `Authorization` каждого запроса API запроса. Сведения о настройке неинтерактивных приложений см. в статье [Проверка подлинности и авторизация для API Azure Time Series Insights](time-series-insights-authentication-and-authorization.md). Кроме того, убедитесь, что все константы, определенные в начале примера, указаны правильно.
1. Запрашивается список сред, к которым у пользователя есть доступ. Выбирается одна из этих сред, и последующие данные запрашиваются для этой среды.
1. В качестве примера HTTPS-запроса для выбранной среды запрашиваются данные о доступности.
1. В качестве примера запроса веб-сокета для выбранной среды запрашиваются объединенные данные событий. Данные запрашиваются для всего диапазона времени доступности.

> [!NOTE]
> Этот пример кода также доступен по адресу [https://github.com/Azure-Samples/Azure-Time-Series-Insights](https://github.com/Azure-Samples/Azure-Time-Series-Insights/tree/master/csharp-tsi-preview-sample).

## <a name="c-example"></a>Пример C#

[!code-csharp[csharpquery-example](~/samples-tsi/csharp-tsi-preview-sample/DataPlaneClientSampleApp/Program.cs)]

> [!NOTE]
> Приведенный выше пример кода можно запустить без изменения значений среды по умолчанию.

## <a name="next-steps"></a>Дальнейшие действия

- Дополнительные сведения о запросах см. в [справочнике по API запросов](https://docs.microsoft.com/rest/api/time-series-insights/preview-query).

- Узнайте, как [подключить приложение JavaScript с помощью клиентского пакета SDK к службе](https://github.com/microsoft/tsiclient) "аналитика временных рядов".