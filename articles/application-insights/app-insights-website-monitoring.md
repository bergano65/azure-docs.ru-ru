---
title: Краткое руководство по мониторингу веб-сайтов с помощью Azure Monitor Application Insights | Документация Майкрософт
description: Инструкции по быстрой настройке мониторинга веб-сайта на стороне клиента или браузера с помощью Azure Monitor Application Insights
services: application-insights
keywords: ''
author: mrbullwinkle
ms.author: mbullwin
ms.date: 10/15/2018
ms.service: application-insights
ms.custom: mvc
ms.topic: quickstart
manager: carmonm
ms.openlocfilehash: f4de10c29364ab96f54e5d07e9c416a2beb038f6
ms.sourcegitcommit: 3a7c1688d1f64ff7f1e68ec4bb799ba8a29a04a8
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/17/2018
ms.locfileid: "49380214"
---
# <a name="start-monitoring-your-website"></a>Запуск мониторинга веб-сайта

С помощью Azure Monitor Application Insights вы можете легко наблюдать за доступностью, производительностью и использованием вашего веб-сайта. Вы также можете быстро идентифицировать и диагностировать ошибки в приложении, не дожидаясь, пока пользователь сообщит о них. Application Insights обеспечивает мониторинг на стороне сервера, а также на стороне клиента или браузера.

В этом кратком руководстве вы добавите [пакет SDK JavaScript с открытым исходным кодом для Application Insights](https://github.com/Microsoft/ApplicationInsights-JS), который поможет вам понять, как работает ваш веб-сайт на стороне клиента или браузера.

## <a name="prerequisites"></a>Предварительные требования

Для работы с этим кратким руководством сделайте следующее:

- Вам понадобится подписка Azure.

Если у вас еще нет подписки Azure, создайте [бесплатную](https://azure.microsoft.com/free/) учетную запись Azure, прежде чем начинать работу.

## <a name="sign-in-to-the-azure-portal"></a>Вход на портал Azure

Войдите на [портале Azure](https://portal.azure.com/).

## <a name="enable-application-insights"></a>Включение Application Insights

Application Insights может собирать данные телеметрии из любого подключенного к Интернету приложения, которое работает в локальной среде или в облаке. Чтобы просмотреть эти данные, сделайте следующее.

1. Выберите **Создать ресурс** > **Средства управления** > **Application Insights**.

   Откроется окно настроек, в котором нужно заполнить все поля в соответствии с приведенной ниже таблицей.

    | Параметры        | Значение           | ОПИСАНИЕ  |
   | ------------- |:-------------|:-----|
   | **Имя**      | Глобально уникальное значение | Имя, идентифицирующее отслеживаемое приложение |
   | **Тип приложения** | Общее приложение | Тип отслеживаемого приложения |
   | **Группа ресурсов**     | myResourceGroup      | Имя новой группы ресурсов для размещения данных App Insights |
   | **Местоположение.** | Восточная часть США | Выберите ближайшее расположение или расположение вблизи места размещения приложения |

2. Нажмите кнопку **Создать**.

## <a name="create-an-html-file"></a>Создание файла HTML

1. На локальном компьютере создайте файл с именем ``hello_world.html``. В этом примере файл будет помещен в корне диска C: ``C:\hello_world.html``.
2. Скопируйте приведенный ниже сценарий в ``hello_world.html``:

    ```html
    <!DOCTYPE html>
    <html>
    <head>
    <title>Azure Monitor Application Insights</title>
    </head>
    <body>
    <h1>Azure Monitor Application Insights Hello World!</h1>
    <p>You can use the Application Insights JavaScript SDK to perform client/browser-side monitoring of your website. To learn about more advanced JavaScript SDK configurations visit the <a href="https://github.com/Microsoft/ApplicationInsights-JS/blob/master/API-reference.md" title="API Reference">API reference</a>.</p>
    </body>
    </html>
    ```

## <a name="configure-app-insights-sdk"></a>Настройка пакета SDK App Insights

1. Выберите **Обзор** > **Основные компоненты** и скопируйте **ключ инструментирования** приложения.

   ![Форма создания ресурса App Insights](./media/app-insights-nodejs-quick-start/instrumentation-key-001.png)

2. Добавьте следующий скрипт в ``hello_world.html`` перед закрывающим тегом ``</head>``:

   ```javascript
      <script type="text/javascript">
        var appInsights=window.appInsights||function(a){
            function b(a){c[a]=function(){var b=arguments;c.queue.push(function(){c[a].apply(c,b)})}}var c={config:a},d=document,e=window;setTimeout(function(){var b=d.createElement("script");b.src=a.url||"https://az416426.vo.msecnd.net/scripts/a/ai.0.js",d.getElementsByTagName("script")[0].parentNode.appendChild(b)});try{c.cookie=d.cookie}catch(a){}c.queue=[];for(var f=["Event","Exception","Metric","PageView","Trace","Dependency"];f.length;)b("track"+f.pop());if(b("setAuthenticatedUserContext"),b("clearAuthenticatedUserContext"),b("startTrackEvent"),b("stopTrackEvent"),b("startTrackPage"),b("stopTrackPage"),b("flush"),!a.disableExceptionTracking){f="onerror",b("_"+f);var g=e[f];e[f]=function(a,b,d,e,h){var i=g&&g(a,b,d,e,h);return!0!==i&&c["_"+f](a,b,d,e,h),i}}return c
        }({
            instrumentationKey: "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxx"
        });
        
        window.appInsights=appInsights,appInsights.queue&&0===appInsights.queue.length&&appInsights.trackPageView();
</script>
   ```

3. Измените ``hello_world.html`` и добавьте ключ инструментирования.

4. Откройте ``hello_world.html`` в сеансе локального браузера. В результате будет создано одностраничное представление. Вы можете обновить свой браузер, чтобы генерировать несколько просмотров тестовых страниц.

## <a name="start-monitoring-in-the-azure-portal"></a>Запуск мониторинга на портале Azure

1. Теперь можно повторно открыть страницу **Обзор** Application Insights на портале Azure, где вы извлекли ключ иснтрументирования, для просмотра сведений о выполняющемся в данный момент приложении. Четыре диаграммы по умолчанию на обзорной странице привязаны к серверным данным приложения. Так как мы рассматриваем взаимодействие на стороне клиента или браузера с использованием пакета SDK для JavaScript, это конкретное представление не применяется, если у нас также не установлен пакет SDK на стороне сервера.

2. Щелкните значок ![Схема приложений](./media/app-insights-nodejs-quick-start/006.png) **Analytics**.  Откроется окно **Analytics**, которое предоставляет полнофункциональный язык запросов для анализа всех данных, собранных Application Insights. Для просмотра данных, связанных с запросами браузера на стороне клиента, выполните следующий запрос:

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

   ![Граф аналитики запросов пользователей за период времени](./media/app-insights-website-monitoring/analytics-query.png)

3. Вернитесь на страницу **Обзор**. Щелкните **Браузер** в заголовке **Исследовать**, а затем выберите **Производительность**. Здесь можно найти метрики, связанные с производительностью веб-сайта. Существует также соответствующее представление для анализа сбоев и исключений на вашем веб-сайте. Вы можете щелкнуть **Примеры**, чтобы развернуть отдельные данные транзакций. На этой странице можно получить доступ к [полным сведениям о транзакциях](app-insights-transaction-diagnostics.md).

   ![Граф метрик сервера](./media/app-insights-website-monitoring/browser-performance.png)

4. Чтобы приступить к изучению возможностей [средств аналитики поведения пользователя](app-insights-usage-overview.md), в главном меню Application Insights выберите [**Пользователи**](app-insights-usage-segmentation.md) в заголовке **Использование**. Так как мы выполняем тестирование на одном компьютере, мы увидим данные только для одного пользователя. Для фактического веб-сайта распределение пользователей может выглядеть следующим образом:

     ![График входа пользователей](./media/app-insights-website-monitoring/usage-users.png)

5. Если бы мы рассматривали более сложный веб-сайт с несколькими страницами, можно было бы использовать другой полезный инструмент — [**Маршруты пользователей**](app-insights-usage-flows.md). Средство **Маршруты пользователей** позволяет отслеживать, как посетители проходят через различные части вашего веб-сайта.

  ![Визуализация маршрутов пользователей](./media/app-insights-website-monitoring/user-flows.png)

Чтобы узнать о более сложных настройках для мониторинга веб-сайтов, ознакомьтесь с руководством [по работе с API пакета SDK для JavaScript](https://github.com/Microsoft/ApplicationInsights-JS/blob/master/API-reference.md).

## <a name="clean-up-resources"></a>Очистка ресурсов

Если вы планируете продолжать работу с этими руководствами по быстрому запуску или обычными руководствами, не удаляйте созданные ресурсы. В противном случае удалите все созданные ресурсы, выполнив на портале Azure следующие действия.

1. В меню слева на портале Azure щелкните **Группы ресурсов**, а затем выберите **myResourceGroup**.
2. На странице группы ресурсов щелкните **Удалить**, в текстовом поле введите **myResourceGroup** и щелкните **Удалить**.

## <a name="next-steps"></a>Дополнительная информация

> [!div class="nextstepaction"]
> [Аналитика в Application Insights](https://docs.microsoft.com/azure/application-insights/app-insights-analytics)
