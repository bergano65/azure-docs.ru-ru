---
title: Подключение Azure Monitor для контейнеров | Документация Майкрософт
description: В этой статье объясняется, как подключить и настроить Azure Monitor для контейнеров, чтобы получать сведения о работе контейнера и выявленных проблемах с его производительностью.
services: azure-monitor
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: azure-monitor
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/27/2019
ms.author: magoedte
ms.openlocfilehash: 403cbeb0a68e39eab714ceb428fcfaefe8de0ff7
ms.sourcegitcommit: c63fe69fd624752d04661f56d52ad9d8693e9d56
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2019
ms.locfileid: "58576247"
---
# <a name="how-to-onboard-azure-monitor-for-containers"></a>Подключение Azure Monitor для контейнеров  

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

В этой статье объясняется, как настроить Azure Monitor для контейнеров, чтобы отслеживать производительность рабочих нагрузок, развернутых в средах Kubernetes и размещенных в [Службе Azure Kubernetes](https://docs.microsoft.com/azure/aks/).

Azure Monitor для контейнеров можно включить для новых или существующих развертываний AKS с помощью следующих методов:

* На портале Azure, Azure PowerShell или с помощью Azure CLI
* используя [Terraform и AKS](../../terraform/terraform-create-k8s-cluster-with-tf-and-aks.md).


## <a name="prerequisites"></a>Технические условия 
Чтобы начать, у вас должны быть следующие компоненты:

- **Рабочая область Log Analytics.** Ее можно создать при включении мониторинга нового кластера AKS. Кроме того, средство подключения может создать рабочую область по умолчанию в стандартной группе ресурсов подписки кластера AKS. Если вы хотите создать ее самостоятельно, используйте [Azure Resource Manager](../../azure-monitor/platform/template-workspace-configuration.md), [PowerShell](../scripts/powershell-sample-create-workspace.md?toc=%2fpowershell%2fmodule%2ftoc.json) или [портал Azure](../../azure-monitor/learn/quick-create-workspace.md).
- Вы являетесь членом **роль "участник" Log Analytics** Чтобы включить мониторинг контейнеров. Дополнительные сведения о том, как управлять доступом к рабочей области Log Analytics, см. в статье [Управление рабочими областями](../../azure-monitor/platform/manage-access.md).
- Вы являетесь членом **[владельца](https://docs.microsoft.com/en-us/azure/role-based-access-control/built-in-role.mds#owner)** роли на ресурс кластера AKS. 

[!INCLUDE [log-analytics-agent-note](../../../includes/log-analytics-agent-note.md)]

## <a name="components"></a>Компоненты 

Возможность отслеживать производительность зависит от контейнерного агента Log Analytics для Linux, специально разработанного для Azure Monitor для контейнеров. Этот специализированный агент собирает данные о производительности и событиях со всех узлов кластера, а потом автоматически развертывается и регистрируется в указанной рабочей области Log Analytics во время развертывания. Версия агента — microsoft/oms:ciprod04202018 и выше. Номер представлен датой в формате *ммддгггг*. 

При выпуске новой версии агент автоматически обновляется в управляемых кластерах Kubernetes, размещенных в Службе Azure Kubernetes (AKS). Выпущенные версии см. в разделе [объявлений о выпуске агента](https://github.com/microsoft/docker-provider/tree/ci_feature_prod). 

>[!NOTE] 
>Если вы уже развернули кластер AKS, то можете включить мониторинг с помощью Azure CLI или предоставив шаблон Azure Resource Manager, как показано далее в этой статье. Невозможно использовать `kubectl` для обновления, удаления, повторного развертывания или развертывания агента. Развертывание шаблона должно проходить в той же группе ресурсов, что и у кластера.

## <a name="sign-in-to-the-azure-portal"></a>Вход на портал Azure
Войдите на [портале Azure](https://portal.azure.com). 

## <a name="enable-monitoring-for-a-new-cluster"></a>Включение мониторинга для нового кластера
Во время развертывания вы можете включить мониторинг нового кластера AKS с помощью портала Azure, Azure CLI или Terraform.  Чтобы включить на портале, выполните действия, описанные в статье примеры использования [развертывание кластера службы Azure Kubernetes (AKS)](../../aks/kubernetes-walkthrough-portal.md) и выполните действия, описанные в разделе **мониторинг работоспособности и журналы**.  

>[!NOTE]
>При действий, описанных в статье быстрого запуска, чтобы включить мониторинг для кластера AKS с портала, предлагается создать новую или выберите существующую рабочую область Log Analytics. 

### <a name="enable-using-azure-cli"></a>Включение мониторинга с помощью Azure CLI
Чтобы включить мониторинг нового кластера AKS, созданного с помощью Azure CLI, следуйте указаниям в разделе [Создание кластера AKS](../../aks/kubernetes-walkthrough.md#create-aks-cluster).  

>[!NOTE]
>Если вы решили использовать Azure CLI, необходимо сначала установить интерфейс командной строки и использовать его локально. Необходимо запустить Azure CLI версии 2.0.59 или более поздней версии. Для определения версии выполните `az --version`. Если вам необходимо установить или обновить Azure CLI, ознакомьтесь со статьей [Установка Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli). 
>

### <a name="enable-using-terraform"></a>Включение с помощью Terraform
Если вы [развертываете новый кластер AKS с помощью Terraform](../../terraform/terraform-create-k8s-cluster-with-tf-and-aks.md), нужно указать аргументы, необходимые в профиле [для создания рабочей области Log Analytics](https://www.terraform.io/docs/providers/azurerm/r/log_analytics_workspace.html), если вы не решили указать уже существующую. 

>[!NOTE]
>Если вы решили использовать Terraform, необходимо запустить Terraform Azure RM Provider версии 1.17.0 или выше.

Чтобы добавить Azure Monitor для контейнеров в рабочую область, просмотрите [azurerm_log_analytics_solution](https://www.terraform.io/docs/providers/azurerm/r/log_analytics_solution.html), заполните профиль, включив [**addon_profile**](https://www.terraform.io/docs/providers/azurerm/r/kubernetes_cluster.html#addon_profile) и укажите **oms_agent**. 

После включения мониторинга и успешного выполнения всех задач настройки можно отслеживать производительность кластера одним из двух способов:

* Непосредственно в кластере AKS, выбрав **Работоспособность** в левой области.
* Выбрав плитку **Monitor Container insights** (Мониторинг аналитических сведений о контейнере) на странице кластеров AKS для выбранного кластера. В Azure Monitor выберите **Работоспособность** в левой области. 

  ![Параметры для выбора Azure Monitor для контейнеров в AKS](./media/container-insights-onboard/kubernetes-select-monitoring-01.png)

После включения мониторинга может пройти около 15 минут, прежде чем вы сможете просмотреть метрики работоспособности кластера. 

## <a name="enable-monitoring-for-existing-managed-clusters"></a>Включение мониторинга для существующих управляемых кластеров
Вы можете включить наблюдение за кластер AKS, который уже развернут с помощью одного из поддерживаемых методов:

* Инфраструктура CLI Azure
* Terraform
* [Из Azure Monitor](#enable-from-azure-monitor-in-the-portal) или [непосредственно из кластера AKS](#enable-directly-from-aks-cluster-in-the-portal) на портале Azure 
* С помощью [предоставленный шаблон Azure Resource Manager](#enable-using-an-azure-resource-manager-template) с помощью командлета Azure PowerShell `New-AzResourceGroupDeployment` или с помощью Azure CLI. 

### <a name="enable-using-azure-cli"></a>Включение мониторинга с помощью Azure CLI
Ниже показано, как включить мониторинг кластера AKS с помощью Azure CLI. В этом примере вы не обязаны предварительно создавать или указывать имеющуюся рабочую область. Эта команда упрощает процесс включения за счет создания рабочей области по умолчанию в стандартной группе ресурсов подписки кластера AKS, если эта область еще не создана в регионе.  Имя созданной рабочей области по умолчанию поддерживает формат *DefaultWorkspace-\<GUID>-\<Region>*.  

```azurecli
az aks enable-addons -a monitoring -n MyExistingManagedCluster -g MyExistingManagedClusterRG  
```

Результат должен выглядеть так:

```azurecli
provisioningState       : Succeeded
```

Если вместо этого потребуется выполнить интеграцию в существующую рабочую область, то для ее указания используйте следующую команду.

```azurecli
az aks enable-addons -a monitoring -n MyExistingManagedCluster -g MyExistingManagedClusterRG --workspace-resource-id <ExistingWorkspaceResourceID> 
```

Результат должен выглядеть так:

```azurecli
provisioningState       : Succeeded
```

### <a name="enable-using-terraform"></a>Включение с помощью Terraform
1. Добавьте дополнительный профиль **oms_agent** к существующему [ресурсу azurerm_kubernetes_cluster](https://www.terraform.io/docs/providers/azurerm/d/kubernetes_cluster.html#addon_profile).

   ```
   addon_profile {
    oms_agent {
      enabled                    = true
      log_analytics_workspace_id = "${azurerm_log_analytics_workspace.test.id}"
     }
   }
   ```

2. Добавьте [azurerm_log_analytics_solution](https://www.terraform.io/docs/providers/azurerm/r/log_analytics_solution.html), выполнив шаги, описанные в документации Terraform.

### <a name="enable-from-azure-monitor-in-the-portal"></a>Включение из Azure Monitor на портале 
Чтобы включить мониторинг кластера AKS на портале Azure из раздела Azure Monitor, выполните следующие действия:

1. На портале Azure выберите **Монитор**. 
2. В списке выберите **Контейнеры**.
3. На странице **Контейнеры** выберите **Non-monitored clusters** (Неотслеживаемые кластеры).
4. В списке неотслеживаемых кластеров выберите контейнер и щелкните **Включить**.   
5. Если рабочая область Log Analytics расположена в той же подписке, что и кластер, выберите ее из раскрывающегося списка на странице **Onboarding to Azure Monitor for containers** (Подключение к Azure Monitor для контейнеров).  
    В списке предварительно выбрана рабочая область по умолчанию и расположение, в которое контейнер AKS развертывается в подписке. 

    ![Включение мониторинга аналитических сведений о контейнере AKS](./media/container-insights-onboard/kubernetes-onboard-brownfield-01.png)

    >[!NOTE]
    >Если вы хотите создать рабочую область Log Analytics для хранения данных мониторинга из кластера, выполните инструкции, описанные в статье [Создание рабочей области Log Analytics на портале Azure](../../azure-monitor/learn/quick-create-workspace.md). Создайте рабочую область в той же подписке, в которой развернут контейнер AKS. 
 
После включения мониторинга может пройти около 15 минут, прежде чем вы сможете просмотреть метрики работоспособности кластера. 

### <a name="enable-directly-from-aks-cluster-in-the-portal"></a>Включить непосредственно из кластера AKS на портале
Чтобы включить мониторинг непосредственно с одного из кластеров AKS на портале Azure, сделайте следующее:

1. На портале Azure щелкните **Все службы**. 
2. В списке ресурсов введите **Контейнеры**.  
    Как только вы начнете вводить данные, список отфильтруется соответствующим образом. 
3. Выберите **Службы Kubernetes**.  

    ![Ссылка на Службы Kubernetes](./media/container-insights-onboard/portal-search-containers-01.png)

4. В списке контейнеров выберите нужный контейнер.
5. На странице обзора контейнеров выберите **Контейнеры мониторов**.  
6. Если имеющаяся рабочая область Log Analytics расположена в той же подписке, что и кластер, выберите ее из раскрывающегося списка на странице **Onboarding to Azure Monitor for containers** (Подключение к Azure Monitor для контейнеров).  
    В списке предварительно выбрана рабочая область по умолчанию и расположение, в которое контейнер AKS развертывается в подписке. 

    ![Включение мониторинга работоспособности контейнера AKS](./media/container-insights-onboard/kubernetes-onboard-brownfield-02.png)

    >[!NOTE]
    >Если вы хотите создать рабочую область Log Analytics для хранения данных мониторинга из кластера, выполните инструкции, описанные в статье [Создание рабочей области Log Analytics на портале Azure](../../azure-monitor/learn/quick-create-workspace.md). Создайте рабочую область в той же подписке, в которой развернут контейнер AKS. 
 
После включения мониторинга может потребоваться подождать около 15 минут, прежде чем вы сможете просмотреть операционные данные кластера. 

### <a name="enable-using-an-azure-resource-manager-template"></a>Включить с помощью шаблона Azure Resource Manager
В этом методе используются два шаблона JSON. Один шаблон задает конфигурацию для включения мониторинга, а другой содержит значения параметров, которые можно настроить, чтобы указать следующее:

* идентификатор ресурса контейнера AKS; 
* группу ресурсов, в которой развернут кластер;

>[!NOTE]
>Развертывание шаблона должно проходить в той же группе ресурсов, что и у кластера.
>

Рабочая область Log Analytics должна создаваться, прежде чем включить мониторинг с помощью Azure PowerShell или интерфейса командной строки. Для создания рабочей области можно использовать [Azure Resource Manager](../../azure-monitor/platform/template-workspace-configuration.md), [PowerShell](../scripts/powershell-sample-create-workspace.md?toc=%2fpowershell%2fmodule%2ftoc.json) или [портал Azure](../../azure-monitor/learn/quick-create-workspace.md).

Если вы не знакомы с концепцией развертывания ресурсов с помощью шаблона, ознакомьтесь со статьями:
* [Развертывание ресурсов с использованием шаблонов Resource Manager и Azure PowerShell](../../azure-resource-manager/resource-group-template-deploy.md)
* [Развертывание ресурсов с использованием шаблонов Resource Manager и Azure CLI](../../azure-resource-manager/resource-group-template-deploy-cli.md)

Если вы решили использовать Azure CLI, необходимо сначала установить интерфейс командной строки и использовать его локально. Необходимо запустить Azure CLI версии 2.0.59 или более поздней версии. Для определения версии выполните `az --version`. Если вам необходимо установить или обновить Azure CLI, ознакомьтесь со статьей [Установка Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli). 

#### <a name="create-and-execute-a-template"></a>Создание и выполнение шаблона

1. Скопируйте и вставьте в него следующий синтаксис JSON:

    ```json
    {
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
      "aksResourceId": {
        "type": "string",
        "metadata": {
           "description": "AKS Cluster Resource ID"
           }
    },
    "aksResourceLocation": {
    "type": "string",
     "metadata": {
        "description": "Location of the AKS resource e.g. \"East US\""
       }
    },
    "workspaceResourceId": {
      "type": "string",
      "metadata": {
         "description": "Azure Monitor Log Analytics Resource ID"
       }
    }
    },
    "resources": [
      {
    "name": "[split(parameters('aksResourceId'),'/')[8]]",
    "type": "Microsoft.ContainerService/managedClusters",
    "location": "[parameters('aksResourceLocation')]",
    "apiVersion": "2018-03-31",
    "properties": {
      "mode": "Incremental",
      "id": "[parameters('aksResourceId')]",
      "addonProfiles": {
        "omsagent": {
          "enabled": true,
          "config": {
            "logAnalyticsWorkspaceResourceID": "[parameters('workspaceResourceId')]"
          }
         }
       }
      }
     }
     ]
    }
    ```

2. Сохраните этот файл как **existingClusterOnboarding.json** в локальную папку.
3. Вставьте в него следующий синтаксис JSON:

    ```json
    {
       "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
       "contentVersion": "1.0.0.0",
       "parameters": {
         "aksResourceId": {
           "value": "/subscriptions/<SubscriptionId>/resourcegroups/<ResourceGroup>/providers/Microsoft.ContainerService/managedClusters/<ResourceName>"
       },
       "aksResourceLocation": {
         "value": "<aksClusterLocation>"
       },
       "workspaceResourceId": {
         "value": "/subscriptions/<SubscriptionId>/resourceGroups/<ResourceGroup>/providers/Microsoft.OperationalInsights/workspaces/<workspaceName>"
       }  
     }
    }
    ```

4. Измените значения для **aksResourceId** и **aksResourceLocation** с помощью значений на **AKS Обзор** страницы в кластере AKS. Значение для **workspaceResourceId** — это полный идентификатор ресурса рабочей области Log Analytics, который включает имя рабочей области. 
5. Сохраните этот файл как **existingClusterParam.json** в локальную папку.
6. Теперь вы можете развернуть этот шаблон. 

   * Чтобы развернуть с помощью Azure PowerShell, используйте следующие команды в папке, содержащей шаблон:

       ```powershell
       New-AzResourceGroupDeployment -Name OnboardCluster -ResourceGroupName <ResourceGroupName> -TemplateFile .\existingClusterOnboarding.json -TemplateParameterFile .\existingClusterParam.json
       ```
       Изменение конфигурации может занять несколько минут. После завершения настройки конфигурации появится сообщение, похожее на приведенное ниже, с таким результатом:

       ```powershell
       provisioningState       : Succeeded
       ```

   * Чтобы развернуть с помощью Azure CLI, выполните следующие команды:
    
       ```azurecli
       az login
       az account set --subscription "Subscription Name"
       az group deployment create --resource-group <ResourceGroupName> --template-file ./existingClusterOnboarding.json --parameters @./existingClusterParam.json
       ```

       Изменение конфигурации может занять несколько минут. После завершения настройки конфигурации появится сообщение, похожее на приведенное ниже, с таким результатом:

       ```azurecli
       provisioningState       : Succeeded
       ```
     После включения мониторинга может пройти около 15 минут, прежде чем вы сможете просмотреть метрики работоспособности кластера. 

## <a name="verify-agent-and-solution-deployment"></a>Проверка развертывания агента и решения
В версии агента *06072018* и выше есть возможность удостовериться, что агент и решение были успешно развернуты. В более ранних версиях агента возможна только проверка развертывания агента.

### <a name="agent-version-06072018-or-later"></a>Версия агента 06072018 или более поздняя
Выполните следующую команду, чтобы проверить успешное развертывание агента. 

```
kubectl get ds omsagent --namespace=kube-system
```

Результат должен выглядеть приблизительно, как показано ниже, что означает успешное выполнение развертывания:

```
User@aksuser:~$ kubectl get ds omsagent --namespace=kube-system 
NAME       DESIRED   CURRENT   READY     UP-TO-DATE   AVAILABLE   NODE SELECTOR                 AGE
omsagent   2         2         2         2            2           beta.kubernetes.io/os=linux   1d
```  

Чтобы проверить развертывание решения, выполните следующую команду:

```
kubectl get deployment omsagent-rs -n=kube-system
```

Результат должен выглядеть приблизительно, как показано ниже, что означает успешное выполнение развертывания:

```
User@aksuser:~$ kubectl get deployment omsagent-rs -n=kube-system 
NAME       DESIRED   CURRENT   UP-TO-DATE   AVAILABLE    AGE
omsagent   1         1         1            1            3h
```

### <a name="agent-version-earlier-than-06072018"></a>Агент версии более ранней, чем 06072018

Чтобы проверить, правильно ли развернут агент Log Analytics версии более ранней, чем *06072018*, выполните следующую команду:  

```
kubectl get ds omsagent --namespace=kube-system
```

Результат должен выглядеть приблизительно, как показано ниже, что означает успешное выполнение развертывания:  

```
User@aksuser:~$ kubectl get ds omsagent --namespace=kube-system 
NAME       DESIRED   CURRENT   READY     UP-TO-DATE   AVAILABLE   NODE SELECTOR                 AGE
omsagent   2         2         2         2            2           beta.kubernetes.io/os=linux   1d
```  

## <a name="view-configuration-with-cli"></a>Просмотр конфигурации с помощью CLI
С помощью команды `aks show` можно получить определенные сведения, например включено ли решение, идентификатор ресурса рабочей области Log Analytics, а также сводные данные о кластере.  

```azurecli
az aks show -g <resourceGroupofAKSCluster> -n <nameofAksCluster>
```

Через несколько минут выполнение команды завершается и отображаются сведения о решении в формате JSON.  В результатах выполнения команды должен отобразиться профиль надстройки наблюдения. Вы должны увидеть результат, аналогичный следующему:

```
"addonProfiles": {
    "omsagent": {
      "config": {
        "logAnalyticsWorkspaceResourceID": "/subscriptions/<WorkspaceSubscription>/resourceGroups/<DefaultWorkspaceRG>/providers/Microsoft.OperationalInsights/workspaces/<defaultWorkspaceName>"
      },
      "enabled": true
    }
  }
```
## <a name="next-steps"></a>Дальнейшие действия

* Если при попытке подключить решение у вас возникли проблемы, ознакомьтесь с [руководством по устранению неполадок](container-insights-troubleshoot.md).

* Если включен мониторинг для сбора метрик работоспособности узлов и контейнеров pod кластера AKS, эти метрики доступны на портале Azure. Сведения об использовании Azure Monitor для контейнеров см. в руководстве по [просмотру данных работоспособности службы Azure Kubernetes](container-insights-analyze.md).
