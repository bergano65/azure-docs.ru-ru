---
title: Анализ использования с помощью Azure Application Insights | Документация Майкрософт
description: Получение сведений о пользователях и их действиях с веб-приложением.
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: mrbullwinkle
ms.author: mbullwin
ms.date: 09/19/2019
ms.openlocfilehash: aa37717c5037294c2b5ec61f7815b007cbf74992
ms.sourcegitcommit: cf36df8406d94c7b7b78a3aabc8c0b163226e1bc
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/09/2019
ms.locfileid: "73884827"
---
# <a name="usage-analysis-with-application-insights"></a>Анализ использования с помощью Application Insights

Какие функции веб-приложения или мобильного приложения наиболее популярны? Достигают ли пользователи своих целей с помощью вашего приложения? Уходят ли они в определенные моменты и возвращаются ли после этого?  [Azure Application Insights](../../azure-monitor/app/app-insights-overview.md) помогает получить важные сведения о том, как люди используют ваше приложение. Каждый раз, обновив приложение, можно оценить, насколько хорошо оно подходит пользователям. Обладая такими сведениями, можно принять решения на основе данных по дальнейшим циклам разработки.

## <a name="send-telemetry-from-your-app"></a>Отправка телеметрии из приложения

Максимальное удобство работы достигается путем установки Application Insights как в серверном коде приложения, так и на веб-страницах. Клиентские и серверные компоненты приложения отправляют данные телеметрии на портал Azure для анализа.

1. **Серверный код:** установите соответствующий модуль для своего приложения [ASP.NET](../../azure-monitor/app/asp-net.md), [Azure](../../azure-monitor/app/app-insights-overview.md), [Java](../../azure-monitor/app/java-get-started.md), [Node.js](../../azure-monitor/app/nodejs.md) или приложения [иного типа](../../azure-monitor/app/platforms.md).

    * *Не хотите устанавливать серверный код? Просто [Создайте ресурс Azure Application Insights](../../azure-monitor/app/create-new-resource.md ).*

2. **Код веб-страницы:** Добавьте следующий скрипт на веб-страницу перед закрывающим ``</head>``. Замените ключ инструментирования на подходящее значение для ресурса Application Insights.
    
    ```html
    <script type="text/javascript">
    var sdkInstance="appInsightsSDK";window[sdkInstance]="appInsights";var aiName=window[sdkInstance],aisdk=window[aiName]||function(e){function n(e){t[e]=function(){var n=arguments;t.queue.push(function(){t[e].apply(t,n)})}}var t={config:e};t.initialize=!0;var i=document,a=window;setTimeout(function(){var n=i.createElement("script");n.src=e.url||"https://az416426.vo.msecnd.net/scripts/b/ai.2.min.js",i.getElementsByTagName("script")[0].parentNode.appendChild(n)});try{t.cookie=i.cookie}catch(e){}t.queue=[],t.version=2;for(var r=["Event","PageView","Exception","Trace","DependencyData","Metric","PageViewPerformance"];r.length;)n("track"+r.pop());n("startTrackPage"),n("stopTrackPage");var s="Track"+r[0];if(n("start"+s),n("stop"+s),n("setAuthenticatedUserContext"),n("clearAuthenticatedUserContext"),n("flush"),!(!0===e.disableExceptionTracking||e.extensionConfig&&e.extensionConfig.ApplicationInsightsAnalytics&&!0===e.extensionConfig.ApplicationInsightsAnalytics.disableExceptionTracking)){n("_"+(r="onerror"));var o=a[r];a[r]=function(e,n,i,a,s){var c=o&&o(e,n,i,a,s);return!0!==c&&t["_"+r]({message:e,url:n,lineNumber:i,columnNumber:a,error:s}),c},e.autoExceptionInstrumented=!0}return t}(
    {
      instrumentationKey:"INSTRUMENTATION_KEY"
    }
    );window[aiName]=aisdk,aisdk.queue&&0===aisdk.queue.length&&aisdk.trackPageView({});
    </script>
    ```

    Дополнительные сведения о дополнительных конфигурациях для мониторинга веб-сайтов см. в [справочной статье по пакету SDK для JavaScript](https://docs.microsoft.com/azure/azure-monitor/app/javascript).

3. **Код мобильного приложения.** Используйте пакет SDK для App Center, чтобы собирать события из приложения, а затем отправлять копии этих событий в Application Insights для анализа, следуя инструкциям в [этом руководстве](../../azure-monitor/learn/mobile-center-quickstart.md).

4. **Получение данных телеметрии:** запустите проект в режиме отладки на несколько минут, а затем просмотрите результаты в колонке "Обзор" в Application Insights.

    Опубликуйте ваше приложение для отслеживания его производительности и узнайте, что делают с ним пользователи.

## <a name="include-user-and-session-id-in-your-telemetry"></a>Добавление идентификатора пользователя и сеанса к телеметрии
Для долговременного отслеживания пользователей Application Insights требуется способ их идентификации. Инструмент "События" — это единственный инструмент использования, не требующий идентификатор пользователя или сеанса.

Начните отправку идентификаторов пользователей и сеансов с помощью [этого процесса](https://docs.microsoft.com/azure/application-insights/app-insights-usage-send-user-context).

## <a name="explore-usage-demographics-and-statistics"></a>Изучение демографических данных об использовании и статистики
Узнайте, когда люди используют ваше приложение, какие страницы им наиболее интересны, где находятся ваши пользователи, какие браузеры и операционные системы они используют. 

Отчеты о пользователях и сеансах позволяют фильтровать данные по страницам или пользовательским событиям, а также разделять их по свойствам, таким как расположение, среда и страница. Можно также добавить собственные фильтры.

![Users](./media/usage-overview/users.png)  

Аналитические сведения справа позволяют выделять важные закономерности в наборе данных.  

* В отчете **Пользователи** подсчитывается количество уникальных пользователей, которые обращаются к страницам в выбранные периоды времени. (Пользователи веб-приложений подсчитываются с помощью файлов cookie. Если пользователь обращается к вашему сайту, используя разные браузеры или клиентские компьютеры, или очищает файлы cookie, он будет учтен соответствующее количество раз.)
* В отчете **Сеансы** подсчитывается количество пользовательских сеансов, обращающихся к узлу. Сеанс — это период действий пользователя, завершающийся периодом бездействия более получаса.

[Дополнительные сведения об инструментах "Пользователи", "Сеансы" и "События".](usage-segmentation.md)  

## <a name="retention---how-many-users-come-back"></a>Период удержания — сколько пользователей вернулось?

Период удержания помогает понять, как часто пользователи возвращаются к использованию своего приложения. Для этого используются когорты пользователей, которые выполнили какое-либо коммерческое действие во время определенного горизонта планирования. 

- Поймите, какие именно функции заставляют пользователей возвращаться чаще, чем другие. 
- Сформируйте гипотезу на основе данных реальных пользователей. 
- Определите, является ли период удержания проблемой для вашего продукта. 

![Сохранение](./media/usage-overview/retention.png) 

Помимо прочего, элементы управления периодом удержания позволяют определить конкретные события и диапазон времени для вычисления периода удержания. График в середине дает визуальное представление об общем проценте удержания за указанный диапазон времени. График внизу отображает период удержания отдельных пользователей за заданный период времени. Такой уровень детализации позволяет лучше понять, что ваши пользователи делают и что может повлиять на возвращение пользователей.  

[Дополнительные сведения об инструменте "Хранение"](usage-retention.md).

## <a name="custom-business-events"></a>Пользовательские бизнес-события

Чтобы получить четкое представление о действиях пользователей с приложением, полезно добавить строки кода для ведения журнала пользовательских событий. Эти события могут отслеживать что угодно, начиная с детализированных действий пользователя (например, нажатие определенных кнопок) и заканчивая более серьезными бизнес-событиями (например, совершение покупки или победа в игре). 

Хотя в некоторых случаях просмотры страниц могут представлять собой полезные события, но в целом это не так. Пользователь может открыть страницу продукта, не приобретая его. 

С помощью специальных бизнес-событий можно создать диаграмму перемещения пользователей по сайту. Можно узнать их предпочтения относительно различных параметров, а также определить, где они покидают сайт или испытывают сложности. Обладая такими сведениями, можно принимать обоснованные решения о приоритетах для невыполненной работы по разработке.

События могут регистрироваться на клиентской стороне приложения:

```JavaScript

    appInsights.trackEvent("ExpandDetailTab", {DetailTab: tabName});
```

Или на стороне сервера:

```csharp
    var tc = new Microsoft.ApplicationInsights.TelemetryClient();
    tc.TrackEvent("CreatedAccount", new Dictionary<string,string> {"AccountType":account.Type}, null);
    ...
    tc.TrackEvent("AddedItemToCart", new Dictionary<string,string> {"Item":item.Name}, null);
    ...
    tc.TrackEvent("CompletedPurchase");
```

В эти события можно вложить значения свойств, чтобы события можно было фильтровать или разделять при изучении на портале. Кроме того, в каждое событие вкладывается стандартный набор свойств, например идентификатор анонимного пользователя, который позволяет выполнять трассировку последовательности действий отдельного пользователя.

Узнайте больше о [пользовательских событиях](../../azure-monitor/app/api-custom-events-metrics.md#trackevent) и [свойствах](../../azure-monitor/app/api-custom-events-metrics.md#properties).

### <a name="slice-and-dice-events"></a>Анализ событий

В инструментах "Пользователи", "Сеансы" и "События" можно анализировать пользовательские события по пользователю, имени события и свойствам.
![Пользователи](./media/usage-overview/users.png)  
  
## <a name="design-the-telemetry-with-the-app"></a>Разработка телеметрии приложения

При разработке каждого компонента приложения обдумайте, как вы будете измерять его успех у пользователей. Решите, какие бизнес-события необходимо записывать, и с самого начала запрограммируйте вызовы отслеживания для этих событий в приложении.

## <a name="a--b-testing"></a>Тестирование A или B
Если неизвестно, какой вариант функции будет более эффективен, выпустите их оба, чтобы каждый из них был доступен для разных пользователей. Измерьте успешность каждого варианта, а затем перейдите к единой версии.

В рамках этого способа необходимо вложить разные значения свойств во все данные телеметрии, отправляемые каждой версией приложения. Для этого нужно определить свойства в активном контексте телеметрии TelemetryContext. Эти свойства по умолчанию добавляются к каждому сообщению телеметрии, которое отправляет приложение, — не только к настраиваемым сообщениям, но также к стандартным данным телеметрии.

Чтобы сравнить различные версии, на портале Application Insights можно отфильтровать и разделить данные по значениям свойств.

Для этого следует [настроить инициализатор телеметрии](../../azure-monitor/app/api-filtering-sampling.md#addmodify-properties-itelemetryinitializer).

**Приложения ASP.NET**

```csharp
    // Telemetry initializer class
    public class MyTelemetryInitializer : ITelemetryInitializer
    {
        public void Initialize (ITelemetry telemetry)
        {
            telemetry.Properties["AppVersion"] = "v2.1";
        }
    }
```

В инициализаторе веб-приложения, например Global.asax.cs, добавьте следующий код.

```csharp

    protected void Application_Start()
    {
        // ...
        TelemetryConfiguration.Active.TelemetryInitializers
         .Add(new MyTelemetryInitializer());
    }
```

**ASP.NET Core приложения**

> [!NOTE]
> Добавление инициализатора с помощью `ApplicationInsights.config` или `TelemetryConfiguration.Active` недопустимо для приложений ASP.NET Core. 

Для [ASP.NET Core](asp-net-core.md#adding-telemetryinitializers) приложений добавление нового `TelemetryInitializer` выполняется путем его добавления в контейнер внедрения зависимостей, как показано ниже. Это делается в методе `ConfigureServices` класса `Startup.cs`.

```csharp
 using Microsoft.ApplicationInsights.Extensibility;
 using CustomInitializer.Telemetry;
 public void ConfigureServices(IServiceCollection services)
{
    services.AddSingleton<ITelemetryInitializer, MyTelemetryInitializer>();
}
```

Все новые клиенты телеметрии автоматически добавляют указанное значение свойства. Отдельные события телеметрии могут переопределять значения по умолчанию.

## <a name="next-steps"></a>Дополнительная информация
   - [Пользователи, сеансы, события](usage-segmentation.md)
   - [Воронки](usage-funnels.md)
   - [Удержание](usage-retention.md)
   - [Средство "Маршруты пользователей"](usage-flows.md)
   - [Книги](../../azure-monitor/app/usage-workbooks.md)
   - [Добавление контекста пользователей](usage-send-user-context.md)
