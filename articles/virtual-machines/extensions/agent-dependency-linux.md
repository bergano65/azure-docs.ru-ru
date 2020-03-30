---
title: Расширение виртуальной машины Azure Monitor Зависимость для Linux
description: Развертывание агента зависимости Azure Monitor на виртуальной машине Linux с помощью виртуального расширения машины.
services: virtual-machines-linux
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 03/29/2019
ms.author: magoedte
ms.openlocfilehash: 82f9c5a67cb056752cf8310be3b7c9f0bd2501e9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79254044"
---
# <a name="azure-monitor-dependency-virtual-machine-extension-for-linux"></a>Расширение виртуальной машины Azure Monitor Зависимость для Linux

Функция схемы в Azure Monitor для виртуальных машин получает данные от Microsoft Dependency Agent. Виртуальное расширение виртуальной машины для Linux-агента Azure VM-агента публикуется и поддерживается корпорацией Майкрософт. Расширение устанавливает агент зависимости на виртуальные машины Azure. В этом документе подробно описаны поддерживаемые платформы, конфигурации и варианты развертывания виртуального расширения виртуальной машины Для Linux агента по зависимости Azure VM.

## <a name="prerequisites"></a>Предварительные требования

### <a name="operating-system"></a>Операционная система

Расширение агента зависимых от Azure VM для Linux может быть запущено против поддерживаемых операционных систем, перечисленных в разделе [Поддерживаемые операционные системы](../../azure-monitor/insights/vminsights-enable-overview.md#supported-operating-systems) статьи о развертывании Azure Monitor для развертывания VMs.

## <a name="extension-schema"></a>Схема расширения

Следующие JSON показывает схему расширения агента зависимого от Azure VM на Azure Linux VM. 

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

| name | Значение/Пример |
| ---- | ---- |
| версия_API | 2015-01-01 |
| publisher | Microsoft.Azure.Monitoring.DependencyAgent |
| type | ЗависимостьAgentLinux |
| typeHandlerVersion | 9,5 |

## <a name="template-deployment"></a>Развертывание шаблона

Вы можете развернуть расширения виртуальной машины Azure с помощью шаблонов Azure Resource Manager. Схему JSON, описанную в предыдущем разделе в шаблоне диспетчера ресурсов Azure, можно использовать для запуска расширения агента зависимых от Azm во время развертывания шаблона шаблона управления ресурсами Azure.

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
        "type": "DependencyAgentLinux",
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
        "type": "DependencyAgentLinux",
        "typeHandlerVersion": "9.5",
        "autoUpgradeMinorVersion": true
    }
}
```

## <a name="azure-cli-deployment"></a>Развертывание с помощью Azure CLI

Для развертывания VM-расширения агента зависимости в существующую виртуальную машину можно использовать Azure CLI.  

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

Данные о состоянии развертывания расширений можно получить с портала Azure и с помощью Azure CLI. Чтобы увидеть состояние развертывания расширений для данного VM, запустите следующую команду с помощью Azure CLI:

```azurecli
az vm extension list --resource-group myResourceGroup --vm-name myVM -o table
```

Выходные данные выполнения расширения регистрируются в следующем файле:

```
/opt/microsoft/dependcency-agent/log/install.log 
```

### <a name="support"></a>Поддержка

Если вам нужна дополнительная помощь в какой-либо момент этой статьи, обратитесь к экспертам Azure на [форумах MSDN Azure и Stack Overflow.](https://azure.microsoft.com/support/forums/) Или можно подать инцидент поддержки Azure. Перейдите на [сайт поддержки Azure](https://azure.microsoft.com/support/options/) и щелкните **Получить поддержку**. Для получения информации о том, как использовать поддержку Azure, прочитайте [часто задаваемые вопросы поддержки Microsoft Azure.](https://azure.microsoft.com/support/faq/)
