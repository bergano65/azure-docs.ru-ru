---
title: Создание контроллера для входа с новым шлюзом приложений
description: В этой статье содержится информация о том, как развернуть контроллер входа в приложение с новым шлюзом приложений.
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: article
ms.date: 11/4/2019
ms.author: caya
ms.openlocfilehash: a0bc6aef1becd53217be0eeb8c865b5c78a5d69f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80239461"
---
# <a name="how-to-install-an-application-gateway-ingress-controller-agic-using-a-new-application-gateway"></a>Как установить контроллер входа в приложение (AGIC) с помощью нового шлюза приложений

Приведенные ниже инструкции предполагают, что контроллер входа в application Gateway (AGIC) будет установлен в среде без уже существующих компонентов.

## <a name="required-command-line-tools"></a>Необходимые инструменты командной строки

Рекомендуем использовать [Azure Cloud Shell](https://shell.azure.com/) для всех командных операций ниже. Запустите оболочку с shell.azure.com или нажав на ссылку:

[![Встраиваемый запуск](https://shell.azure.com/images/launchcloudshell.png "Запуск Azure Cloud Shell")](https://shell.azure.com)

Кроме того, запустите оболочку облака с портала Azure, используя следующий значок:

![Запуск с помощью портала](./media/application-gateway-ingress-controller-install-new/portal-launch-icon.png)

В вашей [облачной оболочке Azure](https://shell.azure.com/) уже есть все необходимые инструменты. Если вы решите использовать другую среду, пожалуйста, убедитесь, что следующие инструменты командной строки установлены:

* `az`- Azure CLI: [инструкции по установке](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)
* `kubectl`- Инструмент командной линии Kubernetes: [инструкции по установке](https://kubernetes.io/docs/tasks/tools/install-kubectl)
* `helm`- Менеджер пакета Kubernetes: [инструкции по установке](https://github.com/helm/helm/releases/latest)
* `jq`- командный процессор JSON: [инструкции по установке](https://stedolan.github.io/jq/download/)


## <a name="create-an-identity"></a>Создание идентификации

Выполните ниже шаги, чтобы создать [основной объект службы](https://docs.microsoft.com/azure/active-directory/develop/app-objects-and-service-principals#service-principal-object)Azure Active Directory (AAD). `appId`Пожалуйста, `password`запишите, и `objectId` значения - они будут использоваться в следующих шагах.

1. Создайте aD-сервис[(Подробнее о RBAC):](https://docs.microsoft.com/azure/role-based-access-control/overview)
    ```azurecli
    az ad sp create-for-rbac --skip-assignment -o json > auth.json
    appId=$(jq -r ".appId" auth.json)
    password=$(jq -r ".password" auth.json)
    ```
    Значения `appId` `password` и значения из выхода JSON будут использоваться в следующих шагах


1. Используйте `appId` выход предыдущей команды, чтобы `objectId` получить новый основной сервис:
    ```azurecli
    objectId=$(az ad sp show --id $appId --query "objectId" -o tsv)
    ```
    Выход этой команды, `objectId`который будет использоваться в шаблоне менеджера ресурсов Azure ниже

1. Создайте файл параметров, который будет использоваться в развертывании шаблона ресурсов Azure Manager позже.
    ```bash
    cat <<EOF > parameters.json
    {
      "aksServicePrincipalAppId": { "value": "$appId" },
      "aksServicePrincipalClientSecret": { "value": "$password" },
      "aksServicePrincipalObjectId": { "value": "$objectId" },
      "aksEnableRBAC": { "value": false }
    }
    EOF
    ```
    Для развертывания кластера с включенным **RBAC** установите поле для `aksEnabledRBAC``true`

## <a name="deploy-components"></a>Развертывание компонентов
Этот шаг добавит в подписку следующие компоненты:

- [Служба Azure Kubernetes](https://docs.microsoft.com/azure/aks/intro-kubernetes)
- [Приложение Шлюз](https://docs.microsoft.com/azure/application-gateway/overview) v2
- [Виртуальная сеть](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview) с 2 [подсетями](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview)
- [Общедоступный IP-адрес](https://docs.microsoft.com/azure/virtual-network/virtual-network-public-ip-address)
- [Управляемая идентичность](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview), которая будет использоваться [AAD Pod Identity](https://github.com/Azure/aad-pod-identity/blob/master/README.md)

1. Загрузите шаблон Azure Resource Manager и измените шаблон по мере необходимости.
    ```bash
    wget https://raw.githubusercontent.com/Azure/application-gateway-kubernetes-ingress/master/deploy/azuredeploy.json -O template.json
    ```

1. Развертывание шаблона управления ресурсами Azure с помощью `az cli`. Это может занять до 5 минут.
    ```azurecli
    resourceGroupName="MyResourceGroup"
    location="westus2"
    deploymentName="ingress-appgw"

    # create a resource group
    az group create -n $resourceGroupName -l $location

    # modify the template as needed
    az group deployment create \
            -g $resourceGroupName \
            -n $deploymentName \
            --template-file template.json \
            --parameters parameters.json
    ```

1. После завершения развертывания загрузите вывод развертывания `deployment-outputs.json`в файл с именем .
    ```azurecli
    az group deployment show -g $resourceGroupName -n $deploymentName --query "properties.outputs" -o json > deployment-outputs.json
    ```

## <a name="set-up-application-gateway-ingress-controller"></a>Настройка контроллера входа в шлюз приложений

С инструкциями в предыдущем разделе мы создали и настроили новый кластер AKS и шлюз приложений. Теперь мы готовы развернуть пример приложения и контроллер входа в нашу новую инфраструктуру Kubernetes.

### <a name="setup-kubernetes-credentials"></a>Настройка Kubernetes полномочия
Для следующих шагов нам нужна команда [kubectl,](https://kubectl.docs.kubernetes.io/) которую мы будем использовать для подключения к нашему новому кластеру Kubernetes. [Облачная оболочка](https://shell.azure.com/) `kubectl` уже установлена. Мы будем `az` использовать CLI для получения учетных данных для Kubernetes.

Получите учетные данные для недавно развернутого AKS[(подробнее):](https://docs.microsoft.com/azure/aks/kubernetes-walkthrough#connect-to-the-cluster)
```azurecli
# use the deployment-outputs.json created after deployment to get the cluster name and resource group name
aksClusterName=$(jq -r ".aksClusterName.value" deployment-outputs.json)
resourceGroupName=$(jq -r ".resourceGroupName.value" deployment-outputs.json)

az aks get-credentials --resource-group $resourceGroupName --name $aksClusterName
```

### <a name="install-aad-pod-identity"></a>Установка AAD Pod Identity
  Azure Active Directory Pod Identity предоставляет доступ к маркерам для [менеджера ресурсов Azure (ARM).](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview)

  [AAD Pod Identity](https://github.com/Azure/aad-pod-identity) добавит следующие компоненты в кластер Kubernetes:
   * Kubernetes [CRDs](https://kubernetes.io/docs/tasks/access-kubernetes-api/custom-resources/custom-resource-definitions/): `AzureIdentity`, `AzureAssignedIdentity``AzureIdentityBinding`
   * компонент [Контроллер управляемых удостоверений (MIC)](https://github.com/Azure/aad-pod-identity#managed-identity-controllermic);
   * компонент [Node Managed Identity (NMI)](https://github.com/Azure/aad-pod-identity#node-managed-identitynmi).


Чтобы установить aAD Pod Identity в кластер:

   - *RBAC включен* Кластер АКС

     ```bash
     kubectl create -f https://raw.githubusercontent.com/Azure/aad-pod-identity/master/deploy/infra/deployment-rbac.yaml
     ```

   - *RBAC отключен* Кластер АКС

     ```bash
     kubectl create -f https://raw.githubusercontent.com/Azure/aad-pod-identity/master/deploy/infra/deployment.yaml
     ```

### <a name="install-helm"></a>Установка Helm
[Хельм](https://docs.microsoft.com/azure/aks/kubernetes-helm) является менеджером пакетов для Kubernetes. Мы будем использовать его `application-gateway-kubernetes-ingress` для установки пакета:

1. Установите [шлем](https://docs.microsoft.com/azure/aks/kubernetes-helm) и запустите следующие, чтобы добавить `application-gateway-kubernetes-ingress` пакет руля:

    - *RBAC включен* Кластер АКС

        ```bash
        kubectl create serviceaccount --namespace kube-system tiller-sa
        kubectl create clusterrolebinding tiller-cluster-rule --clusterrole=cluster-admin --serviceaccount=kube-system:tiller-sa
        helm init --tiller-namespace kube-system --service-account tiller-sa
        ```

    - *RBAC отключен* Кластер АКС

        ```bash
        helm init
        ```

1. Добавьте репозиторий Helm AGIC:
    ```bash
    helm repo add application-gateway-kubernetes-ingress https://appgwingress.blob.core.windows.net/ingress-azure-helm-package/
    helm repo update
    ```

### <a name="install-ingress-controller-helm-chart"></a>Установка диаграммы Helm контроллера входящего трафика

1. Используйте `deployment-outputs.json` файл, созданный выше, и создайте следующие переменные.
    ```bash
    applicationGatewayName=$(jq -r ".applicationGatewayName.value" deployment-outputs.json)
    resourceGroupName=$(jq -r ".resourceGroupName.value" deployment-outputs.json)
    subscriptionId=$(jq -r ".subscriptionId.value" deployment-outputs.json)
    identityClientId=$(jq -r ".identityClientId.value" deployment-outputs.json)
    identityResourceId=$(jq -r ".identityResourceId.value" deployment-outputs.json)
    ```
1. Скачать рулевой-config.yaml, который будет настроить AGIC:
    ```bash
    wget https://raw.githubusercontent.com/Azure/application-gateway-kubernetes-ingress/master/docs/examples/sample-helm-config.yaml -O helm-config.yaml
    ```
    Или скопите файл YAML ниже: 
    
    ```yaml
    # This file contains the essential configs for the ingress controller helm chart

    # Verbosity level of the App Gateway Ingress Controller
    verbosityLevel: 3
    
    ################################################################################
    # Specify which application gateway the ingress controller will manage
    #
    appgw:
        subscriptionId: <subscriptionId>
        resourceGroup: <resourceGroupName>
        name: <applicationGatewayName>
    
        # Setting appgw.shared to "true" will create an AzureIngressProhibitedTarget CRD.
        # This prohibits AGIC from applying config for any host/path.
        # Use "kubectl get AzureIngressProhibitedTargets" to view and change this.
        shared: false
    
    ################################################################################
    # Specify which kubernetes namespace the ingress controller will watch
    # Default value is "default"
    # Leaving this variable out or setting it to blank or empty string would
    # result in Ingress Controller observing all acessible namespaces.
    #
    # kubernetes:
    #   watchNamespace: <namespace>
    
    ################################################################################
    # Specify the authentication with Azure Resource Manager
    #
    # Two authentication methods are available:
    # - Option 1: AAD-Pod-Identity (https://github.com/Azure/aad-pod-identity)
    armAuth:
        type: aadPodIdentity
        identityResourceID: <identityResourceId>
        identityClientID:  <identityClientId>
    
    ## Alternatively you can use Service Principal credentials
    # armAuth:
    #    type: servicePrincipal
    #    secretJSON: <<Generate this value with: "az ad sp create-for-rbac --subscription <subscription-uuid> --sdk-auth | base64 -w0" >>
    
    ################################################################################
    # Specify if the cluster is RBAC enabled or not
    rbac:
        enabled: false # true/false
    
    # Specify aks cluster related information. THIS IS BEING DEPRECATED.
    aksClusterConfiguration:
        apiServerAddress: <aks-api-server-address>
    ```

1. Отображайте недавно загруженный рулевой-конфигурация.yaml и заполните разделы `appgw` и `armAuth`.
    ```bash
    sed -i "s|<subscriptionId>|${subscriptionId}|g" helm-config.yaml
    sed -i "s|<resourceGroupName>|${resourceGroupName}|g" helm-config.yaml
    sed -i "s|<applicationGatewayName>|${applicationGatewayName}|g" helm-config.yaml
    sed -i "s|<identityResourceId>|${identityResourceId}|g" helm-config.yaml
    sed -i "s|<identityClientId>|${identityClientId}|g" helm-config.yaml

    # You can further modify the helm config to enable/disable features
    nano helm-config.yaml
    ```

   Значения:
     - `verbosityLevel`: Устанавливает многословный уровень лесозаготовительной инфраструктуры AGIC. Возможные значения можно найти в разделе [Уровни ведения журнала](https://github.com/Azure/application-gateway-kubernetes-ingress/blob/463a87213bbc3106af6fce0f4023477216d2ad78/docs/troubleshooting.md#logging-levels).
     - `appgw.subscriptionId`: Идентификатор подписки Azure, в котором находится шлюз приложений. Например, `a123b234-a3b4-557d-b2df-a0bc12de1234`.
     - `appgw.resourceGroup`: Название группы ресурсов Azure, в которой был создан шлюз приложений. Например, `app-gw-resource-group`.
     - `appgw.name`: Название шлюза приложения. Например, `applicationgatewayd0f0`.
     - `appgw.shared`: Этот флаг boolean должен `false`быть дефолт . Установите, если `true` вам нужен [общий шлюз приложения.](https://github.com/Azure/application-gateway-kubernetes-ingress/blob/072626cb4e37f7b7a1b0c4578c38d1eadc3e8701/docs/setup/install-existing.md#multi-cluster--shared-app-gateway)
     - `kubernetes.watchNamespace`: Укажите пространство имени, которое AGIC должен смотреть. Это может быть одно значение строки или список разделенных запятой имен.
    - `armAuth.type`: может `aadPodIdentity` быть или`servicePrincipal`
    - `armAuth.identityResourceID`: Идентификатор ресурсов управляемой идентификации Azure
    - `armAuth.identityClientId`: Идентификатор клиента удостоверения личности. Ниже можно узнать больше о identity
    - `armAuth.secretJSON`: Только необходимо, когда служба Основной секретный тип выбран (когда `armAuth.type` был установлен на `servicePrincipal`) 


   > [!NOTE]
   > Значения `identityResourceID` `identityClientID` и значения, созданные во время шагов [«Создать идентификацию»,](https://github.com/Azure/application-gateway-kubernetes-ingress/blob/072626cb4e37f7b7a1b0c4578c38d1eadc3e8701/docs/setup/install-new.md#create-an-identity) и могут быть получены снова с помощью следующей команды:
   > ```azurecli
   > az identity show -g <resource-group> -n <identity-name>
   > ```
   > `<resource-group>`в приведенной выше команде находится группа ресурсов вашего шлюза приложения. `<identity-name>`— это имя созданной идентичности. Все идентификаторы данной подписки могут быть перечислены с помощью:`az identity list`


1. Установите пакет контроллера входящего трафика Шлюза приложений:

    ```bash
    helm install -f helm-config.yaml application-gateway-kubernetes-ingress/ingress-azure
    ```

## <a name="install-a-sample-app"></a>Установка примера приложения
Теперь, когда у нас установлены шлюз приложений, AKS и AGIC, мы можем установить пример приложения через [Azure Cloud Shell:](https://shell.azure.com/)

```yaml
cat <<EOF | kubectl apply -f -
apiVersion: v1
kind: Pod
metadata:
  name: aspnetapp
  labels:
    app: aspnetapp
spec:
  containers:
  - image: "mcr.microsoft.com/dotnet/core/samples:aspnetapp"
    name: aspnetapp-image
    ports:
    - containerPort: 80
      protocol: TCP

---

apiVersion: v1
kind: Service
metadata:
  name: aspnetapp
spec:
  selector:
    app: aspnetapp
  ports:
  - protocol: TCP
    port: 80
    targetPort: 80

---

apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: aspnetapp
  annotations:
    kubernetes.io/ingress.class: azure/application-gateway
spec:
  rules:
  - http:
      paths:
      - path: /
        backend:
          serviceName: aspnetapp
          servicePort: 80
EOF
```

Кроме того, вы можете:

* Скачать файл YAML выше:

```bash
curl https://raw.githubusercontent.com/Azure/application-gateway-kubernetes-ingress/master/docs/examples/aspnetapp.yaml -o aspnetapp.yaml
```

* Примените файл YAML:

```bash
kubectl apply -f aspnetapp.yaml
```


## <a name="other-examples"></a>Другие образцы
Это [руководство](ingress-controller-expose-service-over-http-https.md) содержит больше примеров того, как выявить службу AKS через HTTP или HTTPS в Интернет с помощью шлюза приложений.
