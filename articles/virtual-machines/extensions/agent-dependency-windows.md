---
title: Расширение виртуальной машины Azure Monitor зависимостей для Windows | Документация Майкрософт
description: Развертывание агента зависимостей Azure Monitor на виртуальной машине Windows с помощью расширения виртуальной машины.
services: virtual-machines-windows
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 03/29/2019
ms.author: magoedte
ms.openlocfilehash: 5913e33bc29541adfa1599ad5413ad3702635740
ms.sourcegitcommit: 4b5dcdcd80860764e291f18de081a41753946ec9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/03/2019
ms.locfileid: "68775431"
---
# <a name="azure-monitor-dependency-virtual-machine-extension-for-windows"></a>Расширение виртуальной машины зависимостей Azure Monitor для Windows

Функция схемы в Azure Monitor для виртуальных машин получает данные от Microsoft Dependency Agent. Расширение виртуальной машины агента зависимостей Azure для Windows опубликовано и поддерживается корпорацией Майкрософт. Расширение устанавливает агент зависимостей на виртуальных машинах Azure. В этом документе подробно описаны поддерживаемые платформы, конфигурации и варианты развертывания для расширения виртуальной машины агента зависимостей виртуальных машин Azure для Windows.

## <a name="prerequisites"></a>Предварительные требования

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

### <a name="operating-system"></a>Операционная система

Расширение агента зависимостей виртуальной машины Azure для Windows можно выполнить для поддерживаемых операционных систем, перечисленных в разделе " [Поддерживаемые операционные системы](../../azure-monitor/insights/vminsights-enable-overview.md#supported-operating-systems) " статьи о развертывании Azure Monitor для виртуальных машин.

## <a name="extension-schema"></a>Схема расширения

В следующем JSON показана схема для расширения агента зависимостей виртуальной машины Azure на виртуальной машине Windows Azure.

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

| Название | Значение/пример |
| ---- | ---- |
| apiVersion | 2015-01-01 |
| publisher | Microsoft. Azure. Monitoring. Депенденциажент |
| type | депенденциажентвиндовс |
| typeHandlerVersion | 9.5 |

## <a name="template-deployment"></a>Развертывание шаблона

Расширения виртуальных машин Azure можно развернуть с помощью шаблонов Azure Resource Manager. Вы можете использовать схему JSON, описанную в предыдущем разделе в шаблоне Azure Resource Manager, чтобы запустить расширение агента зависимостей виртуальной машины Azure во время развертывания шаблона Azure Resource Manager.

JSON для расширения виртуальной машины можно вложить в ресурс виртуальной машины. Вы также можете поместить его на корневом или верхнем уровне шаблона диспетчер ресурсов JSON. Размещение JSON влияет на значения имени и типа ресурса. Дополнительные сведения см. в разделе [Указание имени и типа дочернего ресурса в шаблоне Resource Manager](../../azure-resource-manager/child-resource-name-type.md).

В следующем примере предполагается, что расширение агента зависимостей вложено в ресурс виртуальной машины. При вложении ресурса расширения JSON помещается в `"resources": []` объект виртуальной машины.


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

При помещении JSON расширения в корень шаблона имя ресурса включает ссылку на родительскую виртуальную машину. Тип отражает вложенную конфигурацию.

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

С помощью `Set-AzVMExtension` команды можно развернуть расширение виртуальной машины агента зависимостей на существующей виртуальной машине. Перед выполнением команды необходимо сохранить общедоступные и частные конфигурации в хэш-таблице PowerShell.

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

Данные о состоянии развертываний расширений можно получить из портал Azure и с помощью модуля Azure PowerShell. Чтобы просмотреть состояние развертывания расширений для данной виртуальной машины, выполните следующую команду с помощью модуля Azure PowerShell:

```powershell
Get-AzVMExtension -ResourceGroupName myResourceGroup -VMName myVM -Name myExtensionName
```

Выходные данные выполнения расширения регистрируются в файле, расположенном в следующем каталоге:

```cmd
C:\WindowsAzure\Logs\Plugins\Microsoft.Azure.Monitoring.DependencyAgent\
```

### <a name="support"></a>Поддержка

Если в любой момент при изучении этой статьи вам потребуется дополнительная помощь, вы можете обратиться к экспертам по Azure на [форумах MSDN Azure и Stack Overflow](https://azure.microsoft.com/support/forums/). Вы также можете зафайлировать инцидент службы поддержки Azure. Перейдите на [сайт поддержки Azure](https://azure.microsoft.com/support/options/) и щелкните **Получить поддержку**. Сведения об использовании поддержки Azure см. в статье [часто задаваемые вопросы о поддержке Microsoft Azure](https://azure.microsoft.com/support/faq/).
