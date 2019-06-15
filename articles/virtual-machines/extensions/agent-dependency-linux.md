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
ms.openlocfilehash: 5faeebe799bd8cc0ba9a148508ac5b3a6d4b803a
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/13/2019
ms.locfileid: "67120211"
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

| ИМЯ | Значение/Example |
| ---- | ---- |
| apiVersion | 2015-01-01 |
| publisher | Microsoft.Azure.Monitoring.DependencyAgent |
| type | DependencyAgentLinux |
| typeHandlerVersion | 9,5 |

## <a name="template-deployment"></a>Развертывание шаблона

Вы можете развернуть расширения виртуальной машины Azure с помощью шаблонов Azure Resource Manager. Для запуска расширения агента зависимостей виртуальной Машины Azure во время развертывания шаблона Azure Resource Manager можно использовать схему JSON, описанную в предыдущем разделе в шаблоне Azure Resource Manager.

JSON для расширения виртуальной машины могут быть вложенными в ресурс виртуальной машины. Или его можно поместить в корень или на верхний уровень шаблона Resource Manager в формате JSON. Размещение JSON влияет на значения имени и типа ресурса. Дополнительные сведения см. в разделе [Указание имени и типа дочернего ресурса в шаблоне Resource Manager](../../azure-resource-manager/resource-group-authoring-templates.md#child-resources).

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
        "type": "DependencyAgentLinux",
        "typeHandlerVersion": "9.5",
        "autoUpgradeMinorVersion": true
    }
}
```

При размещении JSON расширения в корне шаблона, имя ресурса содержит ссылку на родительскую виртуальную машину. Тип отражает вложенную конфигурацию. 

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

Чтобы развернуть агент зависимостей расширения виртуальной Машины к существующей виртуальной машины можно использовать Azure CLI.  

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

Данные о состоянии развертывания расширения можно получить на портале Azure, а также с помощью Azure CLI. Чтобы просмотреть состояние развертывания расширений для определенной виртуальной Машины, выполните следующую команду с помощью Azure CLI:

```azurecli
az vm extension list --resource-group myResourceGroup --vm-name myVM -o table
```

Выходные данные выполнения расширения регистрируются в следующем файле:

```
/opt/microsoft/dependcency-agent/log/install.log 
```

### <a name="support"></a>Поддержка

Если вам нужна дополнительная помощь в любом месте в этой статье, обратиться к экспертам по Azure на [форумах MSDN Azure и Stack Overflow](https://azure.microsoft.com/support/forums/). Или вы можете отправить обращение за поддержкой Azure. Перейдите на [сайт поддержки Azure](https://azure.microsoft.com/support/options/) и щелкните **Получить поддержку**. Сведения о том, как использовать Azure поддерживают [поддержки Microsoft Azure — часто задаваемые вопросы о](https://azure.microsoft.com/support/faq/).
