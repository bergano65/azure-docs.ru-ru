---
title: Данные телеметрии для аналитики трафика поиска
titleSuffix: Azure Cognitive Search
description: Включите аналитику поиска трафика для Azure Когнитивный поиск, собирайте данные телеметрии и инициированные пользователем события с помощью Application Insights, а затем анализируйте результаты в Power BI отчете.
author: HeidiSteen
manager: nitinme
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 03/18/2020
ms.openlocfilehash: 7c843b45b5a398aaaa1aab66f80961560477cf18
ms.sourcegitcommit: edccc241bc40b8b08f009baf29a5580bf53e220c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/24/2020
ms.locfileid: "82128065"
---
# <a name="collect-telemetry-data-for-search-traffic-analytics"></a>Сбор данных телеметрии для аналитики поиска трафика

Аналитика трафика поиска — это шаблон для сбора телеметрических сведений о взаимодействии пользователей с приложением Azure Когнитивный поиск, например инициированными пользователем событиями щелчка и вводом с клавиатуры. С помощью этих сведений можно определить эффективность решения поиска, включая популярные условия поиска, частоту переходов, а также то, какие входные данные запроса дают нулевые результаты.

Этот шаблон зависит от [Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/app-insights-overview) (функции [Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/)) для получения данных пользователя. Для этого необходимо добавить инструментирование в код клиента, как описано в этой статье. Наконец, для анализа данных потребуется механизм создания отчетов. Рекомендуется Power BI но можно использовать панель мониторинга приложения или любое средство, которое подключается к Application Insights.

> [!NOTE]
> Шаблон, описанный в этой статье, предназначен для расширенных сценариев и навигации данных, создаваемых кодом, который добавляется в клиент. Журналы служб, напротив, просты в настройке, предоставляют ряд метрик и могут быть выполнены на портале без обязательного кода. Для всех сценариев рекомендуется включить ведение журнала. Дополнительные сведения см. в разделе [Получение и анализ данных журнала](search-monitor-logs.md).

## <a name="identify-relevant-search-data"></a>Определение соответствующих данных поиска

Чтобы иметь полезные метрики для аналитики трафика поиска, необходимо зарегистрировать некоторые сигналы от пользователей приложения поиска. Эти сигналы обозначают содержимое, которое пользователи заинтересованы в работе и которые они считают релевантными. Для аналитики поискового трафика:

+ События поиска, созданные пользователем. интересны только поисковые запросы, инициированные пользователем. Поисковые запросы, используемые для заполнения аспектов, дополнительного содержимого или каких-либо внутренних сведений, не являются важными и ведут к искажению результатов поиска.

+ События щелчка, созданные пользователем: на странице результатов поиска событие щелчка обычно означает, что документ является релевантным результатом для конкретного поискового запроса.

Связав Поиск и щелкнув события с ИДЕНТИФИКАТОРом корреляции, вы получите более глубокое представление о том, насколько хорошо работает функция поиска приложения.

## <a name="add-search-traffic-analytics"></a>Добавление аналитики поискового трафика

На странице [портала](https://portal.azure.com) для службы когнитивный Поиск Azure страница Поиск Аналитика трафика содержит лист Памятка по для следующего шаблона телеметрии. На этой странице можно выбрать или создать ресурс Application Insights, получить ключ инструментирования, скопировать фрагменты, которые можно адаптировать для решения, и загрузить Power BI отчет, построенный на основе схемы, отраженной в шаблоне.

![Поиск Аналитика трафика странице на портале](media/search-traffic-analytics/azuresearch-trafficanalytics.png "Поиск Аналитика трафика странице на портале")

## <a name="1---set-up-application-insights"></a>1. Настройка Application Insights

Выберите существующий ресурс Application Insights или [создайте его](https://docs.microsoft.com/azure/azure-monitor/app/create-new-resource) , если он еще не создан. При использовании страницы Поиск Аналитика трафика можно скопировать ключ инструментирования, который требуется приложению для подключения к Application Insights.

Получив ресурс Application Insights, можно выполнить [инструкции для поддерживаемых языков и платформ](https://docs.microsoft.com/azure/azure-monitor/app/platforms) , чтобы зарегистрировать приложение. Регистрация — это просто добавление ключа инструментирования из Application Insights в код, который настраивает связь. Ключ можно найти на портале или на странице Поиск Аналитика трафика при выборе существующего ресурса.

Ярлык, который подходит для некоторых типов проектов Visual Studio, отражается в следующих шагах. Он создает ресурс и регистрирует приложение всего за несколько щелчков мышью.

1. Для разработки Visual Studio и ASP.NET откройте решение и выберите **проект** > **Добавить телеметрия Application Insights**.

1. Щелкните начало **работы**.

1. Зарегистрируйте приложение, предоставив учетная запись Майкрософт, подписку Azure и ресурс Application Insights (по умолчанию используется новый ресурс). Щелкните **Зарегистрировать**.

На этом этапе приложение настроено для мониторинга приложений, что означает, что все загрузки страниц отслеживаются с помощью метрик по умолчанию. Дополнительные сведения о предыдущих шагах см. в разделе [Enable Application Insights телеметрии на стороне сервера](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-core#enable-application-insights-server-side-telemetry-visual-studio).

## <a name="2---add-instrumentation"></a>2. Добавление инструментирования

На этом шаге выполняется Инструментирование собственного приложения поиска с помощью Application Insights ресурса, созданного на предыдущем шаге. Этот процесс состоит из четырех этапов, начиная с создания клиента телеметрии.

### <a name="step-1-create-a-telemetry-client"></a>Шаг 1. Создание клиента телеметрии

Создайте объект, отправляющий события в Application Insights. Вы можете добавить инструментирование в код приложения на стороне сервера или клиентский код, выполняемый в браузере, представленный здесь как варианты C# и JavaScript (для других языков см. полный список [поддерживаемых платформ и инфраструктур](https://docs.microsoft.com/azure/application-insights/app-insights-platforms)). Выберите подход, который предоставляет нужную глубину информации.

Данные телеметрии на стороне сервера захватывают метрики на уровне приложения, например в приложениях, выполняющихся в качестве веб-службы в облаке, или в качестве локального приложения в корпоративной сети. Данные телеметрии на стороне сервера захватывают Поиск и щелчок событий, расположение документа в результатах и сведения о запросах, но сбор данных будет ограничен любой информацией, доступной в этом слое.

На клиенте может быть дополнительный код, который управляет входными запросами, добавляет навигацию или включает контекст (например, запросы, инициированные с домашней страницы и страницы продукта). Если это описание решения, вы можете выбрать инструментирование на стороне клиента, чтобы данные телеметрии отражали дополнительные сведения. Сбор этой дополнительной информации выходит за рамки этого шаблона, но вы можете просматривать [Application Insights для веб-страниц](https://docs.microsoft.com/azure/azure-monitor/app/javascript#explore-browserclient-side-data) в более направлении. 

**Использование C#**

В C# **InstrumentationKey** находится в конфигурации приложения, например appSettings. JSON, если проект является ASP.NET. Если вы не уверены в расположении ключа, обратитесь к инструкциям по регистрации.

```csharp
private static TelemetryClient _telemetryClient;

// Add a constructor that accepts a telemetry client:
public HomeController(TelemetryClient telemetry)
    {
        _telemetryClient = telemetry;
    }
```

**Использование JavaScript**

```javascript
<script type="text/javascript">var appInsights=window.appInsights||function(config){function r(config){t[config]=function(){var i=arguments;t.queue.push(function(){t[config].apply(t,i)})}}var t={config:config},u=document,e=window,o="script",s=u.createElement(o),i,f;s.src=config.url||"//az416426.vo.msecnd.net/scripts/a/ai.0.js";u.getElementsByTagName(o)[0].parentNode.appendChild(s);try{t.cookie=u.cookie}catch(h){}for(t.queue=[],i=["Event","Exception","Metric","PageView","Trace","Dependency"];i.length;)r("track"+i.pop());return r("setAuthenticatedUserContext"),r("clearAuthenticatedUserContext"),config.disableExceptionTracking||(i="onerror",r("_"+i),f=e[i],e[i]=function(config,r,u,e,o){var s=f&&f(config,r,u,e,o);return s!==!0&&t["_"+i](config,r,u,e,o),s}),t}
({
instrumentationKey: "<YOUR INSTRUMENTATION KEY>"
});
window.appInsights=appInsights;
</script>
```

### <a name="step-2-request-a-search-id-for-correlation"></a>Шаг 2. запрос идентификатора поиска для корреляции

Чтобы сопоставить запросы поиска с помощью щелчков, необходимо иметь идентификатор корреляции, связывающий эти два отдельных события. Azure Когнитивный поиск предоставляет идентификатор поиска при запросе заголовка HTTP.

Наличие идентификатора поиска разрешает корреляцию метрик, созданных Когнитивный поиск Azure для самого запроса, с настраиваемыми метриками, которые вы регистрируете в Application Insights.  

**Использование C#**

```csharp
// This sample uses the .NET SDK https://www.nuget.org/packages/Microsoft.Azure.Search

var client = new SearchIndexClient(<SearchServiceName>, <IndexName>, new SearchCredentials(<QueryKey>)

// Use HTTP headers so that you can get the search ID from the response
var headers = new Dictionary<string, List<string>>() { { "x-ms-azs-return-searchid", new List<string>() { "true" } } };
var response = await client.Documents.SearchWithHttpMessagesAsync(searchText: searchText, searchParameters: parameters, customHeaders: headers);
string searchId = string.Empty;
if (response.Response.Headers.TryGetValues("x-ms-azs-searchid", out IEnumerable<string> headerValues)){
    searchId = headerValues.FirstOrDefault();
}
```

**Использование JavaScript (вызов интерфейсов API RESTFUL)**

```javascript
request.setRequestHeader("x-ms-azs-return-searchid", "true");
request.setRequestHeader("Access-Control-Expose-Headers", "x-ms-azs-searchid");
var searchId = request.getResponseHeader('x-ms-azs-searchid');
```

### <a name="step-3-log-search-events"></a>Шаг 3. события поиска по журналам

Каждый раз, когда пользователь выдает запрос на поиск, необходимо зарегистрировать это событие поиска со следующей схемой на Application Insights настраиваемом событии. Не забывайте вести журнал только созданных пользователем поисковых запросов.

+ **SearchServiceName**: (String) имя службы поиска
+ **SearchId**: (GUID) уникальный идентификатор поискового запроса (входит в ответ поиска).
+ **IndexName**: (String) индекс службы поиска для запроса
+ **Куеритермс**: (String) Поиск слов, вводимых пользователем
+ **Ресулткаунт**: (int) число возвращенных документов (входит в ответ поиска).
+ **Скорингпрофиле**: (строка) имя используемого профиля оценки, если таковые имеются

> [!NOTE]
> Запросите число запросов, созданных пользователем, добавив $count = true в поисковый запрос. Дополнительные сведения см. в разделе [Поиск документов (остальное)](/rest/api/searchservice/search-documents#counttrue--false).
>

**Использование C#**

```csharp
var properties = new Dictionary <string, string> {
    {"SearchServiceName", <service name>},
    {"SearchId", <search Id>},
    {"IndexName", <index name>},
    {"QueryTerms", <search terms>},
    {"ResultCount", <results count>},
    {"ScoringProfile", <scoring profile used>}
    };
_telemetryClient.TrackEvent("Search", properties);
```

**Использование JavaScript**

```javascript
appInsights.trackEvent("Search", {
SearchServiceName: <service name>,
SearchId: <search id>,
IndexName: <index name>,
QueryTerms: <search terms>,
ResultCount: <results count>,
ScoringProfile: <scoring profile used>
});
```

### <a name="step-4-log-click-events"></a>Шаг 4. события щелчка журнала

Каждый щелчок документа — это событие, которое необходимо зарегистрировать для анализа поиска. Используйте пользовательские события Application Insights для регистрации этих событий по следующей схеме:

+ **ServiceName**: (String) имя службы поиска
+ **SearchId**: (GUID) уникальный идентификатор связанного поискового запроса.
+ **DocId**: (строка) идентификатор документа
+ **Положение**: (int) ранг документа на странице результатов поиска

> [!NOTE]
> Позиция ссылается на количественный порядок в приложении. Вы можете задать этот номер для сравнения, при условии, что он всегда один и тот же.
>

**Использование C#**

```csharp
var properties = new Dictionary <string, string> {
    {"SearchServiceName", <service name>},
    {"SearchId", <search id>},
    {"ClickedDocId", <clicked document id>},
    {"Rank", <clicked document position>}
    };
_telemetryClient.TrackEvent("Click", properties);
```

**Использование JavaScript**

```javascript
appInsights.trackEvent("Click", {
    SearchServiceName: <service name>,
    SearchId: <search id>,
    ClickedDocId: <clicked document id>,
    Rank: <clicked document position>
});
```

## <a name="3---analyze-in-power-bi"></a>3. Анализ в Power BI

После инструментирования приложения и проверки того, что приложение правильно подключено к Application Insights, вы скачиваете предопределенный шаблон отчета для анализа данных в Power BI Desktop. Отчет содержит стандартные диаграммы и таблицы, полезные для анализа дополнительных данных, собираемых для аналитики трафика поиска.

1. В области навигации панели мониторинга Когнитивный поиск Azure, расположенной слева, в разделе **Параметры**щелкните **Поиск аналитики трафика**.

1. На странице **Поиск аналитики трафика** на шаге 3 щелкните **Получить Power BI Desktop**, чтобы установить Power BI.

   ![Получение отчетов Power BI](./media/search-traffic-analytics/get-use-power-bi.png "Получение отчетов Power BI")

1. На той же странице щелкните **скачать Power BI отчет**.

1. Отчет откроется в Power BI Desktop, и вам будет предложено подключиться к Application Insights и ввести учетные данные. Сведения о подключении можно найти на страницах портал Azure для ресурса Application Insights. Для учетных данных укажите те же имя пользователя и пароль, которые используются для входа на портал.

   ![Подключение к Application Insights](./media/search-traffic-analytics/connect-to-app-insights.png "Подключение к Application Insights")

1. Нажмите кнопку **Загрузить**.

Этот отчет содержит диаграммы и таблицы, которые помогут вам принимать более взвешенные решения для улучшения производительности и релевантности поиска.

Метрики содержат следующие элементы.

+ Поисковый том и наиболее популярные пары терминов и документов: термины, которые приводят к тому же документу, по щелчкам.
+ Поисковые запросы без щелчков. Условия основных запросов, для которых не выполнялись щелчки.

На следующем снимке экрана показано, как может выглядеть встроенный отчет, если вы использовали все элементы схемы.

![Power BI панели мониторинга для Когнитивный поиск Azure](./media/search-traffic-analytics/azuresearch-powerbi-dashboard.png "Power BI панели мониторинга для Когнитивный поиск Azure")

## <a name="next-steps"></a>Следующие шаги

Выполните инструментирование приложения поиска для получения детальных и полезных сведений о службе поиска.

Вы можете узнать больше об [Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/app-insights-overview) и перейти на [страницу цен](https://azure.microsoft.com/pricing/details/application-insights/), чтобы получить дополнительные сведения о соответствующих уровнях служб.

Узнайте больше о создании удивительных отчетов. Дополнительные сведения см. [в разделе Приступая к работе с Power BI Desktop](https://powerbi.microsoft.com/documentation/powerbi-desktop-getting-started/) .