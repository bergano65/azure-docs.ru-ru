---
title: Модель данных телеметрии Azure Application Insights — телеметрия зависимостей | Документы Майкрософт
description: Модель данных Application Insights для телеметрии зависимостей
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: mrbullwinkle
ms.author: mbullwin
ms.date: 04/17/2017
ms.reviewer: sergkanz
ms.openlocfilehash: e0923c20b11aa02f380af1faa6766d2346ad1fb2
ms.sourcegitcommit: 1bd2207c69a0c45076848a094292735faa012d22
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/21/2019
ms.locfileid: "72677436"
---
# <a name="dependency-telemetry-application-insights-data-model"></a>Телеметрия зависимостей: модель данных Application Insights

Телеметрия зависимостей (в [Application Insights](../../azure-monitor/app/app-insights-overview.md)) представляет взаимодействие отслеживаемого компонента с удаленным, таким как SQL или конечная точка HTTP.

## <a name="name"></a>Name

Имя команды, инициированной этим вызовом зависимости. Низкое значение кратности. Примерами являются имя хранимой процедуры и шаблон пути URL-адреса.

## <a name="id"></a>ИД

Идентификатор для экземпляра вызова зависимости. Используется для корреляции с элементом телеметрии запроса, соответствующим этому вызову зависимости. Дополнительные сведения см. на странице [корреляции](../../azure-monitor/app/correlation.md).

## <a name="data"></a>Данные

Команда, инициированная этим вызовом зависимости. Примерами являются оператор SQL и HTTP URL со всеми параметрами запроса.

## <a name="type"></a>Тип

Имя типа зависимости. Низкое значение кратности для логической группировки зависимостей и интерпретации других полей, таких как commandName и resultCode. Примерами являются SQL, таблица Azure и HTTP.

## <a name="target"></a>Выбор пути миграции

Целевой сайт вызова зависимости. Примерами являются имя сервера, адрес узла. Дополнительные сведения см. на странице [корреляции](../../azure-monitor/app/correlation.md).

## <a name="duration"></a>Duration

Длительность запроса в формате: `DD.HH:MM:SS.MMMMMM`. Должно быть меньше `1000` дн.

## <a name="result-code"></a>Код результата

Код результата для вызова зависимости. Примерами являются код ошибки SQL и код состояния HTTP.

## <a name="success"></a>Успешно

Указание того, был ли вызов успешным.

## <a name="custom-properties"></a>Пользовательские свойства

[!INCLUDE [application-insights-data-model-properties](../../../includes/application-insights-data-model-properties.md)]

## <a name="custom-measurements"></a>Пользовательские измерения

[!INCLUDE [application-insights-data-model-measurements](../../../includes/application-insights-data-model-measurements.md)]


## <a name="next-steps"></a>Дальнейшие действия

- Настройка отслеживания зависимостей для платформы [.NET](../../azure-monitor/app/asp-net-dependencies.md)
- Настройка отслеживания зависимостей для платформы [Java](../../azure-monitor/app/java-agent.md)
- [Создание телеметрии настраиваемых зависимостей](../../azure-monitor/app/api-custom-events-metrics.md#trackdependency)
- В [этой статье](data-model.md) представлены типы данных и модель данных для Application Insights.
- Ознакомление с [платформами](../../azure-monitor/app/platforms.md), поддерживаемыми Application Insights.
