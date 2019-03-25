---
title: Профилирование запущенных приложений Azure Service Fabric с помощью Application Insights | Документация Майкрософт
description: Включение Profiler для приложения Service Fabric
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
ms.openlocfilehash: 0b1a06d181fc4d2a44d389d47d1f9480c2fdcb40
ms.sourcegitcommit: 81fa781f907405c215073c4e0441f9952fe80fe5
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/25/2019
ms.locfileid: "58401104"
---
# <a name="profile-live-azure-service-fabric-applications-with-application-insights"></a>Профилирование запущенных приложений Azure Service Fabric с помощью Application Insights

Вы можете развернуть Application Insights Profiler для следующих служб:
* [службе приложений Azure](profiler.md?toc=/azure/azure-monitor/toc.json)
* [Облачные службы Azure](profiler-cloudservice.md?toc=/azure/azure-monitor/toc.json)
* [Виртуальные машины Azure](profiler-vm.md?toc=/azure/azure-monitor/toc.json)

## <a name="set-up-the-environment-deployment-definition"></a>Настройка определения развертывания среды

Application Insights Profiler поставляется в комплекте с системой диагностики Azure. Вы можете установить расширение системы диагностики Azure, используя шаблон Azure Resource Manager для кластера Service Fabric. Получите шаблон [, который устанавливает систему диагностики Azure в кластер Service Fabric](https://github.com/Azure/azure-docs-json-samples/blob/master/application-insights/ServiceFabricCluster.json).

Чтобы настроить среду, выполните следующие действия.

1. Чтобы использовать [.NET Framework версии 4.6.1](https://docs.microsoft.com/dotnet/framework/migration-guide/how-to-determine-which-versions-are-installed) и выше, разверните ОС версии `Windows Server 2012 R2` и выше.

1. Найдите расширение [системы диагностики Azure](https://docs.microsoft.com/azure/monitoring-and-diagnostics/azure-diagnostics) в файле шаблона развертывания.

1. Добавьте следующий раздел `SinksConfig` в качестве дочернего элемента `WadCfg`. Замените значение свойства `ApplicationInsightsProfiler` собственным ключом инструментирования Application Insights:  

      ```json
      "SinksConfig": {
        "Sink": [
          {
            "name": "MyApplicationInsightsProfilerSink",
            "ApplicationInsightsProfiler": "00000000-0000-0000-0000-000000000000"
          }
        ]
      }
      ```

      Сведения о добавлении расширения диагностики в шаблон развертывания см. в статье [Использование мониторинга и системы диагностики с виртуальной машиной Windows и шаблонами Azure Resource Manager](https://docs.microsoft.com/azure/virtual-machines/windows/extensions-diagnostics-template?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

1. Разверните кластер Service Fabric с помощью шаблона Azure Resource Manager.  
  Если все параметры верны, при установке расширения системы диагностики Azure будет установлен и включен Application Insights Profiler. 

1. Добавьте Application Insights в приложение Service Fabric.  
  Для Profiler для сбора профили для ваших запросов приложения должны быть Отслеживание операций с помощью Application Insights. API без отслеживания состояния, см. в инструкциях по [отслеживание запросов для профилирования](profiler-trackrequests.md?toc=/azure/azure-monitor/toc.json). Дополнительные сведения об отслеживании пользовательских операций в другие виды приложений см. [отслеживания пользовательских операций с помощью пакета SDK .NET Application Insights](custom-operations-tracking.md?toc=/azure/azure-monitor/toc.json).

1. Разверните приложение заново.

> [ПОДСКАЗКА] Вместо описанных выше действий на основе JSON для виртуальных машин можно использовать портал Azure. Откройте раздел **Виртуальные машины** > **Параметры диагностики** > **Приемники** > присвойте отправке данных диагностики в Application Insights значение **Включено** и выберите учетную запись Application Insights или определенный ikey.

## <a name="next-steps"></a>Дальнейшие действия

* Создайте трафик к приложению (например, запустите [тест доступности](monitor-web-app-availability.md)). Подождите 10–15 минут, пока трассировки не начнут отправляться в экземпляр Application Insights.
* См. раздел [Включение профилировщика](profiler-overview.md?toc=/azure/azure-monitor/toc.json).
* Сведения об устранении неполадок профилировщика см. в статье [Устранение неполадок по включению и просмотру Application Insights Profiler](profiler-troubleshooting.md?toc=/azure/azure-monitor/toc.json).
