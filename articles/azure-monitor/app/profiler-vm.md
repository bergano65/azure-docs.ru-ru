---
title: Профилирование веб-приложений, работающих на виртуальной машине Azure, с использованием Application Insights Profiler | Документация Майкрософт
description: Профилируйте веб-приложения на виртуальной машине Azure с использованием Application Insights Profiler.
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
ms.openlocfilehash: 3f720cdf369e7377f16bb2ea9cba7e898097cc29
ms.sourcegitcommit: a408b0e5551893e485fa78cd7aa91956197b5018
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/17/2019
ms.locfileid: "54359788"
---
# <a name="profile-web-apps-running-on-an-azure-virtual-machine-or-a-virtual-machine-scale-set-by-using-application-insights-profiler"></a>Профилирование веб-приложений, работающих на виртуальной машине Azure или в масштабируемом наборе виртуальных машин, с использованием Application Insights Profiler

Вы можете развернуть Azure Application Insights Profiler для таких служб:
* [службе приложений Azure](../../azure-monitor/app/profiler.md?toc=/azure/azure-monitor/toc.json)
* [Облачные службы Azure](profiler-cloudservice.md?toc=/azure/azure-monitor/toc.json)
* [Azure Service Fabric](profiler-vm.md?toc=/azure/azure-monitor/toc.json)

## <a name="deploy-profiler-on-a-virtual-machine-or-a-virtual-machine-scale-set"></a>Развертывание Profiler на виртуальной машине или в масштабируемом наборе виртуальных машин
В этой статье показано, как запустить Application Insights Profiler на виртуальной машине Azure или в масштабируемом наборе виртуальных машин Azure. Profiler поставляется с расширением системы диагностики Azure для виртуальных машин. Настройте расширение для выполнения Profiler и встройте пакет SDK для Application Insights в приложение.

1. Добавьте пакет SDK для Application Insights в [приложение ASP.Net](https://docs.microsoft.com/azure/application-insights/app-insights-asp-net) или в обычное [приложение .NET](https://docs.microsoft.com/azure/application-insights/windows-services?toc=/azure/azure-monitor/toc.json).  
  Чтобы просмотреть профили запросов, необходимо отправить телеметрию запроса в Application Insights.

1. Установите на виртуальную машину расширение "Система диагностики Microsoft Azure". Полные примеры шаблонов Resource Manager можно найти здесь:  
    * [Виртуальная машина](https://github.com/Azure/azure-docs-json-samples/blob/master/application-insights/WindowsVirtualMachine.json)
    * [Масштабируемый набор виртуальных машин](https://github.com/Azure/azure-docs-json-samples/blob/master/application-insights/WindowsVirtualMachineScaleSet.json)
    
    Ключевой компонент — ApplicationInsightsProfilerSink в WadCfg. Добавьте в этот раздел другой приемник, чтобы система диагностики Azure позволила Profiler отправлять данные в ваш iKey.
    
    ```json
      "SinksConfig": {
        "Sink": [
          {
            "name": "ApplicationInsightsSink",
            "ApplicationInsights": "85f73556-b1ba-46de-9534-606e08c6120f"
          },
          {
            "name": "MyApplicationInsightsProfilerSink",
            "ApplicationInsightsProfiler": "85f73556-b1ba-46de-9534-606e08c6120f"
          }
        ]
      },
    ```

1. Разверните определение развертывания измененной среды.  

   Чтобы применить изменения, обычно нужно полностью развернуть шаблон или опубликовать его в облачных службах с помощью командлетов PowerShell или Visual Studio.  

   Следующие команды PowerShell позволяют применить для имеющихся виртуальных машин другой метод, который касается только расширения "Система диагностики Azure". Добавьте упомянутый ранее ProfilerSink в конфигурацию, которую возвращает команда Get-AzureRmVMDiagnosticsExtension, а затем передайте обновленную конфигурацию в команду Set-AzureRmVMDiagnosticsExtension.

    ```powershell
    $ConfigFilePath = [IO.Path]::GetTempFileName()
    # After you export the currently deployed Diagnostics config to a file, edit it to include the ApplicationInsightsProfiler sink.
    (Get-AzureRmVMDiagnosticsExtension -ResourceGroupName "MyRG" -VMName "MyVM").PublicSettings | Out-File -Verbose $ConfigFilePath
    # Set-AzureRmVMDiagnosticsExtension might require the -StorageAccountName argument
    # If your original diagnostics configuration had the storageAccountName property in the protectedSettings section (which is not downloadable), be sure to pass the same original value you had in this cmdlet call.
    Set-AzureRmVMDiagnosticsExtension -ResourceGroupName "MyRG" -VMName "MyVM" -DiagnosticsConfigurationPath $ConfigFilePath
    ```

1. Если нужное приложение выполняется с помощью [IIS](https://www.microsoft.com/web/downloads/platform.aspx), необходимо включить компонент Windows `IIS Http Tracing`.

   a. Установите удаленный доступ к среде, а затем используйте окно [Добавить функции Windows]( https://docs.microsoft.com/iis/configuration/system.webserver/tracing/) или выполните следующую команду в PowerShell (с правами администратора):  

    ```powershell
    Enable-WindowsOptionalFeature -FeatureName IIS-HttpTracing -Online -All
    ```  
   b. Если не удается установить удаленный доступ, можно использовать [Azure CLI](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli), чтобы выполнить следующую команду:  

    ```powershell
    az vm run-command invoke -g MyResourceGroupName -n MyVirtualMachineName --command-id RunPowerShellScript --scripts "Enable-WindowsOptionalFeature -FeatureName IIS-HttpTracing -Online -All"
    ```

1. Разверните приложение.

## <a name="can-profiler-run-on-on-premises-servers"></a>Можно ли запустить Profiler на локальных серверах?
Поддержка Application Insights Profiler на локальных серверах не планируется.

## <a name="next-steps"></a>Дополнительная информация

- Создайте трафик к приложению (например, запустите [тест доступности](https://docs.microsoft.com/azure/application-insights/monitor-web-app-availability)). Подождите 10–15 минут, пока трассировки не начнут отправляться в экземпляр Application Insights.
- См. раздел [Включение профилировщика](https://docs.microsoft.com/azure/application-insights/profiler-overview?toc=/azure/azure-monitor/toc.json).
- Сведения об устранении неполадок профилировщика см. в статье [Устранение неполадок по включению и просмотру Application Insights Profiler](profiler-troubleshooting.md?toc=/azure/azure-monitor/toc.json).
