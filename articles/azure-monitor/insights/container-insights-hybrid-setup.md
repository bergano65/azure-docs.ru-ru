---
title: Настройка гибридных кластеров Kubernetes с Azure Monitor для контейнеров | Документация Майкрософт
description: В этой статье описывается, как настроить Azure Monitor для контейнеров для мониторинга кластеров Kubernetes, размещенных в Azure Stack или в другой среде.
ms.service: azure-monitor
ms.subservice: ''
ms.topic: conceptual
author: mgoedtel
ms.author: magoedte
ms.date: 10/15/2019
ms.openlocfilehash: d25b9b3bb155dced973d415b396ebfaa4403b011
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/04/2019
ms.locfileid: "73514619"
---
# <a name="configure-hybrid-kubernetes-clusters-with-azure-monitor-for-containers"></a>Настройка гибридных кластеров Kubernetes с Azure Monitor для контейнеров

Azure Monitor для контейнеров предоставляет широкие возможности мониторинга для кластеров подсистемы Azure Kubernetes Service (AKS) и AKS, размещенных в Azure. В этой статье описывается, как включить мониторинг кластеров Kubernetes, размещенных за пределами Azure, и добиться аналогичного процесса мониторинга.

## <a name="prerequisites"></a>Технические условия

Чтобы начать, у вас должны быть следующие компоненты:

* Рабочая область Log Analytics.

    Azure Monitor для контейнеров поддерживает рабочую область Log Analytics в регионах, перечисленных в списке [продуктов Azure по регионам](https://azure.microsoft.com/global-infrastructure/services/?regions=all&products=monitor). Чтобы создать собственную рабочую область, ее можно создать с помощью [Azure Resource Manager](../platform/template-workspace-configuration.md), [PowerShell](../scripts/powershell-sample-create-workspace.md?toc=%2fpowershell%2fmodule%2ftoc.json)или в [портал Azure](../learn/quick-create-workspace.md).

    >[!NOTE]
    >Включение мониторинга нескольких кластеров с одним и тем же именем кластера в одной рабочей области Log Analytics не поддерживается. Имена кластеров должны быть уникальными.
    >

* Вы являетесь членом **роли участника log Analytics** , чтобы включить мониторинг контейнеров. Дополнительные сведения об управлении доступом к рабочей области Log Analytics см. в разделе [Управление доступом к рабочей области и данным журнала](../platform/manage-access.md) .

* [Helm клиент](https://helm.sh/docs/using_helm/) , чтобы подключить диаграмму Azure Monitor для контейнеров для указанного кластера Kubernetes.

* Следующие сведения о конфигурации прокси-сервера и брандмауэра необходимы для того, чтобы контейнерная версия агента Log Analytics для Linux могла взаимодействовать с Azure Monitor:

    |Ресурс агента|порты; |
    |------|---------|   
    |*.ods.opinsights.azure.com |Порт 443 |  
    |*.oms.opinsights.azure.com |Порт 443 |  
    |*.blob.core.windows.net |Порт 443 |  
    |*. dc.services.visualstudio.com |Порт 443 | 

* Контейнерный агент требует, чтобы `cAdvisor port: 10255` открывались на всех узлах кластера для сбора метрик производительности.

* Контейнерный агент требует, чтобы следующие переменные среды были указаны в контейнере для взаимодействия со службой API Kubernetes в кластере для сбора данных инвентаризации — `KUBERNETES_SERVICE_HOST` и `KUBERNETES_PORT_443_TCP_PORT`. 

>[!IMPORTANT]
>Минимальная версия агента, поддерживаемая для мониторинга гибридных кластеров Kubernetes, — ciprod10182019 или более поздняя. 

## <a name="supported-configurations"></a>Поддерживаемые конфигурации

Ниже официально поддерживаются Azure Monitor для контейнеров.

- Среды: Kubernetes в локальной среде, AKS Engine в Azure и Azure Stack. Дополнительные сведения см. [в разделе AKS Engine on Azure Stack](https://docs.microsoft.com/azure-stack/user/azure-stack-kubernetes-aks-engine-overview?view=azs-1908).
- Версии Kubernetes и политики поддержки совпадают с [поддерживаемыми версиями AKS](../../aks/supported-kubernetes-versions.md). 
- Среда выполнения контейнера: DOCKER и значок Кита
- Выпуск ОС Linux для главного и работающего узла: Ubuntu (18,04 LTS и 16,04 LTS)
- Поддерживается контроль доступа: Kubernetes RBAC и non-RBAC.

## <a name="enable-monitoring"></a>Включение мониторинга

Включение Azure Monitor для контейнеров гибридного кластера Kubernetes состоит из выполнения следующих действий по порядку.

1. Настройте рабочую область Log Analytics с помощью решения Container Insights.

2. Включите диаграмму Azure Monitor для контейнеров HELM с рабочей областью Log Analytics.

### <a name="how-to-add-the-azure-monitor-containers-solution"></a>Добавление решения "контейнеры Azure Monitor"

Вы можете развернуть решение с помощью указанного шаблона Azure Resource Manager, используя командлет Azure PowerShell `New-AzResourceGroupDeployment` или с Azure CLI.

Если вы не знакомы с концепцией развертывания ресурсов с помощью шаблона, ознакомьтесь со статьями:

* [Развертывание ресурсов с использованием шаблонов Resource Manager и Azure PowerShell](../../azure-resource-manager/resource-group-template-deploy.md)

* [Развертывание ресурсов с использованием шаблонов Resource Manager и Azure CLI](../../azure-resource-manager/resource-group-template-deploy-cli.md)

Если вы решили использовать Azure CLI, необходимо сначала установить интерфейс командной строки и использовать его локально. Необходимо запустить Azure CLI версии 2.0.59 или более поздней. Для определения версии выполните `az --version`. Если вам необходимо установить или обновить Azure CLI, ознакомьтесь со статьей [Установка Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli). 

В этом методе используются два шаблона JSON. Один шаблон задает конфигурацию для включения мониторинга, а другой содержит значения параметров, которые можно настроить, чтобы указать следующее:

- **воркспацересаурцеид** — полный идентификатор ресурса рабочей области log Analytics.
- **воркспацерегион** — регион, в котором создана Рабочая область, которая также называется **расположением** в свойствах рабочей области при просмотре из портал Azure.

Чтобы сначала указать полный идентификатор ресурса Log Analytics рабочей области, необходимой для значения параметра `workspaceResourceId` в файле **контаинерсолутионпарамс. JSON** , выполните следующие действия, а затем выполните командлет PowerShell или команду Azure CLI, чтобы добавить решение.

1. Перечислите все подписки, к которым у вас есть доступ, с помощью следующей команды:

    ```azurecli
    az account list --all -o table
    ```

    Результат должен выглядеть так:

    ```azurecli
    Name                                  CloudName    SubscriptionId                        State    IsDefault
    ------------------------------------  -----------  ------------------------------------  -------  -----------
    Microsoft Azure                       AzureCloud   68627f8c-91fO-4905-z48q-b032a81f8vy0  Enabled  True
    ```

    Скопируйте значение для **SubscriptionId**.

2. Перейдите к подписке, в которой размещена рабочая область Log Analytics, с помощью следующей команды:

    ```azurecli
    az account set -s <subscriptionId of the workspace>
    ```

3. В следующем примере отображается список рабочих областей в подписках в формате JSON по умолчанию. 

    ```
    az resource list --resource-type Microsoft.OperationalInsights/workspaces -o json
    ```

    В выходных данных найдите имя рабочей области, а затем скопируйте полный идентификатор ресурса этой Log Analytics рабочей области под **идентификатором**поля.

4. Скопируйте и вставьте в него следующий синтаксис JSON:

    ```json
    {
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "workspaceResourceId": {
            "type": "string",
            "metadata": {
                "description": "Azure Monitor Log Analytics Workspace Resource ID"
            }
        },
        "workspaceRegion": {
            "type": "string",
            "metadata": {
                "description": "Azure Monitor Log Analytics Workspace region"
            }
        }
    },
    "resources": [
        {
            "type": "Microsoft.Resources/deployments",
            "name": "[Concat('ContainerInsights', '-',  uniqueString(parameters('workspaceResourceId')))]",
            "apiVersion": "2017-05-10",
            "subscriptionId": "[split(parameters('workspaceResourceId'),'/')[2]]",
            "resourceGroup": "[split(parameters('workspaceResourceId'),'/')[4]]",
            "properties": {
                "mode": "Incremental",
                "template": {
                    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
                    "contentVersion": "1.0.0.0",
                    "parameters": {},
                    "variables": {},
                    "resources": [
                        {
                            "apiVersion": "2015-11-01-preview",
                            "type": "Microsoft.OperationsManagement/solutions",
                            "location": "[parameters('workspaceRegion')]",
                            "name": "[Concat('ContainerInsights', '(', split(parameters('workspaceResourceId'),'/')[8], ')')]",
                            "properties": {
                                "workspaceResourceId": "[parameters('workspaceResourceId')]"
                            },
                            "plan": {
                                "name": "[Concat('ContainerInsights', '(', split(parameters('workspaceResourceId'),'/')[8], ')')]",
                                "product": "[Concat('OMSGallery/', 'ContainerInsights')]",
                                "promotionCode": "",
                                "publisher": "Microsoft"
                            }
                        }
                    ]
                },
                "parameters": {}
            }
         }
      ]
   }
    ```

5. Сохраните этот файл как Контаинерсолутион. JSON в локальной папке.

6. Вставьте в него следующий синтаксис JSON:

    ```json
    {
      "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
        "workspaceResourceId": {
          "value": "<workspaceResourceId>"
      },
      "workspaceRegion": {
        "value": "<workspaceRegion>"
      }
     }
    }
    ```

7. Измените значения для **воркспацересаурцеид** , используя значение, скопированное на шаге 3, а для **Воркспацерегион** скопируйте значение **региона** после выполнения команды Azure CLI [AZ Monitor журнала-Analytics Workspace](https://docs.microsoft.com/cli/azure/monitor/log-analytics/workspace?view=azure-cli-latest#az-monitor-log-analytics-workspace-list).

8. Сохраните этот файл как Контаинерсолутионпарамс. JSON в локальной папке.

9. Теперь вы можете развернуть этот шаблон. 

   * Чтобы выполнить развертывание с Azure PowerShell, используйте следующие команды в папке, содержащей шаблон:

       ```powershell
       # configure and login to the cloud of log analytics workspace.Specify the corresponding cloud environment of your workspace to below command.
       Connect-AzureRmAccount -Environment <AzureCloud | AzureChinaCloud | AzureUSGovernment>
       ```

       ```powershell
       # set the context of the subscription of Log Analytics workspace
       Set-AzureRmContext -SubscriptionId <subscription Id of log analytics workspace>
       ```
       
       ```powershell
       # execute deployment command to add container insights solution to the specified Log Analytics workspace
       New-AzureRmResourceGroupDeployment -Name OnboardCluster -ResourceGroupName <resource group of log analytics workspace> -TemplateFile .\containerSolution.json -TemplateParameterFile .\containerSolutionParams.json
       ```
       
       Изменение конфигурации может занять несколько минут. После завершения настройки конфигурации появится сообщение, похожее на приведенное ниже, с таким результатом:

       ```powershell
       provisioningState       : Succeeded
       ```

   * Чтобы выполнить развертывание с Azure CLI, выполните следующие команды:
    
       ```azurecli
       az login
       az account set --name <AzureCloud | AzureChinaCloud | AzureUSGovernment>
       az login
       az account set --subscription "Subscription Name"
       # execute deployment command to add container insights solution to the specified Log Analytics workspace
       az group deployment create --resource-group <resource group of log analytics workspace> --template-file ./containerSolution.json --parameters @./containerSolutionParams.json
       ```

       Изменение конфигурации может занять несколько минут. После завершения настройки конфигурации появится сообщение, похожее на приведенное ниже, с таким результатом:

       ```azurecli
       provisioningState       : Succeeded
       ```
     
       После включения мониторинга может пройти около 15 минут, прежде чем вы сможете просмотреть метрики работоспособности кластера. 

## <a name="install-the-chart"></a>Установка диаграммы

Чтобы включить диаграмму HELM, выполните следующие действия.

1. Добавьте репозиторий диаграмм Azure в локальный список, выполнив следующую команду:

    ```
    helm repo add incubator https://kubernetes-charts-incubator.storage.googleapis.com/
    ````

2. Установите диаграмму, выполнив следующую команду:

    ```
    $ helm install --name myrelease-1 \
    --set omsagent.secret.wsid=<your_workspace_id>,omsagent.secret.key=<your_workspace_key>,omsagent.env.clusterName=<my_prod_cluster> incubator/azuremonitor-containers
    ```

    Если Рабочая область Log Analytics находится в Azure для Китая, выполните следующую команду:

    ```
    $ helm install --name myrelease-1 \
     --set omsagent.domain=opinsights.azure.cn,omsagent.secret.wsid=<your_workspace_id>,omsagent.secret.key=<your_workspace_key>,omsagent.env.clusterName=<your_cluster_name> incubator/azuremonitor-containers 
    ```

    Если Рабочая область Log Analytics находится в Azure для государственных организаций США, выполните следующую команду:

    ```
    $ helm install --name myrelease-1 \
    --set omsagent.domain=opinsights.azure.us,omsagent.secret.wsid=<your_workspace_id>,omsagent.secret.key=<your_workspace_key>,omsagent.env.clusterName=<your_cluster_name> incubator/azuremonitor-containers
    ```

## <a name="configure-agent-data-collection"></a>Настройка сбора данных агента

С помощью схемы версии 1.0.0 параметры сбора данных агента контролируются из ConfigMap. См. документацию о параметрах сбора данных агента [здесь](container-insights-agent-config.md). 

После успешного развертывания диаграммы можно ознакомиться с данными для гибридного кластера Kubernetes в Azure Monitor для контейнеров из портал Azure.  

>[!NOTE]
>Задержка приема составляет от агента до десяти минут до фиксации в рабочей области Azure Log Analytics. Состояние кластера отображает значение **нет данных** или **неизвестно** , пока все необходимые данные мониторинга не будут доступны в Azure Monitor. 

## <a name="next-steps"></a>Дальнейшие действия

С включенным наблюдением для получения сведений о работоспособности и использовании ресурсов для гибридного кластера Kubernetes и рабочих нагрузок, которые работают на них, Узнайте, [как использовать](container-insights-analyze.md) Azure Monitor для контейнеров.
