---
title: Веб-приложения профиля на Azure VM - Профиль анализа приложений
description: Профилируйте веб-приложения на виртуальной машине Azure с использованием Application Insights Profiler.
ms.topic: conceptual
author: cweining
ms.author: cweining
ms.date: 11/08/2019
ms.reviewer: mbullwin
ms.openlocfilehash: 7c5dfe6ed08df01f78346c76fd5a35e7d64ab520
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77671585"
---
# <a name="profile-web-apps-running-on-an-azure-virtual-machine-or-a-virtual-machine-scale-set-by-using-application-insights-profiler"></a>Профилирование веб-приложений, работающих на виртуальной машине Azure или в масштабируемом наборе виртуальных машин, с использованием Application Insights Profiler

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Вы можете развернуть Azure Application Insights Profiler для таких служб:
* [Служба приложений Azure](../../azure-monitor/app/profiler.md?toc=/azure/azure-monitor/toc.json)
* [Облачные службы Azure](profiler-cloudservice.md?toc=/azure/azure-monitor/toc.json)
* [Ткань обслуживания Azure](profiler-vm.md?toc=/azure/azure-monitor/toc.json)

## <a name="deploy-profiler-on-a-virtual-machine-or-a-virtual-machine-scale-set"></a>Развертывание Profiler на виртуальной машине или в масштабируемом наборе виртуальных машин
В этой статье показано, как запустить Application Insights Profiler на виртуальной машине Azure или в масштабируемом наборе виртуальных машин Azure. Profiler поставляется с расширением системы диагностики Azure для виртуальных машин. Настройте расширение для выполнения Profiler и встройте пакет SDK для Application Insights в приложение.

1. Добавьте SDK Приложения Insights в ваше [ASP.NET приложение.](https://docs.microsoft.com/azure/application-insights/app-insights-asp-net)

   Чтобы просмотреть профили запросов, необходимо отправить телеметрию запроса в Application Insights.

1. Установите на виртуальную машину расширение "Диагностика Microsoft Azure". Полные примеры шаблонов Resource Manager можно найти здесь:  
   * [Виртуальная машина](https://github.com/Azure/azure-docs-json-samples/blob/master/application-insights/WindowsVirtualMachine.json)
   * [Набор масштабирования виртуальных машин](https://github.com/Azure/azure-docs-json-samples/blob/master/application-insights/WindowsVirtualMachineScaleSet.json)
    
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

   Следующие команды PowerShell являются альтернативным подходом для существующих виртуальных машин, которые касаются только расширения Azure Diagnostics. Добавьте ранее упомянутый ProfilerSink в конфигурацию, возвращенную командой Get-AzVMDiagnosticsExtension. Затем передайте обновленную конфигурацию команде Set-AzVMDiagnosticsExtension.

    ```powershell
    $ConfigFilePath = [IO.Path]::GetTempFileName()
    # After you export the currently deployed Diagnostics config to a file, edit it to include the ApplicationInsightsProfiler sink.
    (Get-AzVMDiagnosticsExtension -ResourceGroupName "MyRG" -VMName "MyVM").PublicSettings | Out-File -Verbose $ConfigFilePath
    # Set-AzVMDiagnosticsExtension might require the -StorageAccountName argument
    # If your original diagnostics configuration had the storageAccountName property in the protectedSettings section (which is not downloadable), be sure to pass the same original value you had in this cmdlet call.
    Set-AzVMDiagnosticsExtension -ResourceGroupName "MyRG" -VMName "MyVM" -DiagnosticsConfigurationPath $ConfigFilePath
    ```

1. Если нужное приложение выполняется с помощью [IIS](https://www.microsoft.com/web/downloads/platform.aspx), необходимо включить компонент Windows `IIS Http Tracing`.

   а. Установите удаленный доступ к среде, а затем используйте окно [Добавить функции Windows]( https://docs.microsoft.com/iis/configuration/system.webserver/tracing/) или выполните следующую команду в PowerShell (с правами администратора):  

    ```powershell
    Enable-WindowsOptionalFeature -FeatureName IIS-HttpTracing -Online -All
    ```  
   b. Если не удается установить удаленный доступ, можно использовать [Azure CLI](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli), чтобы выполнить следующую команду:  

    ```powershell
    az vm run-command invoke -g MyResourceGroupName -n MyVirtualMachineName --command-id RunPowerShellScript --scripts "Enable-WindowsOptionalFeature -FeatureName IIS-HttpTracing -Online -All"
    ```

1. Разверните приложение.

## <a name="set-profiler-sink-using-azure-resource-explorer"></a>Установить профиль раковины с помощью Azure ресурс explorer
У нас пока нет способа установить раковину Профилера Application Insights с портала. Вместо того, чтобы использовать powershell, как описано выше, вы можете использовать Azure Resource Explorer для установки раковины. Но заметьте, если вы развернете VM снова, раковина будет потеряна. Для сохранения этой настройки необходимо обновить приразвертывание VM.

1. Убедитесь, что расширение Windows Azure Diagnostics установлено путем просмотра расширений, установленных для виртуальной машины.  

    ![Проверьте, установлено ли расширение WAD][wadextension]

2. Найдите расширение VM Diagnostics для вашего VM. Перейти [https://resources.azure.com](https://resources.azure.com)к . Расширьте свою группу ресурсов, виртуальные машины Microsoft.Compute, название виртуальной машины и расширения.  

    ![Перейдите к конфигурации WAD в Azure Resource Explorer][azureresourceexplorer]

3. Добавьте приложение Insights Profiler опуститься в узло SinksConfig под WadCfg. Если у вас еще нет раздела SinksConfig, возможно, потребуется добавить его. Не забудьте указать в настройках подходящие исследования приложений iKey. Вам нужно будет переключить режим исследователей на read/Write в правом верхнем углу и нажать на синюю кнопку "Изменить".

    ![Добавить приложение Исследования Профилер раковина][resourceexplorersinksconfig]

4. Когда вы закончите редактирование конфигурации, нажмите "Put". Если положить успешно, зеленый чек появится в середине экрана.

    ![Отправить отложенный запрос для применения изменений][resourceexplorerput]






## <a name="can-profiler-run-on-on-premises-servers"></a>Можно ли запустить Profiler на локальных серверах?
Поддержка Application Insights Profiler на локальных серверах не планируется.

## <a name="next-steps"></a>Дальнейшие действия

- Создайте трафик к приложению (например, запустите [тест доступности](monitor-web-app-availability.md)). Подождите 10–15 минут, пока трассировки не начнут отправляться в экземпляр Application Insights.
- Смотрите [следы Profiler](profiler-overview.md?toc=/azure/azure-monitor/toc.json) на портале Azure.
- Сведения об устранении неполадок профилировщика см. в статье [Устранение неполадок по включению и просмотру Application Insights Profiler](profiler-troubleshooting.md?toc=/azure/azure-monitor/toc.json).

[azureresourceexplorer]: ./media/profiler-vm/azure-resource-explorer.png
[resourceexplorerput]: ./media/profiler-vm/resource-explorer-put.png
[resourceexplorersinksconfig]: ./media/profiler-vm/resource-explorer-sinks-config.png
[wadextension]: ./media/profiler-vm/wad-extension.png
