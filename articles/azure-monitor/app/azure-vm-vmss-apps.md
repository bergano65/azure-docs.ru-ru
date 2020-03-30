---
title: Мониторинг производительности на M-м ими Azure - Azure Application Insights
description: Мониторинг производительности приложений для виртуальных машинных наборов Azure VM и Azure. Диаграмма нагрузки и времени отклика, информация о зависимости и набор оповещений о производительности.
ms.topic: conceptual
ms.date: 08/26/2019
ms.openlocfilehash: d75e14dccef565f0029d06583e74d5693726dd99
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77661334"
---
# <a name="deploy-the-azure-monitor-application-insights-agent-on-azure-virtual-machines-and-azure-virtual-machine-scale-sets"></a>Развертывание агента Azure Monitor Application Insight на виртуальных машинах Azure и наборах виртуальных машин Azure

Включение мониторинга на основе веб-приложений на основе .NET, работающих на [виртуальных машинах Azure](https://azure.microsoft.com/services/virtual-machines/) и [наборах виртуальных машин Azure,](https://docs.microsoft.com/azure/virtual-machine-scale-sets/) теперь стало проще, чем когда-либо. Получите все преимущества использования Application Insights без изменения кода.

Эта статья позволяет использовать мониторинг Application Insights с помощью агента Application Insights и предоставляет предварительные рекомендации по автоматизации процесса для крупномасштабных развертываний.

> [!IMPORTANT]
> В настоящее время Azure Application Insights Agent для .NET находится в открытом доступе.
> Эта предварительная версия предоставляется без соглашения об уровне обслуживания, и мы не рекомендуем ее для производственных нагрузок. Некоторые функции могут быть не поддерживаемыми, а некоторые могут иметь ограниченные возможности.
> Дополнительные сведения см. в статье [Дополнительные условия использования предварительных выпусков Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="enable-application-insights"></a>Включение Application Insights

Существует два способа обеспечения мониторинга приложений для виртуальных машин Azure и наборов виртуальных машин Azure:

* **Безкодава** через агента По анализу приложений
    * Этот метод является самым простым для включения, и не требуется продвинутая конфигурация. Его часто называют мониторингом "времени выполнения".

    * Для виртуальных машин Azure и наборов виртуальных машин Azure мы рекомендуем как минимум включить такой уровень мониторинга. После этого, на основе конкретного сценария, вы можете оценить, требуется ли ручное оборудование.

    * Агент Application Insights автоматически собирает те же сигналы зависимости, что и .NET SDK. Подробнее об этом читайте в [автосборе зависимостей.](https://docs.microsoft.com/azure/azure-monitor/app/auto-collect-dependencies#net)
        > [!NOTE]
        > В настоящее время поддерживаются только приложения, размещенные на IIS .Net. Используйте SDK для инструментирования ASP.NET приложений Core, Java и Node.js, размещенных на виртуальных машинах Azure и виртуальных наборах машин.

* **Код-на основе** через SDK

    * Этот подход гораздо более настраиваемый, но он требует [добавления зависимости от пакетов Application Insights SDK NuGet.](https://docs.microsoft.com/azure/azure-monitor/app/asp-net) Этот метод также означает, что вы должны управлять обновлениями последней версии пакетов самостоятельно.

    * Если вам нужно сделать пользовательские вызовы API для отслеживания событий/зависимостей, не захваченных по умолчанию, с помощью мониторинга на основе агента, необходимо использовать этот метод. Ознакомьтесь с [API для пользовательских событий и метрик статьи,](https://docs.microsoft.com/azure/azure-monitor/app/api-custom-events-metrics) чтобы узнать больше.

> [!NOTE]
> Если как мониторинг на основе агента, так и ручные приборы на основе SDK обнаруживаются только настройки ручного прибора. Это необходимо для предотвращения отправки дубликатов данных. Чтобы узнать больше об этом проверить [раздел устранения неполадок](#troubleshooting) ниже.

## <a name="manage-application-insights-agent-for-net-applications-on-azure-virtual-machines-using-powershell"></a>Управление приложениями Insights Agent для приложений .NET на виртуальных машинах Azure с помощью PowerShell

> [!NOTE]
> Перед установкой агента Application Insights вам понадобится строка подключения. [Создайте новый ресурс Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/create-new-resource) или скопируйте строку соединения из существующего ресурса сведения о приложениях.

> [!NOTE]
> Новичок в силовой оболочке? Проверьте [Получить Начал Руководство](https://docs.microsoft.com/powershell/azure/get-started-azureps?view=azps-2.5.0).

Установка или обновление агента Application Insights в качестве расширения для виртуальных машин Azure
```powershell
$publicCfgJsonString = '
{
  "redfieldConfiguration": {
    "instrumentationKeyMap": {
      "filters": [
        {
          "appFilter": ".*",
          "machineFilter": ".*",
          "virtualPathFilter": ".*",
          "instrumentationSettings" : {
            "connectionString": "InstrumentationKey=xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
          }
        }
      ]
    }
  }
}
';
$privateCfgJsonString = '{}';

Set-AzVMExtension -ResourceGroupName "<myVmResourceGroup>" -VMName "<myVmName>" -Location "<myVmLocation>" -Name "ApplicationMonitoring" -Publisher "Microsoft.Azure.Diagnostics" -Type "ApplicationMonitoringWindows" -Version "2.8" -SettingString $publicCfgJsonString -ProtectedSettingString $privateCfgJsonString
```

> [!NOTE]
> Вы можете установить или обновить агент Application Insights в качестве расширения в нескольких виртуальных машинах в масштабе с помощью цикла Powershell.

Удаление расширения агента application Insights из виртуальной машины Azure
```powershell
Remove-AzVMExtension -ResourceGroupName "<myVmResourceGroup>" -VMName "<myVmName>" -Name "ApplicationMonitoring"
```

Статус расширения агента по поиску приложений для виртуальной машины Azure
```powershell
Get-AzVMExtension -ResourceGroupName "<myVmResourceGroup>" -VMName "<myVmName>" -Name ApplicationMonitoring -Status
```

Получите список установленных расширений для виртуальной машины Azure
```powershell
Get-AzResource -ResourceId "/subscriptions/<mySubscriptionId>/resourceGroups/<myVmResourceGroup>/providers/Microsoft.Compute/virtualMachines/<myVmName>/extensions"

# Name              : ApplicationMonitoring
# ResourceGroupName : <myVmResourceGroup>
# ResourceType      : Microsoft.Compute/virtualMachines/extensions
# Location          : southcentralus
# ResourceId        : /subscriptions/<mySubscriptionId>/resourceGroups/<myVmResourceGroup>/providers/Microsoft.Compute/virtualMachines/<myVmName>/extensions/ApplicationMonitoring
```
Вы также можете просматривать установленные расширения в [лезвии виртуальной машины Azure](https://docs.microsoft.com/azure/virtual-machines/extensions/overview) в портале.

> [!NOTE]
> Проверьте установку, нажав на Live Metrics Stream в ресурсе Application Insights, связанном со строкой подключения, используемой для развертывания расширения агента Application Insights. Если вы отправляете данные из нескольких виртуальных машин, выберите целевые виртуальные машины Azure под названием сервера. Начало потока данных может занять до минуты.

## <a name="manage-application-insights-agent-for-net-applications-on-azure-virtual-machine-scale-sets-using-powershell"></a>Управление application Insights Agent для приложений .NET на наборах виртуальных машин Azure с помощью powershell

Установка или обновление агента Application Insights в качестве расширения для набора виртуальных машин Azure
```powershell
$publicCfgHashtable =
@{
  "redfieldConfiguration"= @{
    "instrumentationKeyMap"= @{
      "filters"= @(
        @{
          "appFilter"= ".*";
          "machineFilter"= ".*";
          "virtualPathFilter": ".*",
          "instrumentationSettings" : {
            "connectionString": "InstrumentationKey=xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx" # Application Insights connection string, create new Application Insights resource if you don't have one. https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/microsoft.insights%2Fcomponents
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

Расширение мониторинга приложений для удаления из наборов виртуальных машин Azure
```powershell
$vmss = Get-AzVmss -ResourceGroupName "<myResourceGroup>" -VMScaleSetName "<myVmssName>"

Remove-AzVmssExtension -VirtualMachineScaleSet $vmss -Name "ApplicationMonitoring"

Update-AzVmss -ResourceGroupName $vmss.ResourceGroupName -Name $vmss.Name -VirtualMachineScaleSet $vmss

# Note: depending on your update policy, you might need to run Update-AzVmssInstance for each instance
```

Статус расширения расширения приложения для запросов для наборов виртуальных машин Azure
```powershell
# Not supported by extensions framework
```

Получите список установленных расширений для наборов виртуальных машин Azure
```powershell
Get-AzResource -ResourceId /subscriptions/<mySubscriptionId>/resourceGroups/<myResourceGroup>/providers/Microsoft.Compute/virtualMachineScaleSets/<myVmssName>/extensions

# Name              : ApplicationMonitoringWindows
# ResourceGroupName : <myResourceGroup>
# ResourceType      : Microsoft.Compute/virtualMachineScaleSets/extensions
# Location          :
# ResourceId        : /subscriptions/<mySubscriptionId>/resourceGroups/<myResourceGroup>/providers/Microsoft.Compute/virtualMachineScaleSets/<myVmssName>/extensions/ApplicationMonitoringWindows
```

## <a name="troubleshooting"></a>Устранение неполадок

Найдите советы по устранению неполадок для расширения агента по мониторингу приложений .NET для приложений .NET, работающих на виртуальных машинах Azure и виртуальных наборах машин.

> [!NOTE]
> Приложения .NET Core, Java и Node.js поддерживаются только на виртуальных машинах Azure и наборах виртуальных машин Azure с помощью ручных приборов на основе SDK, и поэтому приведенные ниже шаги не применяются к этим сценариям.

Выходные данные выполнения расширения регистрируются в файле, расположенном в следующих каталогах:
```Windows
C:\WindowsAzure\Logs\Plugins\Microsoft.Azure.Diagnostics.ApplicationMonitoringWindows\<version>\
```

## <a name="next-steps"></a>Дальнейшие действия
* Узнайте, как [развернуть приложение в наборе виртуальной машины Azure.](../../virtual-machine-scale-sets/virtual-machine-scale-sets-deploy-app.md)
* [Настройка веб-тестов доступности](monitor-web-app-availability.md) для оповещения, если ваша конечная точка не работает.
