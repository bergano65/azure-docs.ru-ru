---
title: Примеры шаблонов Resource Manager для книг
description: Примеры шаблонов Azure Resource Manager для развертывания книг журнала Azure Monitor.
ms.subservice: logs
ms.topic: sample
author: bwren
ms.author: bwren
ms.date: 05/18/2020
ms.openlocfilehash: 72e6e085135c07e962dea6253af7d40333295a86
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/09/2020
ms.locfileid: "83853208"
---
# <a name="resource-manager-template-samples-for-workbooks-in-azure-monitor"></a>Примеры шаблонов Azure Resource Manager для книг в Azure Monitor
В этой статье представлены примеры [шаблонов Azure Resource Manager](../../azure-resource-manager/templates/template-syntax.md) для создания книг в Azure Monitor. Каждый пример включает файл шаблона и файл параметров с примерами значений для предоставления шаблона.

[!INCLUDE [azure-monitor-samples](../../../includes/azure-monitor-resource-manager-samples.md)]

Книги могут быть сложными, поэтому обычной стратегией является создание книги на портале Azure с последующим созданием шаблона Resource Manager. Сведения об этом методе см. в разделе [Шаблон Azure Resource Manager для развертывания шаблона книги](../platform/workbooks-automate.md).

## <a name="create-a-workbook"></a>Создание книги
В следующем примере создается простая книга.


### <a name="template-file"></a>Файл шаблона

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "workbookDisplayName":  {             
            "type":"string",
            "defaultValue": "My Workbook",
            "metadata": {
                "description": "The friendly name for the workbook that is used in the Gallery or Saved List. Needs to be unique in the scope of the resource group and source" 
            }
        },
        "workbookType":  {             
            "type":"string",
            "defaultValue": "tsg",
            "metadata": {
                "description": "The gallery that the workbook will been shown under. Supported values include workbook, `tsg`, Azure Monitor, etc." 
            }
        },
        "workbookSourceId":  {             
            "type":"string",
            "defaultValue": "<insert-your-resource-id-here>",
            "metadata": {
                "description": "The id of resource instance to which the workbook will be associated" 
            }
        },
        "workbookId": {
            "type":"string",
            "defaultValue": "[newGuid()]",
            "metadata": {
                "description": "The unique guid for this workbook instance" 
            }
        }
    },    
    "resources": [
        {
            "name": "[parameters('workbookId')]",
            "type": "Microsoft.Insights/workbooks",
            "location": "[resourceGroup().location]",
            "kind": "shared",
            "apiVersion": "2018-06-17-preview",
            "dependsOn": [],
            "properties": {
                "displayName": "[parameters('workbookDisplayName')]",
                "serializedData": "{\"version\":\"Notebook/1.0\",\"items\":[{\"type\":1,\"content\":\"{\\\"json\\\":\\\"Hello World!\\\"}\",\"conditionalVisibility\":null}],\"isLocked\":false}",
                "version": "1.0",
                "sourceId": "[parameters('workbookSourceId')]",
                "category": "[parameters('workbookType')]"
            }
        }
    ],
    "outputs": {
        "workbookId": {
            "type": "string",
            "value": "[resourceId( 'Microsoft.Insights/workbooks', parameters('workbookId'))]"
        }
    }
}
```

### <a name="parameter-file"></a>Файл параметров

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "workbookDisplayName": {
            "value": "Sample Hello World workbook"
        },
      "workbookType": {
        "value": "workbook"
      },
      "workbookSourceId": {
        "value": "Azure Monitor"
      }
    }
}
```

## <a name="next-steps"></a>Дальнейшие действия

* [Получите другие примеры шаблонов для Azure Monitor.](resource-manager-samples.md)
* [Узнайте о группах действий.](../platform/workbooks-overview.md)
