---
title: Применение расширения системы диагностики Microsoft Azure в облачных службах (Расширенная поддержка)
description: Применение расширения системы диагностики Windows Azure для облачных служб (Расширенная поддержка)
ms.topic: how-to
ms.service: cloud-services-extended-support
author: gachandw
ms.author: gachandw
ms.reviewer: mimckitt
ms.date: 10/13/2020
ms.custom: ''
ms.openlocfilehash: 4511ad979312d58e0a1b9cce9b1280e9ca059007
ms.sourcegitcommit: 6272bc01d8bdb833d43c56375bab1841a9c380a5
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/23/2021
ms.locfileid: "98744825"
---
# <a name="apply-the-windows-azure-diagnostics-extension-in-cloud-services-extended-support"></a>Применение расширения системы диагностики Microsoft Azure в облачных службах (Расширенная поддержка) 
Вы можете отслеживать для облачных служб ключевые показатели производительности. Каждая роль облачной службы собирает минимальный набор данных: использование ЦП, сетевых и дисковых ресурсов. Если облачная служба имеет расширение Microsoft. Azure. Diagnostics, к которому применяется роль, эта роль может выполнять получение дополнительных точек данных. Дополнительные сведения см. в разделе [Общие сведения о расширениях](extensions.md) .

Расширение система диагностики Azure Windows можно включить для облачных служб (Расширенная поддержка) с помощью [PowerShell](deploy-powershell.md) или [шаблона ARM](deploy-template.md) .

## <a name="apply-windows-azure-diagnostics-extension-using-powershell"></a>Применение расширения система диагностики Azure Windows с помощью PowerShell

```powershell
# Create WAD extension object
$wadExtension = New-AzCloudServiceDiagnosticsExtension -Name "WADExtension" -ResourceGroupName "ContosOrg" -CloudServiceName "ContosCS" -StorageAccountName "ContosSA" -StorageAccountKey $storageAccountKey[0].Value -DiagnosticsConfigurationPath $configFile -TypeHandlerVersion "1.5" -AutoUpgradeMinorVersion $true 
$extensionProfile = @{extension = @($rdpExtension, $wadExtension)} 

# Get existing Cloud Service
$cloudService = Get-AzCloudService -ResourceGroup "ContosOrg" -CloudServiceName "ContosoCS"

# Add WAD extension to existing Cloud Service extension object
$cloudService.ExtensionProfileExtension = $cloudService.ExtensionProfileExtension + $wadExtension

# Update Cloud Service
$cloudService | Update-AzCloudService
```

## <a name="apply-windows-azure-diagnostics-extension-using-arm-template"></a>Применение расширения система диагностики Azure Windows с помощью шаблона ARM
```json
"extensionProfile": { 
          "extensions": [ 
            { 
              "name": "Microsoft.Insights.VMDiagnosticsSettings_WebRole1", 
              "properties": { 
                "autoUpgradeMinorVersion": true, 
                "publisher": "Microsoft.Azure.Diagnostics", 
                "type": "PaaSDiagnostics", 
                "typeHandlerVersion": "1.5", 
                "settings": "Include PublicConfig XML as a raw string", 
                "protectedSettings": "Include PrivateConfig XML as a raw string”", 
                "rolesAppliedTo": [ 
                  "WebRole1" 
                ] 
              } 
            }
          ]
        },

```

## <a name="next-steps"></a>Следующие шаги 
- Ознакомьтесь с [предварительными требованиями для развертывания](deploy-prerequisite.md) облачных служб (Расширенная поддержка).
- Ознакомьтесь с [часто задаваемыми вопросами](faq.md) о облачных службах (Расширенная поддержка).
- Разверните облачную службу (расширенную поддержку) с помощью [портал Azure](deploy-portal.md), [PowerShell](deploy-powershell.md), [шаблона](deploy-template.md) или [Visual Studio](deploy-visual-studio.md).