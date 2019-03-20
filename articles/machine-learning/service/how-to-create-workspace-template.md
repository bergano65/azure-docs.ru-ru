---
title: Использование шаблона для создания рабочего пространства
titleSuffix: Azure Machine Learning service
description: Узнайте, как использовать шаблон Azure Resource Manager для создания нового рабочего пространства Службы машинного обучения Azure.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: larryfr
author: Blackmist
ms.date: 02/11/2019
ms.openlocfilehash: 2b5ee1b5079f22985b03aaafac30c11a10a3c8f0
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/18/2019
ms.locfileid: "57850083"
---
# <a name="create-an-azure-machine-learning-service-workspace-by-using-a-template"></a>Создание рабочего пространства Службы машинного обучения Azure с помощью шаблона

В этой статье вы изучите несколько способов создания рабочего пространства Службы машинного обучения Azure с помощью шаблонов Azure Resource Manager. 

Узнайте подробнее [о развертывании приложения с помощью шаблона диспетчера ресурсов Azure](../../azure-resource-manager/resource-group-template-deploy.md).

## <a name="prerequisites"></a>Технические условия

* **Подписка Azure**. Если у вас ее нет, опробуйте [бесплатную или платную версию Службы машинного обучения Azure](https://aka.ms/AMLFree).

* Чтобы использовать шаблон из командной строки, вам потребуется [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview?view=azps-1.2.0) или [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).

## <a name="resource-manager-template"></a>Шаблон Resource Manager

Шаблон Resource Manager позволяет легко создать все ресурсы в отдельной скоординированной операции. Шаблон представляет собой документ JSON, в котором определены необходимые для развертывания ресурсы. Также здесь можно указать параметры развертывания. Эти параметры позволят предоставить входные значения при использовании шаблона.

Чтобы создать рабочее пространство Службы машинного обучения Azure и связанные ресурсы Azure, выполните следующее.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "workspaceName": {
            "type": "string",
            "metadata": {
                "description": "The name of the Azure Machine Learning service workspace."
            }
        },
        "location": {
            "type": "string",
            "allowedValues": [
                "eastus",
                "eastus2",
                "southcentralus",
                "southeastasia",
                "westcentralus",
                "westeurope",
                "westus2"
            ],
            "metadata": {
                "description": "The location where the workspace will be created."
            }
        }
    },
    "variables": {
        "storageAccount": {
            "name": "[concat('sa',uniqueString(resourceGroup().id))]",
            "type": "Standard_LRS"
        },
        "keyVault": {
            "name": "[concat('kv',uniqueString(resourceGroup().id))]",
            "tenantId": "[subscription().tenantId]"
        },
        "applicationInsightsName": "[concat('ai',uniqueString(resourceGroup().id))]",
        "containerRegistryName": "[concat('cr',uniqueString(resourceGroup().id))]"
    },
    "resources": [
        {
            "name": "[variables('storageAccount').name]",
            "type": "Microsoft.Storage/storageAccounts",
            "location": "[parameters('location')]",
            "apiVersion": "2018-07-01",
            "sku": {
                "name": "[variables('storageAccount').type]"
            },
            "kind": "StorageV2",
            "properties": {
                "encryption": {
                    "services": {
                        "blob": {
                            "enabled": true
                        },
                        "file": {
                            "enabled": true
                        }
                    },
                    "keySource": "Microsoft.Storage"
                },
                "supportHttpsTrafficOnly": true
            }
        },
        {
            "name": "[variables('keyVault').name]",
            "type": "Microsoft.KeyVault/vaults",
            "apiVersion": "2018-02-14",
            "location": "[parameters('location')]",
            "properties": {
                "tenantId": "[variables('keyVault').tenantId]",
                "sku": {
                    "name": "standard",
                    "family": "A"
                },
                "accessPolicies": []
            }
        },
        {
            "name": "[variables('applicationInsightsName')]",
            "type": "Microsoft.Insights/components",
            "apiVersion": "2015-05-01",
            "location": "[if(or(equals(parameters('location'),'eastus2'),equals(parameters('location'),'westcentralus')),'southcentralus',parameters('location'))]",
            "kind": "web",
            "properties": {
                "Application_Type": "web"
            }
        },
        {
            "name": "[variables('containerRegistryName')]",
            "type": "Microsoft.ContainerRegistry/registries",
            "apiVersion": "2017-10-01",
            "location": "[parameters('location')]",
            "sku": {
                "name": "Standard"
            },
            "properties": {
                "adminUserEnabled": true
            }
        },
        {
            "name": "[parameters('workspaceName')]",
            "type": "Microsoft.MachineLearningServices/workspaces",
            "apiVersion": "2018-11-19",
            "location": "[parameters('location')]",
            "dependsOn": [
                "[variables('storageAccount').name]",
                "[variables('keyVault').name]",
                "[variables('applicationInsightsName')]",
                "[variables('containerRegistryName')]"
            ],
            "identity": {
                "type": "systemAssigned"
            },
            "properties": {
                "friendlyName": "[parameters('workspaceName')]",
                "keyVault": "[resourceId('Microsoft.KeyVault/vaults',variables('keyVault').name)]",
                "applicationInsights": "[resourceId('Microsoft.Insights/components',variables('applicationInsightsName'))]",
                "containerRegistry": "[resourceId('Microsoft.ContainerRegistry/registries',variables('containerRegistryName'))]",
                "storageAccount": "[resourceId('Microsoft.Storage/storageAccounts/',variables('storageAccount').name)]"
            }
        }
    ]
}
```

Этот шаблон создает следующие ресурсы Azure:

* группа ресурсов Azure.
* Учетная запись хранения Azure
* Хранилище ключей Azure
* Azure Application Insights
* Реестр контейнеров Azure
* Рабочая область службы "Машинное обучение Azure"

Группа ресурсов выполняет функции контейнера, который содержит службы. Для рабочего пространства Службы машинного обучения Azure нужно несколько служб.

Наш пример шаблона принимает два параметра.

* **Расположение** указывает, где будут созданы группа ресурсов и службы.

    Этот шаблон будет использовать выбранное расположение для большинства ресурсов. Исключением является служба Application Insights, которая доступна не во всех тех расположениях, в которых доступны другие службы. Если вы выберете расположение, где эта служба не доступна, ее экземпляр будет создан в регионе "центрально-южная часть США".

* **Имя рабочего пространства** Службы машинного обучения Azure в удобном для пользователя формате.

    Имена для всех остальных служб создаются случайным образом.

Дополнительные сведения о шаблонах вы найдете в следующих статьях.

* [Шаблоны диспетчера ресурсов Azure](../../azure-resource-manager/resource-group-authoring-templates.md)
* [Развертывание ресурсов с использованием шаблонов Resource Manager и Azure CLI](../../azure-resource-manager/resource-group-template-deploy.md)
* [Типы ресурсов Microsoft.MachineLearningServices](https://docs.microsoft.com/azure/templates/microsoft.machinelearningservices/allversions)

## <a name="use-the-azure-portal"></a>Использование портала Azure

1. Выполните инструкции из раздела [Развертывание ресурсов с помощью настраиваемого шаблона](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-deploy-portal#deploy-resources-from-custom-template). Дойдя до экрана __Изменить шаблон__, вставьте шаблон, настроенный при изучении этого раздела.
1. Щелкните __Сохранить__, чтобы применить этот шаблон. Укажите описанные ниже сведения и подтвердите согласие с предложенными условиями.

   * Подписка: Выберите подписку Azure, которую нужно использовать для этих ресурсов.
   * Группа ресурсов: Выберите или создайте группу ресурсов, которая будет содержать службы.
   * Имя рабочей области: Это имя, которое будет присвоено созданному рабочему пространству Службы машинного обучения Azure. Для имени рабочего пространства допускается длина от 3 до 33 символов. Имя может содержать только буквы, цифры и дефис ("-").
   * Расположение. Выберите расположение, в котором будут созданы ресурсы.

     ![Параметры шаблона на портале Azure](media/how-to-create-workspace-template/template-parameters.png)

Дополнительные сведения см. в разделе [Развертывание ресурсов с помощью настраиваемого шаблона](../../azure-resource-manager/resource-group-template-deploy-portal.md#deploy-resources-from-custom-template).

## <a name="use-azure-powershell"></a>Использование Azure PowerShell

В этом примере предполагается, что вы сохранили шаблон в файл с именем `azuredeploy.json` в текущем каталоге.

```powershell
New-AzResourceGroup -Name examplegroup -Location "East US"
new-azresourcegroupdeployment -name exampledeployment `
  -resourcegroupname examplegroup -location "East US" `
  -templatefile .\azuredeploy.json -workspaceName "exampleworkspace"
```

Дополнительные сведения см. в статьях [Развертывание ресурсов с использованием шаблонов Resource Manager и Azure PowerShell](../../azure-resource-manager/resource-group-template-deploy.md) и [Развертывание частного шаблона Resource Manager с использованием токена SAS и Azure PowerShell](../../azure-resource-manager/resource-manager-powershell-sas-token.md).

## <a name="use-azure-cli"></a>Использование интерфейса командной строки Azure

В этом примере предполагается, что вы сохранили шаблон в файл с именем `azuredeploy.json` в текущем каталоге.

```azurecli-interactive
az group create --name examplegroup --location "East US"
az group deployment create \
  --name exampledeployment \
  --resource-group examplegroup \
  --template-file azuredeploy.json \
  --parameters workspaceName=exampleworkspace
```

Дополнительные сведения см. в статьях [Развертывание ресурсов с использованием шаблонов Resource Manager и Azure CLI](../../azure-resource-manager/resource-group-template-deploy-cli.md) и [Развертывание частного шаблона Resource Manager с использованием токена SAS и Azure CLI](../../azure-resource-manager/resource-manager-cli-sas-token.md).

## <a name="next-steps"></a>Дальнейшие действия

* [Развертывание ресурсов с использованием шаблонов и REST API Resource Manager](../../azure-resource-manager/resource-group-template-deploy-rest.md).
* [Создание и развертывание групп ресурсов Azure с помощью Visual Studio](../../azure-resource-manager/vs-azure-tools-resource-groups-deployment-projects-create-deploy.md).
