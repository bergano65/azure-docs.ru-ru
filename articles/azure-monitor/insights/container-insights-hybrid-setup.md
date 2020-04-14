---
title: Налаживание гибридных кластеров Kubernetes с помощью Azure Monitor для контейнеров Документы Майкрософт
description: В этой статье описывается, как можно настроить Azure Monitor для контейнеров для мониторинга кластеров Kubernetes, размещенных в Azure Stack или другой среде.
ms.topic: conceptual
ms.date: 01/24/2020
ms.openlocfilehash: 5a973e7e500906ebe833ec4cc6fd2fa8ee79c19e
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/13/2020
ms.locfileid: "81255436"
---
# <a name="configure-hybrid-kubernetes-clusters-with-azure-monitor-for-containers"></a>Налаживание гибридных кластеров Kubernetes с помощью Azure Monitor для контейнеров

Azure Monitor для контейнеров предоставляет богатый опыт мониторинга для службы Azure Kubernetes (AKS) и [AKS Engine on Azure,](https://github.com/Azure/aks-engine)который является самоуправляемым кластером Kubernetes, размещенным на Azure. В этой статье описывается, как обеспечить мониторинг кластеров Kubernetes, размещенных за пределами Azure, и получить аналогичный опыт мониторинга.

## <a name="prerequisites"></a>Предварительные требования

Чтобы начать, у вас должны быть следующие компоненты:

* Рабочая область Log Analytics.

    Azure Monitor для контейнеров поддерживает рабочее пространство log Analytics в регионах, перечисленных в продуктах Azure [по регионам.](https://azure.microsoft.com/global-infrastructure/services/?regions=all&products=monitor) Чтобы создать собственное рабочее пространство, оно может быть создано через [менеджер ресурсов Azure,](../platform/template-workspace-configuration.md)через [PowerShell](../scripts/powershell-sample-create-workspace.md?toc=%2fpowershell%2fmodule%2ftoc.json)или [на портале Azure.](../learn/quick-create-workspace.md)

    >[!NOTE]
    >Включение мониторинга нескольких кластеров с одинаковым названием кластера в одно и то же рабочее пространство Log Analytics не поддерживается. Названия кластеров должны быть уникальными.
    >

* Вы являетесь участником **роли участника Log Analytics** для включения мониторинга контейнеров. Для получения дополнительной информации о том, как контролировать доступ к рабочей области Log Analytics, [см. Управление доступом к рабочей области и данным журналов](../platform/manage-access.md)

* [Клиент HELM](https://helm.sh/docs/using_helm/) на борту Azure Monitor для диаграммы контейнеров для указанного кластера Kubernetes.

* Для связи с Azure Monitor требуется следующая информация о конфигурации прокси и брандмауэра:

    |Ресурс агента|порты; |
    |------|---------|   
    |*.ods.opinsights.azure.com |Порт 443 |  
    |*.oms.opinsights.azure.com |Порт 443 |  
    |*.blob.core.windows.net |Порт 443 |  
    |В.dc.services.visualstudio.com |Порт 443 |

* Контейнерный агент требует, чтобы `cAdvisor secure port: 10250` Kubelet или `unsecure port :10255` для открытия на всех узлах кластера для сбора метрик производительности. Мы рекомендуем вам `secure port: 10250` настроить на cAdvisor Kubelet, если он еще не настроен.

* Контейнерный агент требует, чтобы в контейнере были указаны следующие экологические переменные для связи с службой `KUBERNETES_SERVICE_HOST` `KUBERNETES_PORT_443_TCP_PORT`API Kubernetes в кластере для сбора кадастровых данных - и .

>[!IMPORTANT]
>Версия минимального агента, поддерживаемая для мониторинга гибридных кластеров Kubernetes, ciprod10182019 или позже.

## <a name="supported-configurations"></a>Поддерживаемые конфигурации

Следующее официально поддерживается Azure Monitor для контейнеров.

- Среды: Kubernetes на месте, AKS двигатель на Azure и Azure стек. Для получения дополнительной [AKS Engine on Azure Stack](https://docs.microsoft.com/azure-stack/user/azure-stack-kubernetes-aks-engine-overview?view=azs-1908)информации см.
- Версии Kubernetes и политика поддержки такие же, как версии [поддерживаемых AKS.](../../aks/supported-kubernetes-versions.md)
- Контейнер Runtime: Докер и Моби
- Выпуск Linux OS для мастерских и сработанных узлов: Ubuntu (18.04 LTS и 16.04 LTS)
- Контроль доступа поддерживается: Kubernetes RBAC и не-RBAC

## <a name="enable-monitoring"></a>Включение мониторинга

Включение Azure Monitor для контейнеров для гибридного кластера Kubernetes состоит в выполнении следующих шагов в порядке.

1. Настроите рабочее пространство журнала Analytics с помощью решения Container Insights.

2. Включите монитор Azure для контейнеров HELM диаграммы с рабочим пространством Log Analytics.

### <a name="how-to-add-the-azure-monitor-containers-solution"></a>Как добавить решение для контейнеров Azure Monitor

Вы можете развернуть решение с предоставленным шаблоном менеджера ресурсов Azure, используя cmdlet `New-AzResourceGroupDeployment` Azure PowerShell или с помощью Azure CLI.

Если вы не знакомы с концепцией развертывания ресурсов с помощью шаблона, ознакомьтесь со статьями:

* [Развертывание ресурсов с использованием шаблонов Resource Manager и Azure PowerShell](../../azure-resource-manager/templates/deploy-powershell.md)

* [Развертывание ресурсов с использованием шаблонов Resource Manager и Azure CLI](../../azure-resource-manager/templates/deploy-cli.md)

Если вы решили использовать Azure CLI, необходимо сначала установить интерфейс командной строки и использовать его локально. Вы должны запустить версию Azure CLI 2.0.59 или позже. Для определения версии выполните `az --version`. Если вам необходимо установить или обновить Azure CLI, ознакомьтесь со статьей [Установка Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli).

В этом методе используются два шаблона JSON. Один шаблон задает конфигурацию для включения мониторинга, а другой содержит значения параметров, которые можно настроить, чтобы указать следующее:

- **workspaceResourceId** - полный идентификатор ресурсов рабочего пространства Log Analytics.
- **workspaceRegion** - область, в которой создается рабочее пространство, которое также называется **Расположением** в свойствах рабочей области при просмотре с портала Azure.

Чтобы сначала определить полный идентификатор ресурса рабочего пространства Log Analytics, необходимого для значения `workspaceResourceId` параметра в файле **containerSolutionParams.json,** выполните следующие действия, а затем запустите команду PowerShell cmdlet или команду Azure CLI для добавления решения.

1. Перечислите все подписки, к которым вы имеете доступ с помощью следующей команды:

    ```azurecli
    az account list --all -o table
    ```

    Результат должен выглядеть так:

    ```azurecli
    Name                                  CloudName    SubscriptionId                        State    IsDefault
    ------------------------------------  -----------  ------------------------------------  -------  -----------
    Microsoft Azure                       AzureCloud   68627f8c-91fO-4905-z48q-b032a81f8vy0  Enabled  True
    ```

    Копирование значения для **SubscriptionId**.

2. Переключитесь на подписку, в которой размещается рабочее пространство Log Analytics, используя следующую команду:

    ```azurecli
    az account set -s <subscriptionId of the workspace>
    ```

3. В следующем примере отображается список рабочих областей в подписках в формате JSON по умолчанию.

    ```
    az resource list --resource-type Microsoft.OperationalInsights/workspaces -o json
    ```

    В выводе найдите имя рабочего пространства, а затем скопируйте полный идентификатор ресурсов рабочего пространства Log Analytics под **идентификатором**поля.

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

5. Сохраните этот файл в виде контейнераSolution.json в локальной папке.

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

7. Отображение значений для **workspaceResourceId** с помощью значения, скопированного на шаг 3, а для **workspaceRegion** скопировать значение **региона** после запуска рабочего пространства мониторинга AzI Az [monitor.](https://docs.microsoft.com/cli/azure/monitor/log-analytics/workspace?view=azure-cli-latest#az-monitor-log-analytics-workspace-list)

8. Сохраните этот файл в виде контейнераSolutionParams.json в локальной папке.

9. Теперь вы можете развернуть этот шаблон.

   * Чтобы развернуть с Помощью Azure PowerShell, используйте следующие команды в папке, содержащей шаблон:

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

   * Чтобы развернуть с Azure CLI, запустите следующие команды:

       ```azurecli
       az login
       az account set --name <AzureCloud | AzureChinaCloud | AzureUSGovernment>
       az login
       az account set --subscription "Subscription Name"
       # execute deployment command to add container insights solution to the specified Log Analytics workspace
       az deployment group create --resource-group <resource group of log analytics workspace> --name <deployment name> --template-file  ./containerSolution.json --parameters @./containerSolutionParams.json
       ```

       Изменение конфигурации может занять несколько минут. После завершения настройки конфигурации появится сообщение, похожее на приведенное ниже, с таким результатом:

       ```azurecli
       provisioningState       : Succeeded
       ```

       После включения мониторинга может пройти около 15 минут, прежде чем вы сможете просмотреть метрики работоспособности кластера.

## <a name="install-the-chart"></a>Установка диаграммы

Для включения диаграммы HELM сделайте следующее:

1. Добавьте репозиторий диаграмм Azure в локальный список, запустив следующую команду:

    ```
    helm repo add incubator https://kubernetes-charts-incubator.storage.googleapis.com/
    ````

2. Установите диаграмму, запустив следующую команду:

    ```
    $ helm install --name myrelease-1 \
    --set omsagent.secret.wsid=<your_workspace_id>,omsagent.secret.key=<your_workspace_key>,omsagent.env.clusterName=<my_prod_cluster> incubator/azuremonitor-containers
    ```

    Если рабочее пространство log Analytics находится в Azure China, запустите следующую команду:

    ```
    $ helm install --name myrelease-1 \
     --set omsagent.domain=opinsights.azure.cn,omsagent.secret.wsid=<your_workspace_id>,omsagent.secret.key=<your_workspace_key>,omsagent.env.clusterName=<your_cluster_name> incubator/azuremonitor-containers
    ```

    Если рабочее пространство log Analytics находится в правительстве Azure, запустите следующую команду:

    ```
    $ helm install --name myrelease-1 \
    --set omsagent.domain=opinsights.azure.us,omsagent.secret.wsid=<your_workspace_id>,omsagent.secret.key=<your_workspace_key>,omsagent.env.clusterName=<your_cluster_name> incubator/azuremonitor-containers
    ```

## <a name="configure-agent-data-collection"></a>Сбор данных агента для настройки

Глядя с диаграммой версии 1.0.0, настройки сбора данных агента контролируются из ConfigMap. Ссылайтесь на документацию о настройках сбора данных агента [здесь](container-insights-agent-config.md).

После успешного развертывания диаграммы можно просмотреть данные для гибридного кластера Kubernetes в Azure Monitor для контейнеров с портала Azure.  

>[!NOTE]
>Задержка приема в течение пяти-десяти минут от агента до фиксации в рабочей области Analytics журнала Azure. Состояние кластера показывает значение **Нет данных** или **неизвестно** до тех пор, пока все необходимые данные мониторинга не будут доступны в Azure Monitor.

## <a name="troubleshooting"></a>Устранение неполадок

Если вы столкнулись с ошибкой при попытке включить мониторинг для гибридного кластера Kubernetes, скопируйте сценарий PowerShell [TroubleshootError_nonAzureK8s.ps1](https://raw.githubusercontent.com/microsoft/OMS-docker/ci_feature/Troubleshoot/TroubleshootError_nonAzureK8s.ps1) и сохраните его в папке на компьютере. Этот скрипт предоставляется, чтобы помочь обнаружить и исправить возникшие проблемы. Вопросы, которые он предназначен для обнаружения и коррекции попыток являются следующие:

* Указанное рабочее пространство Log Analytics является действительным
* Рабочее пространство журнала Analytics настроено с помощью решения Azure Monitor для контейнеров. Если нет, назначайте рабочее пространство.
* OmsAgent репликсет стручки работают
* OmsAgent daemonset стручки работают
* Служба здравоохранения OmsAgent работает
* Идентификатор рабочего пространства Log Analytics и ключ, настроенный на контейнерном агенте, совпадают с рабочим пространством, с помощью этого конфигурируется Insight.
* Проверка всех узлов linux `kubernetes.io/role=agent` рабочих имеют метку для планирования rs стручка. Если его не существует, добавьте его.
* Проверка `cAdvisor secure port:10250` `unsecure port: 10255` или открытие на всех узлах кластера.

Для выполнения с Azure PowerShell используйте следующие команды в папке, содержащей сценарий:

```powershell
.\TroubleshootError_nonAzureK8s.ps1 - azureLogAnalyticsWorkspaceResourceId </subscriptions/<subscriptionId>/resourceGroups/<resourcegroupName>/providers/Microsoft.OperationalInsights/workspaces/<workspaceName> -kubeConfig <kubeConfigFile> -clusterContextInKubeconfig <clusterContext>
```

## <a name="next-steps"></a>Дальнейшие действия

Благодаря мониторингу, позволяющим собирать работоспособность и использование ресурсов гибридного кластера Kubernetes и рабочих нагрузок, работающих на них, [научитесь использовать](container-insights-analyze.md) Azure Monitor для контейнеров.
