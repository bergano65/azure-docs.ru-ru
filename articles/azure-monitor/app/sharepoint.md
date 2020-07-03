---
title: Мониторинг сайта SharePoint с помощью Application Insights
description: Начало мониторинга нового приложения с помощью нового ключа инструментирования
ms.topic: conceptual
ms.date: 07/11/2018
ms.openlocfilehash: 395e8d667985318f4a084428c6fd4c395ee8b956
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2020
ms.locfileid: "77671449"
---
# <a name="monitor-a-sharepoint-site-with-application-insights"></a>Мониторинг сайта SharePoint с помощью Application Insights
Azure Application Insights позволяет отслеживать доступность, производительность и использование приложений. В этой статье вы узнаете, как настроить эту службу для сайта SharePoint.

## <a name="create-an-application-insights-resource"></a>Создание ресурса Application Insights
На [портале Azure](https://portal.azure.com) создайте ресурс Application Insights. Выберите приложение ASP.NET в качестве типа приложения.

![Нажмите «Свойства», выберите ключ и нажмите сочетание клавиш CTRL + C](./media/sharepoint/001.png)

В открывшемся окне будут указаны данные о производительности и использовании приложения. Чтобы возвратиться к нему после очередного входа в Azure, найдите соответствующую плитку на начальном экране. Ее также можно найти, щелкнув «Обзор».

## <a name="add-the-script-to-your-web-pages"></a>Добавление скрипта на веб-страницы

```HTML
<!-- 
To collect user behavior analytics tools about your application, 
insert the following script into each page you want to track.
Place this code immediately before the closing </head> tag,
and before any other scripts. Your first data will appear 
automatically in just a few seconds.
-->
<script type="text/javascript">
var sdkInstance="appInsightsSDK";window[sdkInstance]="appInsights";var aiName=window[sdkInstance],aisdk=window[aiName]||function(n){var o={config:n,initialize:!0},t=document,e=window,i="script";setTimeout(function(){var e=t.createElement(i);e.src=n.url||"https://az416426.vo.msecnd.net/scripts/b/ai.2.min.js",t.getElementsByTagName(i)[0].parentNode.appendChild(e)});try{o.cookie=t.cookie}catch(e){}function a(n){o[n]=function(){var e=arguments;o.queue.push(function(){o[n].apply(o,e)})}}o.queue=[],o.version=2;for(var s=["Event","PageView","Exception","Trace","DependencyData","Metric","PageViewPerformance"];s.length;)a("track"+s.pop());var r="Track",c=r+"Page";a("start"+c),a("stop"+c);var u=r+"Event";if(a("start"+u),a("stop"+u),a("addTelemetryInitializer"),a("setAuthenticatedUserContext"),a("clearAuthenticatedUserContext"),a("flush"),o.SeverityLevel={Verbose:0,Information:1,Warning:2,Error:3,Critical:4},!(!0===n.disableExceptionTracking||n.extensionConfig&&n.extensionConfig.ApplicationInsightsAnalytics&&!0===n.extensionConfig.ApplicationInsightsAnalytics.disableExceptionTracking)){a("_"+(s="onerror"));var p=e[s];e[s]=function(e,n,t,i,a){var r=p&&p(e,n,t,i,a);return!0!==r&&o["_"+s]({message:e,url:n,lineNumber:t,columnNumber:i,error:a}),r},n.autoExceptionInstrumented=!0}return o}(
{
  instrumentationKey:"INSTRUMENTATION_KEY"
}
);(window[aiName]=aisdk).queue&&0===aisdk.queue.length&&aisdk.trackPageView({});
</script>
```

Вставьте сценарий непосредственно перед тегом &lt;/ХЕАД&gt; каждой страницы, которую требуется отвести от него. Если у веб-сайта есть эталонная страница, можно разместить сценарий там. Например, в проекте ASP.NET MVC скрипт следует разместить на странице View\Shared\_Layout.cshtml.

Сценарий содержит ключ инструментирования, который направляет данные телеметрии к ресурсу Application Insights.

### <a name="add-the-code-to-your-site-pages"></a>Добавление кода на страницы сайта
#### <a name="on-the-master-page"></a>На главной странице
Отредактировав главную страницу сайта, можно обеспечить мониторинг каждой страницы сайта.

Ознакомьтесь с главной страницей и измените ее с помощью SharePoint Designer или другого редактора.

![](./media/sharepoint/03-master.png)

Добавьте код прямо перед тегом </head> . 

![](./media/sharepoint/04-code.png)

#### <a name="or-on-individual-pages"></a>На отдельных страницах
Для мониторинга ограниченного набора страниц добавьте сценарий отдельно для каждой страницы. 

Вставьте веб-часть и внедрите в нее фрагмент кода.

![](./media/sharepoint/05-page.png)

## <a name="view-data-about-your-app"></a>Просмотр данных о приложении
Разверните приложение заново.

Вернитесь к колонке приложения на [портале Azure](https://portal.azure.com).

В окне поиска отобразятся первые события. 

![](./media/sharepoint/09-search.png)

Нажмите кнопку «Обновить» через несколько секунд, если ожидаете дополнительные данные.

## <a name="capturing-user-id"></a>Запись идентификатора пользователя
Фрагмент кода стандартной веб-страницы не записывает идентификатор пользователя из SharePoint, однако эту возможность можно реализовать, внеся небольшое изменение.

1. Скопируйте ключ инструментирования приложения из раскрывающегося списка "Основные компоненты" в Application Insights. 

    ![](./media/sharepoint/02-props.png)

1. Вставьте ключ инструментирования вместо строки "XXXX" в приведенном ниже фрагменте кода. 
2. Внедрите скрипт в приложение SharePoint вместо фрагмента кода, полученного с портала.

```


<SharePoint:ScriptLink ID="ScriptLink1" name="SP.js" runat="server" localizable="false" loadafterui="true" /> 
<SharePoint:ScriptLink ID="ScriptLink2" name="SP.UserProfiles.js" runat="server" localizable="false" loadafterui="true" /> 

<script type="text/javascript"> 
var personProperties; 

// Ensure that the SP.UserProfiles.js file is loaded before the custom code runs. 
SP.SOD.executeOrDelayUntilScriptLoaded(getUserProperties, 'SP.UserProfiles.js'); 

function getUserProperties() { 
    // Get the current client context and PeopleManager instance. 
    var clientContext = new SP.ClientContext.get_current(); 
    var peopleManager = new SP.UserProfiles.PeopleManager(clientContext); 

    // Get user properties for the target user. 
    // To get the PersonProperties object for the current user, use the 
    // getMyProperties method. 

    personProperties = peopleManager.getMyProperties(); 

    // Load the PersonProperties object and send the request. 
    clientContext.load(personProperties); 
    clientContext.executeQueryAsync(onRequestSuccess, onRequestFail); 
} 

// This function runs if the executeQueryAsync call succeeds. 
function onRequestSuccess() { 
var appInsights=window.appInsights||function(config){
function s(config){t[config]=function(){var i=arguments;t.queue.push(function(){t[config].apply(t,i)})}}var t={config:config},r=document,f=window,e="script",o=r.createElement(e),i,u;for(o.src=config.url||"//az416426.vo.msecnd.net/scripts/a/ai.0.js",r.getElementsByTagName(e)[0].parentNode.appendChild(o),t.cookie=r.cookie,t.queue=[],i=["Event","Exception","Metric","PageView","Trace"];i.length;)s("track"+i.pop());return config.disableExceptionTracking||(i="onerror",s("_"+i),u=f[i],f[i]=function(config,r,f,e,o){var s=u&&u(config,r,f,e,o);return s!==!0&&t["_"+i](config,r,f,e,o),s}),t
    }({
        instrumentationKey:"XXXX"
    });
    window.appInsights=appInsights;
    appInsights.trackPageView(document.title,window.location.href, {User: personProperties.get_displayName()});
} 

// This function runs if the executeQueryAsync call fails. 
function onRequestFail(sender, args) { 
} 
</script> 


```



## <a name="next-steps"></a>Дальнейшие шаги
* [Использование веб-тестов](../../azure-monitor/app/monitor-web-app-availability.md) для мониторинга доступности сайта.
* [Использование Application Insights](../../azure-monitor/app/app-insights-overview.md) для других типов приложений.

<!--Link references-->


