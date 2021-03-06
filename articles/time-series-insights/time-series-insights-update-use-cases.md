---
title: Варианты использования предварительной версии — служба "аналитика временных рядов Azure" | Документация Майкрософт
description: Сведения о сценариях использования службы "аналитика временных рядов Azure".
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

В этой статье перечислены некоторые распространенные варианты использования предварительной версии службы "аналитика временных рядов Azure". Рекомендации в этой статье служат отправной точкой для разработки приложений и решений с помощью аналитики временных рядов.

В частности, эта статья содержит ответы на следующие вопросы:

* Каковы распространенные варианты использования Аналитики временных рядов Azure?
* Каковы преимущества использования аналитики временных рядов для [исследования данных и обнаружения визуальных аномалий](#data-exploration-and-visual-anomaly-detection)?
* Каковы преимущества использования аналитики временных рядов для [оперативного анализа и эффективности процесса](#operational-analysis-and-driving-process-efficiency)?
* Каковы преимущества использования службы "аналитика временных рядов" для [расширенной аналитики](#advanced-analytics)?

Общие сведения об этих сценариях использования описаны в следующих разделах.

## <a name="introduction"></a>Введение

Служба "аналитика временных рядов Azure" — это комплексное предложение "платформа как услуга". Оно используется, чтобы собирать, обрабатывать, хранить, анализировать и запрашивать масштабируемые данные Интернета вещей с высокой степенью контекстуализации, оптимизированные для временных рядов. Аналитика временных рядов идеально подходит для специализированного исследования данных и оперативного анализа. "Аналитика временных рядов" — это уникально расширяемое, настраиваемое предложение услуги, которое соответствует широким потребностям промышленных развертываний IoT.

## <a name="data-exploration-and-visual-anomaly-detection"></a>Исследование данных и визуализация аномалий

Изучайте и анализируйте миллиарды событий, мгновенно выявляя аномалии и скрытые тенденции в данных. Аналитика временных рядов обеспечивает выполнение рабочих нагрузок анализа для Интернета вещей и DevOps в практически реальном времени.

[![обозреватель данных](media/v2-update-use-cases/data-explorer.png)](media/v2-update-use-cases/data-explorer.png#lightbox)

Большинство клиентов договариваются о том, что минимальное время, необходимое для получения ценной информации, является одной из наиболее примечательным функций аналитики временных рядов:

* Аналитика временных рядов Azure не требует предварительной подготовки данных. 
* Это быстрое подключение к миллиардам событий в центре Интернета вещей Azure или экземплярам концентраторов событий Azure за считаные минуты. 
* Подсоединившись, изучайте и анализируйте миллиарды событий, мгновенно выявляя аномалии и скрытые тенденции в данных.

Служба "Аналитика временных рядов Azure" удобная и простая в использовании. Для взаимодействия с данными вам не потребуется писать свой код. Также не нужно изучать новый язык, хотя аналитика временных рядов предоставляет детализированный текстовый язык запросов для опытных пользователей, знакомых с SQL. Она также предоставляет изучение для новичков с помощью команд "выберите" и "щелкните".

Клиенты могут воспользоваться преимуществами скорости, чтобы быстро диагностировать проблемы, связанные с активами. Они могут выполнять анализ DevOps, чтобы получить основную причину ошибки в решении IoT. Они также могут вымечать области для дальнейшего изучения в рамках своих инициатив по обработке и анализу данных. 

Существует три основных способа для взаимодействия с данными, хранящимися в Аналитике временных рядов Azure.

* Первый и самый простой способ начать работу — с помощью обозревателя Аналитики временных рядов Azure. Его можно использовать для быстрой визуализации всех данных Интернета вещей в одном расположении. Она предоставляет такие средства, как тепловая схема, позволяющая выявить аномалии в данных. Он также предоставляет перспективное представление. Используйте его для сравнения не больше четырех представлений из одной или нескольких сред Аналитики временных рядов на одной панели мониторинга. Панель мониторинга дает вам представление о данных временных рядов для всех расположений. Сведения об [Обозревателе предварительной версии службы "Аналитика временных рядов Azure"](./time-series-insights-update-explorer.md). Узнайте, как спланировать среду Аналитики временных рядов из статьи [Планирование Аналитики временных рядов](./time-series-insights-update-plan.md).

* Второй способ начать работу — использовать пакет SDK для JavaScript для быстрого внедрения эффективных диаграмм и графиков в веб-приложение. С помощью нескольких строк кода вы можете создавать эффективные запросы. Используйте их для заполнения графиков, круговых диаграмм, линейчатых диаграмм, карт рисков, сеток данных и многого другого. Все эти элементы готовы к использованию с помощью пакета SDK. Пакет SDK также извлекает API запросов Аналитики временных рядов. Их можно использовать для создания SQL-подобных предикатов для обращения к данным, которые вы хотите показать на панели мониторинга. Для гибридных решений уровня представления Аналитика временных рядов предлагает параметризованные URL-адреса. Они обеспечивают беспрерывную точку подключения с помощью обозревателя Аналитики временных рядов для подробного анализа данных.

  * Дополнительные сведения о пакете SDK для JavaScript см. в статье о [клиентской библиотеке "аналитика временных рядов" JS](https://github.com/microsoft/tsiclient/blob/master/docs/API.md) и в клиентской документации по службе " [аналитика временных рядов](https://github.com/Microsoft/tsiclient) ".

  * Дополнительные сведения о совместном использовании URL-адресов и нового пользовательского интерфейса см. [в статье Визуализация данных в обозревателе предварительного просмотра службы "аналитика временных рядов Azure"](time-series-insights-update-explorer.md).

* Третий способ работы — использовать эффективные API-интерфейсы для запроса данных, хранящихся в Аналитике временных рядов. Аналитика временных рядов содержит временные операторы, такие как `from`, `to`, `first`и `last`. Он содержит агрегаты и преобразования, такие как `average`, `min`, `max`, `split by`, `order by`и `DateHistogram`. Он также содержит операторы фильтрации, такие как `has`, `in`, `and`, `or`, `greater than`и `REGEX`. Эти операторы позволяют целевому приложению быстро найти необычные тенденции и закономерности в ваших данных. Используйте их для заполнения визуализаций доморощенные для выявления аномалий.

## <a name="operational-analysis-and-driving-process-efficiency"></a>Анализ работы и повышение эффективности процессов

Используйте Аналитику временных рядов для мониторинга работоспособности, использования и производительности оборудования в нужном масштабе. Аналитика временных рядов предоставляет простой способ измерить эффективность производительности. Аналитика временных рядов позволяет обрабатывать разнообразные труднопредсказуемые рабочие нагрузки Интернета вещей без ущерба для приема данных или выполнения запросов.

[![Обзор](media/v2-update-use-cases/overview.png)](media/v2-update-use-cases/overview.png#lightbox)

В сочетании с правильными технологиями и решениями потоковая передача и непрерывная обработка данных рабочих процессов могут успешно преобразить любую компанию. Часто эти решения объединяют несколько систем. Они позволяют исследовать и анализировать данные, которые постоянно меняются, особенно в сфере Интернета вещей, и совместно использовать общий шаблон.

Эти шаблоны часто начинаются с платформ, которые поддерживают Интернет вещей и принимают миллиарды событий от устройств и датчиков с различными языковыми стандартами. Затем они обрабатывают и анализируют потоковые данные для получения подробных сведений в реальном времени. Данные обычно архивируются в горячий и холодное хранение практически в режиме реального времени и пакетной аналитики.

Собранные данные проходят через последовательные этапы обработки для очистки и контекстуализации для целевых запросов и сценариев аналитики. Azure предлагает широкий выбор служб, например, производство и обслуживание средств, которые можно применять к сценариям Интернета вещей. Эти службы включают в себя Аналитику временных рядов, центр Интернета вещей, Центры событий Azure, Azure Stream Analytics, Функции Azure, Azure Logic Apps, Azure Databricks, Машинное обучение Azure и Power BI.

Архитектура решений осуществляется следующими способами.

* Принимая данные через центр Интернета вещей или центры событий, сохраняя тем самым лучшую в своем классе безопасность, пропускную способность и задержку.
* Выполняя обработку данных и вычислений. Пропустите полученные данные через следующие службы: Stream Analytics, Logic Apps и Функции Azure. Использование служб зависит от конкретных потребностей обработки данных.
* Отправляя вычисленные сигналы из конвейера обработки в Аналитику временных рядов для хранения и аналитики.

Аналитика временных рядов обеспечивает исследование данных и анализ средств на основе исторических данных в практически реальном времени. В зависимости от требований вашего предприятия задания MapReduce и Hive можно запускать на данных, хранящихся в Аналитике временных рядов, путем подключения службы к Azure HDInsight. Данные, хранящиеся в Аналитике временных рядов, доступны в Power BI и других клиентских приложениях через запросы Аналитики временных рядов с интерфейсов API на общей поверхности. Эти данные можно использовать на продвинутом этапе работы и для оперативной аналитики.

## <a name="advanced-analytics"></a>Расширенная аналитика

Обеспечьте интеграцию со службами расширенной аналитики, такими как машинное обучение Azure и Azure Databricks. Аналитика временных рядов передает необработанные данные с миллионов устройств. Служба добавляет контекстные данные, которые можно легко использовать с помощью набора служб Azure Analytics.

[Аналитика ![](media/v2-update-use-cases/advanced-analytics.png)](media/v2-update-use-cases/advanced-analytics.png#lightbox)

Расширенная аналитика и машинное обучение используют и обрабатывают большие объемы данных. Эти данные используются для принятия решений на основе данных и прогнозного анализа. В случае использования Интернета вещей расширенные аналитические алгоритмы получают сведения о данных из миллионов устройств. Эти устройства передают данные несколько раз в секунду. Данные, которые поступают с устройств Интернета вещей, не обработаны. Они не содержат контекстной информации, такой как расположение устройства и единицы измерения показания датчика. Таким образом, необработанные данные трудно использовать непосредственно в расширенной аналитике.

Аналитика временных рядов позволяет устранить разрыв между данными Интернета вещей и расширенной аналитикой одним из двух простых и экономически выгодных способов.

* Во-первых, Аналитика временных рядов собирает необработанные данные телеметрии из миллионов устройств с помощью центра Интернета вещей. Она дополняет данные с помощью контекстной информации и преобразует их в формат parquet. Этот формат может легко интегрироваться с такими службами расширенной аналитики, как машинное обучение Azure, Azure Databricks, а также со сторонними приложениями.

    Аналитика временных рядов может служить источником истины для всех данных, используемых на предприятии. Она создает центральный репозиторий для использования рабочих нагрузок по целевой аналитике. Так как Аналитика временных рядов является службой хранения практически в реальном времени, модели расширенной аналитики постоянно дополняются входящими данными телеметрии Интернета вещей. Таким образом, эти модели могут делать более точные прогнозы.

* Во вторых, выходные данные моделей машинного обучения и прогнозирования можно поставлять в аналитику временных рядов для визуализации и хранения результатов. Эта процедура помогает предприятиям оптимизировать и настроить свои модели. Аналитика временных рядов позволяет легко визуализировать потоковые данные телеметрии в одной плоскости, как и обученные выходные данные моделей. Тем самым она помогает командам обработки и анализа данных выявлять аномалии и определять шаблоны.

## <a name="next-steps"></a>Дополнительная информация

* Сведения об [Обозревателе предварительной версии службы "Аналитика временных рядов Azure"](./time-series-insights-update-explorer.md).
* Ознакомьтесь с [планированием предварительного просмотра аналитики временных рядов](./time-series-insights-update-plan.md) , чтобы спланировать среду.
* Ознакомьтесь с документацией [Time Series Insights client](https://github.com/Microsoft/tsiclient) (Клиент службы "Аналитика временных рядов").
