---
title: Распределенная трассировка в Azure Application Insights | Документация Майкрософт
description: Предоставляет информацию о поддержке корпорацией Майкрософт распределенного отслеживания через наше партнерство в проекте OpenCensus
ms.topic: conceptual
author: nikmd23
ms.author: nimolnar
ms.date: 09/17/2018
ms.reviewer: mbullwin
ms.openlocfilehash: 83575aa7f9b63615f453e00bd06b00a5540b9a9e
ms.sourcegitcommit: df8b2c04ae4fc466b9875c7a2520da14beace222
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/08/2020
ms.locfileid: "80892263"
---
# <a name="what-is-distributed-tracing"></a>Что такое распределенная трассировка?

Появление современных архитектур облачных и [микрослужб](https://azure.com/microservices) привело к появлению простых, независимо развертываемых служб, которые могут помочь снизить затраты при одновременном повышении доступности и пропускной готовности. Но в то время как эти движения сделали отдельные услуги легче понять в целом, они сделали общие системы более трудно рассуждать о и отладки.

В монолитных архитектурах мы привыкли отлажьсты со стеками вызовов. Стеки вызовов — это блестящие инструменты для отображения потока выполнения (метод A, вызываемый методом Б, который вызывается методом В), а также данных и параметров каждого из этих вызовов. Это очень удобно для монолитных структур или служб, работающих в одном процессе, но как проводить отладку, когда вызов проходит через границу процесса, а не является просто ссылкой на локальный стек? 

Вот где распределенный розыск приходит дюйма  

Распределенная трассировка является эквивалентом стеков вызовов для современных облачных архитектур и архитектур микрослужб с добавлением упрощенного профилировщика производительности. В Azure Monitor предлагаются два варианта использования распределенной трассировки данных. Первое — это наша [диагностика транзакций](https://docs.microsoft.com/azure/application-insights/app-insights-transaction-diagnostics), которая похожа на стек вызовов с добавлением измерения времени. Представление диагностики транзакций обеспечивает видимость в одной единственной транзакции или запросе и помогает найти основную причину проблем с безопасностью и узкие места производительности для каждого запроса.

Azure Monitor также предлагает представление [схемы приложений](https://docs.microsoft.com/azure/application-insights/app-insights-app-map), которая объединяет много транзакций для отображения топологического вида взаимодействия систем и среднего коэффициента производительности и ошибок. 

## <a name="how-to-enable-distributed-tracing"></a>Включение распределенной трассировки

Включить распределенный отслеживание между службами в приложении так же просто, как добавить в каждую службу соответствующий агент, SDK или библиотеку на основе языка, на котором была реализована служба.

## <a name="enabling-via-application-insights-through-auto-instrumentation-or-sdks"></a>Включение через Application Insights через автоинструментарий или SDK

Агенты Application Insights и/или SDK для .NET, .NET Core, Java, Node.js и JavaScript поддерживают распределенный отслеживание нативной основе. Инструкции по установке и настройке каждого пакета SDK Application Insights приведены ниже.

* [.NET](https://docs.microsoft.com/azure/application-insights/quick-monitor-portal)
* [.NET Core](https://docs.microsoft.com/azure/application-insights/app-insights-dotnetcore-quick-start)
* [Java](https://docs.microsoft.com/azure/azure-monitor/app/java-in-process-agent)
* [Node.js](https://docs.microsoft.com/azure/application-insights/app-insights-nodejs-quick-start)
* [JavaScript](https://docs.microsoft.com/azure/application-insights/app-insights-javascript)
* [Python](opencensus-python.md)

При установке и настройке надлежащего пакета SDK Application Insights для популярных фреймворков, библиотек и технологий информация о трассировке автоматически собирается с помощью сборщиков зависимостей SDK. Полный список поддерживаемых технологий доступен в [документации по автоматическому сбору зависимостей](https://docs.microsoft.com/azure/application-insights/auto-collect-dependencies).

 Кроме того, любую технологию можно отслеживать вручную с помощью вызова [TrackDependency](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics) в методе [TeleletryClient](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics).

## <a name="enable-via-opencensus"></a>Включение с помощью OpenCensus

Помимо пакетов SDK, Application Insights поддерживает распределенную трассировку с помощью [OpenCensus](https://opencensus.io/). OpenCensus является единым для служб распределением библиотек с открытым исходным кодом, независимым от поставщика, для обеспечения сбора метрик и распределенной трассировки. Он также позволяет сообществу открытого исходного кода включить распределенную трассировку с использованием популярных технологий, таких как Redis, Memcached или MongoDB. [Корпорация Майкрософт в OpenCensus сотрудничает с несколькими другими партнерами по мониторингу и облакам](https://open.microsoft.com/2018/06/13/microsoft-joins-the-opencensus-project/).

[Python](opencensus-python.md) 

На веб-сайте OpenCensus поддерживается справочная документация по API для [Python](https://opencensus.io/api/python/trace/usage.html) и [Go](https://godoc.org/go.opencensus.io), а также несколько различных руководств по использованию OpenCensus. 

## <a name="next-steps"></a>Дальнейшие действия

* [Руководство по использованию OpenCensus для Python](https://opencensus.io/api/python/trace/usage.html)
* [Карта приложения](./../../azure-monitor/app/app-map.md)
* [Поиск и диагностика проблем производительности с помощью Azure Application Insights](./../../azure-monitor/learn/tutorial-performance.md)
