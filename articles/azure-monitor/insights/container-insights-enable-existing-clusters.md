---
title: Мониторинг кластера службы Azure Kubernetes (AKS) (AKS) Документы Майкрософт
description: Узнайте, как включить мониторинг кластера службы Azure Kubernetes (AKS) с Azure Monitor для контейнеров, уже развернутых в подписке.
ms.topic: conceptual
ms.date: 09/12/2019
ms.openlocfilehash: 8589ea71b5c7affadc61d5e4543f734a660ab543
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79275455"
---
# <a name="enable-monitoring-of-azure-kubernetes-service-aks-cluster-already-deployed"></a>Включить мониторинг уже развернутого кластера службы Azure Kubernetes (AKS)

В этой статье описывается, как настроить Azure Monitor для контейнеров для мониторинга управляемого кластера Kubernetes, размещенного в [службе Azure Kubernetes,](https://docs.microsoft.com/azure/aks/) которая уже была развернута в вашей подписке.

Можно включить мониторинг кластера AKS, который уже развернут с помощью одного из поддерживаемых методов:

* Azure CLI
* Terraform
* [От Azure Monitor](#enable-from-azure-monitor-in-the-portal) или [непосредственно из кластера AKS](#enable-directly-from-aks-cluster-in-the-portal) на портале Azure
* С [предоставленным шаблоном менеджера ресурсов Azure](#enable-using-an-azure-resource-manager-template) с помощью cmdlet `New-AzResourceGroupDeployment` Azure PowerShell или с Azure CLI.

## <a name="sign-in-to-the-azure-portal"></a>Вход на портал Azure

Войдите на [портал Azure](https://portal.azure.com).

## <a name="enable-using-azure-cli"></a>Включение мониторинга с помощью Azure CLI

Ниже показано, как включить мониторинг кластера AKS с помощью Azure CLI. В этом примере вы не обязаны предварительно создавать или указывать имеющуюся рабочую область. Эта команда упрощает процесс включения за счет создания рабочей области по умолчанию в стандартной группе ресурсов подписки кластера AKS, если эта область еще не создана в регионе.  Имя созданной рабочей области по умолчанию поддерживает формат *DefaultWorkspace-\<GUID>-\<Region>*.  

```azurecli
az aks enable-addons -a monitoring -n MyExistingManagedCluster -g MyExistingManagedClusterRG  
```

Результат должен выглядеть так:

```output
provisioningState       : Succeeded
```

### <a name="integrate-with-an-existing-workspace"></a>Интеграция с существующим рабочим пространством

Если вы предпочитаете интегрироваться с существующим рабочим пространством, выполните следующие шаги, чтобы `--workspace-resource-id` сначала определить полный идентификатор ресурса рабочего пространства Log Analytics, необходимого для параметра, а затем запустить команду, чтобы включить дополнение мониторинга к указанному рабочему пространству.  

1. Перечислите все подписки, к которым вы имеете доступ с помощью следующей команды:

    ```azurecli
    az account list --all -o table
    ```

    Результат должен выглядеть так:

    ```output
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

    ```azurecli
    az resource list --resource-type Microsoft.OperationalInsights/workspaces -o json
    ```

    В выводе найдите имя рабочего пространства, а затем скопируйте полный идентификатор ресурсов рабочего пространства Log Analytics под **идентификатором**поля.

4. Запустите следующую команду, чтобы включить надстройку мониторинга, заменив значение `--workspace-resource-id` параметра. Значение строки должно находиться в пределах двойных котировок:

    ```azurecli
    az aks enable-addons -a monitoring -n ExistingManagedCluster -g ExistingManagedClusterRG --workspace-resource-id "/subscriptions/<SubscriptionId>/resourceGroups/<ResourceGroupName>/providers/Microsoft.OperationalInsights/workspaces/<WorkspaceName>"
    ```

    Результат должен выглядеть так:

    ```output
    provisioningState       : Succeeded
    ```

## <a name="enable-using-terraform"></a>Включение с помощью Terraform

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

## <a name="enable-from-azure-monitor-in-the-portal"></a>Включить от Azure Monitor на портале

Чтобы включить мониторинг кластера AKS на портале Azure из раздела Azure Monitor, выполните следующие действия:

1. На портале Azure выберите **Monitor**.

2. В списке выберите **Контейнеры**.

3. На странице **Контейнеры** выберите **Non-monitored clusters** (Неотслеживаемые кластеры).

4. В списке неотслеживаемых кластеров выберите контейнер и щелкните **Включить**.   

5. Если рабочая область Log Analytics расположена в той же подписке, что и кластер, выберите ее из раскрывающегося списка на странице **Onboarding to Azure Monitor for containers** (Подключение к Azure Monitor для контейнеров).  
    В списке предварительно выбрана рабочая область по умолчанию и расположение, в которое контейнер AKS развертывается в подписке.

    ![Включение мониторинга аналитических сведений о контейнере AKS](./media/container-insights-onboard/kubernetes-onboard-brownfield-01.png)

    >[!NOTE]
    >Если вы хотите создать рабочую область Log Analytics для хранения данных мониторинга из кластера, выполните инструкции, описанные в статье [Создание рабочей области Log Analytics на портале Azure](../../azure-monitor/learn/quick-create-workspace.md). Создайте рабочую область в той же подписке, в которой развернут контейнер AKS.

После включения мониторинга может пройти около 15 минут, прежде чем вы сможете просмотреть метрики работоспособности кластера.

## <a name="enable-directly-from-aks-cluster-in-the-portal"></a>Включить непосредственно из кластера AKS на портале

Для обеспечения непосредственного мониторинга одного из кластеров AKS на портале Azure следует:

1. На портале Azure выберите **все сервисы.**

2. В списке ресурсов введите **Контейнеры**.  Как только вы начнете вводить данные, список отфильтруется соответствующим образом.

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

## <a name="enable-using-an-azure-resource-manager-template"></a>Включить шаблон управления ресурсами Azure

В этом методе используются два шаблона JSON. Один шаблон задает конфигурацию для включения мониторинга, а другой содержит значения параметров, которые можно настроить, чтобы указать следующее:

* идентификатор ресурса контейнера AKS;
* группу ресурсов, в которой развернут кластер;

>[!NOTE]
>Развертывание шаблона должно проходить в той же группе ресурсов, что и у кластера.
>

Рабочее пространство для анализа журналов должно быть создано, прежде чем включить мониторинг с помощью Azure PowerShell или CLI. Для создания рабочей области можно использовать [Azure Resource Manager](../../azure-monitor/platform/template-workspace-configuration.md), [PowerShell](../scripts/powershell-sample-create-workspace.md?toc=%2fpowershell%2fmodule%2ftoc.json) или [портал Azure](../../azure-monitor/learn/quick-create-workspace.md).

Если вы не знакомы с концепцией развертывания ресурсов с помощью шаблона, ознакомьтесь со статьями:

* [Развертывание ресурсов с использованием шаблонов Resource Manager и Azure PowerShell](../../azure-resource-manager/templates/deploy-powershell.md)

* [Развертывание ресурсов с использованием шаблонов Resource Manager и Azure CLI](../../azure-resource-manager/templates/deploy-cli.md)

Если вы решили использовать Azure CLI, необходимо сначала установить интерфейс командной строки и использовать его локально. Вы должны запустить версию Azure CLI 2.0.59 или позже. Для определения версии выполните `az --version`. Если вам необходимо установить или обновить Azure CLI, ознакомьтесь со статьей [Установка Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli).

### <a name="create-and-execute-a-template"></a>Создание и выполнение шаблона

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
        "aksResourceTagValues": {
          "type": "object",
          "metadata": {
            "description": "Existing all tags on AKS Cluster Resource"
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
          "tags": "[parameters('aksResourceTagValues')]",
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
        },
        "aksResourceTagValues": {
          "value": {
            "<existing-tag-name1>": "<existing-tag-value1>",
            "<existing-tag-name2>": "<existing-tag-value2>",
            "<existing-tag-nameN>": "<existing-tag-valueN>"
          }
        }
      }
    }
    ```

4. Изналивайте значения **для aksResourceId** и **aksResourceLocation,** используя значения на странице **ОБЗОР AKS** для кластера AKS. Значение для **workspaceResourceId** — это полный идентификатор ресурса рабочей области Log Analytics, который включает имя рабочей области.

    Изнайте значения **aksResourceTagValues** в соответствии с существующими значениями тегов, указанными для кластера AKS.

5. Сохраните этот файл как **existingClusterParam.json** в локальную папку.

6. Теперь вы можете развернуть этот шаблон.

   * Чтобы развернуть с Помощью Azure PowerShell, используйте следующие команды в папке, содержащей шаблон:

       ```powershell
       New-AzResourceGroupDeployment -Name OnboardCluster -ResourceGroupName <ResourceGroupName> -TemplateFile .\existingClusterOnboarding.json -TemplateParameterFile .\existingClusterParam.json
       ```

       Изменение конфигурации может занять несколько минут. После завершения настройки конфигурации появится сообщение, похожее на приведенное ниже, с таким результатом:

       ```output
       provisioningState       : Succeeded
       ```

   * Чтобы развернуть с Azure CLI, запустите следующие команды:

       ```azurecli
       az login
       az account set --subscription "Subscription Name"
       az group deployment create --resource-group <ResourceGroupName> --template-file ./existingClusterOnboarding.json --parameters @./existingClusterParam.json
       ```

       Изменение конфигурации может занять несколько минут. После завершения настройки конфигурации появится сообщение, похожее на приведенное ниже, с таким результатом:

       ```output
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

```output
User@aksuser:~$ kubectl get ds omsagent --namespace=kube-system
NAME       DESIRED   CURRENT   READY     UP-TO-DATE   AVAILABLE   NODE SELECTOR                 AGE
omsagent   2         2         2         2            2           beta.kubernetes.io/os=linux   1d
```  

Чтобы проверить развертывание решения, выполните следующую команду:

```
kubectl get deployment omsagent-rs -n=kube-system
```

Результат должен выглядеть приблизительно, как показано ниже, что означает успешное выполнение развертывания:

```output
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

```output
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

```output
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

* Благодаря мониторингу, позволяющим собирать работоспособность и использование ресурсов кластера AKS и рабочих нагрузок, работающих на них, [научитесь использовать](container-insights-analyze.md) Azure Monitor для контейнеров.
