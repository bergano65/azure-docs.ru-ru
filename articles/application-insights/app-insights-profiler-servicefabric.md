---
title: Профилирование запущенных приложений Azure Service Fabric с помощью Application Insights | Документация Майкрософт
description: Включение профилировщика для приложения Service Fabric
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: conceptual
ms.reviewer: cawa
ms.date: 08/06/2018
ms.author: mbullwin
ms.openlocfilehash: 071d1acb592220f7da83a47fd7dffa757ef81a95
ms.sourcegitcommit: 0f54b9dbcf82346417ad69cbef266bc7804a5f0e
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/26/2018
ms.locfileid: "50142976"
---
# <a name="profile-live-azure-service-fabric-applications-with-application-insights"></a>Профилирование запущенных приложений Azure Service Fabric с помощью Application Insights

Вы можете развернуть Application Insights Profiler для таких служб:
* [Веб-приложения Azure](app-insights-profiler.md?toc=/azure/azure-monitor/toc.json)
* [Облачные службы](app-insights-profiler-cloudservice.md?toc=/azure/azure-monitor/toc.json)
* [Виртуальные машины](app-insights-profiler-vm.md?toc=/azure/azure-monitor/toc.json)


## <a name="set-up-the-environment-deployment-definition"></a>Настройка определения развертывания среды

Application Insights Profiler поставляется в комплекте с системой диагностики Microsoft Azure (WAD). С помощью шаблона Azure RM для кластера Service Fabric можно установить расширение WAD. [**Здесь**](https://github.com/Azure/azure-docs-json-samples/blob/master/application-insights/ServiceFabricCluster.json) вы найдете пример шаблона, который устанавливает WAD в кластере Service Fabric.

Чтобы настроить среду, выполните следующие действия.
1. Чтобы использовать [.NET Framework версии 4.6.1](https://docs.microsoft.com/dotnet/framework/migration-guide/how-to-determine-which-versions-are-installed) и выше, разверните ОС версии `Windows Server 2012 R2` и выше.

1. Найдите расширение [системы диагностики Azure](https://docs.microsoft.com/azure/monitoring-and-diagnostics/azure-diagnostics) в файле шаблона развертывания и добавьте следующий раздел `SinksConfig` в качестве дочернего элемента `WadCfg`. Замените значение свойства `ApplicationInsightsProfiler` собственным ключом инструментирования Application Insights:  

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
1. Разверните кластер Service Fabric с использованием шаблона Azure Resource Manager. Если все параметры верны, при установке расширения WAD будет установлен и включен Application Insights Profiler. 
1. Добавьте Application Insights в приложение Service Fabric. Чтобы профилировщик мог собирать профили для ваших запросов, приложение должно отправлять данные о запросах в Application Insights. Соответствующие инструкции можно найти [здесь](https://github.com/Microsoft/ApplicationInsights-ServiceFabric).
1. Разверните приложение заново.

> [ПОДСКАЗКА] Вместо описанных выше действий с JSON для виртуальных машин можно использовать портал Azure. Откройте раздел **Виртуальные машины**>**Параметры диагностики**>**Приемники**, затем **включите** отправку данных диагностики в Application Insights и выберите учетную запись Application Insights или определенный iKey.

## <a name="next-steps"></a>Дополнительная информация

- Создайте трафик к приложению (например, запустите [тест доступности](https://docs.microsoft.com/azure/application-insights/app-insights-monitor-web-app-availability)). Подождите 10–15 минут, пока трассировки не начнут отправляться в экземпляр Application Insights.
- См. раздел [Включение профилировщика](https://docs.microsoft.com/azure/application-insights/app-insights-profiler-overview?toc=/azure/azure-monitor/toc.json).
- Сведения об устранении неполадок профилировщика см. в разделе [Устранение неполадок](app-insights-profiler-troubleshooting.md?toc=/azure/azure-monitor/toc.json).