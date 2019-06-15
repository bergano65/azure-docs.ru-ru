---
title: Мониторинг приложений Docker в Azure Application Insights | Документация Майкрософт
description: Счетчики производительности, события и исключения Docker могут отображаться в Application Insights вместе с данными телеметрии из контейнерных приложений.
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.assetid: 27a3083d-d67f-4a07-8f3c-4edb65a0a685
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 03/14/2019
ms.author: mbullwin
ms.openlocfilehash: 115e2d6b041ecc3f38a2a6438d90777da9660221
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/13/2019
ms.locfileid: "62098037"
---
# <a name="monitor-docker-applications-in-application-insights-deprecated"></a>Мониторинг приложений Docker в Application Insights (не рекомендуется)

> [!NOTE]
> Эта статья признана устаревшей. Дополнительные сведения о наших текущих инвестициях в мониторинг контейнеров см. в статье [Общие сведения об Azure Monitor для контейнеров (предварительная версия)](https://docs.microsoft.com/azure/azure-monitor/insights/container-insights-overview).

События жизненного цикла и счетчики производительности из контейнеров [Docker](https://www.docker.com/) можно выводить в виде диаграмм в Application Insights. Установите образ [Application Insights](https://hub.docker.com/r/microsoft/applicationinsights/) в контейнер в узле. Он будет отображать счетчики производительности для этого узла, а также для других образов.

С помощью Docker ваши приложения распространяются в упрощенных контейнерах со всеми зависимостями. Их можно запустить на любом хост-компьютере с модулем Docker.

При запуске [образа Application Insights](https://hub.docker.com/r/microsoft/applicationinsights/) в узле Docker вы получите следующие преимущества.

* Сведения о телеметрии жизненного цикла для всех контейнеров, запущенных на узле, — запуск, остановка и т. д.
* Счетчики производительности для всех контейнеров. ЦП, память, использование сети и многое другое.
* Если вы [установили пакет SDK для Application Insights для Java](../../azure-monitor/app/java-get-started.md) в приложениях, выполняющихся в контейнерах, у всех данных телеметрии этих приложений будут дополнительные свойства, идентифицирующие контейнер и хост-компьютер. Например, если имеются экземпляры приложения, запущенные на нескольких узлах, вы легко сможете отфильтровать данные телеметрии приложения по узлу.

## <a name="set-up-your-application-insights-resource"></a>Настройка ресурса Application Insights

1. Выполните вход на [портал Microsoft Azure](https://azure.com) и [создайте ресурс Application Insights](../../azure-monitor/app/create-new-resource.md ) для своего приложения или откройте имеющийся. 
   
    *Какой ресурс использовать?* Если приложения, которые выполняются на узле, были созданы другим разработчиком, вам потребуется [создать новый ресурс Application Insights](../../azure-monitor/app/create-new-resource.md ). Там вы можете просматривать и анализировать данные телеметрии. (Выберите тип приложения "Общее".)
   
    Но если разработчиком приложений являетесь вы, мы надеемся, что вы [добавили пакет SDK для Application Insights](../../azure-monitor/app/java-get-started.md) в каждое из них. Если все они действительно являются компонентами одного бизнес-приложения, вы можете настроить их на отправку данных телеметрии в один ресурс, а затем использовать этот ресурс для отображения данных о производительности и жизненном цикле Docker. 
   
    Третий сценарий — вы разработали большинство приложений, но используете отдельные ресурсы для отображения их телеметрии. В этом случае, возможно, вам потребуется создать отдельный ресурс для данных Docker.

2. Щелкните раскрывающийся список **Основные компоненты** и скопируйте ключ инструментирования. Этот ключ указывает пакету SDK, куда отправлять данные телеметрии.

Не закрывайте это окно браузера: скоро вы будете просматривать в нем данные телеметрии.

## <a name="run-the-application-insights-monitor-on-your-host"></a>Запуск монитора Application Insights на узле

Теперь, когда вы создали ресурс для отображения данных телеметрии, можно настроить контейнерное приложение, которое будет собирать и отправлять их.

1. Подключитесь к узлу Docker.
2. Включите свой ключ инструментирования в следующую команду и выполните ее:
   
   ```
   
   docker run -v /var/run/docker.sock:/docker.sock -d microsoft/applicationinsights ikey=000000-1111-2222-3333-444444444
   ```

Для каждого узла Docker требуется только один образ Application Insights. Если приложение развертывается на нескольких узлах Docker, выполните эту команду на каждом узле.

## <a name="update-your-app"></a>Обновление приложения
Если приложение инструментируется с помощью [пакета SDK для Application Insights для Java](../../azure-monitor/app/java-get-started.md), добавьте следующую строку в файл ApplicationInsights.xml в проекте после элемента `<TelemetryInitializers>`:

```xml

    <Add type="com.microsoft.applicationinsights.extensibility.initializer.docker.DockerContextInitializer"/> 
```

Это добавляет сведения телеметрии Docker, такие как контейнер и идентификатор узла, в каждый элемент, отправляемый из приложения.

## <a name="view-your-telemetry"></a>Просмотр телеметрии
Вернитесь к ресурсу Application Insights на портале Azure.

Щелкните плитку Docker.

Вскоре вы увидите данные, поступающие из приложения Docker, особенно при наличии других контейнеров, работающих в модуле Docker.

### <a name="docker-container-events"></a>События контейнера Docker
![пример](./media/docker/13.png)

Для анализа отдельных событий щелкните [Поиск](../../azure-monitor/app/diagnostic-search.md). Найдите и отфильтруйте нужные вам события. Щелкните любое событие, чтобы просмотреть подробные данные.

### <a name="exceptions-by-container-name"></a>Исключения по имени контейнера
![пример](./media/docker/14.png)

### <a name="docker-context-added-to-app-telemetry"></a>Добавление контекста Docker в телеметрию приложения
Запрос данных телеметрии, отправленный из приложения, инструментированного с помощью пакета SDK AI, с дополнительной контекстной информацией Docker:

## <a name="q--a"></a>Вопросы и ответы
*Какие возможности Application Insights отсутствуют в Docker?*

* Подробные показатели счетчиков производительности по контейнерам и образам.
* Интегрированные данные контейнера и приложения на одной панели мониторинга.
* [Экспорт данных телеметрии](export-telemetry.md) в базу данных, Power BI или другую панель мониторинга для дальнейшего анализа.

*Как получить данные телеметрии из самого приложения?*

* Установите пакет SDK Application Insights в приложении. Узнайте, как это сделать в [веб-приложениях Java](../../azure-monitor/app/java-get-started.md) и [веб-приложениях Windows](../../azure-monitor/app/asp-net.md).

## <a name="video"></a>Видео

> [!VIDEO https://channel9.msdn.com/events/Connect/2016/100/player]

## <a name="next-steps"></a>Дальнейшие действия

* [Application Insights для Java](../../azure-monitor/app/java-get-started.md)
* [Application Insights для Node.js](../../azure-monitor/app/nodejs.md)
* [Application Insights для ASP.NET](../../azure-monitor/app/asp-net.md)
