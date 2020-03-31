---
title: Телеметрия для анализа поискового трафика
titleSuffix: Azure Cognitive Search
description: Включите аналитику поискового трафика для Azure Cognitive Search, соберите телеметрию и события, инициированные пользователями, с помощью Application Insights, а затем проанализируйте выводы в отчете Power BI.
author: HeidiSteen
manager: nitinme
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 03/18/2020
ms.openlocfilehash: db8c1781061f038cc90310fcd00c220fa6f5d1a0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80258215"
---
# <a name="collect-telemetry-data-for-search-traffic-analytics"></a>Сбор телеметрических данных для анализа поискового трафика

Аналитика поискового трафика — это шаблон для сбора телеметрии о взаимодействии пользователей с вашим приложением Azure Cognitive Search, например, инициированные пользователем события клика и входы клавиатуры. Используя эту информацию, вы можете определить эффективность поискового решения, включая популярные поисковые термины, скорость кликов и ввод и какие входы запроса дают нулевые результаты.

Этот шаблон требует зависимости от [Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/app-insights-overview) (функция [Azure Monitor)](https://docs.microsoft.com/azure/azure-monitor/)для сбора пользовательских данных. Он требует, чтобы вы добавили приборы в свой клиентский код, как описано в этой статье. Наконец, для анализа данных потребуется механизм отчетности. Мы рекомендуем Power BI, но вы можете использовать панель мониторинга приложений или любой инструмент, который подключается к Application Insights.

> [!NOTE]
> Шаблон, описанный в этой статье, предназначен для продвинутых сценариев и данных по ссылкам, генерируемых кодом, который вы добавляете клиенту. В отличие от этого, журналы служб легко настроить, обеспечить ряд метрик, и может быть сделано на портале без кода требуется. Для всех сценариев рекомендуется включить диагностическую регистрацию. Для получения дополнительной информации смотрите [сбор и анализ данных журнала](search-monitor-logs.md).

## <a name="identify-relevant-search-data"></a>Определение соответствующих данных поиска

Чтобы иметь полезные метрики для анализа поискового трафика, необходимо регистрировать некоторые сигналы от пользователей вашего приложения поиска. Эти сигналы обозначает контент, который интересует пользователей и который они считают актуальным. Для анализа поискового трафика, они включают в себя:

+ События поиска, созданные пользователями: интересны только поисковые запросы, инициированные пользователем. Поисковые запросы, используемые для заполнения аспектов, дополнительного содержимого или каких-либо внутренних сведений, не являются важными и ведут к искажению результатов поиска.

+ События клика, генерируемые пользователем: На странице результатов поиска событие, как правило, означает, что документ является соответствующим результатом для конкретного поискового запроса.

Связывая события поиска и клика с идентификатором корреляции, вы получите более глубокое понимание того, насколько хорошо работает функция поиска вашего приложения.

## <a name="add-search-traffic-analytics"></a>Добавление аналитики поискового трафика

На странице [портала](https://portal.azure.com) для службы когнитивного поиска Azure страница Search Traffic Analytics содержит шпаргалку для выполнения этого шаблона телеметрии. На этой странице можно выбрать или создать ресурс Application Insights, получить ключ приборов, скопировать фрагменты, которые можно адаптировать для решения, и загрузить отчет Power BI, построенный по схеме, отраженной в шаблоне.

![Поиск трафика Аналитика страницы на портале](media/search-traffic-analytics/azuresearch-trafficanalytics.png "Поиск трафика Аналитика страницы на портале")

## <a name="1---set-up-application-insights"></a>1 - Настройка приложения Исследования

Выберите существующий ресурс Application Insights или [создайте его,](https://docs.microsoft.com/azure/azure-monitor/app/create-new-resource) если его у вас еще нет. Если вы используете страницу Search Traffic Analytics, вы можете скопировать ключ приборов, необходимый приложению для подключения к Application Insights.

Если у вас есть ресурс Application Insights, вы можете следовать [инструкциям для поддерживаемых языков и платформ](https://docs.microsoft.com/azure/azure-monitor/app/platforms) для регистрации приложения. Регистрация — это просто добавление ключа приборов из Application Insights в код, который настраивает ассоциацию. Вы можете найти ключ на портале или на странице аналитики поискового трафика при выборе существующего ресурса.

Короткий сотон, который работает для некоторых типов проектов Visual Studio, отражается в следующих шагах. Он создает ресурс и регистрирует ваше приложение всего за несколько кликов.

1. Для разработки Visual Studio и ASP.NET **Project** > откройте свое решение и выберите**телеметрию Project Add Application.**

1. Нажмите **Получить начало**.

1. Зарегистрируйте приложение, предоставив учетную запись Майкрософт, подписку Azure и ресурс Application Insights (новый ресурс по умолчанию). Нажмите кнопку **Зарегистрировать**.

На этом этапе приложение настроено для мониторинга приложений, что означает, что все загрузки страниц отслеживаются с помощью метрик по умолчанию. Для получения дополнительной информации о предыдущих шагах [см. Телеметрия на стороне сервера Enable Application Insights.](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-core#enable-application-insights-server-side-telemetry-visual-studio)

## <a name="2---add-instrumentation"></a>2. Добавление инструментирования

Этот шаг, где вы инструмент собственного приложения поиска, используя ресурс Application Insights ваш созданный в шаге выше. Есть четыре шага к этому процессу, начиная с создания телеметрического клиента.

### <a name="step-1-create-a-telemetry-client"></a>Шаг 1: Создание телеметрического клиента

Создайте объект, отправляя события в Application Insights. Вы можете добавить инструменты в код приложения на стороне сервера или код на стороне клиента, работающий в браузере, выраженный здесь как варианты C и JavaScript (для других языков см. полный список [поддерживаемых платформ и инфраструктур.](https://docs.microsoft.com/azure/application-insights/app-insights-platforms) Выберите подход, который дает вам нужную глубину информации.

Телеметрия на стороне сервера фиксирует метрики на уровне приложения, например в приложениях, работающих в качестве веб-сервиса в облаке или в качестве внутреннего приложения в корпоративной сети. Телеметрия сервера захватывает события поиска и щелчка, положение документа в результатах и информацию запроса, но ваш сбор данных будет прикладен к любой информации, доступной на этом уровне.

В клиенте может быть дополнительный код, который манипулирует входными данными запросов, добавляет навигацию или включает контекст (например, запросы, инициированные с главной страницы по сравнению со страницей продукта). Если это описывает ваше решение, вы можете выбрать приборы на стороне клиента, чтобы телеметрия отражала дополнительную деталь. То, как собирается эта дополнительная деталь, выходит за рамки этого шаблона, но вы можете просмотреть [Application Insights для веб-страниц](https://docs.microsoft.com/azure/azure-monitor/app/javascript#explore-browserclient-side-data) для получения большего направления. 

**Использование C#**

Для C, **инструментальный ключ** находится в конфигурации приложения, например appsettings.json, если ваш проект ASP.NET. Обратитесь к инструкциям по регистрации, если вы не уверены в ключевом местоположении.

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

### <a name="step-2-request-a-search-id-for-correlation"></a>Шаг 2: Запрос идентификатора поиска для корреляции

Для корреляции поисковых запросов с кликами необходимо иметь идентификатор корреляции, который соотносит эти два отдельных события. Azure Cognitive Search предоставляет вам идентификатор поиска при запросе с помощью заголовка HTTP.

Наличие идентификатора поиска позволяет соизлучать метрики, излучаемые Azure Cognitive Search для самого запроса, с пользовательскими метриками, которые вы регистрируете в Application Insights.  

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

**Используйте JavaScript (вызов REST AIS)**

```javascript
request.setRequestHeader("x-ms-azs-return-searchid", "true");
request.setRequestHeader("Access-Control-Expose-Headers", "x-ms-azs-searchid");
var searchId = request.getResponseHeader('x-ms-azs-searchid');
```

### <a name="step-3-log-search-events"></a>Шаг 3: События поиска журнала

Каждый раз, когда запрос на поиск выдается пользователем, вы должны войти в журнал в качестве события поиска со следующей схемой на пользовательском событии Application Insights. Не забудьте войти только пользовательские поисковые запросы.

+ **Имя поисковой службы SearchServiceName**: (строка) имя службы поиска
+ **SearchId**: (guid) уникальный идентификатор поискового запроса (входит в поисковый ответ)
+ **IndexName**: (строка) индекс поисковой службы, который будет запрошен
+ Условия поиска **queryTerms**: (строка) условия поиска, введенные пользователем
+ **ResultCount**: (int) количество документов, которые были возвращены (входит в ответ на поиск)
+ **ScoringProfile**: (строка) имя забил профиль используется, если таковые были

> [!NOTE]
> Запросите количество запросов, созданных пользователями, добавив $count-верный в свой поисковый запрос. Для получения дополнительной информации [см.](/rest/api/searchservice/search-documents#counttrue--false)
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

### <a name="step-4-log-click-events"></a>Шаг 4: Войти Нажмите события

Каждый щелчок документа — это событие, которое необходимо зарегистрировать для анализа поиска. Используйте пользовательские события Application Insights для регистрации этих событий по следующей схеме:

+ **Имя службы**обслуживания ServiceName : (строка) имя поисковой службы
+ **SearchId**: (guid) уникальный идентификатор соответствующего поискового запроса
+ **DocId**: (строка) идентификатор документа
+ **Позиция**: (int) ранг документа на странице результатов поиска

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

После того как вы сделали инструментировало ваше приложение и проверили, что ваше приложение правильно подключено к Application Insights, вы загружаете заранее определенный шаблон отчета для анализа данных на рабочем столе Power BI. Отчет содержит предопределенные диаграммы и таблицы, полезные для анализа дополнительных данных, собранных для анализа поискового трафика.

1. В панели мониторинга Azure Cognitive Search левая навигационная панель под **настройками**щелкните **аналитики трафика поиска.**

1. На странице **Поиск аналитики трафика** на шаге 3 щелкните **Получить Power BI Desktop**, чтобы установить Power BI.

   ![Получить отчеты Power BI](./media/search-traffic-analytics/get-use-power-bi.png "Получить отчеты Power BI")

1. На той же странице нажмите **Скачать Power BI отчет**.

1. Отчет открывается в Power BI Desktop, и вам предлагается подключиться к Application Insights и предоставить учетные данные. Информацию о подключении можно найти на страницах портала Azure для ресурса Application Insights. Для учетных данных укажите то же имя пользователя и пароль, которое вы используете для входе в систему портала.

   ![Подключение к Application Insights](./media/search-traffic-analytics/connect-to-app-insights.png "Подключение к Application Insights")

1. Нажмите кнопку **Загрузить**.

Этот отчет содержит диаграммы и таблицы, которые помогут вам принимать более взвешенные решения для улучшения производительности и релевантности поиска.

Метрики содержат следующие элементы.

+ Объем поиска и наиболее популярные пары термин-документов: термины, которые приводят к тому же документу нажал, заказанные кликами.
+ Поисковые запросы без щелчков. Условия основных запросов, для которых не выполнялись щелчки.

На следующем скриншоте показано, как может выглядеть встроенный отчет, если вы использовали все элементы схемы.

![Панель мониторинга Power BI для когнитивного поиска Azure](./media/search-traffic-analytics/azuresearch-powerbi-dashboard.png "Панель мониторинга Power BI для когнитивного поиска Azure")

## <a name="next-steps"></a>Дальнейшие действия

Выполните инструментирование приложения поиска для получения детальных и полезных сведений о службе поиска.

Вы можете узнать больше об [Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/app-insights-overview) и перейти на [страницу цен](https://azure.microsoft.com/pricing/details/application-insights/), чтобы получить дополнительные сведения о соответствующих уровнях служб.

Узнайте больше о создании удивительных отчетов. Смотрите [Начало работы с Power BI Desktop](https://powerbi.microsoft.com/documentation/powerbi-desktop-getting-started/) для получения подробной информации.