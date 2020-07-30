---
title: Краткое руководство. Мониторинг веб-сайтов с помощью Azure Monitor Application Insights
description: В этом кратком руководстве показано, как настроить мониторинг веб-сайта на стороне клиента или браузера с помощью Application Insights в Azure Monitor.
ms.topic: quickstart
ms.date: 07/15/2019
ms.custom: mvc
ms.openlocfilehash: eb6c0a8319257949cee8f35be6cdfac22f1fe5d4
ms.sourcegitcommit: a76ff927bd57d2fcc122fa36f7cb21eb22154cfa
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/28/2020
ms.locfileid: "87323440"
---
# <a name="quickstart-start-monitoring-your-website-with-azure-monitor-application-insights"></a>Краткое руководство. Запуск мониторинга веб-сайта с помощью Application Insights в Azure Monitor

В этом кратком руководстве содержатся сведения о добавлении пакета SDK с открытым кодом для JavaScript Application Insights на веб-сайт. Вы также получите сведения, которые помогут улучшить понимание взаимодействия на стороне клиента или браузера для посетителей веб-сайта.

С помощью Azure Monitor Application Insights вы можете легко наблюдать за доступностью, производительностью и использованием вашего веб-сайта. Вы также можете быстро идентифицировать и диагностировать в нем ошибки, не дожидаясь, пока пользователь сообщит о них. Application Insights обеспечивает мониторинг на стороне сервера, а также на стороне клиента или браузера.

## <a name="prerequisites"></a>Предварительные требования

* Учетная запись Azure с активной подпиской. [Создайте учетную запись](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) бесплатно.
* Веб-сайт, к которому можно добавить пакет SDK для Application Insights JavaScript.

## <a name="enable-application-insights"></a>Включение Application Insights

Application Insights может собирать данные телеметрии из любого подключенного к Интернету приложения, которое работает в локальной среде или облаке. Чтобы просмотреть эти данные, выполните следующие действия.

1. Войдите на [портал Azure](https://portal.azure.com/).
1. Выберите **Создать ресурс** > **Средства управления** > **Application Insights**.

   > [!NOTE]
   >См. сведения о [создании ресурса Application Insights](./create-new-resource.md).
1. Когда откроется окно конфигурации, заполните поля, как показано в таблице ниже.

    | Настройки        | Значение           | Описание  |
   | ------------- |:-------------|:-----|
   | **имя**;      | Глобально уникальное значение | Имя, идентифицирующее отслеживаемое приложение. |
   | **Группа ресурсов**     | myResourceGroup      | Имя новой группы ресурсов для размещения данных Application Insights. Создайте новую группу ресурсов или выберите существующую. |
   | **Расположение** | Восточная часть США | Выберите ближайшее к вам расположение или расположение рядом с местом размещения приложения. |
1. Нажмите кнопку **создания**.

## <a name="create-an-html-file"></a>Создание файла HTML

1. На локальном компьютере создайте файл с именем ``hello_world.html``. Для этого примера создайте файл в корне диска C, чтобы он выглядел так: ``C:\hello_world.html``.
1. Скопируйте и вставьте следующий скрипт в ``hello_world.html``.

    ```html
    <!DOCTYPE html>
    <html>
    <head>
    <title>Azure Monitor Application Insights</title>
    </head>
    <body>
    <h1>Azure Monitor Application Insights Hello World!</h1>
    <p>You can use the Application Insights JavaScript SDK to perform client/browser-side monitoring of your website. To learn about more advanced JavaScript SDK configurations, visit the <a href="https://github.com/Microsoft/ApplicationInsights-JS/blob/master/API-reference.md" title="API Reference">API reference</a>.</p>
    </body>
    </html>
    ```

## <a name="configure-application-insights-sdk"></a>Настройка пакета SDK для Application Insights

1. Щелкните **Обзор** > **Основные компоненты** и скопируйте **ключ инструментирования** приложения.

   ![Форма создания ресурса Application Insights](media/website-monitoring/instrumentation-key-001.png)

1. Добавьте следующий скрипт в файл ``hello_world.html`` перед закрывающим тегом ``</head>``.

   ```javascript
   <script type="text/javascript">
      var sdkInstance="appInsightsSDK";window[sdkInstance]="appInsights";var aiName=window[sdkInstance],aisdk=window[aiName]||function(e){function n(e){t[e]=function(){var n=arguments;t.queue.push(function(){t[e].apply(t,n)})}}var t={config:e};t.initialize=!0;var i=document,a=window;setTimeout(function(){var n=i.createElement("script");n.src=e.url||"https://az416426.vo.msecnd.net/scripts/b/ai.2.min.js",i.getElementsByTagName("script")[0].parentNode.appendChild(n)});try{t.cookie=i.cookie}catch(e){}t.queue=[],t.version=2;for(var r=["Event","PageView","Exception","Trace","DependencyData","Metric","PageViewPerformance"];r.length;)n("track"+r.pop());n("startTrackPage"),n("stopTrackPage");var s="Track"+r[0];if(n("start"+s),n("stop"+s),n("setAuthenticatedUserContext"),n("clearAuthenticatedUserContext"),n("flush"),!(!0===e.disableExceptionTracking||e.extensionConfig&&e.extensionConfig.ApplicationInsightsAnalytics&&!0===e.extensionConfig.ApplicationInsightsAnalytics.disableExceptionTracking)){n("_"+(r="onerror"));var o=a[r];a[r]=function(e,n,i,a,s){var c=o&&o(e,n,i,a,s);return!0!==c&&t["_"+r]({message:e,url:n,lineNumber:i,columnNumber:a,error:s}),c},e.autoExceptionInstrumented=!0}return t}(
      {
         instrumentationKey:"INSTRUMENTATION_KEY"
      }
      );window[aiName]=aisdk,aisdk.queue&&0===aisdk.queue.length&&aisdk.trackPageView({});
   </script>
   ```

1. Измените ``hello_world.html`` и добавьте ключ инструментирования.

1. Откройте ``hello_world.html`` в сеансе локального браузера. Это действие создает один просмотр страницы. Вы можете обновить свой браузер, чтобы сгенерировать несколько просмотров тестовых страниц.

## <a name="monitor-your-website-in-the-azure-portal"></a>Мониторинг веб-сайта на портале Azure

1. Откройте страницу **Обзор** Application Insights на портале Azure для просмотра сведений о выполняющемся приложении. Страница **Обзор** является местом получения ключа инструментирования.

   Четыре диаграммы по умолчанию на обзорной странице привязаны к серверным данным приложения. Так как мы рассматриваем взаимодействие на стороне клиента или браузера с использованием пакета SDK для JavaScript, это конкретное представление не применяется, если у нас также не установлен пакет SDK на стороне сервера.

1. Щелкните значок **Схема приложений** ![Analytics](media/website-monitoring/006.png).  Это действие приводит к открытию окна **Analytics**, которое предоставляет полнофункциональный язык запросов для анализа всех данных, собранных Application Insights. Для просмотра данных, связанных с запросами браузера на стороне клиента, выполните следующий запрос.

    ```kusto
    // average pageView duration by name
    let timeGrain=1s;
    let dataset=pageViews
    // additional filters can be applied here
    | where timestamp > ago(15m)
    | where client_Type == "Browser" ;
    // calculate average pageView duration for all pageViews
    dataset
    | summarize avg(duration) by bin(timestamp, timeGrain)
    | extend pageView='Overall'
    // render result in a chart
    | render timechart
    ```

   ![Граф аналитики запросов пользователей за период времени](./media/website-monitoring/analytics-query.png)

1. Вернитесь на страницу **Обзор**. Под заголовком **Изучение** щелкните **Браузер** и **Производительность**.  Отобразятся метрики, отображающие сведения о производительности вашего веб-сайта. Доступно соответствующее представление для анализа сбоев и исключений на веб-сайте. Щелкните **Примеры**, чтобы получить доступ к [полным сведениям о транзакциях](./transaction-diagnostics.md).

   ![Граф метрик сервера](./media/website-monitoring/browser-performance.png)

1. Чтобы изучить возможности [средств аналитики поведения пользователей](./usage-overview.md), в главном меню Application Insights под заголовком **Использование** щелкните [**Пользователи**](./usage-segmentation.md). Так как мы выполняем тестирование на одном компьютере, мы увидим данные только для одного пользователя. Для фактического веб-сайта распределение пользователей может выглядеть следующим образом.

     ![График входа пользователей](./media/website-monitoring/usage-users.png)

1. Для более сложного веб-сайта с несколькими страницами можно использовать средство [**Маршруты пользователей**](./usage-flows.md) для визуализации перемещения посетителей по веб-сайту.

   ![Визуализация маршрутов пользователей](./media/website-monitoring/user-flows.png)

Сведения о более сложных настройках для мониторинга веб-сайтов см. в справочнике по [API пакета SDK для JavaScript](https://github.com/Microsoft/ApplicationInsights-JS/blob/master/API-reference.md).

## <a name="clean-up-resources"></a>Очистка ресурсов

Если вы планируете продолжать работу с другими руководствами, не удаляйте созданные ресурсы. В противном случае выполните на портале Azure следующие действия, чтобы удалить все созданные ресурсы.

> [!NOTE]
> Если вы использовали существующую группу ресурсов, приведенные ниже инструкции неприменимы. Вместо этого можете просто удалить отдельный ресурс Application Insights. Помните, что при удалении группы ресурсов все входящие в нее базовые ресурсы также будут удалены.

1. В меню слева на портале Azure щелкните **Группы ресурсов** и выберите **myResourceGroup** или имя временной группы ресурсов.
1. На странице группы ресурсов щелкните **Удалить**, в текстовом поле введите **myResourceGroup** и выберите **Удалить**.

## <a name="next-steps"></a>Дальнейшие действия

> [!div class="nextstepaction"]
> [Аналитика в Application Insights](../log-query/log-query-overview.md)

