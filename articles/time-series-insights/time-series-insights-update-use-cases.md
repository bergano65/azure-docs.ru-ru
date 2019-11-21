---
title: Preview use cases - Azure Time Series Insights | Microsoft Docs
description: Learn about Azure Time Series Insights Preview use cases.
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 11/19/2019
ms.custom: seodec18
ms.openlocfilehash: 92d738542076b755a26e8cff2e7fb1aa0384cb22
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/20/2019
ms.locfileid: "74227728"
---
# <a name="azure-time-series-insights-preview-use-cases"></a>Примеры использования предварительной версии службы "Аналитика временных рядов Azure"

This article summarizes several common use cases for Azure Time Series Insights Preview. The recommendations in this article serve as a starting point to develop your applications and solutions with Time Series Insights.

Specifically, this article answers the following questions:

* Каковы распространенные варианты использования Аналитики временных рядов Azure?
* What are the benefits of using Time Series Insights for [data exploration and visual anomaly detection](#data-exploration-and-visual-anomaly-detection)?
* What are the benefits of using Time Series Insights for [operational analysis and process efficiency](#operational-analysis-and-driving-process-efficiency)?
* What are the benefits of using Time Series Insights for [advanced analytics](#advanced-analytics)?

An overview of these use scenarios is described in the following sections.

## <a name="introduction"></a>Общие сведения

Azure Time Series Insights is an end-to-end, platform-as-a-service offering. Оно используется, чтобы собирать, обрабатывать, хранить, анализировать и запрашивать масштабируемые данные Интернета вещей с высокой степенью контекстуализации, оптимизированные для временных рядов. Аналитика временных рядов идеально подходит для специализированного исследования данных и оперативного анализа. Time Series Insights is a uniquely extensible, customized service offering that meets the broad needs of industrial IoT deployments.

## <a name="data-exploration-and-visual-anomaly-detection"></a>Исследование данных и визуализация аномалий

Изучайте и анализируйте миллиарды событий, мгновенно выявляя аномалии и скрытые тенденции в данных. Аналитика временных рядов обеспечивает выполнение рабочих нагрузок анализа для Интернета вещей и DevOps практически в реальном времени.

[![Data explorer](media/v2-update-use-cases/data-explorer.png)](media/v2-update-use-cases/data-explorer.png#lightbox)

Most customers agree that the minimal amount of time required to gain insight is one of the standout features of Time Series Insights:

* Аналитика временных рядов Azure не требует предварительной подготовки данных. 
* It works fast to connect you to billions of events in your Azure IoT Hub or Azure Event Hubs instances in minutes. 
* Подсоединившись, изучайте и анализируйте миллиарды событий, мгновенно выявляя аномалии и скрытые тенденции в данных.

Служба "Аналитика временных рядов Azure" удобная и простая в использовании. Для взаимодействия с данными вам не потребуется писать свой код. There’s also no new language you're required to learn, although Time Series Insights provides a granular text-based querying language for advanced users who are familiar with SQL. Она также предоставляет изучение для новичков с помощью команд "выберите" и "щелкните".

Customers can take advantage of the speed to diagnose asset-related issues quickly. They can perform DevOps analysis to get to the root cause of a bug in an IoT solution. They also can identify areas to flag for further investigation as part of their data science initiatives. 

Существует три основных способа для взаимодействия с данными, хранящимися в Аналитике временных рядов Azure.

* Первый и самый простой способ начать работу — с помощью обозревателя Аналитики временных рядов Azure. Его можно использовать для быстрой визуализации всех данных Интернета вещей в одном расположении. It provides tools like the heat map to help you spot anomalies in your data. Он также предоставляет перспективное представление. Используйте его для сравнения не больше четырех представлений из одной или нескольких сред Аналитики временных рядов на одной панели мониторинга. Панель мониторинга дает вам представление о данных временных рядов для всех расположений. Сведения об [Обозревателе предварительной версии службы "Аналитика временных рядов Azure"](./time-series-insights-update-explorer.md). Узнайте, как спланировать среду Аналитики временных рядов из статьи [Планирование Аналитики временных рядов](./time-series-insights-update-plan.md).

* The second way to start is to use the JavaScript SDK to quickly embed powerful charts and graphs in your web application. С помощью нескольких строк кода вы можете создавать эффективные запросы. Use them to populate line charts, pie charts, bar charts, heat maps, data grids, and more. Все эти элементы готовы к использованию с помощью пакета SDK. Пакет SDK также извлекает API запросов Аналитики временных рядов. Их можно использовать для создания SQL-подобных предикатов для обращения к данным, которые вы хотите показать на панели мониторинга. Для гибридных решений уровня представления Аналитика временных рядов предлагает параметризованные URL-адреса. Они обеспечивают беспрерывную точку подключения с помощью обозревателя Аналитики временных рядов для подробного анализа данных.

  * Read about the [Time Series Insights JS client library](https://github.com/microsoft/tsiclient/blob/master/docs/API.md) and the [Time Series Insights client](https://github.com/Microsoft/tsiclient) documentation to learn more about the JavaScript SDK.

  * Learn more about sharing URLs and the new UI by reviewing [Visualize data in the Azure Time Series Insights Preview explorer](time-series-insights-update-explorer.md).

* Третий способ работы — использовать эффективные API-интерфейсы для запроса данных, хранящихся в Аналитике временных рядов. Time Series Insights has temporal operators such as `from`, `to`, `first`, and `last`. It has aggregations and transformations such as `average`, `min`, `max`, `split by`, `order by`, and `DateHistogram`. It also has filtering operators such as `has`, `in`, `and`, `or`, `greater than`, and `REGEX`. Эти операторы позволяют целевому приложению быстро найти необычные тенденции и закономерности в ваших данных. Use them to populate homegrown visualizations to spot anomalies.

## <a name="operational-analysis-and-driving-process-efficiency"></a>Анализ работы и повышение эффективности процессов

Используйте Аналитику временных рядов для мониторинга работоспособности, использования и производительности оборудования в нужном масштабе. Аналитика временных рядов предоставляет простой способ измерить эффективность производительности. Аналитика временных рядов позволяет обрабатывать разнообразные, труднопредсказуемые рабочие нагрузки Интернета вещей без ущерба для приема данных или выполнения запросов.

[![Обзор](media/v2-update-use-cases/overview.png)](media/v2-update-use-cases/overview.png#lightbox)

В сочетании с правильными технологиями и решениями потоковая передача и непрерывная обработка данных рабочих процессов могут успешно преобразить любую компанию. Часто эти решения объединяют несколько систем. They enable exploration and analysis of data that changes constantly, especially in the IoT realm, and share a common pattern.

Эти шаблоны часто начинаются с платформ, которые поддерживают Интернет вещей и принимают миллиарды событий от устройств и датчиков с различными языковыми стандартами. Затем они обрабатывают и анализируют потоковые данные для получения подробных сведений в реальном времени. Data is typically archived to warm and cold store for near real-time and batch analytics.

Собранные данные проходят через последовательные этапы обработки для очистки и контекстуализации для целевых запросов и сценариев аналитики. Azure предлагает широкий выбор служб, например, производство и обслуживание средств, которые можно применять к сценариям Интернета вещей. Эти службы включают в себя Аналитику временных рядов, центр Интернета вещей, Центры событий Azure, Azure Stream Analytics, Функции Azure, Azure Logic Apps, Azure Databricks, Машинное обучение Azure и Power BI.

Архитектура решений осуществляется следующими способами.

* Принимая данные через центр Интернета вещей или центры событий, сохраняя тем самым лучшую в своем классе безопасность, пропускную способность и задержку.
* Выполняя обработку данных и вычислений. Пропустите полученные данные через следующие службы: Stream Analytics, Logic Apps и Функции Azure. Использование служб зависит от конкретных потребностей обработки данных.
* Отправляя вычисленные сигналы из конвейера обработки в Аналитику временных рядов для хранения и аналитики.

Аналитика временных рядов обеспечивает исследование данных и анализ средств на основе исторических данных в практически реальном времени. В зависимости от требований вашего предприятия задания MapReduce и Hive можно запускать на данных, хранящихся в Аналитике временных рядов, путем подключения службы к Azure HDInsight. Данные, хранящиеся в Аналитике временных рядов, доступны в Power BI и других клиентских приложениях через запросы Аналитики временных рядов с интерфейсов API на общей поверхности. Эти данные можно использовать на продвинутом этапе работы и для оперативной аналитики.

## <a name="advanced-analytics"></a>Продвинутая аналитика

Обеспечьте интеграцию со службами расширенной аналитики, такими как машинное обучение Azure и Azure Databricks. Аналитика временных рядов передает необработанные данные с миллионов устройств. Служба добавляет контекстные данные, которые можно легко использовать с помощью набора служб Azure Analytics.

[![Analytics](media/v2-update-use-cases/advanced-analytics.png)](media/v2-update-use-cases/advanced-analytics.png#lightbox)

Расширенная аналитика и машинное обучение используют и обрабатывают большие объемы данных. Эти данные используются для принятия решений на основе данных и прогнозного анализа. В случае использования Интернета вещей расширенные аналитические алгоритмы получают сведения о данных из миллионов устройств. Эти устройства передают данные несколько раз в секунду. Данные, которые поступают с устройств Интернета вещей, не обработаны. Они не содержат контекстной информации, такой как расположение устройства и единицы измерения показания датчика. Таким образом, необработанные данные трудно использовать непосредственно в расширенной аналитике.

Аналитика временных рядов позволяет устранить разрыв между данными Интернета вещей и расширенной аналитикой одним из двух простых и экономически выгодных способов.

* Во-первых, Аналитика временных рядов собирает необработанные данные телеметрии из миллионов устройств с помощью центра Интернета вещей. Она дополняет данные с помощью контекстной информации и преобразует их в формат parquet. Этот формат может легко интегрироваться с такими службами расширенной аналитики, как машинное обучение Azure, Azure Databricks, а также со сторонними приложениями.

    Аналитика временных рядов может служить источником истины для всех данных, используемых на предприятии. Она создает центральный репозиторий для использования рабочих нагрузок по целевой аналитике. Так как Аналитика временных рядов является службой хранения практически в реальном времени, модели расширенной аналитики постоянно дополняются входящими данными телеметрии Интернета вещей. Таким образом, эти модели могут делать более точные прогнозы.

* Second, the output of machine learning and prediction models can be fed into Time Series Insights to visualize and store their results. Эта процедура помогает предприятиям оптимизировать и настроить свои модели. Аналитика временных рядов позволяет легко визуализировать потоковые данные телеметрии в одной плоскости, как и обученные выходные данные моделей. Тем самым она помогает командам обработки и анализа данных выявлять аномалии и определять шаблоны.

## <a name="next-steps"></a>Дальнейшие действия

* Сведения об [Обозревателе предварительной версии службы "Аналитика временных рядов Azure"](./time-series-insights-update-explorer.md).
* Read [Time Series Insights Preview planning](./time-series-insights-update-plan.md) to plan out your environment.
* Ознакомьтесь с документацией [Time Series Insights client](https://github.com/Microsoft/tsiclient) (Клиент службы "Аналитика временных рядов").
