---
title: Как остановить мониторинг кластера Azure Red Hat OpenShift Документы Майкрософт
description: В этой статье описывается, как можно прекратить мониторинг кластера Azure Red Hat OpenShift с помощью Azure Monitor для контейнеров.
ms.topic: conceptual
ms.date: 11/21/2019
ms.openlocfilehash: eff5203aeedd3c7ad283b55ba12f0e45a556460d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78250735"
---
# <a name="how-to-stop-monitoring-your-azure-red-hat-openshift-cluster-with-azure-monitor-for-containers"></a>Как прекратить мониторинг кластера Azure Red Hat OpenShift с помощью Azure Monitor для контейнеров

После включения мониторинга кластера Azure Red Hat OpenShift можно прекратить мониторинг кластера, если вы решите, что больше не хотите его контролировать. В этой статье показано, как это сделать с помощью предоставленных шаблонов менеджера ресурсов Azure.  

## <a name="azure-resource-manager-template"></a>Шаблон Azure Resource Manager

Предоставлены два шаблона Azure Resource Manager, которые позволяют последовательно и многократно удалять ресурсы решения в вашей группе ресурсов. Одним из них является шаблон JSON, определяющий конфигурацию для прекращения мониторинга, а другой содержит значения параметров, которые вы настраиваете для определения идентификатора ресурсов кластера OpenShift и области Azure, в которой развертывается кластер.

Если вы не знакомы с концепцией развертывания ресурсов с помощью шаблона, ознакомьтесь со статьями:
* [Развертывание ресурсов с использованием шаблонов Resource Manager и Azure PowerShell](../../azure-resource-manager/templates/deploy-powershell.md)
* [Развертывание ресурсов с использованием шаблонов Resource Manager и Azure CLI](../../azure-resource-manager/templates/deploy-cli.md)

Если вы решили использовать Azure CLI, необходимо сначала установить интерфейс командной строки и использовать его локально. Вы должны запустить версию Azure CLI 2.0.65 или позже. Для определения версии выполните `az --version`. Если вам необходимо установить или обновить Azure CLI, ознакомьтесь со статьей [Установка Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli).

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

4. Изнайдите значения **для aroResourceId** и **aroResourceLocation,** используя значения кластера OpenShift, которые можно найти на странице **Свойств** для выбранного кластера.

    ![Страница свойств контейнера](media/container-insights-optout-openshift/cluster-properties-page.png)

5. Сохраните этот файл как **OptOutParam.json** в локальной папке.

6. Теперь вы можете развернуть этот шаблон.

### <a name="remove-the-solution-using-azure-cli"></a>Удаление решения с помощью Azure CLI

Выполните следующую команду с Azure CLI на Linux, чтобы удалить решение и очистить конфигурацию в кластере.

```azurecli
az login   
az account set --subscription "Subscription Name"
az group deployment create --resource-group <ResourceGroupName> --template-file ./OptOutTemplate.json --parameters @./OptOutParam.json  
```

Изменение конфигурации может занять несколько минут. По завершении выполнения вы увидите сообщение, подобное приведенному ниже, с возвращенным результатом:

```output
ProvisioningState       : Succeeded
```

### <a name="remove-the-solution-using-powershell"></a>Удаление решения с помощью PowerShell

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Выполните следующие команды PowerShell в папке, содержащей шаблон, чтобы удалить решение и очистить конфигурацию от кластера.    

```powershell
Connect-AzAccount
Select-AzSubscription -SubscriptionName <yourSubscriptionName>
New-AzResourceGroupDeployment -Name opt-out -ResourceGroupName <ResourceGroupName> -TemplateFile .\OptOutTemplate.json -TemplateParameterFile .\OptOutParam.json
```

Изменение конфигурации может занять несколько минут. По завершении выполнения вы увидите сообщение, подобное приведенному ниже, с возвращенным результатом:

```output
ProvisioningState       : Succeeded
```

## <a name="next-steps"></a>Дальнейшие действия

Если рабочая область была создана только для мониторинга кластера и больше не требуется, ее необходимо удалить вручную. Если вы не знакомы с тем, как удалить рабочее пространство, [см.](../../log-analytics/log-analytics-manage-del-workspace.md)
