---
title: Профилирование облачных служб реального времени Azure с помощью Application Insights | Документация Майкрософт
description: Включите Application Insights Profiler для облачных служб Azure.
services: application-insights
documentationcenter: ''
author: cweining
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.reviewer: mbullwin
ms.date: 08/06/2018
ms.author: cweining
ms.openlocfilehash: 8ad472b9c92e3bc2164146191a63985fd26becab
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/23/2019
ms.locfileid: "60306384"
---
# <a name="profile-live-azure-cloud-services-with-application-insights"></a>Профилирование облачных служб реального времени Azure с помощью Application Insights

Вы можете развернуть Application Insights Profiler для следующих служб.
* [службе приложений Azure](profiler.md?toc=/azure/azure-monitor/toc.json)
* [Приложения Azure Service Fabric](profiler-servicefabric.md?toc=/azure/azure-monitor/toc.json)
* [Виртуальные машины Azure](profiler-vm.md?toc=/azure/azure-monitor/toc.json)

Application Insights Profiler поставляется с расширением системы диагностики Azure. Вам достаточно настроить систему диагностики Azure, чтобы установить Profiler и отправлять профили в ресурс Application Insights.

## <a name="enable-profiler-for-azure-cloud-services"></a>Включение Profiler для облачных служб Azure
1. Убедитесь, что вы используете [.NET Framework 4.6.1](https://docs.microsoft.com/dotnet/framework/migration-guide/how-to-determine-which-versions-are-installed) или более поздней версии. Для этого достаточно проверить, что в файлах *ServiceConfiguration.\*.cscfg* для параметра `osFamily` задано по меньшей мере значение 5.

1. Добавьте [Application Insights для облачных служб Azure](../../azure-monitor/app/cloudservices.md?toc=/azure/azure-monitor/toc.json).

    **Исправил ошибку в профилировщик, который поставляется в WAD для облачных служб.** Последнюю версию WAD (1.12.2.0) для облачных служб работает с всех последних версиях пакета SDK App Insights. Узлы облачных служб автоматически обновит WAD, но он не выполняется немедленно. Чтобы принудительно выполнить обновление, можно повторно разверните службу или перезагрузите узел.

1. Отслеживание запросов с помощью Application Insights.

    * Application Insights может автоматически отслеживает запросы для веб-ролей ASP.NET.

    * Сведения о рабочих ролях см. в статье [Написание кода для отслеживания запросов с помощью Azure Application Insights](profiler-trackrequests.md?toc=/azure/azure-monitor/toc.json).

1. Настройка расширения системы диагностики Azure для включения Profiler:

    a. Найдите файл *diagnostics.wadcfgx* [системы диагностики Azure](https://docs.microsoft.com/azure/monitoring-and-diagnostics/azure-diagnostics) для роли приложения, как показано ниже.  

      ![Расположение файла конфигурации диагностики](./media/profiler-cloudservice/cloudservice-solutionexplorer.png)  

      Если вы не можете найти файл, см. статью [Настройка системы диагностики для облачных служб и виртуальных машин Azure](https://docs.microsoft.com/azure/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines).

    2. Добавьте следующий раздел `SinksConfig` в качестве дочернего элемента `WadCfg`:  

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

    > [!NOTE]
    > Если файл *diagnostics.wadcfgx* содержит другой приемник типа ApplicationInsights, все три ключа инструментирования должны совпадать.  
    > * Ключ, используемый в приложении. 
    > * Ключ, используемый в приемнике ApplicationInsights. 
    > * Ключ, используемый в приемнике ApplicationInsightsProfiler. 
    >
    > Фактическое значения ключа инструментирования, который используется приемником `ApplicationInsights`, можно найти в файлах *ServiceConfiguration.\*.cscfg*. 
    > Начиная с выпуска пакета SDK Azure для Visual Studio 15.5, совпадать должны только ключи инструментирования, используемые приложением и приемником ApplicationInsightsProfiler.

1. Разверните службу с новой конфигурацией диагностики. При этом Application Insights Profiler настраивается для этой службы.
 
## <a name="next-steps"></a>Дальнейшие действия

* Создайте трафик к приложению (например, запустите [тест доступности](monitor-web-app-availability.md)). Подождите 10–15 минут, пока трассировки не начнут отправляться в экземпляр Application Insights.
* См. раздел [Включение профилировщика](profiler-overview.md?toc=/azure/azure-monitor/toc.json).
* Дополнительные сведения об устранении неполадок Profiler см. в статье [Устранение неполадок по включению и просмотру Application Insights Profiler](profiler-troubleshooting.md?toc=/azure/azure-monitor/toc.json).
