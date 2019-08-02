---
title: Мониторинг производительности приложений, размещенных на ВИРТУАЛЬНЫХ машинах Azure и масштабируемых наборах виртуальных машин Azure | Документация Майкрософт
description: Мониторинг производительности приложений для ВИРТУАЛЬНОЙ машины Azure и масштабируемых наборов виртуальных машин Azure. Загрузка диаграммы и время отклика, сведения о зависимостях и Настройка оповещений о производительности.
services: application-insights
documentationcenter: .net
author: mrbullwinkle
manager: carmonm
ms.service: application-insights
ms.topic: conceptual
ms.date: 06/27/2019
ms.author: mbullwin
ms.openlocfilehash: f2c6b98fd0be2061e9d8cab5c063cafadf71476a
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/29/2019
ms.locfileid: "68597448"
---
# <a name="monitor-application-performance-hosted-on-azure-vm-and-azure-virtual-machine-scale-sets"></a>Мониторинг производительности приложений, размещенных на ВИРТУАЛЬНЫХ машинах Azure и масштабируемых наборах виртуальных машин Azure

Включение мониторинга для веб-приложений на основе .NET, работающих на [виртуальных машинах Azure](https://azure.microsoft.com/services/virtual-machines/) и [масштабируемых наборах виртуальных машин Azure](https://docs.microsoft.com/azure/virtual-machine-scale-sets/) , теперь стало проще, чем когда бы то ни было. Получите все преимущества использования Application Insights без изменения кода.

В этой статье описано, как включить мониторинг Application Insights с помощью расширения Аппликатионмониторингвиндовс и предоставить предварительные рекомендации по автоматизации процесса для крупномасштабных развертываний.

> [!IMPORTANT]
> Расширение Azure Аппликатионмониторингвиндовс в настоящее время находится в общедоступной предварительной версии.
> Эта предварительная версия предоставляется без соглашения об уровне обслуживания и не рекомендуется для рабочих нагрузок в рабочей среде. Некоторые функции могут не поддерживаться, а некоторые могут иметь ограниченные возможности.
> Дополнительные сведения см. в статье [Дополнительные условия использования предварительных выпусков Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="enable-application-insights"></a>Включить Application Insights

Существует два способа включения мониторинга приложений для приложений, размещенных в масштабируемом наборе виртуальных машин Azure, и для них.

* **Мониторинг приложений на основе агентов** (Расширение Аппликатионмониторингвиндовс).
    * Этот метод является самым простым для включения и не требует дополнительной настройки. Часто это называется мониторингом среды выполнения. Для виртуальных машин Azure и масштабируемых наборов виртуальных машин Azure рекомендуется как минимум включить этот уровень мониторинга. После этого в зависимости от конкретного сценария можно оценить, требуется ли ручное инструментирование.
    * В настоящее время поддерживаются только приложения, размещенные в среде .NET IIS.

* **Ручное инструментирование приложения с помощью кода** путем установки пакета SDK для Application Insights.

    * Этот подход гораздо более настраиваемый, но требует [добавления зависимости от Application Insights пакетов NUGET SDK](https://docs.microsoft.com/azure/azure-monitor/app/asp-net). Этот метод также означает, что вам нужно самостоятельно управлять обновлениями для последних версий пакетов.

    * Если необходимо выполнить пользовательские вызовы API для отслеживания событий или зависимостей, не отслеживаемых по умолчанию с помощью мониторинга на основе агентов, необходимо использовать этот метод. Дополнительные сведения см. в [статье об API для получения пользовательских событий и метрик](https://docs.microsoft.com/azure/azure-monitor/app/api-custom-events-metrics) .

> [!NOTE]
> Если будет обнаружено отслеживание на основе агента и ручное инструментирование на основе пакета SDK, будут учитываться только параметры инструментирования вручную. Это позволяет предотвратить отправку повторяющихся данных. Чтобы узнать больше об этом, ознакомьтесь с [разделом устранение неполадок](#troubleshooting) ниже.

## <a name="manage-agent-based-monitoring-for-net-applications-on-vm-using-powershell"></a>Управление мониторингом на основе агентов для приложений .NET на виртуальной машине с помощью PowerShell

Установка или обновление расширения мониторинга приложений для виртуальной машины
```powershell
$publicCfgJsonString = '
{
  "RedfieldConfiguration": {
    "InstrumentationKeyMap": {
      "Filters": [
        {
          "AppFilter": ".*",
          "MachineFilter": ".*",
          "InstrumentationSettings" : {
            "InstrumentationKey": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
          }
        }
      ]
    }
  }
}
';
$privateCfgJsonString = '{}';

Set-AzVMExtension -ResourceGroupName "<myVmResourceGroup>" -VMName "<myVmName>" -Location "South Central US" -Name "ApplicationMonitoring" -Publisher "Microsoft.Azure.Diagnostics" -Type "ApplicationMonitoringWindows" -Version "2.8" -SettingString $publicCfgJsonString -ProtectedSettingString $privateCfgJsonString
```

Удаление расширения мониторинга приложений с виртуальной машины
```powershell
Remove-AzVMExtension -ResourceGroupName "<myVmResourceGroup>" -VMName "<myVmName>" -Name "ApplicationMonitoring"
```

Запрос состояния расширения мониторинга приложений для виртуальной машины
```powershell
Get-AzVMExtension -ResourceGroupName "<myVmResourceGroup>" -VMName "<myVmName>" -Name ApplicationMonitoring -Status
```

Получение списка установленных расширений для виртуальной машины
```powershell
Get-AzResource -ResourceId "/subscriptions/<mySubscriptionId>/resourceGroups/<myVmResourceGroup>/providers/Microsoft.Compute/virtualMachines/<myVmName>/extensions"

# Name              : ApplicationMonitoring
# ResourceGroupName : <myVmResourceGroup>
# ResourceType      : Microsoft.Compute/virtualMachines/extensions
# Location          : southcentralus
# ResourceId        : /subscriptions/<mySubscriptionId>/resourceGroups/<myVmResourceGroup>/providers/Microsoft.Compute/virtualMachines/<myVmName>/extensions/ApplicationMonitoring
```

## <a name="manage-agent-based-monitoring-for-net-applications-on-azure-virtual-machine-scale-set-using-powershell"></a>Управление мониторингом на основе агентов для приложений .NET в масштабируемом наборе виртуальных машин Azure с помощью PowerShell

Установка или обновление расширения мониторинга приложений для масштабируемого набора виртуальных машин Azure
```powershell
$publicCfgHashtable =
@{
  "RedfieldConfiguration"= @{
    "InstrumentationKeyMap"= @{
      "Filters"= @(
        @{
          "AppFilter"= ".*";
          "MachineFilter"= ".*";
          "InstrumentationSettings"= @{
            "InstrumentationKey"= "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"; # Application Insights Instrumentation Key, create new Application Insights resource if you don't have one. https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/microsoft.insights%2Fcomponents
          }
        }
      )
    }
  }
};
$privateCfgHashtable = @{};

$vmss = Get-AzVmss -ResourceGroupName "<myResourceGroup>" -VMScaleSetName "<myVmssName>"

Add-AzVmssExtension -VirtualMachineScaleSet $vmss -Name "ApplicationMonitoring" -Publisher "Microsoft.Azure.Diagnostics" -Type "ApplicationMonitoringWindows" -TypeHandlerVersion "2.8" -Setting $publicCfgHashtable -ProtectedSetting $privateCfgHashtable

Update-AzVmss -ResourceGroupName $vmss.ResourceGroupName -Name $vmss.Name -VirtualMachineScaleSet $vmss

# Note: depending on your update policy, you might need to run Update-AzVmssInstance for each instance
```

Удаление расширения мониторинга приложений из масштабируемого набора виртуальных машин Azure
```powershell
$vmss = Get-AzVmss -ResourceGroupName "<myResourceGroup>" -VMScaleSetName "<myVmssName>"

Remove-AzVmssExtension -VirtualMachineScaleSet $vmss -Name "ApplicationMonitoring"

Update-AzVmss -ResourceGroupName $vmss.ResourceGroupName -Name $vmss.Name -VirtualMachineScaleSet $vmss

# Note: depending on your update policy, you might need to run Update-AzVmssInstance for each instance
```

Запрос состояния расширения мониторинга приложений для масштабируемого набора виртуальных машин Azure
```powershell
# Not supported by extensions framework
```

Получение списка установленных расширений для масштабируемого набора виртуальных машин Azure
```powershell
Get-AzResource -ResourceId /subscriptions/<mySubscriptionId>/resourceGroups/<myResourceGroup>/providers/Microsoft.Compute/virtualMachineScaleSets/<myVmssName>/extensions

# Name              : ApplicationMonitoringWindows
# ResourceGroupName : <myResourceGroup>
# ResourceType      : Microsoft.Compute/virtualMachineScaleSets/extensions
# Location          :
# ResourceId        : /subscriptions/<mySubscriptionId>/resourceGroups/<myResourceGroup>/providers/Microsoft.Compute/virtualMachineScaleSets/<myVmssName>/extensions/ApplicationMonitoringWindows
```

## <a name="troubleshooting"></a>Устранение неполадок

Ниже приведено пошаговое руководство по устранению неполадок для мониторинга на основе расширений для приложений .NET, работающих на виртуальных машинах Azure и масштабируемых наборах виртуальных машин Azure.

> [!NOTE]
> Приложения .NET Core, Java и Node. js поддерживаются только на виртуальных машинах Azure и масштабируемых наборах виртуальных машин Azure с помощью инструментирования вручную на основе пакета SDK, поэтому приведенные ниже действия не применяются к этим сценариям.

Выходные данные выполнения расширения регистрируются в файле, расположенном в следующих каталогах:
```Windows
C:\WindowsAzure\Logs\Plugins\Microsoft.Azure.Diagnostics.ApplicationMonitoringWindows\<version>\
```

## <a name="next-steps"></a>Следующие шаги
* Узнайте, как [развернуть приложение в масштабируемом наборе виртуальных машин Azure](../../virtual-machine-scale-sets/virtual-machine-scale-sets-deploy-app.md).
* [Настройте веб-тесты доступности](monitor-web-app-availability.md) , которые будут оповещены, если ваша конечная точка не работает.
