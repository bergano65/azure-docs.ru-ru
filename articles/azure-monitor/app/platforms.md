---
title: 'Application Insights: языки программирования, платформы, интеграция | Документация Майкрософт'
description: Языки программирования, платформы и варианты интеграции для Application Insights
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.assetid: 974db106-54ff-4318-9f8b-f7b3a869e536
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 09/01/2016
ms.reviewer: olegan
ms.author: mbullwin
ms.openlocfilehash: 825f51742a65ead545f7c7fcb0a53d1a82a08b1a
ms.sourcegitcommit: e69fc381852ce8615ee318b5f77ae7c6123a744c
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/11/2019
ms.locfileid: "55992812"
---
# <a name="developer-analytics-languages-platforms-and-integrations"></a>Аналитические средства для разработчиков: языки программирования, платформы, интеграция
Ниже перечислены известные нам реализации [Application Insights](../../azure-monitor/app/app-insights-overview.md) , в том числе реализации от сторонних производителей.

## <a name="languages---officially-supported-by-application-insights-team"></a>Языки программирования, официально поддерживаемые командой Application Insights
* [C#|VB (.NET)](../../azure-monitor/app/asp-net.md)
* [Java](../../azure-monitor/app/java-get-started.md)
* [Веб-страницы JavaScript](../../azure-monitor/app/javascript.md)
* [Node.JS](../../azure-monitor/app/nodejs.md)

## <a name="languages---community-supported"></a>Языки программирования, поддерживаемые сообществом
* [F#](https://safe-stack.github.io/docs/template-azure-ai/)
* [PHP](https://github.com/Microsoft/ApplicationInsights-PHP)
* [Python](https://pypi.python.org/pypi/applicationinsights/0.1.0)
* [Ruby](https://rubygems.org/gems/application_insights)
* [Другие варианты](#projects)

## <a name="platforms-and-frameworks"></a>Платформы и среды
* [ASP.NET](../../azure-monitor/app/asp-net.md)
* [ASP.NET — для приложений, которые уже доступны в Интернете](../../azure-monitor/app/monitor-performance-live-website-now.md)
* [ASP.NET Core](../../azure-monitor/app/asp-net-core.md)
* [Android](../../azure-monitor/learn/mobile-center-quickstart.md) (Центр приложений)
* [Android](https://github.com/Microsoft/ApplicationInsights-Android) (Центр приложений)
* [Angular](https://github.com/MarkPieszak/angular-application-insights)
* [службе приложений Azure](../../azure-monitor/app/azure-web-apps.md)
* [Облачные службы Azure](../../azure-monitor/app/cloudservices.md) — рабочие роли и веб-роли
* [Функции Azure](https://github.com/christopheranderson/azure-functions-app-insights-sample)
* [Docker](../../azure-monitor/app/docker.md)
* [Glimpse](https://azure.microsoft.com/blog/glimpse-application-insights/)
* [iOS](../../azure-monitor/learn/mobile-center-quickstart.md) (Центр приложений)
* [Ionic](https://github.com/SoftwarePioniere/ionic-application-insights)
* [iOS](https://github.com/Microsoft/ApplicationInsights-iOS) (Центр приложений)
* [J2EE](../../azure-monitor/app/java-get-started.md)
* [J2EE — для приложений, которые уже доступны в Интернете](../../azure-monitor/app/java-live.md)
* [Node.JS](https://www.npmjs.com/package/applicationinsights)
* [OSX](https://github.com/Microsoft/ApplicationInsights-OSX)
* [SAFE Stack](https://safe-stack.github.io/docs/template-azure-ai/)
* [Spring](https://joe.blog.freemansoft.com/2015/12/enabling-microsoft-application-insight.html)
* [Универсальное приложение для Windows](../../azure-monitor/learn/mobile-center-quickstart.md) (Центр приложений)
* [WCF](https://github.com/Microsoft/ApplicationInsights-SDK-Labs/blob/master/WCF/readme.md)
* [Классические приложения, службы и рабочие роли Windows](../../azure-monitor/app/windows-desktop.md)
* [Другие варианты](#projects)

## <a name="logging-frameworks"></a>Платформы ведения журналов
* [Log4Net, NLog или System.Diagnostics.Trace](../../azure-monitor/app/asp-net-trace-logs.md)
* [Java, Log4J или Logback](../../azure-monitor/app/java-trace-logs.md)
* [Semantic Logging (SLAB)](https://github.com/fidmor89/SLAB_AppInsights) — интеграция с [Semantic Logging Application Block](https://msdn.microsoft.com/library/dn440729.aspx)
* [Облачное нагрузочное тестирование](https://blogs.msdn.com/b/visualstudioalm/archive/2015/07/30/getting-application-insights-counters-with-cloud-based-load-testing.aspx)
* [Подключаемый модуль LogStash](https://github.com/Azure/azure-diagnostics-tools/tree/master/Logstash/logstash-output-applicationinsights)
* [Logary](https://www.nuget.org/packages/Logary.Targets.AppInsights/)
* [Logrus](https://github.com/jjcollinge/logrus-appinsights)
* [Azure Monitor](https://blogs.technet.microsoft.com/msoms/2016/09/26/application-insights-connector-in-oms/)

## <a name="content-management-systems"></a>Системы управления содержимым
* [Concrete](https://github.com/fidmor89/appInsights-Concrete)
* [Drupal](https://github.com/fidmor89/AppInsights-Drupal)
* [Joomla](https://github.com/fidmor89/AppInsights-Joomla)
* [Orchard](https://azure.microsoft.com/blog/integrating-application-insights-into-a-modular-cms-and-a-multi-tenant-public-saas/preview/)
* [SharePoint](../../azure-monitor/app/sharepoint.md)
* [WordPress](https://wordpress.org/plugins/application-insights/)

## <a name="export-and-data-analysis"></a>Экспорт и анализ данных
* [Alooma](https://www.alooma.com/blog/application-insights-amazon-redshift)
* [Power BI](https://blogs.msdn.com/b/powerbi/archive/2015/11/04/explore-your-application-insights-data-with-power-bi.aspx)
* [Анализ потока](../../azure-monitor/app/export-power-bi.md )

## <a name="projects"></a> Создание собственного пакета SDK
Если для языка программирования или платформы, которые вы используете, не существует пакета SDK, возможно, вы захотите создать его. Просмотрите код существующих пакетов SDK, перечисленных в описании [проекта пакета SDK для Application Insights на GitHub](https://github.com/Microsoft/AppInsights-Home).
