---
title: Создание контроллера входящего трафика с помощью существующего шлюза приложений
description: В этой статье содержатся сведения о развертывании контроллера входящего трафика шлюза приложений с помощью существующего шлюза приложений.
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: article
ms.date: 10/22/2019
ms.author: caya
ms.openlocfilehash: 045fb54956e78e826b06dc1c56c29e1c7bd430bd
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/04/2019
ms.locfileid: "73513423"
---
# <a name="install-an-application-gateway-ingress-controller-agic-using-an-existing-application-gateway"></a>Установка контроллера входящего трафика шлюза приложений (АГИК) с помощью существующего шлюза приложений

Контроллер входящего трафика шлюза приложений (АГИК) — это Pod в кластере Kubernetes.
АГИК отслеживает [Входящие ресурсы Kubernetes](https://kubernetes.io/docs/concepts/services-networking/ingress/) и создает и применяет конфигурацию шлюза приложений в зависимости от состояния кластера Kubernetes.

## <a name="outline"></a>Обзор
- [Предварительные требования](#prerequisites)
- [Проверка подлинности Azure Resource Manager (ARM)](#azure-resource-manager-authentication)
    - Вариант 1. [Настройка AAD-Pod-Identity](#set-up-aad-pod-identity) и создание удостоверения Azure в подлокотниках
    - Вариант 2. [Использование субъекта-службы](#using-a-service-principal)
- [Установка контроллера входящего трафика с помощью Helm](#install-ingress-controller-as-a-helm-chart)
- [Шлюз приложений с несколькими кластерами и общим доступом](#multi-cluster--shared-application-gateway). Установите агик в среде, где шлюз приложений является общим для одного или нескольких кластеров AKS и (или) других компонентов Azure.

## <a name="prerequisites"></a>Технические условия
В этом документе предполагается, что у вас уже установлены следующие средства и инфраструктура:
- [AKS](https://azure.microsoft.com/services/kubernetes-service/) с включенной поддержкой [расширенных сетей](https://docs.microsoft.com/azure/aks/configure-azure-cni)
- [Шлюз приложений версии 2](https://docs.microsoft.com/azure/application-gateway/create-zone-redundant) в той же виртуальной сети, что и AKS
- [Удостоверение Pod AAD](https://github.com/Azure/aad-pod-identity) , установленное в КЛАСТЕРе AKS
- [Cloud Shell](https://shell.azure.com/) — это среда оболочки Azure, в которой установлены `az` CLI, `kubectl`и `helm`. Эти средства необходимы для приведенных ниже команд.

Перед установкой АГИК создайте __резервную копию конфигурации шлюза приложений__ :
  1. использование [портал Azure](https://portal.azure.com/) перехода к экземпляру `Application Gateway`
  2. в `Export template` щелкните `Download`

Скачанный ZIP-файл будет содержать шаблоны JSON, bash и скрипты PowerShell, которые можно использовать для восстановления шлюза приложений, если это необходимо.

## <a name="install-helm"></a>Установка Helm
[Helm](https://docs.microsoft.com/azure/aks/kubernetes-helm) — это диспетчер пакетов для Kubernetes. Мы будем использовать его для установки пакета `application-gateway-kubernetes-ingress`.
Для установки Helm используйте [Cloud Shell](https://shell.azure.com/) :

1. Установите [Helm](https://docs.microsoft.com/azure/aks/kubernetes-helm) и выполните следующую команду, чтобы добавить пакет Helm `application-gateway-kubernetes-ingress`:

    - *RBAC включен* Кластер AKS

    ```bash
    kubectl create serviceaccount --namespace kube-system tiller-sa
    kubectl create clusterrolebinding tiller-cluster-rule --clusterrole=cluster-admin --serviceaccount=kube-system:tiller-sa
    helm init --tiller-namespace kube-system --service-account tiller-sa
    ```

    - *RBAC отключен* Кластер AKS

    ```bash
    helm init
    ```

1. Добавьте репозиторий АГИК Helm:
    ```bash
    helm repo add application-gateway-kubernetes-ingress https://appgwingress.blob.core.windows.net/ingress-azure-helm-package/
    helm repo update
    ```

## <a name="azure-resource-manager-authentication"></a>Проверка подлинности Azure Resource Manager

АГИК взаимодействует с сервером API Kubernetes и Azure Resource Manager. Для доступа к этим API-интерфейсам требуется удостоверение.

## <a name="set-up-aad-pod-identity"></a>Настройка удостоверения Pod для AAD

[Удостоверение типа POD AAD](https://github.com/Azure/aad-pod-identity) — это контроллер, аналогичный агик, который также работает на AKS. Он привязывает Azure Active Directory удостоверения к модулям Pod Kubernetes. Удостоверение требуется для того, чтобы приложение в Kubernetes Pod могло взаимодействовать с другими компонентами Azure. В конкретном случае для модуля АГИК требуется авторизация для выполнения HTTP-запросов к [ARM](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview).

Чтобы добавить этот компонент в AKS, следуйте [инструкциям по установке удостоверения Pod AAD](https://github.com/Azure/aad-pod-identity#deploy-the-azure-aad-identity-infra) .

Далее нам нужно создать удостоверение Azure и дать ему права на ARM.
Используйте [Cloud Shell](https://shell.azure.com/) , чтобы выполнить все приведенные ниже команды и создать удостоверение.

1. Создайте удостоверение Azure **в той же группе ресурсов, что и узлы AKS**. Важно выбрать правильную группу ресурсов. Группа ресурсов, необходимая в приведенной ниже команде, *не* является ссылкой на область портала AKS. Это группа ресурсов `aks-agentpool` виртуальных машин. Обычно эта группа ресурсов начинается с `MC_` и содержит имя AKS. Например: `MC_resourceGroup_aksABCD_westus`

    ```bash
    az identity create -g <agent-pool-resource-group> -n <identity-name>
    ```

1. Для приведенных ниже команд назначения ролей необходимо получить `principalId` для вновь созданного удостоверения:

    ```bash
    az identity show -g <resourcegroup> -n <identity-name>
    ```

1. Предоставьте удостоверению `Contributor` доступ к шлюзу приложений. Для этого вам потребуется идентификатор шлюза приложений, который будет выглядеть примерно так: `/subscriptions/A/resourceGroups/B/providers/Microsoft.Network/applicationGateways/C`

    Получите список идентификаторов шлюзов приложений в подписке с помощью: `az network application-gateway list --query '[].id'`

    ```bash
    az role assignment create \
        --role Contributor \
        --assignee <principalId> \
        --scope <App-Gateway-ID>
    ```

1. Предоставьте удостоверению `Reader` доступ к группе ресурсов шлюза приложений. Идентификатор группы ресурсов будет выглядеть так: `/subscriptions/A/resourceGroups/B`. Все группы ресурсов можно получить с помощью: `az group list --query '[].id'`

    ```bash
    az role assignment create \
        --role Reader \
        --assignee <principalId> \
        --scope <App-Gateway-Resource-Group-ID>
    ```

## <a name="using-a-service-principal"></a>Использование субъекта-службы
Также можно предоставить АГИК доступ к ARM через секрет Kubernetes.

1. Создайте субъект-службу Active Directory и закодировать его с помощью Base64. Для сохранения большого двоичного объекта JSON в Kubernetes требуется кодировка Base64.

```bash
az ad sp create-for-rbac --subscription <subscription-uuid> --sdk-auth | base64 -w0
```

2. Добавьте большой двоичный объект JSON в кодировке Base64 в файл `helm-config.yaml`. Дополнительные сведения о `helm-config.yaml` см. в следующем разделе.
```yaml
armAuth:
    type: servicePrincipal
    secretJSON: <Base64-Encoded-Credentials>
```

## <a name="install-ingress-controller-as-a-helm-chart"></a>Установка контроллера входящего трафика как Helm диаграммы
В первых нескольких шагах мы установим Helm в кластере Kubernetes. Для установки пакета Helm АГИК используйте [Cloud Shell](https://shell.azure.com/) :

1. Добавление `application-gateway-kubernetes-ingress` репозитория Helm и выполнение обновления Helm

    ```bash
    helm repo add application-gateway-kubernetes-ingress https://appgwingress.blob.core.windows.net/ingress-azure-helm-package/
    helm repo update
    ```

1. Скачайте Helm-config. YAML, который будет настраивать АГИК:
    ```bash
    wget https://raw.githubusercontent.com/Azure/application-gateway-kubernetes-ingress/master/docs/examples/sample-helm-config.yaml -O helm-config.yaml
    ```
    Или скопируйте файл YAML ниже: 
    
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

1. Измените Helm-config. YAML и заполните значения для `appgw` и `armAuth`.
    ```bash
    nano helm-config.yaml
    ```

    > [!NOTE] 
    > `<identity-resource-id>` и `<identity-client-id>` являются свойствами удостоверения Azure AD, настроенного в предыдущем разделе. Эти сведения можно получить, выполнив следующую команду: `az identity show -g <resourcegroup> -n <identity-name>`, где `<resourcegroup>` — это группа ресурсов, в которой развернуты объект кластера AKS верхнего уровня, шлюз приложений и управляемая оценка.

1. Установка `application-gateway-kubernetes-ingress` диаграммы Helm с конфигурацией `helm-config.yaml` из предыдущего шага

    ```bash
    helm install -f <helm-config.yaml> application-gateway-kubernetes-ingress/ingress-azure
    ```

    Кроме того, можно объединить `helm-config.yaml` и команду Helm в одном шаге:
    ```bash
    helm install ./helm/ingress-azure \
         --name ingress-azure \
         --namespace default \
         --debug \
         --set appgw.name=applicationgatewayABCD \
         --set appgw.resourceGroup=your-resource-group \
         --set appgw.subscriptionId=subscription-uuid \
         --set appgw.shared=false \
         --set armAuth.type=servicePrincipal \
         --set armAuth.secretJSON=$(az ad sp create-for-rbac --subscription <subscription-uuid> --sdk-auth | base64 -w0) \
         --set rbac.enabled=true \
         --set verbosityLevel=3 \
         --set kubernetes.watchNamespace=default \
         --set aksClusterConfiguration.apiServerAddress=aks-abcdefg.hcp.westus2.azmk8s.io
    ```

1. Проверьте журнал созданного модуля, чтобы проверить, правильно ли он запущен.

Ознакомьтесь с [этим руководством](ingress-controller-expose-service-over-http-https.md) , чтобы понять, как можно предоставить службу AKS через HTTP или HTTPS в Интернете с помощью шлюза приложений Azure.



## <a name="multi-cluster--shared-application-gateway"></a>Шлюз приложений с несколькими кластерами или общим доступом
По умолчанию АГИК предполагает полное владение шлюзом приложений, с которым он связан. АГИК версии 0.8.0 и более поздних версий могут совместно использовать один шлюз приложений с другими компонентами Azure. Например, можно использовать один и тот же шлюз приложений для приложения, размещенного в масштабируемом наборе виртуальных машин, а также в кластере AKS.

Перед включением этого параметра создайте __резервную копию конфигурации шлюза приложений__ :
  1. использование [портал Azure](https://portal.azure.com/) перехода к экземпляру `Application Gateway`
  2. в `Export template` щелкните `Download`

Скачанный ZIP-файл будет содержать шаблоны JSON, bash и сценарии PowerShell, которые можно использовать для восстановления шлюза приложений.

### <a name="example-scenario"></a>Пример сценария
Рассмотрим воображаемый шлюз приложений, который управляет трафиком для двух веб-сайтов:
  - `dev.contoso.com`, размещенный на новом AKS с использованием шлюза приложений и АГИК
  - `prod.contoso.com`, размещенный в [масштабируемом наборе машин Azure виртуальную](https://azure.microsoft.com/services/virtual-machine-scale-sets/)

При использовании параметров по умолчанию АГИК предполагает владение шлюзом приложений на 100%, на который указывает. АГИК перезаписывает все настройки шлюза приложений. Если бы мы вручную создали прослушиватель для `prod.contoso.com` (в шлюзе приложений), не определяя его в Kubernetes, АГИК удалит конфигурацию `prod.contoso.com` в течение нескольких секунд.

Чтобы установить АГИК и также обслуживать `prod.contoso.com` на виртуальных машинах масштабируемого набора, необходимо ограничить АГИК для настройки только `dev.contoso.com`. Это упрощается путем создания экземпляра следующих [CRD](https://kubernetes.io/docs/concepts/extend-kubernetes/api-extension/custom-resources/):

```bash
cat <<EOF | kubectl apply -f -
apiVersion: "appgw.ingress.k8s.io/v1"
kind: AzureIngressProhibitedTarget
metadata:
  name: prod-contoso-com
spec:
  hostname: prod.contoso.com
EOF
```

Приведенная выше команда создает объект `AzureIngressProhibitedTarget`. Таким образом, АГИК (версия 0.8.0 и более поздние версии) осведомлена о существовании конфигурации шлюза приложений для `prod.contoso.com` и явно инструктирует его, чтобы избежать изменения конфигурации, связанной с этим именем узла.


### <a name="enable-with-new-agic-installation"></a>Включить при установке новой АГИК
Чтобы ограничить АГИК (Version 0.8.0 и более поздние версии) подмножеством конфигурации шлюза приложений, измените шаблон `helm-config.yaml`.
В разделе `appgw:` добавьте ключ `shared` и задайте для него значение `true`.

```yaml
appgw:
    subscriptionId: <subscriptionId>    # existing field
    resourceGroup: <resourceGroupName>  # existing field
    name: <applicationGatewayName>      # existing field
    shared: true                        # <<<<< Add this field to enable shared Application Gateway >>>>>
```

Применить изменения Helm:
  1. Убедитесь, что `AzureIngressProhibitedTarget` CRD установлен с помощью:
      ```bash
      kubectl apply -f https://raw.githubusercontent.com/Azure/application-gateway-kubernetes-ingress/ae695ef9bd05c8b708cedf6ff545595d0b7022dc/crds/AzureIngressProhibitedTarget.yaml
      ```
  2. Обновление Helm:
      ```bash
      helm upgrade \
          --recreate-pods \
          -f helm-config.yaml \
          ingress-azure application-gateway-kubernetes-ingress/ingress-azure
      ```

В результате у AKS будет новый экземпляр `AzureIngressProhibitedTarget` с именем `prohibit-all-targets`:
```bash
kubectl get AzureIngressProhibitedTargets prohibit-all-targets -o yaml
```

Объект `prohibit-all-targets`, как подразумевает имя, запрещает АГИК изменять конфигурацию для *любого* узла и пути.
Helm install с `appgw.shared=true` выполнит развертывание АГИК, но не внесет никаких изменений в шлюз приложений.


### <a name="broaden-permissions"></a>Расширить разрешения
Поскольку Helm с `appgw.shared=true` и `prohibit-all-targets` по умолчанию блокирует применение любой конфигурации.

Расширить разрешения АГИК с помощью:
1. Создайте новый `AzureIngressProhibitedTarget` с конкретной настройкой:
    ```bash
    cat <<EOF | kubectl apply -f -
    apiVersion: "appgw.ingress.k8s.io/v1"
    kind: AzureIngressProhibitedTarget
    metadata:
      name: your-custom-prohibitions
    spec:
      hostname: your.own-hostname.com
    EOF
    ```

2. Вы можете удалить значение по умолчанию, которое является слишком широким, только после создания собственного пользовательского запрета.

    ```bash
    kubectl delete AzureIngressProhibitedTarget prohibit-all-targets
    ```

### <a name="enable-for-an-existing-agic-installation"></a>Включить для существующей установки АГИК
Предположим, что у нас уже есть рабочий AKS, шлюз приложений и настроенный АГИК в нашем кластере. У нас есть входящий `prod.contosor.com` и они успешно обслуживают трафик из AKS. Мы хотим добавить `staging.contoso.com` к существующему шлюзу приложений, но необходимо разместить его на [виртуальной машине](https://azure.microsoft.com/services/virtual-machines/). Мы будем повторно использовать существующий шлюз приложений и вручную настроить прослушиватель и пулы серверной части для `staging.contoso.com`. Но Настройка конфигурации шлюза приложений вручную (через [портал](https://portal.azure.com), [API ARM](https://docs.microsoft.com/rest/api/resources/) или [terraform](https://www.terraform.io/)) противоречит предположениям полного владения агик. Вскоре после применения изменений АГИК будет перезаписывать или удалять их.

Мы можем запретить АГИК внесение изменений в подмножество конфигурации.

1. Создайте объект `AzureIngressProhibitedTarget`:
    ```bash
    cat <<EOF | kubectl apply -f -
    apiVersion: "appgw.ingress.k8s.io/v1"
    kind: AzureIngressProhibitedTarget
    metadata:
      name: manually-configured-staging-environment
    spec:
      hostname: staging.contoso.com
    EOF
    ```

2. Просмотрите только что созданный объект:
    ```bash
    kubectl get AzureIngressProhibitedTargets
    ```

3. Изменение конфигурации шлюза приложений с помощью портала — Добавление прослушивателей, правил маршрутизации, серверной точки и т. д. Новый созданный объект (`manually-configured-staging-environment`) не позволит АГИК перезаписывать конфигурацию шлюза приложений, связанную с `staging.contoso.com`.
