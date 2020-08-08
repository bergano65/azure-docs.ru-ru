---
title: Предварительная версия — знакомство с политикой Azure для Kubernetes
description: Узнайте, как Политика Azure использует Rego и Open Policy Agent для управления кластерами, работающими под управлением Kubernetes в Azure или в локальной среде. Это ознакомительная версия функции.
ms.date: 08/07/2020
ms.topic: conceptual
ms.openlocfilehash: dc81d22677eeab16ae06e782c5ae47c121af04c6
ms.sourcegitcommit: 98854e3bd1ab04ce42816cae1892ed0caeedf461
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/07/2020
ms.locfileid: "88003501"
---
# <a name="understand-azure-policy-for-kubernetes-clusters-preview"></a>Общие сведения о службе "Политика Azure" для кластеров Kubernetes (предварительная версия)

Политика Azure расширяет [Gatekeeper](https://github.com/open-policy-agent/gatekeeper) v3, _веб-перехватчик контроллера допуска_ для [Open Policy Agent](https://www.openpolicyagent.org/) (OPA), чтобы обеспечить централизованное и согласованное применение масштабных ограничений и мер безопасности в кластерах. Политика Azure позволяет управлять состоянием соответствия кластеров Kubernetes и сообщать о нем из одного места. Эта надстройка выполняет следующие функции:

- проверяет назначения политик в кластере с помощью службы Политики Azure;
- развертывает определения политик в кластере как [шаблоны ограничения](https://github.com/open-policy-agent/gatekeeper#constraint-templates) и настраиваемые ресурсы [ограничения](https://github.com/open-policy-agent/gatekeeper#constraints).
- Сообщает службе Политики Azure сведения об аудите и соответствии.

Политика Azure для Kubernetes поддерживает следующие кластерные среды:

- [Служба Azure Kubernetes (AKS)](../../../aks/intro-kubernetes.md)
- [Kubernetes с поддержкой Azure Arc](../../../azure-arc/kubernetes/overview.md)
- [Обработчик AKS](https://github.com/Azure/aks-engine/blob/master/docs/README.md)

> [!IMPORTANT]
> Политика Azure для Kubernetes доступна в предварительной версии и поддерживает только пулы узлов Linux и встроенные определения политик. Встроенные определения политик находятся в категории **Kubernetes**. Определения политик ограниченного просмотра с **енфорцеопаконстраинт** и **енфорцерегополици** , а также связанная категория **службы Kubernetes** являются _устаревшими_. Вместо этого используйте режим "эффекты _аудита_ " и " _запретить_ " с помощью поставщика ресурсов `Microsoft.Kubernetes.Data` .

## <a name="overview"></a>Обзор

Чтобы включить и использовать Политику Azure с кластером Kubernetes, выполните следующие действия.

1. Настройте кластер Kubernetes и установите надстройку:
   - [Служба Azure Kubernetes (AKS)](#install-azure-policy-add-on-for-aks)
   - [Kubernetes с поддержкой Azure Arc](#install-azure-policy-add-on-for-azure-arc-enabled-kubernetes)
   - [Обработчик AKS](#install-azure-policy-add-on-for-aks-engine)

   > [!NOTE]
   > Распространенные проблемы с установкой см. [в разделе Устранение неполадок в надстройке политики Azure](../troubleshoot/general.md#add-on-installation-errors).

1. [Общие сведения о языке Политики Azure для Kubernetes](#policy-language)

1. [Назначение встроенного определения кластеру Kubernetes](#assign-a-built-in-policy-definition)

1. [Ожидание проверки](#policy-evaluation)

## <a name="install-azure-policy-add-on-for-aks"></a>Установка надстройки Политики Azure для AKS

Прежде чем устанавливать надстройку Политики Azure или включать какие-либо функции службы, ваша подписка должна включить поставщики ресурсов **Microsoft.PolicyInsights** и **Microsoft.PolicyInsights**.

1. Необходимо установить и настроить Azure CLI версии 2.0.62 или более поздней. Чтобы узнать версию, выполните команду `az --version`. Если вам необходимо выполнить установку или обновление, см. статью [Установка Azure CLI](/cli/azure/install-azure-cli).

1. Зарегистрируйте поставщики ресурсов и предварительные версии функций.

   - Портал Azure:

     1. Зарегистрируйте поставщики ресурсов **Microsoft. ContainerService** и **Microsoft.PolicyInsights**. Действия см. в [Поставщики и типы ресурсов](../../../azure-resource-manager/management/resource-providers-and-types.md#azure-portal).

     1. Запустите службу Политика Azure на портале Azure, щелкнув **Все службы**, а затем выполнив поиск и выбрав **Политика**.

        :::image type="content" source="../media/policy-for-kubernetes/search-policy.png" alt-text="Поиск политики в разделе Все службы" border="false":::

     1. Выберите **Присоединиться к предварительной версии** слева на странице Политики Azure.

        :::image type="content" source="../media/policy-for-kubernetes/join-aks-preview.png" alt-text="Предварительная версия функции присоединения политики для AKS" border="false":::

     1. Выберите строку подписки, которую нужно добавить в предварительную версию.

     1. В верхней части списка подписок нажмите кнопку **Согласиться**.

   - Azure CLI:

     ```azurecli-interactive
     # Log in first with az login if you're not using Cloud Shell
   
     # Provider register: Register the Azure Kubernetes Service provider
     az provider register --namespace Microsoft.ContainerService
   
     # Provider register: Register the Azure Policy provider
     az provider register --namespace Microsoft.PolicyInsights
   
     # Feature register: enables installing the add-on
     az feature register --namespace Microsoft.ContainerService --name AKS-AzurePolicyAutoApprove
     
     # Use the following to confirm the feature has registered
     az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/AKS-AzurePolicyAutoApprove')].   {Name:name,State:properties.state}"
     
     # Once the above shows 'Registered' run the following to propagate the update
     az provider register -n Microsoft.ContainerService
     ```

1. Если установлены определения политики ограниченной предварительной версии, удалите надстройку с помощью кнопки **Отключить** в кластере AKS на странице **Политики (предварительная версия)** .

1. Кластер AKS должен быть версии _1.14_ или более новой. Используйте следующий скрипт для проверки версии кластера AKS:

   ```azurecli-interactive
   # Log in first with az login if you're not using Cloud Shell

   # Look for the value in kubernetesVersion
   az aks list
   ```

1. Установите версию _0.4.0_ расширения предварительной версии Azure CLI для AKS, `aks-preview`:

   ```azurecli-interactive
   # Log in first with az login if you're not using Cloud Shell

   # Install/update the preview extension
   az extension add --name aks-preview

   # Validate the version of the preview extension
   az extension show --name aks-preview --query [version]
   ```

   > [!NOTE]
   > Если вы ранее установили расширение _aks-preview_, установите обновления с помощью команды `az extension update --name aks-preview`.

После выполнения указанных выше предварительных требований установите надстройку "Политика Azure" в кластере AKS, которым хотите управлять.

- Портал Azure

  1. Запустите службу AKS на портале Azure, щелкнув **Все службы**, а затем выполнив поиск и выбрав **Службы Kubernetes**.

  1. Выберите один из кластеров AKS.

  1. Выберите **Политики (предварительная версия)** в левой части страницы службы Kubernetes.

     :::image type="content" source="../media/policy-for-kubernetes/policies-preview-from-aks-cluster.png" alt-text="Определения политик из кластера AKS" border="false":::

  1. На главной странице нажмите кнопку **Включить надстройку**.

     :::image type="content" source="../media/policy-for-kubernetes/enable-policy-add-on.png" alt-text="Включение надстройки "Политика Azure для AKS"":::

     <a name="migrate-from-v1"></a>
     > [!NOTE]
     > Если кнопка **Включить надстройку** неактивна, подписка еще не добавлена в предварительную версию. Если кнопка **отключить надстройку** включена и отображается сообщение предупреждение о миграции версии 2, то надстройка v1 установлена и должна быть удалена до назначения определений политики v2. _Устаревшая_ надстройка v1 будет автоматически заменена надстройкой v2, начиная с 24 августа 2020. После этого необходимо назначить новые версии v2 определений политик. Чтобы выполнить обновление сейчас, выполните следующие действия.
     > 
     > 1. Проверьте, установлена надстройка v1 в кластере AKS. для этого откройте страницу **политики (Предварительная версия)** в кластере AKS и убедитесь, что в текущем кластере используется надстройка политики Azure v1... Сообщение.
     > 1. [Удалите надстройку](#remove-the-add-on-from-aks).
     > 1. Нажмите кнопку **включить надстройку** , чтобы установить версию v2 надстройки.
     > 1. [Назначение версий 2 для встроенных определений политик v1](#assign-a-built-in-policy-definition)

- Azure CLI

  ```azurecli-interactive
  # Log in first with az login if you're not using Cloud Shell

  az aks enable-addons --addons azure-policy --name MyAKSCluster --resource-group MyResourceGroup
  ```

Чтобы убедиться, что установка надстройки прошла успешно и что объекты pod _azure-policy_ и _gatekeeper_ запущены, выполните следующую команду:

```bash
# azure-policy pod is installed in kube-system namespace
kubectl get pods -n kube-system

# gatekeeper pod is installed in gatekeeper-system namespace
kubectl get pods -n gatekeeper-system
```

Наконец, убедитесь, что последняя надстройка установлена, выполнив следующую команду Azure CLI, с заменой `<rg>` на имя группы ресурсов и `<cluster-name>` на имя кластера AKS: `az aks show -g <rg> -n <cluster-name>`. Результат должен выглядеть примерно так, как и следующие выходные данные, и **config.version** должен иметь вид `v2`:

```output
"addonProfiles": {
    "azurepolicy": {
        "config": {
            "version": "v2"
        },
        "enabled": true,
        "identity": null
    },
}
```

## <a name="install-azure-policy-add-on-for-azure-arc-enabled-kubernetes"></a>Установка надстройки Политики Azure для Kubernetes с поддержкой Azure Arc

Прежде чем устанавливать надстройку Политики Azure или включать какие-либо функции службы, ваша подписка должна активировать поставщик ресурсов **Microsoft.PolicyInsights** и создать назначение роли для субъекта-службы кластера.

1. Необходимо установить и настроить Azure CLI версии 2.0.62 или более поздней. Чтобы узнать версию, выполните команду `az --version`. Если вам необходимо выполнить установку или обновление, см. статью [Установка Azure CLI](/cli/azure/install-azure-cli).

1. Чтобы включить поставщик ресурсов, выполните действия, описанные в статье [Поставщики и типы ресурсов](../../../azure-resource-manager/management/resource-providers-and-types.md#azure-portal), или выполните команду Azure CLI или Azure PowerShell:

   - Azure CLI

     ```azurecli-interactive
     # Log in first with az login if you're not using Cloud Shell
     
     # Provider register: Register the Azure Policy provider
     az provider register --namespace 'Microsoft.PolicyInsights'
     ```

   - Azure PowerShell
   
     ```azurepowershell-interactive
     # Log in first with Connect-AzAccount if you're not using Cloud Shell
   
     # Provider register: Register the Azure Policy provider
     Register-AzResourceProvider -ProviderNamespace 'Microsoft.PolicyInsights'
     ```

1. Кластер Kubernetes должен быть версии _1.14_ или более новой.

1. Установите [Helm 3](https://v3.helm.sh/docs/intro/install/).

1. Кластер Kubernetes поддерживает Azure Arc. Дополнительные сведения см. в [подключение кластера Kubernetes к Azure Arc](../../../azure-arc/kubernetes/connect-cluster.md).

1. Получите полный идентификатор ресурса Azure для кластера Kubernetes с поддержкой Azure Arc. 

1. Откройте порты для надстройки. Надстройка политики Azure использует эти домены и порты для выборки определений и назначений политик, а также для создания отчетов о соответствии кластера, возвращаемых Политике Azure.

   |Домен |Порт |
   |---|---|
   |`gov-prod-policy-data.trafficmanager.net` |`443` |
   |`raw.githubusercontent.com` |`443` |
   |`login.windows.net` |`443` |
   |`dc.services.visualstudio.com` |`443` |

1. Назначьте роль "Редактор данных анализа политик (предварительная версия)" для кластера Kubernetes с поддержкой Azure Arc. Замените `<subscriptionId>` своим идентификатором подписки, `<rg>` группой ресурсов кластера Kubernetes с поддержкой Azure Arc и `<clusterName>` именем кластера Kubernetes с поддержкой Azure Arc. Следите за возвращаемыми значениями _appId_ (идентификатора приложения), _password_ (пароля) и _tenant_ (клиента) для шагов установки.

   - Azure CLI

     ```azurecli-interactive
     az ad sp create-for-rbac --role "Policy Insights Data Writer (Preview)" --scopes "/subscriptions/<subscriptionId>/resourceGroups/<rg>/providers/Microsoft.Kubernetes/connectedClusters/<clusterName>"
     ```

   - Azure PowerShell

     ```azure powershell-interactive
     $sp = New-AzADServicePrincipal -Role "Policy Insights Data Writer (Preview)" -Scope "/subscriptions/<subscriptionId>/resourceGroups/<rg>/providers/Microsoft.Kubernetes/connectedClusters/<clusterName>"

     @{ appId=$sp.ApplicationId;password=[System.Runtime.InteropServices.Marshal]::PtrToStringAuto([System.Runtime.InteropServices.Marshal]::SecureStringToBSTR($sp.Secret));tenant=(Get-AzContext).Tenant.Id } | ConvertTo-Json
     ```

   Пример результата выполнения вышеприведенных команд:

   ```json
   {
       "appId": "aaaaaaaa-aaaa-aaaa-aaaa-aaaaaaaaaaaa",
       "password": "bbbbbbbb-bbbb-bbbb-bbbb-bbbbbbbbbbbb",
       "tenant": "cccccccc-cccc-cccc-cccc-cccccccccccc"
   }
   ```

После выполнения указанных выше предварительных требований установите надстройку Политика Azure в кластере Kubernetes с поддержкой Azure Arc, которым хотите управлять.

1. Добавьте репозиторий надстройки Политики Azure в Helm.

   ```bash
   helm repo add azure-policy https://raw.githubusercontent.com/Azure/azure-policy/master/extensions/policy-addon-kubernetes/helm-charts
   ```

1. Установите надстройку Политики Azure с помощью диаграммы Helm:

   ```bash
   # In below command, replace the following values with those gathered above.
   #    <AzureArcClusterResourceId> with your Azure Arc enabled Kubernetes cluster resource Id. For example: /subscriptions/<subscriptionId>/resourceGroups/<rg>/providers/Microsoft.Kubernetes/connectedClusters/<clusterName>
   #    <ServicePrincipalAppId> with app Id of the service principal created during prerequisites.
   #    <ServicePrincipalPassword> with password of the service principal created during prerequisites.
   #    <ServicePrincipalTenantId> with tenant of the service principal created during prerequisites.
   helm install azure-policy-addon azure-policy/azure-policy-addon-arc-clusters \
       --set azurepolicy.env.resourceid=<AzureArcClusterResourceId> \
       --set azurepolicy.env.clientid=<ServicePrincipalAppId> \
       --set azurepolicy.env.clientsecret=<ServicePrincipalPassword> \
       --set azurepolicy.env.tenantid=<ServicePrincipalTenantId>
   ```

   Дополнительные сведения о том, какие компоненты и функции устанавливает диаграмма Helm, см. в статье о [диаграмме Helm для надстройки Политики Azure](https://github.com/Azure/azure-policy/tree/master/extensions/policy-addon-kubernetes/helm-charts/azure-policy-addon-arc-clusters) на сайте GitHub.

Чтобы убедиться, что установка надстройки прошла успешно и что объекты pod _azure-policy_ и _gatekeeper_ запущены, выполните следующую команду:

```bash
# azure-policy pod is installed in kube-system namespace
kubectl get pods -n kube-system

# gatekeeper pod is installed in gatekeeper-system namespace
kubectl get pods -n gatekeeper-system
```

## <a name="install-azure-policy-add-on-for-aks-engine"></a>Установка надстройки Политики Azure для Обработчика AKS

Прежде чем устанавливать надстройку Политики Azure или включать какие-либо функции службы, ваша подписка должна активировать поставщик ресурсов **Microsoft.PolicyInsights** и создать назначение роли для субъекта-службы кластера.

1. Необходимо установить и настроить Azure CLI версии 2.0.62 или более поздней. Чтобы узнать версию, выполните команду `az --version`. Если вам необходимо выполнить установку или обновление, см. статью [Установка Azure CLI](/cli/azure/install-azure-cli).

1. Чтобы включить поставщик ресурсов, выполните действия, описанные в статье [Поставщики и типы ресурсов](../../../azure-resource-manager/management/resource-providers-and-types.md#azure-portal), или выполните команду Azure CLI или Azure PowerShell:

   - Azure CLI

     ```azurecli-interactive
     # Log in first with az login if you're not using Cloud Shell
     
     # Provider register: Register the Azure Policy provider
     az provider register --namespace 'Microsoft.PolicyInsights'
     ```

   - Azure PowerShell
   
     ```azurepowershell-interactive
     # Log in first with Connect-AzAccount if you're not using Cloud Shell
   
     # Provider register: Register the Azure Policy provider
     Register-AzResourceProvider -ProviderNamespace 'Microsoft.PolicyInsights'
     ```

1. Создайте назначение роли для субъекта-службы кластера.

   - Если идентификатор приложения субъекта-службы кластера неизвестен, найдите его с помощью следующей команды.

     ```bash
     # Get the kube-apiserver pod name
     kubectl get pods -n kube-system
   
     # Find the aadClientID value
     kubectl exec <kube-apiserver pod name> -n kube-system cat /etc/kubernetes/azure.json
     ```

   - Назначьте идентификатору приложения субъекта-службы кластера (значение _aadClientID_ из предыдущего шага) роль "Редактор данных анализа политик (предварительная версия)" с помощью Azure CLI. Замените `<subscriptionId>` идентификатором подписки, а `<aks engine cluster resource group>` — группой ресурсов, в которой находится самоуправляемый кластер Kubernetes Обработчика AKS.

     ```azurecli-interactive
     az role assignment create --assignee <cluster service principal app ID> --scope "/subscriptions/<subscriptionId>/resourceGroups/<aks engine cluster resource group>" --role "Policy Insights Data Writer (Preview)"
     ```

После выполнения указанных выше предварительных требований установите надстройку Политики Azure. Установка может осуществляться во время цикла создания или обновления Обработчика AKS или выполняться как независимое действие в существующем кластере.

- Установка во время цикла создания или обновления

  Чтобы включить надстройку Политики Azure во время создания самоуправляемого кластера или в качестве обновления существующего кластера, добавьте определение кластера свойств [addons](https://github.com/Azure/aks-engine/tree/master/examples/addons/azure-policy) для Обработчика AKS.

  ```json
  "addons": [{
      "name": "azure-policy",
      "enabled": true
  }]
  ```

  Дополнительные сведения см. в руководстве по [определению кластера Обработчика AKS](https://github.com/Azure/aks-engine/blob/master/docs/topics/clusterdefinitions.md).

- Установка в существующем кластере с помощью диаграмм Helm

  Чтобы подготовить кластер и установить надстройку, выполните следующие действия.

  1. Установите [Helm 3](https://v3.helm.sh/docs/intro/install/).

  1. Добавьте репозиторий Политики Azure в Helm.

     ```bash
     helm repo add azure-policy https://raw.githubusercontent.com/Azure/azure-policy/master/extensions/policy-addon-kubernetes/helm-charts
     ```

     Дополнительные сведения см. в [кратком руководстве по диаграммам Helm](https://helm.sh/docs/using_helm/#quickstart-guide).

  1. Установите надстройку с помощью диаграммы Helm. Замените `<subscriptionId>` идентификатором подписки, а `<aks engine cluster resource group>` — группой ресурсов, в которой находится самоуправляемый кластер Kubernetes Обработчика AKS.

     ```bash
     helm install azure-policy-addon azure-policy/azure-policy-addon-aks-engine --set azurepolicy.env.resourceid="/subscriptions/<subscriptionId>/resourceGroups/<aks engine cluster resource group>"
     ```

     Дополнительные сведения о том, какие компоненты и функции устанавливает диаграмма Helm, см. в статье о [диаграмме Helm для надстройки Политики Azure](https://github.com/Azure/azure-policy/tree/master/extensions/policy-addon-kubernetes/helm-charts/azure-policy-addon-aks-engine) на сайте GitHub.

     > [!NOTE]
     > Из-за существующей связи между надстройкой Политики Azure и идентификатором группы ресурсов Политика Azure поддерживает только один кластер Обработчика AKS для каждой группы ресурсов.

Чтобы убедиться, что установка надстройки прошла успешно и что объекты pod _azure-policy_ и _gatekeeper_ запущены, выполните следующую команду:

```bash
# azure-policy pod is installed in kube-system namespace
kubectl get pods -n kube-system

# gatekeeper pod is installed in gatekeeper-system namespace
kubectl get pods -n gatekeeper-system
```

## <a name="policy-language"></a>Язык политики

Языковая структура Политики Azure для управления Kubernetes соответствует языковой структуре существующих определений политик. В [режиме поставщика ресурсов](./definition-structure.md#resource-provider-modes) `Microsoft.Kubernetes.Data` для управления кластерами Kubernetes используются эффекты [Audit](./effects.md#audit) и [Deny](./effects.md#deny) . _Аудит_ и _отклонение_ должны предоставлять свойства **сведений** , относящиеся к работе с [инфраструктурой ограничений непрозра](https://github.com/open-policy-agent/frameworks/tree/master/constraint) и привратником v3.

Политика Azure передает URI этих [CustomResourceDefinitions](https://github.com/open-policy-agent/gatekeeper#constraint-templates) (CRD) надстройке в составе свойств _details.constraintTemplate_ и _details.constraint_ в определении политики. Rego — это язык, поддерживаемый OPA и Gatekeeper для проверки запросов к кластеру Kubernetes. Поддерживая существующий стандарт управления Kubernetes, Политика Azure позволяет повторно использовать существующие правила и объединять их с Политикой Azure для формирования унифицированной отчетности о совместимости с облачными технологиями. Дополнительные сведения о Rego см. в [этой статье](https://www.openpolicyagent.org/docs/latest/policy-language/#what-is-rego).

## <a name="assign-a-built-in-policy-definition"></a>Назначение встроенного определения политики

Чтобы назначить определение политики кластеру Kubernetes, необходимо назначить соответствующие операции назначения политики управления доступом на основе ролей (RBAC). У встроенной в Azure роли **участника политики ресурсов** и **владельца** есть такие операции. Дополнительные сведения см. в [Разрешения RBAC в службе "Политика Azure"](../overview.md#rbac-permissions-in-azure-policy).

Чтобы найти встроенные политики для управления кластером с помощью портала Azure, выполните следующие действия.

1. Запустите службу Политики Azure на портале Azure. На панели слева выберите **Все службы**, а затем выполните поиск по запросу **Политика** и выберите этот пункт.

1. Выберите **Определения** на левой панели страницы "Политика Azure".

1. В раскрывающемся списке "Категория" щелкните **Выбрать все**, чтобы очистить фильтр, а затем выберите **Kubernetes**.

1. Выберите определение политики, а затем нажмите кнопку **Назначить**.

1. Задайте в качестве **Области** группу управления, подписку или группу ресурсов кластера Kubernetes, к которым будет применяться назначение политики.

   > [!NOTE]    
   > При назначении политики Azure для определения Kubernetes **Область** должна включать ресурс кластера. Для кластера Обработчика AKS **Область** должна быть группой ресурсов кластера.

1. Присвойте назначению политики **Имя** и **Описание**, по которым его можно будет легко отличить.    

1. Задайте для [Принудительного применения политики](./assignment-structure.md#enforcement-mode) одно из значений    
   ниже.   

   - **Включено** — принудительно применить политику в кластере. Запросы на допуск Kubernetes с нарушениями отклоняются.    

   - **Отключено** — не выполнять принудительного применения политики в кластере. Запросы на допуск Kubernetes с нарушениями не отклоняются. Результаты оценки соответствия по-прежнему доступны. При развертывании новых определений политик в работающих кластерах параметр _Отключено_ может оказаться полезным для тестирования определений политик, так как запросы на допуск с нарушениями не будут отклоняться.

1. Выберите **Далее**. 

1. Установка **значений параметра** 

   - Чтобы исключить пространства имен Kubernetes из оценки политики, укажите список пространств имен в параметре **Исключения пространства имен**. Рекомендуется исключить следующие пространства имен: _kube-system_, _gatekeeper-system_ и _azure-arc_.

1. Выберите **Review + create** (Просмотреть и создать).

Или воспользуйтесь кратким руководством [Назначение политики с помощью портала](../assign-policy-portal.md) для поиска и назначения политики Kubernetes. Выполните поиск определения политики Kubernetes вместо примера audit vms.

> [!IMPORTANT]
> Встроенные определения политик доступны для кластеров Kubernetes в категории **Kubernetes**. Список встроенных определений политик см. в [Образцы Kubernetes](../samples/built-in-policies.md#kubernetes).

## <a name="policy-evaluation"></a>Оценка политики

Надстройка сверяет назначения политик со службой Политики Azure на наличие изменений каждые 15 минут.
Во время этого цикла обновления надстройка проверяет наличие изменений. Эти изменения активируют операции создания, обновления или удаления шаблонов ограничений и ограничений.

В кластере Kubernetes, если пространство имен имеет одну из следующих меток, запросы на допуск с нарушениями не отклоняются. Результаты оценки соответствия по-прежнему доступны.

- `control-plane`
- `admission.policy.azure.com/ignore`

> [!NOTE]
> Хотя администратор кластера может иметь разрешение на создание и обновление шаблонов ограничений и ресурсов ограничений, устанавливаемых надстройкой Политики Azure, эти сценарии не поддерживаются, так как выполненные вручную обновления перезаписываются. Gatekeeper продолжит оценивать политики, существовавшие до установки надстройки и назначения определений политик Политики Azure.

Каждые 15 минут надстройка вызывает полную проверку кластера. После сбора сведений, полученных в ходе полной проверки, и выполненных в режиме реального времени оценок Gatekeeper попыток внести изменения в кластер надстройка отправляет результаты в Политику Azure для включения их в [сведения о соответствии](../how-to/get-compliance-data.md) подобно любому назначению Политики Azure. Во время цикла аудита возвращаются только результаты активных назначений политик. Результаты аудита также могут отображаться как [нарушения](https://github.com/open-policy-agent/gatekeeper#audit), указанные в поле состояния ограничения, завершившегося сбоем.

> [!NOTE]
> Каждый отчет о соответствии в Политике Azure для кластеров Kubernetes включает все нарушения за последние 45 минут. Метка времени указывает, когда произошло нарушение.

## <a name="logging"></a>Logging

И объект pod _azure-policy_ и _gatekeeper_, являющиеся контроллерами/контейнерами Kubernetes, хранят журналы в кластере Kubernetes. Журналы можно предоставить на странице **Insights** (Аналитические сведения) кластера Kubernetes.
Дополнительные сведения см. в статье [Мониторинг производительности кластера Kubernetes с помощью Azure Monitor для контейнеров](../../../azure-monitor/insights/container-insights-analyze.md).

Чтобы просмотреть журналы надстроек, используйте `kubectl`:

```bash
# Get the azure-policy pod name installed in kube-system namespace
kubectl logs <azure-policy pod name> -n kube-system

# Get the gatekeeper pod name installed in gatekeeper-system namespace
kubectl logs <gatekeeper pod name> -n gatekeeper-system
```

Дополнительные сведения см. в статье об [отладке Gatekeeper](https://github.com/open-policy-agent/gatekeeper#debugging) в документации по Gatekeeper.

## <a name="remove-the-add-on"></a>Удаление надстройки

### <a name="remove-the-add-on-from-aks"></a>Удаление надстройки из AKS

Чтобы удалить надстройку "Политика Azure" из кластера AKS, используйте либо портал Azure, либо Azure CLI.

- Портал Azure

  1. Запустите службу AKS на портале Azure, щелкнув **Все службы**, а затем выполнив поиск и выбрав **Службы Kubernetes**.

  1. Выберите кластер AKS, в котором необходимо отключить надстройку Политики Azure.

  1. Выберите **Политики (предварительная версия)** в левой части страницы службы Kubernetes.

     :::image type="content" source="../media/policy-for-kubernetes/policies-preview-from-aks-cluster.png" alt-text="Определения политик из кластера AKS" border="false":::

  1. На главной странице нажмите кнопку **Отключить надстройку**.

     :::image type="content" source="../media/policy-for-kubernetes/disable-policy-add-on.png" alt-text="Отключение надстройки "Политика Azure для AKS"" border="false":::

- Azure CLI

  ```azurecli-interactive
  # Log in first with az login if you're not using Cloud Shell

  az aks disable-addons --addons azure-policy --name MyAKSCluster --resource-group MyResourceGroup
  ```

### <a name="remove-the-add-on-from-azure-arc-enabled-kubernetes"></a>Удаление надстройки из Kubernetes с поддержкой Azure Arc

Чтобы удалить надстройку Политики Azure и Gatekeeper из кластера Kubernetes с поддержкой Azure Arc, выполните следующую команду Helm:

```bash
helm uninstall azure-policy-addon
```

### <a name="remove-the-add-on-from-aks-engine"></a>Удаление надстройки из Обработчика AKS

Чтобы удалить надстройку Политики Azure и Gatekeeper из кластера Обработчика AKS, используйте метод, соответствующий способу установки надстройки.

- Если установка выполнена путем задания свойства **addons** в определении кластера для Обработчика AKS:

  повторно разверните определение кластера в Обработчике AKS после изменения значения свойства **addons** для _azure-policy_ на false:


  ```json
  "addons": [{
      "name": "azure-policy",
      "enabled": false
  }]
  ```

  Дополнительные сведения см. в [Обработчик AKS. Отключение надстройки Политики Azure](https://github.com/Azure/aks-engine/blob/master/examples/addons/azure-policy/README.md#disable-azure-policy-add-on).

- При установке с диаграммами Helm выполните следующую команду Helm:

  ```bash
  helm uninstall azure-policy-addon
  ```

## <a name="diagnostic-data-collected-by-azure-policy-add-on"></a>Диагностические данные, собираемые надстройкой Политики Azure

Надстройка Политики Azure для Kubernetes собирает ограниченные диагностические данные кластера. Они являются важнейшими техническими данными, относящимися к программному обеспечению и производительности. Их можно использовать следующими способами:

- поддержка надстройки Политики Azure в актуальном состоянии;
- обеспечение безопасности, надежности и эффективности надстройки Политики Azure;
- совершенствование надстройки Политики Azure с помощью статистического анализа использования надстройки.

Сведения, собираемые надстройкой, не являются персональными данными. В настоящее время ведется сбор следующих сведений:

- Версия агента надстройки Политики Azure
- Тип кластера
- Регион кластера
- Группа ресурсов кластера
- ИД ресурса кластера
- ИД подписки кластера
- ОС кластера (например: Linux)
- Город кластера (например: Сиэтл)
- Штат или провинция кластера (например: Вашингтон)
- Страна или регион кластера (например: США)
- Исключения и ошибки, обнаруженные надстройкой Политики Azure во время установки агента при оценке политики
- Число определений политик Gatekeeper, не установленных надстройкой Политики Azure

## <a name="next-steps"></a>Дальнейшие действия

- См. [примеры для Политики Azure](../samples/index.md).
- См. дополнительные сведения о [структуре определения Политики Azure](definition-structure.md).
- Изучите [сведения о действии политик](effects.md).
- Узнайте о [программном создании политик](../how-to/programmatically-create.md).
- Узнайте, как [получать сведения о соответствии](../how-to/get-compliance-data.md).
- Узнайте, как [исправлять несоответствующие ресурсы](../how-to/remediate-resources.md).
- Дополнительные сведения о группе управления см. в статье [Упорядочивание ресурсов с помощью групп управления Azure](../../management-groups/overview.md).