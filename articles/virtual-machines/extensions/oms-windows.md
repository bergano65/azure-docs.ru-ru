---
title: Расширение виртуальной машины Azure Monitor для Windows | Документация Майкрософт
description: Развертывание агента Log Analytics на виртуальной машине Windows с помощью расширения виртуальной машины.
services: virtual-machines-windows
documentationcenter: ''
author: roiyz-msft
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: feae6176-2373-4034-b5d9-a32c6b4e1f10
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 08/12/2019
ms.author: roiyz
ms.openlocfilehash: 393b01d7815d94e4ad623c552f4110421c9d98bd
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/28/2019
ms.locfileid: "70092253"
---
# <a name="azure-monitor-virtual-machine-extension-for-windows"></a>Расширение виртуальной машины Azure Monitor для Windows

Журналы Azure Monitor предоставляют возможности мониторинга в облачных и локальных ресурсах. Расширение виртуальной машины агента Log Analytics для Windows предоставляет и поддерживает корпорация Майкрософт. Это расширение устанавливает агент Log Analytics на виртуальных машинах Azure и регистрирует виртуальные машины в существующей рабочей области Log Analytics. В этом документе описаны поддерживаемые платформы, конфигурации и параметры развертывания для Azure Monitor расширения виртуальной машины для Windows.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="prerequisites"></a>Предварительные требования

### <a name="operating-system"></a>Операционная система

Дополнительные сведения о поддерживаемых операционных системах Windows см. в статье [Обзор агента log Analytics](../../azure-monitor/platform/log-analytics-agent.md#supported-windows-operating-systems) .

### <a name="agent-and-vm-extension-version"></a>Версия агента и расширения виртуальной машины
В следующей таблице представлено сопоставление версии расширения виртуальной машины Windows Azure Monitor и пакета Log Analytics агента для каждого выпуска. 

| Log Analytics версии пакета агента Windows | Версия расширения виртуальной машины Windows Azure Monitor | Дата выпуска | Заметки о выпуске |
|--------------------------------|--------------------------|--------------------------|--------------------------|
| 10.20.18011 | 1.0.18011 | 2019 июля | <ul><li> Исправления незначительных ошибок и усовершенствования стабилизации </li><li> Увеличено Максекспрессиондепс до 10000 </li></ul> |
| 10.20.18001 | 1.0.18001 | Июнь 2019 г. | <ul><li> Исправления незначительных ошибок и усовершенствования стабилизации </li><li> Добавлена возможность отключения учетных данных по умолчанию при создании прокси-соединения (поддержка WINHTTP_AUTOLOGON_SECURITY_LEVEL_HIGH). </li></ul>|
| 10.19.13515 | 1.0.13515 | Март 2019 г. | <ul><li>Незначительные исправления стабилизации </li></ul> |
| 10.19.10006 | Н/Д | Dec 2018 | <ul><li> Незначительные исправления стабилизации </li></ul> | 
| 8.0.11136 | Н/Д | Сентябрь 2018 |  <ul><li> Добавлена поддержка обнаружения изменения идентификатора ресурса при перемещении виртуальной машины. </li><li> Добавлена поддержка для идентификатора ресурса отчетов при использовании установки без расширения. </li></ul>| 
| 8.0.11103 | Н/Д |  Апрель 2018 г. | |
| 8.0.11081 | 1.0.11081 | 2017 ноября | | 
| 8.0.11072 | 1.0.11072 | Сентябрь 2017 | |
| 8.0.11049 | 1.0.11049 | Фев 2017 | |

### <a name="azure-security-center"></a>Центр безопасности Azure

Центр безопасности Azure автоматически подготавливает агент Log Analytics и подключает его к рабочей области Log Analytics по умолчанию подписки Azure. Если вы используете центр безопасности Azure, не выполняйте инструкции в этом документе. Это приведет к перезаписи настроенной рабочей области и разрыву подключения с центром безопасности Azure.

### <a name="internet-connectivity"></a>Подключение к Интернету
Для расширения агента Log Analytics для Windows требуется, чтобы целевая виртуальная машина была подключена к Интернету. 

## <a name="extension-schema"></a>Схема расширения

В следующем объекте JSON показана схема для расширения агента Log Analytics. Для расширения требуется идентификатор рабочей области и ключ рабочей области из целевой Log Analytics рабочей области. Их можно найти в разделе параметров рабочей области на портале Azure. Так как ключ рабочей области должен рассматриваться в качестве конфиденциальных данных, его следует хранить в защищенной конфигурации параметров. Данные защищенных параметров расширения виртуальной машины Azure зашифрованы. Они расшифровываются только на целевой виртуальной машине. Обратите внимание, что в **workspaceId** и **workspaceKey** учитывается регистр знаков.

```json
{
    "type": "extensions",
    "name": "OMSExtension",
    "apiVersion": "[variables('apiVersion')]",
    "location": "[resourceGroup().location]",
    "dependsOn": [
        "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'))]"
    ],
    "properties": {
        "publisher": "Microsoft.EnterpriseCloud.Monitoring",
        "type": "MicrosoftMonitoringAgent",
        "typeHandlerVersion": "1.0",
        "autoUpgradeMinorVersion": true,
        "settings": {
            "workspaceId": "myWorkSpaceId"
        },
        "protectedSettings": {
            "workspaceKey": "myWorkspaceKey"
        }
    }
}
```
### <a name="property-values"></a>Значения свойств

| Название | Значение и пример |
| ---- | ---- |
| apiVersion | 2015-06-15 |
| publisher | Microsoft.EnterpriseCloud.Monitoring |
| type | MicrosoftMonitoringAgent |
| typeHandlerVersion | 1.0 |
| workspaceId (пример)* | 6f680a37-00c6-41c7-a93f-1437e3462574 |
| workspaceKey (пример) | z4bU3p1/GrnWpQkky4gdabWXAhbWSTz70hm4m2Xt92XI+rSRgE8qVvRhsGo9TXffbrTahyrwv35W0pOqQAU7uQ== |

\* В API-интерфейсе Log Analytics параметр workspaceId называется consumerId.

## <a name="template-deployment"></a>Развертывание шаблона

Расширения виртуальной машины Azure можно развернуть с помощью шаблонов Azure Resource Manager. Для запуска расширения агента Log Analytics во время развертывания шаблона Azure Resource Manager в нем можно использовать схему JSON, описанную в предыдущем разделе. Пример шаблона, включающего в себя расширение виртуальной машины агента Log Analytics, можно найти в [коллекции быстрого запуска Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/201-oms-extension-windows-vm). 

>[!NOTE]
>Шаблон не поддерживает указание более одного идентификатора рабочей области и ключа рабочей области, если требуется настроить агент для передачи отчетов в несколько рабочих областей. Сведения о настройке агента для передачи отчетов в несколько рабочих областей см. в разделе [Добавление или удаление рабочей области](../../azure-monitor/platform/agent-manage.md#adding-or-removing-a-workspace).  

JSON для расширения виртуальной машины можно вложить в ресурс виртуальной машины или поместить в корень или на верхний уровень JSON-файла шаблона Resource Manager. Размещение JSON влияет на значения имени и типа ресурса. Дополнительные сведения см. в разделе [Указание имени и типа дочернего ресурса в шаблоне Resource Manager](../../azure-resource-manager/child-resource-name-type.md). 

В следующем примере предполагается, что расширение Azure Monitor вложено в ресурс виртуальной машины. При вложении ресурса расширения JSON помещается в объект `"resources": []` виртуальной машины.


```json
{
    "type": "extensions",
    "name": "OMSExtension",
    "apiVersion": "[variables('apiVersion')]",
    "location": "[resourceGroup().location]",
    "dependsOn": [
        "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'))]"
    ],
    "properties": {
        "publisher": "Microsoft.EnterpriseCloud.Monitoring",
        "type": "MicrosoftMonitoringAgent",
        "typeHandlerVersion": "1.0",
        "autoUpgradeMinorVersion": true,
        "settings": {
            "workspaceId": "myWorkSpaceId"
        },
        "protectedSettings": {
            "workspaceKey": "myWorkspaceKey"
        }
    }
}
```

При размещении JSON расширения в корне шаблона имя ресурса содержит ссылку на родительскую виртуальную машину, а тип отражает вложенную конфигурацию. 

```json
{
    "type": "Microsoft.Compute/virtualMachines/extensions",
    "name": "<parentVmResource>/OMSExtension",
    "apiVersion": "[variables('apiVersion')]",
    "location": "[resourceGroup().location]",
    "dependsOn": [
        "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'))]"
    ],
    "properties": {
        "publisher": "Microsoft.EnterpriseCloud.Monitoring",
        "type": "MicrosoftMonitoringAgent",
        "typeHandlerVersion": "1.0",
        "autoUpgradeMinorVersion": true,
        "settings": {
            "workspaceId": "myWorkSpaceId"
        },
        "protectedSettings": {
            "workspaceKey": "myWorkspaceKey"
        }
    }
}
```

## <a name="powershell-deployment"></a>Развертывание с помощью PowerShell

Команду `Set-AzVMExtension` можно использовать для развертывания расширения виртуальной машины агента Log Analytics на существующей виртуальной машине. Перед выполнением команды необходимо сохранить открытые и закрытые конфигурации в хэш-таблице PowerShell. 

```powershell
$PublicSettings = @{"workspaceId" = "myWorkspaceId"}
$ProtectedSettings = @{"workspaceKey" = "myWorkspaceKey"}

Set-AzVMExtension -ExtensionName "MicrosoftMonitoringAgent" `
    -ResourceGroupName "myResourceGroup" `
    -VMName "myVM" `
    -Publisher "Microsoft.EnterpriseCloud.Monitoring" `
    -ExtensionType "MicrosoftMonitoringAgent" `
    -TypeHandlerVersion 1.0 `
    -Settings $PublicSettings `
    -ProtectedSettings $ProtectedSettings `
    -Location WestUS 
```

## <a name="troubleshoot-and-support"></a>Устранение неполадок и поддержка

### <a name="troubleshoot"></a>Устранение неполадок

Данные о состоянии развертывания расширения можно получить на портале Azure, а также с помощью модуля Azure PowerShell. Чтобы просмотреть состояние развертывания расширений для определенной виртуальной машины, выполните следующую команду в модуле Azure PowerShell.

```powershell
Get-AzVMExtension -ResourceGroupName myResourceGroup -VMName myVM -Name myExtensionName
```

Выходные данные выполнения расширения регистрируются в файле, расположенном в следующем каталоге:

```cmd
C:\WindowsAzure\Logs\Plugins\Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent\
```

### <a name="support"></a>Поддержка

Если в любой момент при изучении этой статьи вам потребуется дополнительная помощь, вы можете обратиться к экспертам по Azure на [форумах MSDN Azure и Stack Overflow](https://azure.microsoft.com/support/forums/). Кроме того, можно зарегистрировать обращение в службу поддержки Azure. Перейдите на [сайт поддержки Azure](https://azure.microsoft.com/support/options/) и щелкните "Получить поддержку". Дополнительные сведения об использовании службы поддержки Azure см. в статье [Часто задаваемые вопросы о поддержке Microsoft Azure](https://azure.microsoft.com/support/faq/).
