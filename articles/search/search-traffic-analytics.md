---
title: Реализация аналитики поискового трафика в Поиске Azure
description: Включите аналитику поискового трафика для Поиска Azure, чтобы добавить данные телеметрии и событий, инициированных пользователем, в файлы журнала.
author: HeidiSteen
manager: cgronlun
services: search
ms.service: search
ms.topic: conceptual
ms.date: 01/25/2019
ms.author: heidist
ms.custom: seodec2018
ms.openlocfilehash: c30c8bae3e76778a31cdd0695acde52b5b1c6b02
ms.sourcegitcommit: 58dc0d48ab4403eb64201ff231af3ddfa8412331
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/26/2019
ms.locfileid: "55079670"
---
# <a name="implement-search-traffic-analytics-in-azure-search"></a>Реализация аналитики поискового трафика в Поиске Azure
Аналитика поискового трафика — это модель реализации цикла обратной связи для службы поиска. Эта модель описывает необходимые данные и метод их сбора с помощью Application Insights, лидирующего решения для мониторинга служб на нескольких платформах.

Аналитика поискового трафика позволяет обеспечить видимость службы поиска и получить сведения о пользователях и их поведении. Получив сведения о выборе пользователей, можно принимать решения о дальнейшем усовершенствовании возможностей поиска или выполнить откат при отсутствии нужных результатов.

В службе поиска Azure доступно решение телеметрии, интегрирующее Azure Application Insights и Power BI для обеспечения детализированного мониторинга и отслеживания. Так как взаимодействие со службой поиска Azure происходит только через API, разработчикам следует реализовать телеметрию с использованием поиска, следуя инструкциям на этой странице.

## <a name="identify-relevant-search-data"></a>Определение соответствующих данных поиска

Чтобы получать полезные метрики поиска, необходимо регистрировать некоторые пользовательские события в приложении поиска. Эти события обозначают интересующее пользователей содержимое, которое соответствует их потребностям.

Аналитика поискового трафика учитывает два типа событий:

1. События поиска, инициированные пользователями. Представляют интерес только поисковые запросы, инициируемые пользователем. Поисковые запросы, используемые для заполнения аспектов, дополнительного содержимого или каких-либо внутренних сведений, не являются важными и ведут к искажению результатов поиска.

2. Пользовательские события щелчка. Щелчки в этом документе обозначают выбор пользователем определенного результата поиска, возвращенного из поискового запроса. Щелчок обычно означает, что документ релевантный для конкретного поискового запроса.

Благодаря связи события щелчка и поиска с идентификатором корреляции вы можете анализировать поведения пользователей в приложении. Эти сведения о поиске невозможно получить с помощью только журналов поискового трафика.

## <a name="add-search-traffic-analytics"></a>Добавление аналитики поискового трафика

События, указанные в предыдущем разделе, необходимо собрать из приложения поиска, так как с ним взаимодействует пользователь. Application Insights — это расширяемое решение для мониторинга с гибкими параметрами инструментирования, доступное для нескольких платформ. Это решение позволяет использовать отчеты Power BI о поиске, созданные службой поиска Azure, чтобы упростить анализ данных.

На странице [портала](https://portal.azure.com) службы поиска Azure в колонке "Поиск аналитики трафика" содержится памятка по следующему шаблону телеметрии. Вы также можете выбрать или создать ресурс Application Insights и просмотреть необходимые данные — все в одном расположении.

![Инструкции по аналитике поискового трафика][1]

## <a name="1---select-a-resource"></a>1. Выбор ресурса

Вам необходимо выбрать или создать ресурс Application Insights (если у вас его нет). Вы можете использовать ресурс, уже находящийся в применении, для регистрации требуемых пользовательских событий.

При создании ресурса Application Insights все типы приложений допустимы для этого сценария. Выберите тот, который лучше всего соответствует используемой платформе.

Чтобы создать клиент телеметрии для приложения, требуется ключ инструментирования. Вы можете получить его на панели мониторинга портала Application Insights или на странице аналитики поискового трафика, выбрав нужный экземпляр.

## <a name="2---add-instrumentation"></a>2. Добавление инструментирования

На этом шаге выполняется инструментирование собственного приложения поиска с помощью ресурса Application Insights, созданного на предыдущем шаге. Этот процесс состоит из четырех этапов:

**Шаг 1. Создание клиента телеметрии**. Это объект, который отправляет события в ресурс Application Insights.

*C#*

    private TelemetryClient telemetryClient = new TelemetryClient();
    telemetryClient.InstrumentationKey = "<YOUR INSTRUMENTATION KEY>";

*JavaScript*

    <script type="text/javascript">var appInsights=window.appInsights||function(config){function r(config){t[config]=function(){var i=arguments;t.queue.push(function(){t[config].apply(t,i)})}}var t={config:config},u=document,e=window,o="script",s=u.createElement(o),i,f;s.src=config.url||"//az416426.vo.msecnd.net/scripts/a/ai.0.js";u.getElementsByTagName(o)[0].parentNode.appendChild(s);try{t.cookie=u.cookie}catch(h){}for(t.queue=[],i=["Event","Exception","Metric","PageView","Trace","Dependency"];i.length;)r("track"+i.pop());return r("setAuthenticatedUserContext"),r("clearAuthenticatedUserContext"),config.disableExceptionTracking||(i="onerror",r("_"+i),f=e[i],e[i]=function(config,r,u,e,o){var s=f&&f(config,r,u,e,o);return s!==!0&&t["_"+i](config,r,u,e,o),s}),t}
    ({
    instrumentationKey: "<YOUR INSTRUMENTATION KEY>"
    });
    window.appInsights=appInsights;
    </script>

Для других языков и платформ см. полный [список](https://docs.microsoft.com/azure/application-insights/app-insights-platforms).

**Шаг 2. Запрос идентификатора поиска для корреляции**. Для корреляции поисковых запросов с щелчками необходим идентификатор корреляции, связывающий эти два различных события. Служба поиска Azure предоставляет идентификатор поиска, когда он запрашивается с заголовком:

*C#*

    // This sample uses the Azure Search .NET SDK https://www.nuget.org/packages/Microsoft.Azure.Search

    var client = new SearchIndexClient(<ServiceName>, <IndexName>, new SearchCredentials(<QueryKey>)
    var headers = new Dictionary<string, List<string>>() { { "x-ms-azs-return-searchid", new List<string>() { "true" } } };
    var response = await client.Documents.SearchWithHttpMessagesAsync(searchText: searchText, searchParameters: parameters, customHeaders: headers);
    IEnumerable<string> headerValues;
    string searchId = string.Empty;
    if (response.Response.Headers.TryGetValues("x-ms-azs-searchid", out headerValues)){
     searchId = headerValues.FirstOrDefault();
    }

*JavaScript*

    request.setRequestHeader("x-ms-azs-return-searchid", "true");
    request.setRequestHeader("Access-Control-Expose-Headers", "x-ms-azs-searchid");
    var searchId = request.getResponseHeader('x-ms-azs-searchid');

**Шаг 3. Регистрация событий поиска.**

Каждый отправляемый поисковой запрос необходимо регистрировать в качестве события поиска, используя следующую схему пользовательского события Application Insights:

**ServiceName**: (строка) имя службы поиска. **SearchId**: (GUID) уникальный идентификатор поискового запроса (отображается в ответе поиска). **IndexName**: (строка) индекс службы поиска для запроса. **QueryTerms**: (строка) условия поиска, введенные пользователем. **ResultCount**: (целое число) количество возвращенных документов (отображается в ответе поиска). **ScoringProfile**: (строка) имя используемого профиля оценки (при наличии).

> [!NOTE]
> Получите число запросов, создаваемых пользователем, добавив $count=true в поисковый запрос. Дополнительные сведения см. [здесь](https://docs.microsoft.com/rest/api/searchservice/search-documents#request).
>

> [!NOTE]
> Помните, что необходимо регистрировать только поисковые запросы, создаваемые пользователями.
>

*C#*

    var properties = new Dictionary <string, string> {
    {"SearchServiceName", <service name>},
    {"SearchId", <search Id>},
    {"IndexName", <index name>},
    {"QueryTerms", <search terms>},
    {"ResultCount", <results count>},
    {"ScoringProfile", <scoring profile used>}
    };
    telemetryClient.TrackEvent("Search", properties);

*JavaScript*

    appInsights.trackEvent("Search", {
    SearchServiceName: <service name>,
    SearchId: <search id>,
    IndexName: <index name>,
    QueryTerms: <search terms>,
    ResultCount: <results count>,
    ScoringProfile: <scoring profile used>
    });

**Шаг 4. Регистрация событий щелчка.**

Каждый щелчок документа — это событие, которое необходимо зарегистрировать для анализа поиска. Используйте пользовательские события Application Insights для регистрации этих событий по следующей схеме:

**ServiceName**: (строка) имя службы поиска. **SearchId**: (GUID) уникальный идентификатор связанного поискового запроса. **DocId**: (строка) идентификатор документа. **Position**: (целое число) позиция документа на странице результатов поиска.

> [!NOTE]
> Позиция ссылается на количественный порядок в приложении. Вы можете задать этот номер для сравнения, при условии, что он всегда один и тот же.
>

*C#*

    var properties = new Dictionary <string, string> {
    {"SearchServiceName", <service name>},
    {"SearchId", <search id>},
    {"ClickedDocId", <clicked document id>},
    {"Rank", <clicked document position>}
    };
    telemetryClient.TrackEvent("Click", properties);

*JavaScript*

    appInsights.trackEvent("Click", {
        SearchServiceName: <service name>,
        SearchId: <search id>,
        ClickedDocId: <clicked document id>,
        Rank: <clicked document position>
    });

## <a name="3---analyze-in-power-bi"></a>3. Анализ в Power BI

После инструментирования приложения и проверки правильности его подключения к Application Insights можно использовать стандартный шаблон, созданный службой поиска Azure для Power BI Desktop. 

Поиск Azure предлагает [пакет содержимого Power BI](https://app.powerbi.com/getdata/services/azure-search) для мониторинга, позволяющий анализировать данные. Пакет содержимого добавляет предварительно определенные диаграммы и таблицы, которые можно использовать при анализе дополнительных данных, полученных для аналитики поискового трафика. Дополнительные сведения см. на [странице справки по пакету содержимого](https://powerbi.microsoft.com/documentation/powerbi-content-pack-azure-search/). 

1. В области навигации слева на панели мониторинга Поиска Azure, в разделе **Параметры**, щелкните **Поиск аналитики трафика**.

2. На странице **Поиск аналитики трафика** на шаге 3 щелкните **Получить Power BI Desktop**, чтобы установить Power BI.

   ![Получение отчетов Power BI](./media/search-traffic-analytics/get-use-power-bi.png "Получение отчетов Power BI")

2. На этой же странице щелкните **Скачать отчет Power BI**.

3. В Power BI Desktop откроется отчет, и вам будет предложено подключиться к Application Insights. Эти сведения можно найти на страницах портала Azure для вашего ресурса Application Insights.

   ![Подключение к Application Insights](./media/search-traffic-analytics/connect-to-app-insights.png "Подключение к Application Insights")

4. Нажмите кнопку **Загрузить**.

Этот отчет содержит диаграммы и таблицы, которые помогут вам принимать более взвешенные решения для улучшения производительности и релевантности поиска.

Метрики содержат следующие элементы.

* Кликабельность (CTR). Отношение количества выбора пользователями определенного документа к количеству общих поисковых запросов.
* Поисковые запросы без щелчков. Условия основных запросов, для которых не выполнялись щелчки.
* Документы с наибольшим количеством щелчков. Определяются по идентификатору за последние 24 часа, неделю и месяц.
* Распространенные пары "условие —документ". Условия, в результате которых щелкают тот же документ, c упорядочиванием по щелчкам.
* Время щелчка. Щелчки, упорядоченные по времени с момента выполнения поискового запроса.

На следующем снимке экрана показаны встроенные отчеты и диаграммы для анализа данных аналитики поискового трафика.

![Панель мониторинга Power BI для Поиска Azure](./media/search-traffic-analytics/AzureSearch-PowerBI-Dashboard.png "Панель мониторинга Power BI для Поиска Azure")

## <a name="next-steps"></a>Дополнительная информация
Выполните инструментирование приложения поиска для получения детальных и полезных сведений о службе поиска.

Вы можете узнать больше об [Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/app-insights-overview) и перейти на [страницу цен](https://azure.microsoft.com/pricing/details/application-insights/), чтобы получить дополнительные сведения о соответствующих уровнях служб.

Узнайте больше о создании удивительных отчетов. См. статью [Начало работы с Power BI Desktop](https://powerbi.microsoft.com/documentation/powerbi-desktop-getting-started/).

<!--Image references-->
[1]: ./media/search-traffic-analytics/AzureSearch-TrafficAnalytics.png
[2]: ./media/search-traffic-analytics/AzureSearch-AppInsightsData.png
[3]: ./media/search-traffic-analytics/AzureSearch-PBITemplate.png
