---
title: Профилирование веб-приложений, работающих на виртуальной машине Azure, с помощью Application Insights Profiler | Документация Майкрософт
description: Профилирование веб-приложений на виртуальной машине Azure с помощью Application Insights Profiler.
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
ms.openlocfilehash: e8f80e7d19a961c22b4e1e88556ac165d2558034
ms.sourcegitcommit: fbf0124ae39fa526fc7e7768952efe32093e3591
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/08/2019
ms.locfileid: "54081952"
---
# <a name="profile-web-apps-running-on-an-azure-virtual-machine-or-virtual-machine-scale-set-with-application-insights-profiler"></a>Профилирование веб-приложений, работающих на виртуальной машине Azure или в масштабируемом наборе виртуальных машин, с помощью Application Insights Profiler
Вы можете развернуть Application Insights Profiler для таких служб:
* [службе приложений Azure](../../azure-monitor/app/profiler.md?toc=/azure/azure-monitor/toc.json)
* [Облачные службы](profiler-cloudservice.md ?toc=/azure/azure-monitor/toc.json)
* [Service Fabric](profiler-vm.md?toc=/azure/azure-monitor/toc.json)

## <a name="deploy-profiler-on-a-virtual-machine-or-scale-set"></a>Развертывание Profiler на виртуальной машине или в масштабируемом наборе
На этой странице описан процесс настройки Application Insights Profiler на виртуальной машине Azure или в масштабируемом наборе виртуальных машин Azure. Application Insights Profiler поставляется в комплекте с расширением "Система диагностики Microsoft Azure" для виртуальных машин. Это расширение требует настройки для работы с профилировщиком. Кроме того, в приложение должен быть встроен пакет SDK App Insights.

1. Добавьте пакет SDK для Application Insights в [приложение ASP.Net](https://docs.microsoft.com/azure/application-insights/app-insights-asp-net) или в обычное [приложение .NET.](https://docs.microsoft.com/azure/application-insights/app-insights-windows-services?toc=/azure/azure-monitor/toc.json) Вы должны отправлять телеметрию запросов, чтобы решение Application Insights получало сведения из профилей для этих запросов.
1. Установите на виртуальную машину расширение "Система диагностики Microsoft Azure". Полные примеры шаблонов Resource Manager можно найти здесь:  
    * [Виртуальная машина](https://github.com/Azure/azure-docs-json-samples/blob/master/application-insights/WindowsVirtualMachine.json)
    * [Масштабируемый набор виртуальных машин](https://github.com/Azure/azure-docs-json-samples/blob/master/application-insights/WindowsVirtualMachineScaleSet.json)
    
    Ключевой компонент — ApplicationInsightsProfilerSink в WadCfg. Добавьте в этот раздел другой приемник. Таким образом WAD будет понимать, что нужно включить профилировщик и отправлять данные в iKey.
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

   Следующие команды PowerShell позволяют применить для существующих виртуальных машин другой метод, который касается только расширения "Система диагностики Azure". Вам нужно просто добавить ProfilerSink в конфигурацию (см. выше), которую возвращает команда Get-AzureRmVMDiagnosticsExtension, а затем передать обновленную конфигурацию в команду Set-AzureRmVMDiagnosticsExcension.

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

## <a name="can-profiler-run-on-on-premises-servers"></a>Можно ли запустить профилировщик на локальных серверах?
Поддержка Application Insights Profiler на локальных серверах не планируется.

## <a name="next-steps"></a>Дополнительная информация

- Создайте трафик к приложению (например, запустите [тест доступности](https://docs.microsoft.com/azure/application-insights/app-insights-monitor-web-app-availability)). Подождите 10–15 минут, пока трассировки не начнут отправляться в экземпляр Application Insights.
- См. раздел [Включение профилировщика](https://docs.microsoft.com/azure/application-insights/app-insights-profiler-overview?toc=/azure/azure-monitor/toc.json).
- Сведения об устранении неполадок профилировщика см. в разделе [Устранение неполадок](profiler-troubleshooting.md ?toc=/azure/azure-monitor/toc.json).
