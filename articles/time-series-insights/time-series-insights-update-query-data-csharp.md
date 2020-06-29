---
title: Запрос данных из среды предварительной версии с помощью кода C# — Аналитика временных рядов Azure | Документация Майкрософт
description: Узнайте, как запрашивать данные из среды службы Аналитики временных рядов Azure с помощью приложения, написанного на C#.
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2020
ms.locfileid: "81379817"
---
# <a name="query-data-from-the-azure-time-series-insights-preview-environment-using-c"></a>Запрос данных из среды Аналитика временных рядов Azure (предварительная версия) с помощью C#

В этом C# примере показано, как запросить данные из [предварительной версии API доступа к данным](https://docs.microsoft.com/rest/api/time-series-insights/preview) в средах службы Аналитики временных рядов Azure (предварительная версия).

> [!TIP]
> Ознакомьтесь с примерами кода предварительной версии C# на сайте [https://github.com/Azure-Samples/Azure-Time-Series-Insights](https://github.com/Azure-Samples/Azure-Time-Series-Insights/tree/master/csharp-tsi-preview-sample).

## <a name="summary"></a>Сводка

Приведенный ниже пример кода демонстрирует следующие возможности.

* Поддержка автоматического создания пакетов SDK из [Azure AutoRest](https://github.com/Azure/AutoRest).
* Получение маркера доступа с помощью Azure Active Directory с использованием [Microsoft.IdentityModel.Clients.ActiveDirectory](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory/).
* Передача полученного маркера доступа в заголовок `Authorization` последующих запросов API доступа к данным. 
* В примере показан интерфейс консоли, демонстрирующий, как выполняются HTTP-запросы к:

    * [API сред предварительной версии](https://docs.microsoft.com/rest/api/time-series-insights/preview#preview-environments-apis)
        * [API получения сведений о доступности сред](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/query/getavailability) и [API получения схемы событий](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/query/geteventschema)
    * [API запросов предварительной версии](https://docs.microsoft.com/rest/api/time-series-insights/preview#query-apis)
        * [API получения событий](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/query/execute#getevents), [API получения последовательности](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/query/execute#getseries) и [API получения последовательности статических выражений](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/query/execute#aggregateseries)
    * [API модели временных рядов](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/query/execute#aggregateseries)
        * [API получения иерархий](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/timeserieshierarchies/get) и [API пакета иерархий](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/timeserieshierarchies/executebatch)
        * [API получения типов](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/timeseriestypes/get) и [API пакета типов](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/timeseriestypes/executebatch)
        * [API получения экземпляров](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/timeseriesinstances/get) и [API пакета экземпляров](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/timeseriesinstances/executebatch)
* Возможности расширенного [поиска](https://docs.microsoft.com/rest/api/time-series-insights/preview#search-features) и [TSX](https://docs.microsoft.com/rest/api/time-series-insights/preview#time-series-expression-and-syntax).

## <a name="prerequisites-and-setup"></a>Предварительные требования и настройка

Перед компиляцией и запуском примера кода выполните следующие шаги.

1. [Подготовьте предварительную версию среды Аналитики временных рядов Azure к работе](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-update-how-to-manage#create-the-environment).
1. Настройте среду службы Аналитики временных рядов Azure для Azure Active Directory, как описано в разделе [Проверка подлинности и авторизация](time-series-insights-authentication-and-authorization.md). 
1. Запустите файл [GenerateCode.bat](https://github.com/Azure-Samples/Azure-Time-Series-Insights/blob/master/csharp-tsi-preview-sample/DataPlaneClient/GenerateCode.bat), как указано в [Readme.md](https://github.com/Azure-Samples/Azure-Time-Series-Insights/blob/master/csharp-tsi-preview-sample/DataPlaneClient/Readme.md), чтобы создать зависимости клиентов Аналитики временных рядов Azure (предварительная версия).
1. Откройте решение `TSIPreviewDataPlaneclient.sln` и задайте `DataPlaneClientSampleApp` в качестве проекта по умолчанию в Visual Studio.
1. Установите необходимые зависимости проекта, выполнив действия, описанные [ниже](#project-dependencies), и скомпилируйте пример в исполняемый файл `.exe`.
1. Запустите файл `.exe`, дважды щелкнув его.

## <a name="project-dependencies"></a>Зависимости проектов

Рекомендуется использовать последнюю версию Visual Studio:

* [Visual Studio 2019](https://visualstudio.microsoft.com/vs/) — версия 16.4.2 или более поздняя

В примере кода имеется несколько обязательных зависимостей, которые можно просмотреть в файле [packages.config](https://github.com/Azure-Samples/Azure-Time-Series-Insights/blob/master/csharp-tsi-preview-sample/DataPlaneClientSampleApp/packages.config).

Скачайте пакеты в Visual Studio 2019, выбрав параметр **Сборка** > **Собрать решение**. 

Кроме того, добавьте каждый пакет с помощью [NuGet 2.12 +](https://www.nuget.org/). Пример:

* `dotnet add package Microsoft.IdentityModel.Clients.ActiveDirectory --version 4.5.1`

## <a name="c-sample-code"></a>Пример кода C#

[!code-csharp[csharpquery-example](~/samples-tsi/csharp-tsi-preview-sample/DataPlaneClientSampleApp/Program.cs)]

> [!NOTE]
> * Пример кода можно выполнить без изменения переменных среды по умолчанию.
> * Пример кода компилируется в исполняемое консольное приложение .NET.

## <a name="next-steps"></a>Дальнейшие действия

- Дополнительные сведения о запросах см. в [справочнике по API запросов](https://docs.microsoft.com/rest/api/time-series-insights/preview-query).

- Узнайте, как [подключить приложение JavaScript с помощью клиентского пакета SDK](https://github.com/microsoft/tsiclient) к службе Аналитики временных рядов Azure.
