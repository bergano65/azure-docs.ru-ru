---
title: Профилирование веб-приложений, работающих на виртуальной машине Azure, с использованием Application Insights Profiler | Документация Майкрософт
description: Профилируйте веб-приложения на виртуальной машине Azure с использованием Application Insights Profiler.
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
ms.openlocfilehash: 4cca65e2be44d2c846cd4034f0a9d7e8c7d9af28
ms.sourcegitcommit: aa3be9ed0b92a0ac5a29c83095a7b20dd0693463
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/20/2019
ms.locfileid: "58260052"
---
# <a name="profile-web-apps-running-on-an-azure-virtual-machine-or-a-virtual-machine-scale-set-by-using-application-insights-profiler"></a>Профилирование веб-приложений, работающих на виртуальной машине Azure или в масштабируемом наборе виртуальных машин, с использованием Application Insights Profiler

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Вы можете развернуть Azure Application Insights Profiler для таких служб:
* [службе приложений Azure](../../azure-monitor/app/profiler.md?toc=/azure/azure-monitor/toc.json)
* [Облачные службы Azure](profiler-cloudservice.md?toc=/azure/azure-monitor/toc.json)
* [Azure Service Fabric](profiler-vm.md?toc=/azure/azure-monitor/toc.json)

## <a name="deploy-profiler-on-a-virtual-machine-or-a-virtual-machine-scale-set"></a>Развертывание Profiler на виртуальной машине или в масштабируемом наборе виртуальных машин
В этой статье показано, как запустить Application Insights Profiler на виртуальной машине Azure или в масштабируемом наборе виртуальных машин Azure. Profiler поставляется с расширением системы диагностики Azure для виртуальных машин. Настройте расширение для выполнения Profiler и встройте пакет SDK для Application Insights в приложение.

1. Добавьте пакет SDK для Application Insights в [приложение ASP.Net](https://docs.microsoft.com/azure/application-insights/app-insights-asp-net) или в обычное [приложение .NET](windows-services.md?toc=/azure/azure-monitor/toc.json).  
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

   Следующие команды PowerShell, альтернативный подход для существующих виртуальных машин, которые затрагивают только расширение системы диагностики Azure. Добавьте файл конфигурации, который возвращается с помощью команды Get-AzVMDiagnosticsExtension ProfilerSink упомянутых ранее. Затем передается команде Set-AzVMDiagnosticsExtension обновленный файл конфигурации.

    ```powershell
    $ConfigFilePath = [IO.Path]::GetTempFileName()
    # After you export the currently deployed Diagnostics config to a file, edit it to include the ApplicationInsightsProfiler sink.
    (Get-AzVMDiagnosticsExtension -ResourceGroupName "MyRG" -VMName "MyVM").PublicSettings | Out-File -Verbose $ConfigFilePath
    # Set-AzVMDiagnosticsExtension might require the -StorageAccountName argument
    # If your original diagnostics configuration had the storageAccountName property in the protectedSettings section (which is not downloadable), be sure to pass the same original value you had in this cmdlet call.
    Set-AzVMDiagnosticsExtension -ResourceGroupName "MyRG" -VMName "MyVM" -DiagnosticsConfigurationPath $ConfigFilePath
    ```

1. Если нужное приложение выполняется с помощью [IIS](https://www.microsoft.com/web/downloads/platform.aspx), необходимо включить компонент Windows `IIS Http Tracing`.

   a. Установите удаленный доступ к среде, а затем используйте окно [Добавить функции Windows]( https://docs.microsoft.com/iis/configuration/system.webserver/tracing/) или выполните следующую команду в PowerShell (с правами администратора):  

    ```powershell
    Enable-WindowsOptionalFeature -FeatureName IIS-HttpTracing -Online -All
    ```  
   2. Если не удается установить удаленный доступ, можно использовать [Azure CLI](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli), чтобы выполнить следующую команду:  

    ```powershell
    az vm run-command invoke -g MyResourceGroupName -n MyVirtualMachineName --command-id RunPowerShellScript --scripts "Enable-WindowsOptionalFeature -FeatureName IIS-HttpTracing -Online -All"
    ```

1. Разверните приложение.

## <a name="set-profiler-sink-using-azure-resource-explorer"></a>Набор Profiler приемника с помощью обозревателя ресурсов Azure
У нас еще нет можно задать в приемник Application Insights Profiler на портале. Вместо использования powershell, как описано выше, можно использовать обозреватель ресурсов Azure для задания в приемник. Но Обратите внимание, если вы развертываете виртуальную Машину, приемник будут потеряны. Необходимо обновить файл конфигурации, используемого при развертывании виртуальной Машины, чтобы сохранить задание.

1. Проверьте установку расширения системы диагностики Windows Azure, просмотрев расширения, установленные для виртуальной машины.  

    ![Проверьте, установлены ли расширение WAD][wadextension]

1. Найдите нужное расширение системы диагностики виртуальной Машины для виртуальной Машины. Разверните вашей группе ресурсов, Microsoft.Compute virtualMachines, имя виртуальной машины и расширения.  

    ![Перейдите к конфигурации WAD в обозревателе ресурсов Azure][azureresourceexplorer]

1. Добавление приемника Application Insights Profiler в SinksConfig узел в разделе WadCfg. Если у вас нет раздела SinksConfig, может потребоваться добавить его. Не забудьте указать правильный ключ инструментирования Application Insights в параметрах. Нужно будет переключить режим обозревателей на чтение и запись в правом верхнем углу и нажмите синюю кнопку «Изменить».

    ![Добавление приемника Application Insights Profiler][resourceexplorersinksconfig]

1. После завершения редактирования файла конфигурации, нажмите клавишу «Put». При успешном выполнении put Зеленый флажок отображается по центру экрана.

    ![Отправить запрос put для применения изменений][resourceexplorerput]






## <a name="can-profiler-run-on-on-premises-servers"></a>Можно ли запустить Profiler на локальных серверах?
Поддержка Application Insights Profiler на локальных серверах не планируется.

## <a name="next-steps"></a>Дальнейшие действия

- Создайте трафик к приложению (например, запустите [тест доступности](monitor-web-app-availability.md)). Подождите 10–15 минут, пока трассировки не начнут отправляться в экземпляр Application Insights.
- См. раздел [Включение профилировщика](profiler-overview.md?toc=/azure/azure-monitor/toc.json).
- Сведения об устранении неполадок профилировщика см. в статье [Устранение неполадок по включению и просмотру Application Insights Profiler](profiler-troubleshooting.md?toc=/azure/azure-monitor/toc.json).

[azureresourceexplorer]: ./media/profiler-vm/azure-resource-explorer.png
[resourceexplorerput]: ./media/profiler-vm/resource-explorer-put.png
[resourceexplorersinksconfig]: ./media/profiler-vm/resource-explorer-sinks-config.png
[wadextension]: ./media/profiler-vm/wad-extension.png
