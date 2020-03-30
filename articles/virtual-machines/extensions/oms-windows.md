---
title: Расширение виртуальной машины Log Analytics для Windows
description: Развертывание агента Log Analytics на виртуальной машине Windows с помощью расширения виртуальной машины.
services: virtual-machines-windows
documentationcenter: ''
author: axayjo
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: feae6176-2373-4034-b5d9-a32c6b4e1f10
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 01/30/2020
ms.author: akjosh
ms.openlocfilehash: 85977819d30ddc8745eb9231242eb1990222676c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79530994"
---
# <a name="log-analytics-virtual-machine-extension-for-windows"></a>Расширение виртуальной машины Log Analytics для Windows

Журналы Azure Monitor обеспечивают возможности мониторинга в облачных и локтях. Расширение виртуальной машины агента Log Analytics для Windows предоставляет и поддерживает корпорация Майкрософт. Это расширение устанавливает агент Log Analytics на виртуальных машинах Azure и регистрирует виртуальные машины в существующей рабочей области Log Analytics. В этом документе подробно описаны поддерживаемые платформы, конфигурации и параметры развертывания для расширения виртуальной машины Log Analytics для Windows.

## <a name="prerequisites"></a>Предварительные требования

### <a name="operating-system"></a>Операционная система

Для получения подробной информации об поддерживаемых операционных системах Windows обратитесь в обзор [агентства Log Analytics.](../../azure-monitor/platform/log-analytics-agent.md#supported-windows-operating-systems)

### <a name="agent-and-vm-extension-version"></a>Версия агента и расширения виртуальной машины
В следующей таблице приводится отображение версии расширения Windows Log Analytics VM и пакета агентов Log Analytics для каждого выпуска. 

| Версия пакета агента Log Analytics Windows | Версия расширения windows VM для журналов Analytics | Дата выпуска | Заметки о выпуске |
|--------------------------------|--------------------------|--------------------------|--------------------------|
| 10.20.18029 | 1.0.18029 | Март 2020 г.   | <ul><li>Добавляет поддержку подписи кода SHA-2</li><li>Улучшает установку и управление расширением VM</li><li>Устраняет ошибку в Azure Arc для интеграции серверов</li><li>Добавлен встроенный инструмент устранения неполадок для поддержки клиентов</li><li>Добавляет поддержку для дополнительных регионов правительства Azure</li> |
| 10.20.18018 | 1.0.18018 | Октябрь 2019 г. | <ul><li> Незначительные исправления ошибок и улучшения стабилизации </li></ul> |
| 10.20.18011 | 1.0.18011 | Июль 2019 г. | <ul><li> Незначительные исправления ошибок и улучшения стабилизации </li><li> Увеличено MaxExpressionDepth до 10000 </li></ul> |
| 10.20.18001 | 1.0.18001 | Июнь 2019 г. | <ul><li> Незначительные исправления ошибок и улучшения стабилизации </li><li> Добавлена возможность отключить учетные данные по умолчанию при подключении прокси (поддержка WINHTTP_AUTOLOGON_SECURITY_LEVEL_HIGH) </li></ul>|
| 10.19.13515 | 1.0.13515 | Март 2019 г. | <ul><li>Незначительные стабилизационные исправления </li></ul> |
| 10.19.10006 | Недоступно | Декабрь 2018 г. | <ul><li> Незначительные стабилизационные исправления </li></ul> | 
| 8.0.11136 | Недоступно | Сентябрь 2018 г. |  <ul><li> Добавлена поддержка для обнаружения изменения идентификатора ресурса на VM move </li><li> Добавлена поддержка идентификатора отчета об идентификаторе ресурса при использовании установки, не эннтакжедении </li></ul>| 
| 8.0.11103 | Недоступно |  Апрель 2018 г. | |
| 8.0.11081 | 1.0.11081 | Ноябрь 2017 г. | | 
| 8.0.11072 | 1.0.11072 | Сентябрь 2017 г. | |
| 8.0.11049 | 1.0.11049 | Февраль 2017 г. | |


### <a name="azure-security-center"></a>Центр безопасности Azure

Центр безопасности Azure автоматически обеспечивает агент уlog Analytics и соединяет его с рабочим пространством анализа журналов по умолчанию подписки Azure. Если вы используете центр безопасности Azure, не выполняйте инструкции в этом документе. Это приведет к перезаписи настроенной рабочей области и разрыву подключения с центром безопасности Azure.

### <a name="internet-connectivity"></a>Подключение к Интернету
Для расширения агента Log Analytics для Windows требуется, чтобы целевая виртуальная машина была подключена к Интернету. 

## <a name="extension-schema"></a>Схема расширения

В следующем объекте JSON показана схема для расширения агента Log Analytics. Расширение требует идентификатора рабочего пространства и ключа рабочей области из рабочего пространства log Analytics. Их можно найти в разделе параметров рабочей области на портале Azure. Так как ключ рабочей области должен рассматриваться в качестве конфиденциальных данных, его следует хранить в защищенной конфигурации параметров. Данные защищенных параметров расширения виртуальной машины Azure зашифрованы. Они расшифровываются только на целевой виртуальной машине. Обратите внимание, что в **workspaceId** и **workspaceKey** учитывается регистр знаков.

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

| name | Значение и пример |
| ---- | ---- |
| версия_API | 2015-06-15 |
| publisher | Microsoft.EnterpriseCloud.Monitoring |
| type | MicrosoftMonitoringAgent |
| typeHandlerVersion | 1.0 |
| workspaceID (пример)* | 6f680a37-00c6-41c7-a93f-1437e3462574 |
| workspaceKey (пример) | z4bU3p1/GrnWpQkky4gdabWXAhbWSTz70hm4m2Xt92XI+rSRgE8qVvRhsGo9TXffbrTahyrwv35W0pOqQAU7uQ== |

\* В API-интерфейсе Log Analytics параметр workspaceId называется consumerId.

> [!NOTE]
> Для дополнительных свойств см Azure [Подключите Windows Компьютеры к azure Monitor.](https://docs.microsoft.com/azure/azure-monitor/platform/agent-windows)

## <a name="template-deployment"></a>Развертывание шаблона

Расширения виртуальной машины Azure можно развернуть с помощью шаблонов Azure Resource Manager. Для запуска расширения агента Log Analytics во время развертывания шаблона Azure Resource Manager в нем можно использовать схему JSON, описанную в предыдущем разделе. Образец шаблона, включающий расширение агента Анализа журнала VM, можно найти в [галерее Azure quickstart.](https://github.com/Azure/azure-quickstart-templates/tree/master/201-oms-extension-windows-vm) 

>[!NOTE]
>Шаблон не поддерживает указание более одного идентификатора рабочего пространства и ключа рабочего пространства, когда требуется настроить агента для отчета в нескольких рабочих пространствах. Чтобы настроить агента для отчета в несколько рабочих областей, [см. Добавление или удаление рабочего пространства.](../../azure-monitor/platform/agent-manage.md#adding-or-removing-a-workspace)  

JSON для расширения виртуальной машины можно вложить в ресурс виртуальной машины или поместить в корень или на верхний уровень JSON-файла шаблона Resource Manager. Размещение JSON влияет на значения имени и типа ресурса. Для получения дополнительной информации [см.](../../azure-resource-manager/templates/child-resource-name-type.md) 

В следующем примере предполагается, что расширение Log Analytics вложено в ресурс виртуальной машины. При вложении ресурса расширения JSON помещается в объект `"resources": []` виртуальной машины.


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

Если вам нужна дополнительная помощь в какой-либо момент этой статьи, вы можете связаться с экспертами Azure на [форумах MSDN Azure и Stack Overflow.](https://azure.microsoft.com/support/forums/) Кроме того, можно зарегистрировать обращение в службу поддержки Azure. Перейдите на [сайт поддержки Azure](https://azure.microsoft.com/support/options/) и выберите Получить поддержку. Дополнительные сведения об использовании службы поддержки Azure см. в статье [Часто задаваемые вопросы о поддержке Microsoft Azure](https://azure.microsoft.com/support/faq/).
