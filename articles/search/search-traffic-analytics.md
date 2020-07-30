---
title: Данные телеметрии для аналитики трафика поиска
titleSuffix: Azure Cognitive Search
description: Включите аналитику трафика поиска для Когнитивного поиска Azure, собирайте данные телеметрии и сведения о событиях, инициированных пользователем, с помощью Application Insights, а затем анализируйте полученные результаты в отчете Power BI.
author: HeidiSteen
manager: nitinme
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 03/18/2020
ms.custom: devx-track-javascript
ms.openlocfilehash: 6ab32a2ccb4c7eb79309798c2b53d326723ad6ea
ms.sourcegitcommit: e71da24cc108efc2c194007f976f74dd596ab013
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/29/2020
ms.locfileid: "87420079"
---
# <a name="collect-telemetry-data-for-search-traffic-analytics"></a>Сбор данных телеметрии для аналитики трафика поиска

Аналитика трафика поиска — это шаблон для сбора данных телеметрии о взаимодействии пользователей с приложением Когнитивного поиска Azure, таких как инициированные пользователем события щелчка мышью и ввода с клавиатуры. С помощью этих сведений можно определить эффективность решения для поиска, включая популярные условия поиска, частоту переходов, а также то, какие запросы выдают нулевые результаты.

Этот шаблон зависит от [Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/app-insights-overview) (функция [Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/)) для сбора данных пользователей. Для этого необходимо добавить инструментирование в код клиента, как описано в этой статье. Наконец, для анализа данных потребуется механизм создания отчетов. Рекомендуем использовать Power BI, однако можно использовать панель мониторинга приложения или любое средство, которое подключается к Application Insights.

> [!NOTE]
> Шаблон, описанный в этой статье, предназначен для расширенных сценариев и навигации данных, создаваемых кодом, который добавляется в клиент. Журналы служб, напротив, просты в настройке, предоставляют ряд метрик и могут выполняться на портале без кода. Для всех сценариев рекомендуется включить ведение журнала. Дополнительные сведения см. в разделе [Сбор и анализ данных журналов](search-monitor-logs.md).

## <a name="identify-relevant-search-data"></a>Определение соответствующих данных поиска

Для получения полезных метрик трафика поиска необходимо организовать регистрацию некоторых пользовательских событий в приложении поиска. Эти события обозначают интересующее пользователей содержимое, которое является соответствующим для них. Для аналитики трафика поиска такие события включают следующее.

+ События поиска, инициированные пользователями. Представляют интерес только поисковые запросы, инициируемые пользователем. Поисковые запросы, используемые для заполнения аспектов, дополнительного содержимого или каких-либо внутренних сведений, не являются важными и ведут к искажению результатов поиска.

+ Пользовательские события щелчка мышью. На странице результатов поиска щелчок мышью обычно означает, что документ является релевантным для конкретного поискового запроса.

Связав события поиска и щелчка мышью с идентификатором корреляции, вы получите более глубокое представление о том, насколько хорошо работает функция поиска приложения.

## <a name="add-search-traffic-analytics"></a>Добавление аналитики поискового трафика

На странице [портала](https://portal.azure.com) службы Когнитивного поиска Azure в разделе "Поиск аналитики трафика" содержится памятка по следующему шаблону телеметрии. На этой странице можно выбрать или создать ресурс Application Insights, получить ключ инструментирования, скопировать фрагменты кода, которые можно адаптировать для решения, и загрузить отчет Power BI, созданный на основе схемы, которая отражена в шаблоне.

![Страница "Поиск аналитики трафика" на портале](media/search-traffic-analytics/azuresearch-trafficanalytics.png "Страница "Поиск аналитики трафика" на портале")

## <a name="1---set-up-application-insights"></a>1\. Настройка Application Insights

Выберите или [создайте](https://docs.microsoft.com/azure/azure-monitor/app/create-new-resource) ресурс Application Insights (если у вас его нет). При использовании страницы "Поиск аналитики трафика" можно скопировать ключ инструментирования, который требуется приложению для подключения к Application Insights.

Получив ресурс Application Insights, можно выполнить [инструкции для поддерживаемых языков и платформ](https://docs.microsoft.com/azure/azure-monitor/app/platforms), чтобы зарегистрировать приложение. Регистрация — это просто добавление ключа инструментирования из Application Insights в код, который настраивает связь. Ключ находится на портале или на странице "Поиск аналитики трафика" при выборе существующего ресурса.

Ярлык, который подходит для некоторых типов проектов Visual Studio, представлен на следующих шагах. Он служит для создания ресурса и регистрации приложения всего несколькими щелчками мышью.

1. Для разработки с помощью Visual Studio и ASP.NET откройте решение и выберите **Проект**  >  **Добавить телеметрию Application Insights**.

1. Щелкните **Начать работу**.

1. Зарегистрируйте приложение, предоставив учетную запись Майкрософт, подписку Azure и ресурс Application Insights (по умолчанию используется новый ресурс). Щелкните **Зарегистрировать**.

На этом этапе приложение настроено для мониторинга приложений, что означает, что все загрузки страниц отслеживаются с помощью метрик по умолчанию. Дополнительные сведения о предыдущих шагах см. в разделе [Включение телеметрии Enable Application Insights на стороне сервера](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-core#enable-application-insights-server-side-telemetry-visual-studio).

## <a name="2---add-instrumentation"></a>2\. Добавление инструментирования

На этом шаге выполняется инструментирование собственного приложения поиска с помощью ресурса Application Insights, созданного на предыдущем шаге. Этот процесс состоит из четырех этапов, начиная с создания клиента телеметрии.

### <a name="step-1-create-a-telemetry-client"></a>Шаг 1. Создание клиента телеметрии

Создайте объект, отправляющий события в Application Insights. Можно добавить инструментирование в код приложения на стороне сервера или клиентский код, выполняемый в браузере, который представлен здесь как варианты C# и JavaScript (для других языков см. полный список [поддерживаемых платформ и инфраструктур](https://docs.microsoft.com/azure/application-insights/app-insights-platforms)). Выберите подход, который обеспечивает нужную глубину информации.

Данные телеметрии на стороне сервера захватывают метрики на уровне приложения, например в приложениях, выполняющихся в качестве веб-службы в облаке или в качестве локального приложения в корпоративной сети. Данные телеметрии на стороне сервера захватывают события поиска и щелчка мышью, расположение документа в результатах и сведения о запросах, однако сбор данных будет ограничен любой информацией, доступной на этом уровне.

В клиенте может иметься дополнительный код, который управляет вводом запросов, добавляет навигацию или включает контекст (например, запросы, инициированные с домашней страницы и страницы продукта). Если это описание подходит для вашего решения, вы можете выбрать инструментирование на стороне клиента, чтобы в данных телеметрии отражались дополнительные сведения. Сбор этой дополнительной информации выходит за рамки этого шаблона, однако вы можете ознакомиться со статьей [Application Insights для веб-страниц](https://docs.microsoft.com/azure/azure-monitor/app/javascript#explore-browserclient-side-data), чтобы узнать подробнее. 

**Использование C#**

Для C# **InstrumentationKey** находится в конфигурации приложения, например в файле appsettings.json, если проект ASP.NET. Если вы точно не знаете, где расположен ключ, обратитесь к инструкциям по регистрации.

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

### <a name="step-2-request-a-search-id-for-correlation"></a>Шаг 2. Запрос идентификатора поиска для корреляции

Для корреляции поисковых запросов с щелчками необходим идентификатор корреляции, связывающий эти два различных события. Служба Когнитивного поиска Azure предоставляет идентификатор поиска, когда он запрашивается с заголовком HTTP.

Наличие идентификатора поиска обеспечивает корреляцию метрик, созданных Когнитивным поиском Azure для самого запроса, с настраиваемыми метриками, которые вы регистрируете в Application Insights.  

**Использование C#**

```csharp
// This sample uses the .NET SDK https://www.nuget.org/packages/Microsoft.Azure.Search

var client = new SearchIndexClient(<SearchServiceName>, <IndexName>, new SearchCredentials(<QueryKey>)

// Use HTTP headers so that you can get the search ID from the response
var headers = new Dictionary<string, List<string>>() { { "x-ms-azs-return-searchid", new List<string>() { "true" } } };
var response = await client.Documents.SearchWithHttpMessagesAsync(searchText: searchText, searchParameters: parameters, customHeaders: headers);
string searchId = string.Empty;
if (response.Response.Headers.TryGetValues("x-ms-azs-searchid", out IEnumerable<string> headerValues))
{
    searchId = headerValues.FirstOrDefault();
}
```

**Использование JavaScript (вызов REST API)**

```javascript
request.setRequestHeader("x-ms-azs-return-searchid", "true");
request.setRequestHeader("Access-Control-Expose-Headers", "x-ms-azs-searchid");
var searchId = request.getResponseHeader('x-ms-azs-searchid');
```

### <a name="step-3-log-search-events"></a>Шаг 3. Регистрация событий поиска

Каждый отправляемый поисковой запрос необходимо регистрировать в качестве события поиска, используя следующую схему настраиваемого события Application Insights. Не забывайте регистрировать только те поисковые запросы, которые созданы пользователем.

+ **SearchServiceName**: (строка) имя службы поиска
+ **SearchId**: (GUID) уникальный идентификатор поискового запроса (входит в ответ поиска)
+ **IndexName**: (строка) индекс службы поиска для запроса
+ **QueryTerms**: (строка) слова для поиска, вводимые пользователем
+ **ResultCount**: (целое число) число возвращенных документов (входит в ответ поиска)
+ **ScoringProfile**: (строка) имя используемого профиля оценки, если имеется

> [!NOTE]
> Получите число запросов, создаваемых пользователем, добавив $count=true в поисковый запрос. Дополнительные сведения см. в статье [Поиск документов (REST)](/rest/api/searchservice/search-documents#counttrue--false).
>

**Использование C#**

```csharp
var properties = new Dictionary <string, string> 
{
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

### <a name="step-4-log-click-events"></a>Шаг 4. Регистрация событий щелчка мышью

Каждый щелчок документа — это событие, которое необходимо зарегистрировать для анализа поиска. Используйте пользовательские события Application Insights для регистрации этих событий по следующей схеме:

+ **ServiceName**: (строка) имя службы поиска
+ **SearchId**: (GUID) уникальный идентификатор связанного поискового запроса
+ **DocId**: (строка) идентификатор документа
+ **Position**: (целое число) положение документа на странице результатов поиска

> [!NOTE]
> Позиция ссылается на количественный порядок в приложении. Вы можете задать этот номер для сравнения, при условии, что он всегда один и тот же.
>

**Использование C#**

```csharp
var properties = new Dictionary <string, string> 
{
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

## <a name="3---analyze-in-power-bi"></a>3\. Анализ в Power BI

После инструментирования приложения и проверки правильности его подключения к Application Insights можно загрузить предварительно заданный шаблон отчета, чтобы проанализировать данные в Power BI Desktop. В отчете содержатся предварительно заданные диаграммы и таблицы, которые можно использовать при анализе дополнительных данных, полученных для аналитики поискового трафика.

1. В области навигации слева на панели мониторинга Когнитивного поиска Azure в разделе **Параметры** щелкните **Поиск аналитики трафика**.

1. На странице **Поиск аналитики трафика** на шаге 3 щелкните **Получить Power BI Desktop**, чтобы установить Power BI.

   ![Получение отчетов Power BI](./media/search-traffic-analytics/get-use-power-bi.png "Получение отчетов Power BI")

1. На этой же странице щелкните **Скачать отчет Power BI**.

1. В Power BI Desktop откроется отчет, и вам будет предложено подключиться к Application Insights и указать учетные данные. Сведения о подключении представлены на страницах портала Azure для вашего ресурса Application Insights. Для учетных данных укажите те же имя пользователя и пароль, которые используются для входа на портал.

   ![Подключение к Application Insights](./media/search-traffic-analytics/connect-to-app-insights.png "Подключение к Application Insights")

1. Нажмите кнопку **Загрузить**.

Этот отчет содержит диаграммы и таблицы, которые помогут вам принимать более взвешенные решения для улучшения производительности и релевантности поиска.

Метрики содержат следующие элементы.

+ Объем поиска и распространенные пары "условие —документ". Условия, в результате которых щелкают тот же документ, c упорядочиванием по щелчкам.
+ Поисковые запросы без щелчков. Условия основных запросов, для которых не выполнялись щелчки.

На следующем снимке экрана показано, как может выглядеть встроенный отчет, если вы использовали все элементы схемы.

![Панель мониторинга Power BI для Когнитивного поиска Azure](./media/search-traffic-analytics/azuresearch-powerbi-dashboard.png "Панель мониторинга Power BI для Когнитивного поиска Azure")

## <a name="next-steps"></a>Дальнейшие действия

Выполните инструментирование приложения поиска для получения детальных и полезных сведений о службе поиска.

Вы можете узнать больше об [Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/app-insights-overview) и перейти на [страницу цен](https://azure.microsoft.com/pricing/details/application-insights/), чтобы получить дополнительные сведения о соответствующих уровнях служб.

Узнайте больше о создании удивительных отчетов. См. статью [Начало работы с Power BI Desktop](https://docs.microsoft.com/power-bi/fundamentals/desktop-getting-started).
