---
title: Анализ использования с помощью Azure Application Insights | Документация Майкрософт
description: Получение сведений о пользователях и их действиях с веб-приложением.
services: application-insights
documentationcenter: ''
author: NumberByColors
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 10/10/2017
ms.pm_owner: daviste;NumberByColors
ms.reviewer: mbullwin
ms.author: daviste
ms.openlocfilehash: ba29688958ee11aa9906a820f7a3d2bf41223743
ms.sourcegitcommit: 66237bcd9b08359a6cce8d671f846b0c93ee6a82
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/11/2019
ms.locfileid: "67798169"
---
# <a name="usage-analysis-with-application-insights"></a>Анализ использования с помощью Application Insights

Какие функции веб-приложения или мобильного приложения наиболее популярны? Достигают ли пользователи своих целей с помощью вашего приложения? Уходят ли они в определенные моменты и возвращаются ли после этого?  [Azure Application Insights](../../azure-monitor/app/app-insights-overview.md) помогает получить важные сведения о том, как люди используют ваше приложение. Каждый раз, обновив приложение, можно оценить, насколько хорошо оно подходит пользователям. Обладая такими сведениями, можно принять решения на основе данных по дальнейшим циклам разработки.

## <a name="send-telemetry-from-your-app"></a>Отправка телеметрии из приложения

Максимальное удобство работы достигается путем установки Application Insights как в серверном коде приложения, так и на веб-страницах. Клиентские и серверные компоненты приложения отправляют данные телеметрии на портал Azure для анализа.

1. **Серверный код.** Установите соответствующий модуль для своего приложения [ASP.NET](../../azure-monitor/app/asp-net.md), [Azure](../../azure-monitor/app/app-insights-overview.md), [Java](../../azure-monitor/app/java-get-started.md), [Node.js](../../azure-monitor/app/nodejs.md) или приложения [иного типа](../../azure-monitor/app/platforms.md).

    * *Не хотите устанавливать серверный код? Просто [создайте ресурс Azure Application Insights](../../azure-monitor/app/create-new-resource.md ).*

2. **Код веб-страницы.** Добавьте следующий скрипт на веб-страницу перед закрывающим тегом ``</head>``. Замените ключ инструментирования на подходящее значение для ресурса Application Insights.

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
    Чтобы узнать о более сложных настройках для мониторинга веб-сайтов, ознакомьтесь с руководством [по работе с API пакета SDK для JavaScript](https://github.com/Microsoft/ApplicationInsights-JS/blob/master/API-reference.md).

3. **Код мобильного приложения.** Используйте пакет SDK для Центра приложений, чтобы собирать события из приложения, а затем отправлять копии этих событий в Application Insights для анализа, следуя инструкциям в [этом руководстве](../../azure-monitor/learn/mobile-center-quickstart.md).

4. **Получение данных телеметрии.** Запустите проект в режиме отладки на несколько минут, а затем просмотрите результаты в колонке "Обзор" в Application Insights.

    Опубликуйте ваше приложение для отслеживания его производительности и узнайте, что делают с ним пользователи.

## <a name="include-user-and-session-id-in-your-telemetry"></a>Добавление идентификатора пользователя и сеанса к телеметрии
Для долговременного отслеживания пользователей Application Insights требуется способ их идентификации. Инструмент "События" — это единственный инструмент использования, не требующий идентификатор пользователя или сеанса.

Начните отправку идентификаторов пользователей и сеансов с помощью [этого процесса](https://docs.microsoft.com/azure/application-insights/app-insights-usage-send-user-context).

## <a name="explore-usage-demographics-and-statistics"></a>Изучение демографических данных об использовании и статистики
Узнайте, когда люди используют ваше приложение, какие страницы им наиболее интересны, где находятся ваши пользователи, какие браузеры и операционные системы они используют. 

Отчеты о пользователях и сеансах позволяют фильтровать данные по страницам или пользовательским событиям, а также разделять их по свойствам, таким как расположение, среда и страница. Можно также добавить собственные фильтры.

![Пользователи](./media/usage-overview/users.png)  

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

Для этого следует [настроить инициализатор телеметрии](../../azure-monitor/app/api-filtering-sampling.md#add-properties-itelemetryinitializer).

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

**Приложения ASP.NET Core**

> [!NOTE]
> Добавление инициализатор `ApplicationInsights.config` или с помощью `TelemetryConfiguration.Active` не является действительным для приложений ASP.NET Core. 

Для [ASP.NET Core](asp-net-core.md#adding-telemetryinitializers) приложений, добавив новый `TelemetryInitializer` можно сделать, добавив его в контейнер внедрения зависимостей, как показано ниже. Это можно сделать в `ConfigureServices` метод вашей `Startup.cs` класса.

```csharp
 using Microsoft.ApplicationInsights.Extensibility;
 using CustomInitializer.Telemetry;
 public void ConfigureServices(IServiceCollection services)
{
    services.AddSingleton<ITelemetryInitializer, MyTelemetryInitializer>();
}
```

Все новые клиенты телеметрии автоматически добавляют указанное значение свойства. Отдельные события телеметрии могут переопределять значения по умолчанию.

## <a name="next-steps"></a>Следующие шаги
   - [Пользователи, сеансы, события](usage-segmentation.md)
   - [Воронки](usage-funnels.md)
   - [Сохранение](usage-retention.md)
   - [Средство "Маршруты пользователей"](usage-flows.md)
   - [Книги](../../azure-monitor/app/usage-workbooks.md)
   - [Добавление контекста пользователей](usage-send-user-context.md)
