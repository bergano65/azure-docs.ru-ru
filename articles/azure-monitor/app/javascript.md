---
title: Исследования приложений Azure для веб-приложений JavaScript
description: Получайте количество просмотров страниц и сеансов, данные веб-клиентов, приложения для одной страницы (SPA) и отслеживайте шаблоны использования. Выявляйте исключения и проблемы с производительностью на веб-страницах JavaScript.
ms.topic: conceptual
author: Dawgfan
ms.author: mmcc
ms.date: 09/20/2019
ms.openlocfilehash: 5414a70180a82be8253dace7d800c90c1ae6a9bd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79276079"
---
# <a name="application-insights-for-web-pages"></a>Application Insights для веб-страниц

Узнайте о производительности и использовании своей веб-страницы или приложения. Если вы добавите Application Insights в сценарий [страницы,](app-insights-overview.md) вы получите тайминги нагрузок страниц ы и ajax вызовов, подсчетов и деталей исключений браузера и сбоев AJAX, а также количество пользователей и сеансов. Все эти данные можно разбить по страницам, версии клиентской ОС и браузера, географическому расположению и другим показателям. Можно также настроить оповещения для определенного количества сбоев или медленной загрузки страниц. Кроме того, вставив вызовы трассировки в код JavaScript, вы можете отслеживать использование различных функций приложения веб-страницы.

Application Insights можно использовать с любыми веб-страницами — просто добавьте небольшой фрагмент кода JavaScript. Если веб-сервис [Java](java-get-started.md) или [ASP.NET,](asp-net.md)вы можете использовать SDK на стороне сервера в сочетании с клиентской JavaScript SDK, чтобы получить полное представление о производительности приложения.

## <a name="adding-the-javascript-sdk"></a>Добавление JavaScript SDK

1. Сначала вам нужен ресурс Application Insights. Если у вас еще нет ключа ресурса и приборов, следуйте [инструкциям по созданию новых ресурсов.](create-new-resource.md)
2. Копируйте ключ приборов с ресурса, на котором вы хотите отправить телеметрию JavaScript.
3. Добавьте Приложение Insights JavaScript SDK на веб-страницу или приложение через один из следующих двух вариантов:
    * [npm Настройка](#npm-based-setup)
    * [Фрагмент JavaScript](#snippet-based-setup)

> [!IMPORTANT]
> Используйте только один метод для добавления JavaScript SDK в приложение. Если вы используете настройку NPM, не используйте фрагмент и наоборот.

> [!NOTE]
> Настройка NPM устанавливает JavaScript SDK в качестве зависимости от вашего проекта, позволяя IntelliSense, в то время как Фрагмент получает SDK во время выполнения. Оба поддерживают одни и те же функции. Тем не менее, разработчики, которые желают больше пользовательских событий и конфигурации, как правило, выбирают NPM Setup в то время как пользователи, ищущие быстрое включение вне коробки веб-аналитики выбирают фрагмент.

### <a name="npm-based-setup"></a>npm на основе установки

```js
import { ApplicationInsights } from '@microsoft/applicationinsights-web'

const appInsights = new ApplicationInsights({ config: {
  instrumentationKey: 'YOUR_INSTRUMENTATION_KEY_GOES_HERE'
  /* ...Other Configuration Options... */
} });
appInsights.loadAppInsights();
appInsights.trackPageView(); // Manually call trackPageView to establish the current user/session/pageview
```

### <a name="snippet-based-setup"></a>Установка на основе фрагмента

Если ваше приложение не использует npm, вы можете непосредственно инструментировать ваши веб-страницы с Application Insights, вставив этот фрагмент в верхней части каждой страницы. Предпочтительно, это должен быть первый сценарий в разделе, `<head>` чтобы он мог отслеживать любые потенциальные проблемы со всеми вашими зависимостями. Если вы используете приложение Blazor Server, добавьте фрагмент `_Host.cshtml` в `<head>` верхней части файла в разделе.

```html
<script type="text/javascript">
var sdkInstance="appInsightsSDK";window[sdkInstance]="appInsights";var aiName=window[sdkInstance],aisdk=window[aiName]||function(n){var o={config:n,initialize:!0},t=document,e=window,i="script";setTimeout(function(){var e=t.createElement(i);e.src=n.url||"https://az416426.vo.msecnd.net/scripts/b/ai.2.min.js",t.getElementsByTagName(i)[0].parentNode.appendChild(e)});try{o.cookie=t.cookie}catch(e){}function a(n){o[n]=function(){var e=arguments;o.queue.push(function(){o[n].apply(o,e)})}}o.queue=[],o.version=2;for(var s=["Event","PageView","Exception","Trace","DependencyData","Metric","PageViewPerformance"];s.length;)a("track"+s.pop());var r="Track",c=r+"Page";a("start"+c),a("stop"+c);var u=r+"Event";if(a("start"+u),a("stop"+u),a("addTelemetryInitializer"),a("setAuthenticatedUserContext"),a("clearAuthenticatedUserContext"),a("flush"),o.SeverityLevel={Verbose:0,Information:1,Warning:2,Error:3,Critical:4},!(!0===n.disableExceptionTracking||n.extensionConfig&&n.extensionConfig.ApplicationInsightsAnalytics&&!0===n.extensionConfig.ApplicationInsightsAnalytics.disableExceptionTracking)){a("_"+(s="onerror"));var p=e[s];e[s]=function(e,n,t,i,a){var r=p&&p(e,n,t,i,a);return!0!==r&&o["_"+s]({message:e,url:n,lineNumber:t,columnNumber:i,error:a}),r},n.autoExceptionInstrumented=!0}return o}(
{
  instrumentationKey:"INSTRUMENTATION_KEY"
}
);(window[aiName]=aisdk).queue&&0===aisdk.queue.length&&aisdk.trackPageView({});
</script>
```

### <a name="sending-telemetry-to-the-azure-portal"></a>Отправка телеметрии на портал Azure

По умолчанию Application Insights JavaScript SDK автоматически собирает ряд элементов телеметрии, которые полезны для определения работоспособности приложения и основного пользовательского опыта. Сюда входит следующее.

- **Непойманные исключения** в вашем приложении, включая информацию о
    - Трассировка стека
    - Детали исключения и сообщения, сопровождающие ошибку
    - Строка & столбец номер ошибки
    - URL, где была поднята ошибка
- **Запросы на сетевую зависимость,** сделанные вашим приложением **XHR** и **Fetch** (сбор данных отключен по умолчанию) запросы, включают информацию о
    - Url источника зависимости
    - Метод командования & используется для запроса зависимости
    - Длительность запроса
    - Код результата и состояние успеха запроса
    - Идентификатор (если такового имеется) пользователя, делающих запрос
    - Контекст корреляции (если таковые) в случае запроса
- **Информация о пользователях** (например, местоположение, сеть, IP)
- **Информация об устройстве** (например, браузер, ОС, версия, язык, разрешение, модель)
- **Сведения о сеансе**

### <a name="telemetry-initializers"></a>Инициали телеметрии
Инициали телеметрии используются для изменения содержимого собранной телеметрии перед отправкой из браузера пользователя. Они также могут быть использованы для предотвращения некоторых телеметрии от отправки, путем возвращения `false`. Несколько инициалителей телеметрии могут быть добавлены в экземпляр Application Insights, и они выполняются для их добавления.

Входный аргумент `addTelemetryInitializer` является обратный вызов, [`ITelemetryItem`](https://github.com/microsoft/ApplicationInsights-JS/blob/master/API-reference.md#addTelemetryInitializer) который принимает в `boolean` `void`качестве аргумента и возвращает или . При `false`возвращении, элемент телеметрии не отправляется, иначе он переходит к следующему инициализатору телеметрии, если таковые, или отправляется в конечную точку сбора телеметрии.

Пример использования инициализаторов телеметрии:
```ts
var telemetryInitializer = (envelope) => {
  envelope.data.someField = 'This item passed through my telemetry initializer';
};
appInsights.addTelemetryInitializer(telemetryInitializer);
appInsights.trackTrace({message: 'This message will use a telemetry initializer'});

appInsights.addTelemetryInitializer(() => false); // Nothing is sent after this is executed
appInsights.trackTrace({message: 'this message will not be sent'}); // Not sent
```

## <a name="configuration"></a>Параметр Configuration
Большинство полей конфигурации названы таким образом, что они могут быть по умолчанию ложными. Все поля не `instrumentationKey`являются обязательными, за исключением .

| name | Значение по умолчанию | Описание |
|------|---------|-------------|
| instrumentationKey | null | **Обязательно**<br>Ключ инструментария, полученный на портале Azure. |
| accountId | null | Дополнительный идентификатор учетной записи, если ваше приложение группирует пользователей в учетные записи. Нет пробелов, запятых, запятых, равных или вертикальных баров |
| сессииRenewalMs | 1800000 | Сеанс регистрируется, если пользователь неактивен в течение этого периода времени в миллисекундах. Значение по умолчанию — 30 минут. |
| sessionExpirationMs | 86400000 | Сеанс регистрируется, если он продолжался в течение этого периода времени в миллисекундах. По умолчанию 24 часа |
| maxbatchSizeInBytes | 10000 | Максимальный размер телеметрической партии. Если пакет превышает этот предел, она немедленно отправляется и запускается новая партия |
| maxBatchInterval | 15 000 | Как долго для пакетной телеметрии перед отправкой (миллисекунды) |
| отключитьИсключениеTracking | false | Если это так, исключения не являются автособранными. Значение по умолчанию — false. |
| отключитьТелеметрия | false | Если это так, телеметрия не собирается и не отправляется. Значение по умолчанию — false. |
| включитьДебаг | false | Если это так, **данные внутренней** отладки выбрасываются в качестве исключения, **а не** регистрируются, независимо от настроек регистрации SDK. Значение по умолчанию — false. <br>***Примечание:*** Включение этого параметра приведет к удалению телеметрии всякий раз, когда происходит внутренняя ошибка. Это может быть полезно для быстрого выявления проблем с конфигурацией или использованием SDK. Если вы не хотите терять телеметрию во `consoleLoggingLevel` время `telemetryLoggingLevel` отладки, рассмотрите возможность использования или вместо `enableDebug`. |
| loggingLevelConsole | 0 | Входы **внутренних** ошибок Application Insights для утешения. <br>0: выключен, <br>1: Критические ошибки только, <br>2: Все (ошибки & предупреждения) |
| лесозаготовительныйLevelTelemetry | 1 | Отправляет **внутренние** ошибки Application Insights как телеметрия. <br>0: выключен, <br>1: Критические ошибки только, <br>2: Все (ошибки & предупреждения) |
| диагностическийLogInterval | 10000 | (внутренний) Интервал опроса (в мс) для внутренней очереди регистрации |
| выборкаПроцент | 100 | Процент событий, которые будут отправлены. По умолчанию 100, то есть все события отправляются. Установите это, если вы хотите сохранить крышку данных для крупномасштабных приложений. |
| autoTrackPageVisitTime | false | Если верно, то на странице время просмотра предыдущей инструментальной страницы отслеживается и отправляется в виде телеметрии, а для текущего просмотра страниц запускается новый таймер. Значение по умолчанию — false. |
| отключитьAjaxTracking | false | Если это так, то вызовы Ajax не собираются автоматически. Значение по умолчанию — false. |
| отключитьЧкуTracking | Да | Если это так, запросы Fetch не собираются автоматически. Значение по умолчанию — true. |
| переопределитьPageViewDuration | false | Если это так, поведение trackPageView по умолчанию изменяется на запись интервала действия представления страницы при вызове trackPageView. Если для отслеживания PageView предусмотрена ложная и не предусмотрена пользовательская продолжительность, производительность представления страницы рассчитывается с помощью API времени навигации. Значение по умолчанию — false. |
| maxAjaxCallsPerView | 500 | По умолчанию 500 - контролирует, сколько вызовов Ajax будет контролироваться на просмотр страницы. Установите -1 для мониторинга всех (неограниченных) вызовов Ajax на странице. |
| отключитьАнализ данных | Да | Если это неверно, внутренние буферы отправителя телеметрии будут проверены при запуске на наличие еще не отправленных элементов. |
| отключитьCorrelationHeaders | false | Если sDK является ложным, sDK добавит два заголовка ('Request-Id' и 'Request-Context') ко всем запросам зависимостей, чтобы соотнести их с соответствующими запросами на стороне сервера. Значение по умолчанию — false. |
| корреляцияХедераИзеддотеддодомены |  | Отключить заголовки корреляций для конкретных доменов |
| корреляцияHeaderДометы |  | Включить заголовки корреляций для конкретных доменов |
| отключитьFlushOnBeforeUnload | false | По умолчанию ложно. Если это так, метод флеша не будет вызываться при срабатывании события onBeforeUnload |
| включитьSessionStorageBuffer | Да | По умолчанию верно. Если это так, буфер со всей неотправленной телеметрией хранится в хранилище сеансов. Буфер восстанавливается при загрузке страницы |
| isCookieUseDisabledDisabled | false | По умолчанию ложно. Если это так, SDK не будет хранить и читать какие-либо данные из файлов cookie.|
| cookieDomain | null | Пользовательский домен cookie. Это полезно, если вы хотите поделиться файлами файлов Application Insights в поддоменах. |
| isRetryDisabled | false | По умолчанию ложно. Если ложный, повторная попытка на 206 (частичный успех), 408 (тайм-аут), 429 (слишком много запросов), 500 (внутренняя ошибка сервера), 503 (обслуживание недоступно) и 0 (в автономном режиме, только если обнаружено) |
| isStorageUseDisabled | false | Если это так, То SDK не будет хранить и читать данные из локального и сессионного хранилища. Значение по умолчанию — false. |
| isBeaconApiDisabled | Да | В случае ложности, SDK отправит всю телеметрию с помощью [API-маяка](https://www.w3.org/TR/beacon) |
| onunloadDisableBeacon | false | По умолчанию ложно. когда вкладка закрыта, SDK отправит всю оставшуюся телеметрию с помощью [API-маяка](https://www.w3.org/TR/beacon) |
| sdkExtension | null | Устанавливает имя расширения sdk. Разрешены только алфавитные символы. Имя расширения добавляется в качестве приставки к тегу 'ai.internal.sdkVersion' (например, 'ext_javascript:2.0.0'). Значение по умолчанию — null. |
| isBrowserLinkTracking | false | Значение по умолчанию — false. Если это так, SDK будет отслеживать все запросы [браузера ссылка.](https://docs.microsoft.com/aspnet/core/client-side/using-browserlink) |
| appId | null | AppId используется для корреляции между зависимостями AJAX, происходящими на стороне клиента, с запросами на стороне сервера. При включении API-избн Beacon он не может быть использован автоматически, но может быть установлен вручную в конфигурации. По умолчанию является нулевым |
| enableCorsCorrelation | false | Если это так, То SDK добавит два заголовка («Запрос-Id» и «Запрос-Контекст») ко всем запросам CORS для соотношения исходящих зависимостей AJAX с соответствующими запросами на стороне сервера. Значение по умолчанию — false. |
| имяПрефикс | неопределенный | Дополнительное значение, которое будет использоваться в качестве почтового иссправления для localStorage и имя cookie.
| включитьAutoRouteTracking | false | Автоматически отслеживать изменения маршрута в приложениях одной страницы (SPA). Если это так, каждое изменение маршрута отправит новое Представление страниц в Application Insights. Изменения маршрута Hash ()`example.com/foo#bar`также регистрируются как новые представления страниц.
| enableRequestHeaderTracking | false | Если это правда, заголовки запросов AJAX & Fetch отслеживаются, по умолчанию это ложно.
| включитьResponseHeaderTracking | false | Если это правда, то заголовки ответов запроса «& Fetch» отслеживаются, по умолчанию это ложно.
| распространенныйTracingMode | `DistributedTracingModes.AI` | Устанавливает режим распределенного отслеживания. Если AI_AND_W3C режиме или режиме W3C установлен, заголовки контекста трассировки W3C (traceparent/tracestate) будут сгенерированы и включены во все исходящие запросы. AI_AND_W3C предусмотрена для обратной совместимости с любыми устаревшими инструментами Application Insights.

## <a name="single-page-applications"></a>Приложения для одной страницы

По умолчанию этот SDK **не** будет обрабатывать изменения маршрута на основе состояния, которое происходит в приложениях на одной странице. Для автоматического отслеживания изменения маршрута для приложения `enableAutoRouteTracking: true` с одной страницей можно добавить в конфигурацию настройки.

В настоящее время мы предлагаем отдельный [плагин React,](#react-extensions) который вы можете инициализировать с этим SDK. Он также будет выполнять отслеживание изменения маршрута для вас, а также собирать [другие Реагировать конкретных телеметрии.](https://github.com/microsoft/ApplicationInsights-JS/blob/17ef50442f73fd02a758fbd74134933d92607ecf/extensions/applicationinsights-react-js/README.md)

## <a name="react-extensions"></a>Расширения реакции

| Расширения |
|---------------|
| [React](https://github.com/microsoft/ApplicationInsights-JS/blob/17ef50442f73fd02a758fbd74134933d92607ecf/extensions/applicationinsights-react-js/README.md)|
| [React Native](https://github.com/microsoft/ApplicationInsights-JS/blob/17ef50442f73fd02a758fbd74134933d92607ecf/extensions/applicationinsights-react-native/README.md)|

## <a name="explore-browserclient-side-data"></a>Изучение данных браузера/клиента

Данные браузера/клиента можно просматривать, перейдя в **Metrics** и добавив отдельные метрики, которые вас интересуют:

![](./media/javascript/page-view-load-time.png)

Вы также можете просматривать свои данные с JavaScript SDK через браузер на портале.

Выберите **браузер,** а затем выберите **сбои** или **производительность.**

![](./media/javascript/browser.png)

### <a name="performance"></a>Производительность

![](./media/javascript/performance-operations.png)

### <a name="dependencies"></a>Зависимости

![](./media/javascript/performance-dependencies.png)

### <a name="analytics"></a>Analytics

Чтобы задать вопрос о телеметрии, собранной JavaScript SDK, выберите кнопку **«Вид в журналах» (Analytics).** Добавляя `where` `client_Type == "Browser"`оператор, вы увидите только данные из JavaScript SDK, и любая телеметрия на стороне сервера, собранная другими SDK, будет исключена.
 
```kusto
// average pageView duration by name
let timeGrain=5m;
let dataset=pageViews
// additional filters can be applied here
| where timestamp > ago(1d)
| where client_Type == "Browser" ;
// calculate average pageView duration for all pageViews
dataset
| summarize avg(duration) by bin(timestamp, timeGrain)
| extend pageView='Overall'
// render result in a chart
| render timechart
```

### <a name="source-map-support"></a>Поддержка исходной карты

На портале Azure можно неосвоить minified коллстакт телеметрии исключения. Все существующие интеграции на панели «Детали исключения» будут работать с недавно неиспеченным коллстаком.

#### <a name="link-to-blob-storage-account"></a>Ссылка на учетную запись хранения Blob

Вы можете связать ресурс Application Insights с собственным контейнером для хранения Azure Blob Storage, чтобы автоматически отменить стеки вызовов. Для начала [см.](./source-map-support.md)

### <a name="drag-and-drop"></a>Перетаскивание

1. Выберите элемент телеметрии исключений на портале Azure для просмотра его "Подробности транзакции в конце концов"
2. Определите, какие исходные карты соответствуют этому стеку вызовов. Исходная карта должна соответствовать исходного файла кадра стека, но суффиксирована с`.map`
3. Перетащите и бросьте исходные карты в стек вызова на портале Azure![](https://i.imgur.com/Efue9nU.gif)

### <a name="application-insights-web-basic"></a>Приложение Исследования веб-основы

Для легкого опыта можно установить базовую версию Application Insights
```
npm i --save @microsoft/applicationinsights-web-basic
```
Эта версия поставляется с минимальным количеством функций и функциональных возможностей и полагается на вас, чтобы построить его, как вы считаете нужным. Например, он не выполняет autocollection (непойманные исключения, AJAX и т.д.). AA для отправки определенных типов `trackTrace` `trackException`телеметрии, как , и т.д., не включены в эту версию, так что вам нужно будет предоставить свой собственный обертку. Единственный доступный API `track`. Здесь находится [образец.](https://github.com/Azure-Samples/applicationinsights-web-sample1/blob/master/testlightsku.html)

## <a name="examples"></a>Примеры

Для примеров, которые можно найти, приведены в [примерах анализа приложений JavaScript SDK](https://github.com/topics/applicationinsights-js-demo)

## <a name="upgrading-from-the-old-version-of-application-insights"></a>Обновление от старой версии Application Insights

Нарушение изменений в версии SDK V2:
- Для улучшения подписей API были обновлены некоторые вызовы API, такие как trackPageView и trackException. Запуск в Internet Explorer 8 и более ранних версиях браузера не поддерживается.
- Конверт телеметрии имеет поле в именем и структурой изменения из-за обновления схемы данных.
- `context.operation` Перемещено `context.telemetryTrace`в . Некоторые поля также`operation.id` --> `telemetryTrace.traceID`были изменены ().
  - Для ручного обновления текущего идентификатора просмотра `appInsights.properties.context.telemetryTrace.traceID = Util.generateW3CId()`страницы (например, в приложениях SPA) используйте.
    > [!NOTE]
    > Чтобы сохранить идентификатор трассировки уникальным, где вы ранее использовали, `Util.newId()`теперь используйте. `Util.generateW3CId()` Оба в конечном итоге в конечном итоге идентификатор операции.

Если вы используете текущие сведения о приложениях PRODUCTION SDK (1.0.20) и хотите узнать, работает ли новый SDK во время выполнения, обновите URL-адрес в зависимости от текущего сценария загрузки SDK.

- Скачать через сценарий CDN: Обновите фрагмент кода, который вы в настоящее время используете, чтобы указать на следующий URL:
   ```
   "https://az416426.vo.msecnd.net/scripts/b/ai.2.min.js"
   ```

- сценарий npm: Вызов `downloadAndSetup` скачать полный сценарий ApplicationInsights с CDN и инициализировать его с ключом приборов:

   ```ts
   appInsights.downloadAndSetup({
     instrumentationKey: "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxx",
     url: "https://az416426.vo.msecnd.net/scripts/b/ai.2.min.js"
     });
   ```

Тест во внутренней среде для проверки телеметрии мониторинга работает как ожидалось. Если все работает, обновите подписи API в соответствии с версией SDK V2 и разместите их в производственных средах.

## <a name="sdk-performanceoverhead"></a>Производительность/накладные расходы SDK

На только 25 КБ gzipped, и принимая только 15 мс, чтобы инициализировать, Application Insights добавляет незначительное количество нагрузок на ваш сайт. С помощью фрагмента, минимальные компоненты библиотеки быстро загружаются. В то же время, полный скрипт загружается в фоновом режиме.

В то время как скрипт загружается с CDN, все отслеживания вашей страницы в очереди. Как только загруженный скрипт завершается асинхронно инициализационным, отслеживаются все события, которые были в очереди. В результате, вы не потеряете телеметрию в течение всего жизненного цикла вашей страницы. Этот процесс настройки обеспечивает вашу страницу с бесшовной системой аналитики, невидимой для пользователей.

> Сводка.
> - **25 КБ** гциппед
> - **15 мс** общее время инициализации
> - **Нулевое** отслеживание пропущено в течение жизненного цикла страницы

## <a name="browser-support"></a>Поддержка браузеров

![Chrome](https://raw.githubusercontent.com/alrra/browser-logos/master/src/chrome/chrome_48x48.png) | ![Firefox](https://raw.githubusercontent.com/alrra/browser-logos/master/src/firefox/firefox_48x48.png) | ![IE](https://raw.githubusercontent.com/alrra/browser-logos/master/src/edge/edge_48x48.png) | ![Opera](https://raw.githubusercontent.com/alrra/browser-logos/master/src/opera/opera_48x48.png) | ![Safari](https://raw.githubusercontent.com/alrra/browser-logos/master/src/safari/safari_48x48.png)
--- | --- | --- | --- | --- |
Chrome Последние ✔ |  Firefox Последние ✔ | IE 9 "& Край ✔ | Опера Последние ✔ | Safari Последние ✔ |

## <a name="open-source-sdk"></a>Пакет SDK с открытым исходным кодом

Приложение Insights JavaScript SDK является открытым исходным кодом для просмотра исходного кода или внести свой вклад в проект посетить [официальный репозиторий GitHub](https://github.com/Microsoft/ApplicationInsights-JS).

## <a name="next-steps"></a><a name="next"></a> Дальнейшие действия
* [Отслеживание использования](usage-overview.md)
* [Пользовательские события и метрики](api-custom-events-metrics.md)
* [Сборка, измерение и обучение](usage-overview.md)
