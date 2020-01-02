---
title: Отчет о данных анализа трафика поиска
titleSuffix: Azure Cognitive Search
description: Включите аналитику поиска трафика для Azure Когнитивный поиск, собирайте данные телеметрии и инициированные пользователем события с помощью Application Insights, а затем анализируйте результаты в Power BI отчете.
author: HeidiSteen
manager: nitinme
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 12/05/2019
ms.openlocfilehash: b9b0ba85aed4d63fe6bb939c9ed3b99d3e789397
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/08/2019
ms.locfileid: "74932571"
---
# <a name="implement-search-traffic-analytics-in-azure-cognitive-search"></a>Реализация аналитики трафика поиска в Azure Когнитивный поиск

Аналитика поискового трафика — это модель реализации цикла обратной связи для службы поиска. Цель заключается в сборе данных телеметрии о событиях щелчка, инициированных пользователем, и вводе с клавиатуры. С помощью этих сведений можно определить эффективность решения поиска, включая популярные условия поиска, частоту переходов, а также то, какие входные данные запроса дают нулевые результаты.

Этот шаблон зависит от [Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/app-insights-overview) (функции [Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/)) для получения данных пользователя. Также потребуется добавить инструментирование в код клиента, как описано в этой статье. Наконец, для анализа данных потребуется механизм создания отчетов. Рекомендуется Power BI но можно использовать любое средство, которое подключается к Application Insights.

> [!NOTE]
> Шаблон, описанный в этой статье, предназначен для расширенных сценариев и навигации данных, создаваемых клиентом. Кроме того, можно создать отчет о данных журнала, создаваемых службой поиска. Дополнительные сведения об отчетах журнала службы см. в статье [мониторинг потребления ресурсов и действий запросов](search-monitor-usage.md).

## <a name="identify-relevant-search-data"></a>Определение соответствующих данных поиска

Чтобы получать полезные метрики поиска, необходимо регистрировать некоторые пользовательские события в приложении поиска. Эти события обозначают интересующее пользователей содержимое, которое соответствует их потребностям.

Аналитика поискового трафика учитывает два типа событий:

+ События поиска, созданные пользователем. интересны только поисковые запросы, инициированные пользователем. Поисковые запросы, используемые для заполнения аспектов, дополнительного содержимого или каких-либо внутренних сведений, не являются важными и ведут к искажению результатов поиска.

+ События щелчка, созданные пользователем: щелкнув этот документ, мы будем называть пользователя выбором определенного результата поиска, возвращенного из поискового запроса. Щелчок обычно означает, что документ релевантный для конкретного поискового запроса.

Связывая Поиск и щелкая события с ИДЕНТИФИКАТОРом корреляции, можно анализировать поведение пользователей приложения. Эти сведения о поиске невозможно получить с помощью только журналов поискового трафика.

## <a name="add-search-traffic-analytics"></a>Добавление аналитики поискового трафика

События, указанные в предыдущем разделе, необходимо собрать из приложения поиска, так как с ним взаимодействует пользователь. Application Insights — это расширяемое решение для мониторинга с гибкими параметрами инструментирования, доступное для нескольких платформ. Использование Application Insights позволяет воспользоваться преимуществами отчетов по поиску Power BI, созданных Когнитивный поиск Azure, чтобы упростить анализ данных.

На странице [портала](https://portal.azure.com) для службы когнитивный Поиск Azure страница Поиск Аналитика трафика содержит лист Памятка по для следующего шаблона телеметрии. Вы также можете выбрать или создать ресурс Application Insights и просмотреть необходимые данные — все в одном расположении.

![Инструкции по аналитике поискового трафика][1]

## <a name="1---select-a-resource"></a>1\. Выбор ресурса

Вам необходимо выбрать или создать ресурс Application Insights (если у вас его нет). Вы можете использовать ресурс, уже находящийся в применении, для регистрации требуемых пользовательских событий.

При создании ресурса Application Insights все типы приложений допустимы для этого сценария. Выберите тот, который лучше всего соответствует используемой платформе.

Чтобы создать клиент телеметрии для приложения, требуется ключ инструментирования. Вы можете получить его на панели мониторинга портала Application Insights или на странице аналитики поискового трафика, выбрав нужный экземпляр.

## <a name="2---add-instrumentation"></a>2\. Добавление инструментирования

На этом шаге выполняется Инструментирование собственного приложения поиска с помощью Application Insights ресурса, созданного на предыдущем шаге. Этот процесс состоит из четырех этапов:

**Шаг 1. Создание клиента телеметрии**

Это объект, отправляющий события в ресурс Application Insights.

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

**Шаг 2. запрос идентификатора поиска для корреляции**

Чтобы сопоставить запросы поиска с помощью щелчков, необходимо иметь идентификатор корреляции, связывающий эти два отдельных события. Azure Когнитивный поиск предоставляет идентификатор поиска при запросе с заголовком:

*C#*

    // This sample uses the .NET SDK https://www.nuget.org/packages/Microsoft.Azure.Search

    var client = new SearchIndexClient(<SearchServiceName>, <IndexName>, new SearchCredentials(<QueryKey>)
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

**Шаг 3. события поиска по журналам**

Каждый отправляемый поисковой запрос необходимо регистрировать в качестве события поиска, используя следующую схему пользовательского события Application Insights:

**SearchServiceName**: (String) имя службы поиска **SearchId**: (GUID) уникальный идентификатор поискового запроса (в ответе на поиск). индекс службы поиска **IndexName**: (String) для запроса **куеритермс**: (String) Поиск слов, вводимых пользователем **ресулткаунт**: (int) количество документов, которые были возвращены (в ответе на поиск). имя профиля оценки (в строке **).**

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

**Шаг 4. события щелчка журнала**

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

## <a name="3---analyze-in-power-bi"></a>3\. Анализ в Power BI

После инструментирования приложения и проверки правильности подключения приложения к Application Insights можно использовать стандартный шаблон, созданный Когнитивный поиск Azure для Power BI Desktop. 

Поисковый запрос Azure содержит [пакет содержимого Power BI](https://app.powerbi.com/getdata/services/azure-search) мониторинга, позволяющий анализировать данные журнала. Пакет содержимого добавляет предварительно определенные диаграммы и таблицы, которые можно использовать при анализе дополнительных данных, полученных для аналитики поискового трафика. Дополнительные сведения см. на [странице справки по пакету содержимого](https://powerbi.microsoft.com/documentation/powerbi-content-pack-azure-search/). 

1. В области навигации панели мониторинга Когнитивный поиск Azure, расположенной слева, в разделе **Параметры**щелкните **Поиск аналитики трафика**.

2. На странице **Поиск аналитики трафика** на шаге 3 щелкните **Получить Power BI Desktop**, чтобы установить Power BI.

   ![Получение отчетов Power BI](./media/search-traffic-analytics/get-use-power-bi.png "Получение отчетов Power BI")

2. На той же странице щелкните **скачать Power BI отчет**.

3. В Power BI Desktop откроется отчет, и вам будет предложено подключиться к Application Insights. Эти сведения можно найти на страницах портал Azure для ресурса Application Insights.

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

![Power BI панели мониторинга для Когнитивный поиск Azure](./media/search-traffic-analytics/azuresearch-powerbi-dashboard.png "Power BI панели мониторинга для Когнитивный поиск Azure")

## <a name="next-steps"></a>Дальнейшие действия
Выполните инструментирование приложения поиска для получения детальных и полезных сведений о службе поиска.

Вы можете узнать больше об [Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/app-insights-overview) и перейти на [страницу цен](https://azure.microsoft.com/pricing/details/application-insights/), чтобы получить дополнительные сведения о соответствующих уровнях служб.

Узнайте больше о создании удивительных отчетов. Дополнительные сведения см. [в разделе Приступая к работе с Power BI Desktop](https://powerbi.microsoft.com/documentation/powerbi-desktop-getting-started/) .

<!--Image references-->
[1]: ./media/search-traffic-analytics/azuresearch-trafficanalytics.png
[2]: ./media/search-traffic-analytics/azuresearch-appinsightsdata.png
[3]: ./media/search-traffic-analytics/azuresearch-pbitemplate.png
