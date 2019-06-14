---
title: Расширение виртуальной машины Azure монитор зависимостей для Linux | Документация Майкрософт
description: Разверните агент зависимостей монитор Azure на виртуальной машине Linux с помощью расширения виртуальной машины.
services: virtual-machines-linux
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 03/29/2019
ms.author: magoedte
ms.openlocfilehash: 750393e6dba17ab8ba024f9f1fbb2f9127dd81ab
ms.sourcegitcommit: bb85a238f7dbe1ef2b1acf1b6d368d2abdc89f10
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/10/2019
ms.locfileid: "65521706"
---
# <a name="azure-monitor-dependency-virtual-machine-extension-for-linux"></a>Расширение виртуальной машины Azure монитор зависимостей для Linux

Функция схемы в Azure Monitor для виртуальных машин получает данные от Microsoft Dependency Agent. Расширение виртуальной машины агента зависимостей виртуальной Машины Azure для Linux публикации и поддерживается корпорацией Майкрософт. Расширение устанавливает агент зависимостей на виртуальных машинах Azure. В этом документе описаны поддерживаемые платформы, конфигурации и параметры развертывания для расширения виртуальной машины агента зависимостей виртуальной Машины Azure для Linux.

## <a name="prerequisites"></a>Технические условия

### <a name="operating-system"></a>Операционная система

Расширение агента зависимостей виртуальной Машины Azure для Linux может выполняться для поддерживаемых операционных систем, перечисленных в [поддерживаемые операционные системы](../../azure-monitor/insights/vminsights-enable-overview.md#supported-operating-systems) раздел Azure Monitor для развертывания виртуальных машин Azure.

## <a name="extension-schema"></a>Схема расширения

Приведенный ниже код JSON показана схема для расширения агента зависимостей виртуальной Машины Azure на виртуальной Машине Azure Linux. 

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
  "parameters": {
    "vmName": {
      "type": "string",
      "metadata": {
        "description": "The name of existing Linux Azure VM."
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
        "type": "DependencyAgentLinux",
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

| ИМЯ | Значение и пример |
| ---- | ---- |
| apiVersion | 2015-01-01 |
| publisher | Microsoft.Azure.Monitoring.DependencyAgent |
| type | DependencyAgentLinux |
| typeHandlerVersion | 9,5 |

## <a name="template-deployment"></a>Развертывание шаблона

Расширения виртуальной машины Azure можно развернуть с помощью шаблонов Azure Resource Manager. Для запуска расширения агента зависимостей виртуальной Машины Azure во время развертывания шаблона Azure Resource Manager в шаблоне Azure Resource Manager можно использовать схему JSON, описанную в предыдущем разделе. 

JSON для расширения виртуальной машины можно вложить в ресурс виртуальной машины или поместить в корень или на верхний уровень JSON-файла шаблона Resource Manager. Размещение JSON влияет на значения имени и типа ресурса. Дополнительные сведения см. в разделе [Указание имени и типа дочернего ресурса в шаблоне Resource Manager](../../azure-resource-manager/resource-group-authoring-templates.md#child-resources). 

В следующем примере предполагается, что расширение агента зависимостей вложено в ресурс виртуальной машины. При вложении ресурса расширения JSON помещается в объект `"resources": []` виртуальной машины.


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
        "type": "DependencyAgentLinux",
        "typeHandlerVersion": "9.5",
        "autoUpgradeMinorVersion": true
    }
}
```

При размещении JSON расширения в корне шаблона имя ресурса содержит ссылку на родительскую виртуальную машину, а тип отражает вложенную конфигурацию. 

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
        "type": "DependencyAgentLinux",
        "typeHandlerVersion": "9.5",
        "autoUpgradeMinorVersion": true
    }
}
```

## <a name="azure-cli-deployment"></a>Развертывание с помощью Azure CLI

Azure CLI можно использовать для развертывания расширения виртуальной Машины агента зависимостей на существующей виртуальной машины.  

```azurecli

az vm extension set \
    --resource-group myResourceGroup \
    --vm-name myVM \
    --name DependencyAgentLinux \
    --publisher Microsoft.Azure.Monitoring.DependencyAgent \
    --version 9.5 
```

## <a name="troubleshoot-and-support"></a>Устранение неполадок и поддержка

### <a name="troubleshoot"></a>Устранение неполадок

Данные о состоянии развертывания расширения можно получить на портале Azure, а также использовав Azure CLI. Чтобы просмотреть состояние развертывания расширений для определенной виртуальной машины, выполните следующую команду в Azure CLI.

```azurecli
az vm extension list --resource-group myResourceGroup --vm-name myVM -o table
```

Выходные данные выполнения расширения регистрируются в следующем файле:

```
/opt/microsoft/dependcency-agent/log/install.log 
```

### <a name="support"></a>Поддержка

Если в любой момент при изучении этой статьи вам потребуется дополнительная помощь, вы можете обратиться к экспертам по Azure на [форумах MSDN Azure и Stack Overflow](https://azure.microsoft.com/support/forums/). Кроме того, можно зарегистрировать обращение в службу поддержки Azure. Перейдите на [сайт поддержки Azure](https://azure.microsoft.com/support/options/) и щелкните "Получить поддержку". Дополнительные сведения об использовании службы поддержки Azure см. в статье [Часто задаваемые вопросы о поддержке Microsoft Azure](https://azure.microsoft.com/support/faq/).
