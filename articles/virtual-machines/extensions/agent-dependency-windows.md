---
title: Виртуальное расширение зависимости от azure Monitor для Windows
description: Развертывание агента зависимости Azure Monitor на виртуальной машине Windows с помощью виртуального расширения машины.
services: virtual-machines-windows
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 03/29/2019
ms.author: magoedte
ms.openlocfilehash: 27d43af2d5860d287d8b5914379747ae528db34b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79250677"
---
# <a name="azure-monitor-dependency-virtual-machine-extension-for-windows"></a>Виртуальное расширение зависимости от azure Monitor для Windows

Функция схемы в Azure Monitor для виртуальных машин получает данные от Microsoft Dependency Agent. Виртуальное расширение виртуальной машины для Windows, разработанное и поддерживаемое корпорацией Майкрософт, публикуется и поддерживается агентом Azure VM. Расширение устанавливает агент зависимости на виртуальные машины Azure. В этом документе подробно описаны поддерживаемые платформы, конфигурации и варианты развертывания виртуального удлинительа зависимого агента Azure VM для Windows.

## <a name="operating-system"></a>Операционная система

Расширение агента зависимых от Azure VM для Windows может быть запущено против поддерживаемых операционных систем, перечисленных в разделе [Поддерживаемые операционные системы](../../azure-monitor/insights/vminsights-enable-overview.md#supported-operating-systems) статьи azure Monitor для развертывания VMs.

## <a name="extension-schema"></a>Схема расширения

Ниже приведена схема JSON для расширения агента зависимости Azure VM на VM-m Сwindows.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
  "parameters": {
    "vmName": {
      "type": "string",
      "metadata": {
        "description": "The name of existing Azure VM. Supported Windows Server versions:  2008 R2 and above (x64)."
      }
    }
  },
  "variables": {
    "vmExtensionsApiVersion": "2017-03-30"
  },
  "resources": [
    {
      "type": "Microsoft.Compute/virtualMachines/extensions",
      "name": "[concat(parameters('vmName'),'/DAExtension')]",
      "apiVersion": "[variables('vmExtensionsApiVersion')]",
      "location": "[resourceGroup().location]",
      "dependsOn": [
      ],
      "properties": {
        "publisher": "Microsoft.Azure.Monitoring.DependencyAgent",
        "type": "DependencyAgentWindows",
        "typeHandlerVersion": "9.5",
        "autoUpgradeMinorVersion": true
      }
    }
  ],
    "outputs": {
    }
}
```

### <a name="property-values"></a>Значения свойств

| name | Значение/Пример |
| ---- | ---- |
| версия_API | 2015-01-01 |
| publisher | Microsoft.Azure.Monitoring.DependencyAgent |
| type | DependencyAgentWindows |
| typeHandlerVersion | 9,5 |

## <a name="template-deployment"></a>Развертывание шаблона

Вы можете развернуть расширения Azure VM с шаблонами управления ресурсами Azure. Схему JSON, описанную в предыдущем разделе в шаблоне диспетчера ресурсов Azure, можно использовать для запуска расширения агента зависимых от Azm во время развертывания шаблона шаблона управления ресурсами Azure.

JSON для виртуального расширения машины может быть вложен внутри ресурса виртуальной машины. Или вы можете разместить его на корне или верхнем уровне шаблона менеджера ресурсов JSON. Размещение JSON влияет на значения имени и типа ресурса. Для получения дополнительной информации [см.](../../azure-resource-manager/templates/child-resource-name-type.md)

Следующий пример предполагает, что расширение агента зависимости вложено внутри ресурса виртуальной машины. При размещении ресурса расширения JSON `"resources": []` помещается в объект виртуальной машины.


```json
{
    "type": "extensions",
    "name": "DAExtension",
    "apiVersion": "[variables('apiVersion')]",
    "location": "[resourceGroup().location]",
    "dependsOn": [
        "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'))]"
    ],
    "properties": {
        "publisher": "Microsoft.Azure.Monitoring.DependencyAgent",
        "type": "DependencyAgentWindows",
        "typeHandlerVersion": "9.5",
        "autoUpgradeMinorVersion": true
    }
}
```

При помещании расширения JSON в корне шаблона имя ресурса включает ссылку на материнскую виртуальную машину. Тип отражает вложенную конфигурацию.

```json
{
    "type": "Microsoft.Compute/virtualMachines/extensions",
    "name": "<parentVmResource>/DAExtension",
    "apiVersion": "[variables('apiVersion')]",
    "location": "[resourceGroup().location]",
    "dependsOn": [
        "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'))]"
    ],
    "properties": {
        "publisher": "Microsoft.Azure.Monitoring.DependencyAgent",
        "type": "DependencyAgentWindows",
        "typeHandlerVersion": "9.5",
        "autoUpgradeMinorVersion": true
    }
}
```

## <a name="powershell-deployment"></a>Развертывание с помощью PowerShell

Вы можете `Set-AzVMExtension` использовать команду для развертывания виртуального расширения виртуальной машины агента зависимости в существующую виртуальную машину. Перед запуском команды общедоступные и частные конфигурации должны храниться в таблице хэш-данных PowerShell.

```powershell

Set-AzVMExtension -ExtensionName "Microsoft.Azure.Monitoring.DependencyAgent" `
    -ResourceGroupName "myResourceGroup" `
    -VMName "myVM" `
    -Publisher "Microsoft.Azure.Monitoring.DependencyAgent" `
    -ExtensionType "DependencyAgentWindows" `
    -TypeHandlerVersion 9.5 `
    -Location WestUS 
```

## <a name="troubleshoot-and-support"></a>Устранение неполадок и поддержка

### <a name="troubleshoot"></a>Устранение неполадок

Данные о состоянии развертывания расширений можно получить с портала Azure и с помощью модуля Azure PowerShell. Чтобы увидеть состояние развертывания расширений для данного VM, запустите следующую команду с помощью модуля Azure PowerShell:

```powershell
Get-AzVMExtension -ResourceGroupName myResourceGroup -VMName myVM -Name myExtensionName
```

Выходные данные выполнения расширения регистрируются в файле, расположенном в следующем каталоге:

```cmd
C:\WindowsAzure\Logs\Plugins\Microsoft.Azure.Monitoring.DependencyAgent\
```

### <a name="support"></a>Поддержка

Если вам нужна дополнительная помощь в какой-либо момент этой статьи, вы можете связаться с экспертами Azure на [форумах MSDN Azure и Stack Overflow.](https://azure.microsoft.com/support/forums/) Или можно подать инцидент поддержки Azure. Перейдите на [сайт поддержки Azure](https://azure.microsoft.com/support/options/) и щелкните **Получить поддержку**. Для получения информации о том, как использовать поддержку Azure, прочитайте [часто задаваемые вопросы поддержки Microsoft Azure.](https://azure.microsoft.com/support/faq/)
