---
title: Профилирование облачных служб реального времени Azure с помощью Application Insights | Документация Майкрософт
description: Включение Azure Application Insights Profiler для облачных служб.
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.reviewer: cawa
ms.date: 08/06/2018
ms.author: mbullwin
ms.openlocfilehash: 5bb70bf56efac28029401b69ee4f87c2738c52e3
ms.sourcegitcommit: 333d4246f62b858e376dcdcda789ecbc0c93cd92
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/01/2018
ms.locfileid: "52721856"
---
# <a name="profile-live-azure-cloud-services-with-application-insights"></a>Профилирование облачных служб реального времени Azure с помощью Application Insights

Вы можете развернуть Application Insights Profiler для таких служб:
* [Веб-приложения Azure](app-insights-profiler.md?toc=/azure/azure-monitor/toc.json)
* [Приложения Service Fabric](app-insights-profiler-servicefabric.md?toc=/azure/azure-monitor/toc.json)
* [Виртуальные машины](app-insights-profiler-vm.md?toc=/azure/azure-monitor/toc.json)

Application Insights Profiler поставляется в комплекте с расширением системы диагностики Microsoft Azure (WAD). Вам достаточно настроить WAD, чтобы установить профилировщик и отправлять профили в ресурс Application Insights.

## <a name="enable-profiler-for-your-azure-cloud-service"></a>Включение профилировщика для облачной службы Azure
1. Убедитесь, что вы используете [.NET Framework 4.6.1](https://docs.microsoft.com/dotnet/framework/migration-guide/how-to-determine-which-versions-are-installed) или более поздней версии.  Для этого достаточно проверить, что в файлах *ServiceConfiguration.\*.cscfg* для параметра `osFamily` задано по меньшей мере значение 5.
1. Добавьте [пакет SDK для Application Insights в облачную службу](app-insights-cloudservices.md?toc=/azure/azure-monitor/toc.json).
1. Отслеживание запросов с помощью Application Insights.

    Application Insights может автоматически отслеживает запросы для веб-ролей ASP.Net.

    Для рабочих ролей нужно [добавить код для отслеживания запросов.](app-insights-profiler-trackrequests.md?toc=/azure/azure-monitor/toc.json)

    

1. Настройте использование профилировщика в расширении Windows Azure Diagnostics (WAD).



    1. Найдите файл *diagnostics.wadcfgx* [системы диагностики Azure](https://docs.microsoft.com/azure/monitoring-and-diagnostics/azure-diagnostics) для роли приложения, как показано ниже.  

       ![Расположение файла конфигурации диагностики](./media/enable-profiler-compute/cloudservice-solutionexplorer.png)  

        Если не удается найти файл, см. статью [Настройка системы диагностики для облачных служб и виртуальных машин Azure](https://docs.microsoft.com/azure/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines#enable-diagnostics-in-cloud-service-projects-before-deploying-them), чтобы узнать, как включить расширение диагностики в проекте облачных служб Azure.

    1. Добавьте следующий раздел `SinksConfig` в качестве дочернего элемента `WadCfg`:  

      ```xml
      <WadCfg>
        <DiagnosticMonitorConfiguration>...</DiagnosticMonitorConfiguration>
        <SinksConfig>
          <Sink name="MyApplicationInsightsProfiler">
            <!-- Replace with your own Application Insights instrumentation key. -->
            <ApplicationInsightsProfiler>00000000-0000-0000-0000-000000000000</ApplicationInsightsProfiler>
          </Sink>
        </SinksConfig>
      </WadCfg>
      ```

    >   **Примечание**. Если файл диагностики *diagnostics.wadcfgx* также содержит другой приемник типа `ApplicationInsights`, все три ключа инструментирования должны совпадать:  
    >  * Ключ, используемый в приложении.  
    >  * Ключ, используемый в приемнике `ApplicationInsights`.  
    >  * Ключ, используемый в приемнике `ApplicationInsightsProfiler`.  
    >
    > Фактическое значения ключа инструментирования, который используется приемником `ApplicationInsights`, можно найти в файлах *ServiceConfiguration.\*.cscfg*.  
    > Начиная с выпуска пакета SDK Azure для Visual Studio 15.5, совпадать должны только ключи инструментирования, используемые в приложении и приемнике `ApplicationInsightsProfiler`.
1. Разверните службу с новой конфигурацией диагностики. При этом автоматически настраивается Application Insights Profiler для этой службы.
 
## <a name="next-steps"></a>Дополнительная информация

- Создайте трафик к приложению (например, запустите [тест доступности](https://docs.microsoft.com/azure/application-insights/app-insights-monitor-web-app-availability)). Подождите 10–15 минут, пока трассировки не начнут отправляться в экземпляр Application Insights.
- См. раздел [Включение профилировщика](https://docs.microsoft.com/azure/application-insights/app-insights-profiler-overview?toc=/azure/azure-monitor/toc.json).
- Сведения об устранении неполадок профилировщика см. в разделе [Устранение неполадок](app-insights-profiler-troubleshooting.md?toc=/azure/azure-monitor/toc.json).