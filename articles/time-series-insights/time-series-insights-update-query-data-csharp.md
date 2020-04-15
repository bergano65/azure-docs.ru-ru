---
title: Данные запроса из среды предварительного просмотра с помощью C - Azure Time Series Исследования (ru) Документы Майкрософт
description: Узнайте, как заставить данные из среды Azure Time Series Insights с помощью приложения, написанного на C..
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
ms.openlocfilehash: fbc2cbc29cb23a21e7d3713091fc22f01bb1b15a
ms.sourcegitcommit: ea006cd8e62888271b2601d5ed4ec78fb40e8427
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/14/2020
ms.locfileid: "81379817"
---
# <a name="query-data-from-the-azure-time-series-insights-preview-environment-using-c"></a>Данные запросов из среды просмотра обзоров временных обзоров Azure Time #

В этом примере на примере C's показано, как загонять данные из [AA-ино-развязок доступа к данным Preview Data Access](https://docs.microsoft.com/rest/api/time-series-insights/preview) в средах Preview Time Series.

> [!TIP]
> Просмотр образцов кода Preview [https://github.com/Azure-Samples/Azure-Time-Series-Insights](https://github.com/Azure-Samples/Azure-Time-Series-Insights/tree/master/csharp-tsi-preview-sample)C' на .

## <a name="summary"></a>Сводка

Приведенный ниже пример кода демонстрирует следующие особенности:

* Поддержка автоматического поколения SDK от [Azure AutoRest.](https://github.com/Azure/AutoRest)
* Как приобрести токен доступа через Активный каталог Azure с помощью [Microsoft.IdentityModel.Customers.ActiveDirectory.](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory/)
* Как передать тот же токен, приобретенный доступ, в заголовке `Authorization` последующих запросов API доступа к данным. 
* В примере представлен консольный интерфейс, демонстрирующий, как запросы HTTP делаются на:

    * [Предварительный API среды](https://docs.microsoft.com/rest/api/time-series-insights/preview#preview-environments-apis)
        * [Получите API доступности среды](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/query/getavailability) и [API schema](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/query/geteventschema)
    * [Предварительный aPI запроса](https://docs.microsoft.com/rest/api/time-series-insights/preview#query-apis)
        * [Получите API событий,](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/query/execute#getevents) [получите API серии](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/query/execute#getseries)и [агрегированный API серии](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/query/execute#aggregateseries)
    * [Модель AIS серии времени](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/query/execute#aggregateseries)
        * [Получить API иерархий](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/timeserieshierarchies/get) и [API пакета иерархий](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/timeserieshierarchies/executebatch)
        * [Получить API типов](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/timeseriestypes/get) и [типы aPI](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/timeseriestypes/executebatch)
        * [Получите API ИА "Instances"](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/timeseriesinstances/get) и [API пакетных инстанций instances](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/timeseriesinstances/executebatch)
* Расширенный [поиск](https://docs.microsoft.com/rest/api/time-series-insights/preview#search-features) и [TSX](https://docs.microsoft.com/rest/api/time-series-insights/preview#time-series-expression-and-syntax) возможности.

## <a name="prerequisites-and-setup"></a>Предпосылки и настройки

Перед компиляцией и запуском примера кода выполните следующие шаги.

1. [Предоставить среду исследования временных обзоров Для просмотра Azure.](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-update-how-to-manage#create-the-environment)
1. Наверсможно настройку среды Azure Time Series Insights для active Directory Azure, описанную в [аутентификации и авторизации.](time-series-insights-authentication-and-authorization.md) 
1. Запустите [GenerateCode.bat,](https://github.com/Azure-Samples/Azure-Time-Series-Insights/blob/master/csharp-tsi-preview-sample/DataPlaneClient/GenerateCode.bat) как указано в [Readme.md](https://github.com/Azure-Samples/Azure-Time-Series-Insights/blob/master/csharp-tsi-preview-sample/DataPlaneClient/Readme.md) для создания зависимостей клиентов Time Series Insights Preview.
1. Откройте `TSIPreviewDataPlaneclient.sln` решение `DataPlaneClientSampleApp` и установите как проект по умолчанию в Visual Studio.
1. Установите требуемые зависимости проекта с помощью описанных [ниже](#project-dependencies) `.exe` шагов и компилировать пример в исполняемый файл.
1. Выполнить `.exe` файл, дважды нажав на него.

## <a name="project-dependencies"></a>Зависимости проектов

Рекомендуется использовать новейшую версию Visual Studio:

* [Визуальная студия 2019](https://visualstudio.microsoft.com/vs/) - Версия 16.4.2

Код образца имеет несколько требуемых зависимостей, которые можно просмотреть в файле [packages.config.](https://github.com/Azure-Samples/Azure-Time-Series-Insights/blob/master/csharp-tsi-preview-sample/DataPlaneClientSampleApp/packages.config)

Загрузите пакеты в Visual Studio 2019, выбрав опцию **Build** > **Build Solution.** 

Кроме того, добавьте каждый пакет с помощью [NuGet 2.12 .](https://www.nuget.org/) Пример:

* `dotnet add package Microsoft.IdentityModel.Clients.ActiveDirectory --version 4.5.1`

## <a name="c-sample-code"></a>Пример кода C#

[!code-csharp[csharpquery-example](~/samples-tsi/csharp-tsi-preview-sample/DataPlaneClientSampleApp/Program.cs)]

> [!NOTE]
> * Образец кода может быть выполнен без изменения переменных среды по умолчанию.
> * Образец кода будет компилироваться в исполняемое консольное приложение .NET.

## <a name="next-steps"></a>Следующие шаги

- Чтобы узнать больше о запросе, прочитайте [ссылку API запроса](https://docs.microsoft.com/rest/api/time-series-insights/preview-query).

- Узнайте, как [подключить приложение JavaScript с помощью клиента SDK](https://github.com/microsoft/tsiclient) к Time Series Insights.
