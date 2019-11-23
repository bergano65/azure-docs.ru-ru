---
title: How to stop monitoring your Azure Red Hat OpenShift cluster | Microsoft Docs
description: This article describes how you can stop monitoring of your Azure Red Hat OpenShift cluster with Azure Monitor for containers.
ms.service: azure-monitor
ms.subservice: ''
ms.topic: conceptual
author: mgoedtel
ms.author: magoedte
ms.date: 11/21/2019
ms.openlocfilehash: f769749532a05260bf3c2c9f99483c5607d985a6
ms.sourcegitcommit: f523c8a8557ade6c4db6be12d7a01e535ff32f32
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/22/2019
ms.locfileid: "74384335"
---
# <a name="how-to-stop-monitoring-your-azure-red-hat-openshift-cluster-with-azure-monitor-for-containers"></a>How to stop monitoring your Azure Red Hat OpenShift cluster with Azure Monitor for containers

After you enable monitoring of your Azure Red Hat OpenShift cluster, you can stop monitoring the cluster if you decide you no longer want to monitor it. This article shows how to accomplish this using the provided Azure Resource Manager templates.  

## <a name="azure-resource-manager-template"></a>Шаблон Azure Resource Manager

Предоставлены два шаблона Azure Resource Manager, которые позволяют последовательно и многократно удалять ресурсы решения в вашей группе ресурсов. One is a JSON template specifying the configuration to stop monitoring and the other contains parameter values that you configure to specify the OpenShift cluster resource ID and Azure region that the cluster is deployed in. 

Если вы не знакомы с концепцией развертывания ресурсов с помощью шаблона, ознакомьтесь со статьями:
* [Развертывание ресурсов с использованием шаблонов Resource Manager и Azure PowerShell](../../azure-resource-manager/resource-group-template-deploy.md)
* [Развертывание ресурсов с использованием шаблонов Resource Manager и Azure CLI](../../azure-resource-manager/resource-group-template-deploy-cli.md)

Если вы решили использовать Azure CLI, необходимо сначала установить интерфейс командной строки и использовать его локально. You must be running the Azure CLI version 2.0.65 or later. Для определения версии выполните `az --version`. Если вам необходимо установить или обновить Azure CLI, ознакомьтесь со статьей [Установка Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli). 

### <a name="create-template"></a>Создание шаблона

1. Скопируйте и вставьте в него следующий синтаксис JSON:

    ```json
    {
       "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
       "contentVersion": "1.0.0.0",
       "parameters": {
         "aroResourceId": {
           "type": "string",
           "metadata": {
             "description": "ARO Cluster Resource ID"
          }
        },
        "aroResourceLocation": {
          "type": "string",
          "metadata": {
            "description": "Location of the aro cluster resource e.g. westcentralus"
          }
        }
      },
      "resources": [
        {
           "name": "[split(parameters('aroResourceId'),'/')[8]]",
           "type": "Microsoft.ContainerService/openShiftManagedClusters",
           "location": "[parameters('aroResourceLocation')]",
           "apiVersion": "2019-09-30-preview",
           "properties": {
             "mode": "Incremental",
             "id": "[parameters('aroResourceId')]",
             "monitorProfile": {
               "workspaceResourceID": null,
               "enabled": false
            }
          }
        }
      ]
    }
    ```

2. Сохраните этот файл как **OptOutTemplate.json** в локальной папке.

3. Вставьте в него следующий синтаксис JSON:

    ```json
    {
      "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
        "aroResourceId": {
          "value": "/subscriptions/<subscriptionId>/resourceGroups/<ResourceGroupName>/providers/Microsoft.ContainerService/openShiftManagedClusters/<clusterName>"
        },
        "aroResourceLocation": {
          "value": "<azure region of the cluster e.g. westcentralus>"
        }
      }
    }
    ```

4. Edit the values for **aroResourceId** and **aroResourceLocation** by using the values of the OpenShift cluster, which you can find on the **Properties** page for the selected cluster.

    ![Страница свойств контейнера](media/container-insights-optout-openshift/cluster-properties-page.png)

5. Сохраните этот файл как **OptOutParam.json** в локальной папке.

6. Теперь вы можете развернуть этот шаблон. 

### <a name="remove-the-solution-using-azure-cli"></a>Удаление решения с помощью Azure CLI

Execute the following command with Azure CLI on Linux to remove the solution and clean up the configuration on your cluster.

```azurecli
az login   
az account set --subscription "Subscription Name" 
az group deployment create --resource-group <ResourceGroupName> --template-file ./OptOutTemplate.json --parameters @./OptOutParam.json  
```

Изменение конфигурации может занять несколько минут. По завершении выполнения вы увидите сообщение, подобное приведенному ниже, с возвращенным результатом:

```azurecli
ProvisioningState       : Succeeded
```

### <a name="remove-the-solution-using-powershell"></a>Удаление решения с помощью PowerShell

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Execute the following PowerShell commands in the folder containing the template to remove the solution and clean up the configuration from your cluster.    

```powershell
Connect-AzAccount
Select-AzSubscription -SubscriptionName <yourSubscriptionName>
New-AzResourceGroupDeployment -Name opt-out -ResourceGroupName <ResourceGroupName> -TemplateFile .\OptOutTemplate.json -TemplateParameterFile .\OptOutParam.json
```

Изменение конфигурации может занять несколько минут. По завершении выполнения вы увидите сообщение, подобное приведенному ниже, с возвращенным результатом:

```powershell
ProvisioningState       : Succeeded
```

## <a name="next-steps"></a>Дальнейшие действия

Если рабочая область была создана только для мониторинга кластера и больше не требуется, ее необходимо удалить вручную. If you are not familiar with how to delete a workspace, see [Delete an Azure Log Analytics workspace](../../log-analytics/log-analytics-manage-del-workspace.md). 
