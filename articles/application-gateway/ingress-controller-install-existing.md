---
title: Создание контроллера входа с существующим шлюзом приложений
description: В этой статье содержится информация о том, как развернуть контроллер входа в шлюз приложений с существующим шлюзом приложений.
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: article
ms.date: 11/4/2019
ms.author: caya
ms.openlocfilehash: 048ab7249b27839890bab3e677154ca3c7a0cc98
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80239436"
---
# <a name="install-an-application-gateway-ingress-controller-agic-using-an-existing-application-gateway"></a>Установка контроллера входа в шлюз приложений (AGIC) с помощью существующего шлюза приложений

Контроллер входа в приложение (AGIC) — это стручок в вашем кластере Kubernetes.
AGIC отслеживает ресурсы Kubernetes [Ingress](https://kubernetes.io/docs/concepts/services-networking/ingress/) и создает и применяет конфигурацию Application Gateway в зависимости от статуса кластера Kubernetes.

## <a name="outline"></a>Контур:
- [Предварительные требования](#prerequisites)
- [Аутентификация менеджера ресурсов Azure (ARM)](#azure-resource-manager-authentication)
    - Вариант 1: [Настройка aad-pod-identity](#set-up-aad-pod-identity) и создание идентификационных данных Azure на ARMs
    - Вариант 2: [Использование директора службы](#using-a-service-principal)
- [Установка контроллера Ingress с помощью helm](#install-ingress-controller-as-a-helm-chart)
- [Многокластерный / общий шлюз приложений](#multi-cluster--shared-application-gateway): Установка AGIC в среде, где шлюз приложения распределяется между одним или несколькими кластерами AKS и/или другими компонентами Azure.

## <a name="prerequisites"></a>Предварительные требования
Этот документ предполагает, что у вас уже установлены следующие инструменты и инфраструктура:
- [AKS](https://azure.microsoft.com/services/kubernetes-service/) с помощью [расширенной сети](https://docs.microsoft.com/azure/aks/configure-azure-cni)
- [Приложение Gateway v2](https://docs.microsoft.com/azure/application-gateway/create-zone-redundant) в той же виртуальной сети, что и AKS
- [Идентификация AAD Pod](https://github.com/Azure/aad-pod-identity) установлена на кластере AKS
- [Обупотечная оболочка](https://shell.azure.com/) — `az` это среда `kubectl`оболочки Azure, которая имеет CLI и `helm` устанавливается. Эти инструменты необходимы для команд ниже.

Пожалуйста, __резервное копирование конфигурации вашего приложения шлюза__ перед установкой AGIC:
  1. с помощью [портала Azure](https://portal.azure.com/) перейти к экземпляру `Application Gateway`
  2. от `Export template` клика`Download`

Заспрозагруженный файл на молнии будет иметь шаблоны JSON, скрипты bash и PowerShell, которые можно использовать для восстановления App Gateway, если это станет необходимым

## <a name="install-helm"></a>Установка Helm
[Хельм](https://docs.microsoft.com/azure/aks/kubernetes-helm) является менеджером пакетов для Kubernetes. Мы будем использовать его `application-gateway-kubernetes-ingress` для установки пакета.
Используйте [облачную оболочку](https://shell.azure.com/) для установки Helm:

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

## <a name="azure-resource-manager-authentication"></a>Аутентификация менеджера ресурсов Azure

AGIC общается с сервером API Kubernetes и менеджером ресурсов Azure. Для доступа к этим AAP требуется идентификация.

## <a name="set-up-aad-pod-identity"></a>Настройка идентичности AAD Pod

[AAD Pod Identity](https://github.com/Azure/aad-pod-identity) — это контроллер, похожий на AGIC, который также работает на вашем AKS. Он связывает идентификаторы Azure Active Directory с вашими стручками Kubernetes. Identity необходим для того, чтобы приложение в стручке Kubernetes могло общаться с другими компонентами Azure. В данном конкретном случае нам нужно разрешение для стручка AGIC, чтобы сделать http запросы [на ARM](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview).

Следуйте инструкциям по [установке AAD Pod Identity,](https://github.com/Azure/aad-pod-identity#deploy-the-azure-aad-identity-infra) чтобы добавить этот компонент в AKS.

Далее нам необходимо создать идентификатор Azure и дать ей разрешения ARM.
Используйте [Cloud Shell](https://shell.azure.com/) для выполнения всех следующих команд и создания идентификации:

1. Создайте иноеудостоверения Azure **в той же группе ресурсов, что и узлы AKS.** Важно выбрать правильную группу ресурсов. Ресурсная группа, требуемая в команде ниже, *не* является той, на что ссылается на панели портала AKS. Это ресурсная группа `aks-agentpool` виртуальных машин. Обычно эта группа `MC_` ресурсов начинается с и содержит имя вашего AKS. Например:`MC_resourceGroup_aksABCD_westus`

    ```azurecli
    az identity create -g <agent-pool-resource-group> -n <identity-name>
    ```

1. Для команды назначения ролей ниже нам `principalId` нужно получить для вновь созданного удостоверения:

    ```azurecli
    az identity show -g <resourcegroup> -n <identity-name>
    ```

1. Предоставьте `Contributor` identity-доступ к шлюзу приложения. Для этого вам нужен идентификатор шлюза приложения, который будет выглядеть примерно так:`/subscriptions/A/resourceGroups/B/providers/Microsoft.Network/applicationGateways/C`

    Получите список идов шлюза приложения в подписке с:`az network application-gateway list --query '[].id'`

    ```azurecli
    az role assignment create \
        --role Contributor \
        --assignee <principalId> \
        --scope <App-Gateway-ID>
    ```

1. Предоставь `Reader` идентификационный доступ к группе ресурсов Application Gateway. Идентификатор группы `/subscriptions/A/resourceGroups/B`ресурсов будет выглядеть следующим: . Вы можете получить все группы ресурсов с:`az group list --query '[].id'`

    ```azurecli
    az role assignment create \
        --role Reader \
        --assignee <principalId> \
        --scope <App-Gateway-Resource-Group-ID>
    ```

## <a name="using-a-service-principal"></a>Использование директора службы
Кроме того, можно предоставить AGIC доступ к ARM через секрет Kubernetes.

1. Создайте принцип службы активного каталога и закодивите с помощью base64. Кодирование base64 необходимо для того, чтобы капля JSON была сохранена в Кубернете.

```azurecli
az ad sp create-for-rbac --subscription <subscription-uuid> --sdk-auth | base64 -w0
```

2. Добавьте в `helm-config.yaml` файл закодированную d'Sda JSON blob. Более подробная информация о `helm-config.yaml` следующем разделе.
```yaml
armAuth:
    type: servicePrincipal
    secretJSON: <Base64-Encoded-Credentials>
```

## <a name="install-ingress-controller-as-a-helm-chart"></a>Установка контроллера Ingress как диаграмма helm
В первые несколько шагов мы устанавливаем Helm's Tiller в кластер Еберунете. Используйте [Cloud Shell](https://shell.azure.com/) для установки пакета AGIC Helm:

1. Добавить `application-gateway-kubernetes-ingress` репо руля и выполнить обновление руля

    ```bash
    helm repo add application-gateway-kubernetes-ingress https://appgwingress.blob.core.windows.net/ingress-azure-helm-package/
    helm repo update
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

1. Оторите рулевой-config.yaml и `appgw` заполните в значениях для и `armAuth`.
    ```bash
    nano helm-config.yaml
    ```

    > [!NOTE] 
    > `<identity-client-id>` Свойства `<identity-resource-id>` идентификатора Azure AD Identity, который вы установили в предыдущем разделе. Вы можете получить эту информацию, `az identity show -g <resourcegroup> -n <identity-name>`запустив следующую команду: , где `<resourcegroup>` находится группа ресурсов, в которой развернуты объект кластера AKS верхнего уровня, шлюз приложений и Управляемое определение.

1. Установка диаграммы `application-gateway-kubernetes-ingress` `helm-config.yaml` helm с конфигурацией предыдущего шага

    ```bash
    helm install -f <helm-config.yaml> application-gateway-kubernetes-ingress/ingress-azure
    ```

    Кроме того, вы `helm-config.yaml` можете объединить команду Helm в одном шаге:
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

1. Проверьте журнал вновь созданного стручка, чтобы проверить, правильно ли он начался

Обратитесь к [этому руководству,](ingress-controller-expose-service-over-http-https.md) чтобы понять, как можно разоблачить службу AKS через HTTP или HTTPS в Интернете, используя шлюз приложения Azure.



## <a name="multi-cluster--shared-application-gateway"></a>Многокластерный / общий шлюз приложений
По умолчанию AGIC берет на себя полное владение шлюзом приложения, с которым он связан. ВЕРСИЯ AGIC 0.8.0 и позже может совместно с другими компонентами Azure делиться одним шлюзом приложений. Например, мы могли бы использовать тот же шлюз приложений для приложения, размещенного на наборе виртуальной шкалы машин, а также кластера AKS.

Пожалуйста, __резервное копирование конфигурации вашего шлюза приложения,__ прежде чем включить эту настройку:
  1. с помощью [портала Azure](https://portal.azure.com/) перейти к экземпляру `Application Gateway`
  2. от `Export template` клика`Download`

Заспрозагруженный файл на молнии будет иметь шаблоны JSON, скрипты bash и PowerShell, которые можно использовать для восстановления шлюза приложения

### <a name="example-scenario"></a>Пример сценария
Давайте посмотрим на воображаемый шлюз приложений, который управляет трафиком для двух веб-сайтов:
  - `dev.contoso.com`- размещенный на новом AKS, используя шлюз приложений и AGIC
  - `prod.contoso.com`- размещение на [Azure Виртуальный набор масштабов машины](https://azure.microsoft.com/services/virtual-machine-scale-sets/)

С настройками по умолчанию AGIC предполагает 100% владение шлюзом приложения, на который указывается. AGIC перезаписывает всю конфигурацию App Gateway. Если бы мы вручную создали `prod.contoso.com` слушателя для (на портале приложений), не определив `prod.contoso.com` его в Kubernetes Ingress, AGIC удалит конфигурацию в течение нескольких секунд.

Чтобы установить AGIC, а также подавать из `prod.contoso.com` наших виртуальных машин `dev.contoso.com` машины шкалы набор машин, мы должны ограничить AGIC для настройки только. Этому способствует мгновенное выполнение следующих [CRD:](https://kubernetes.io/docs/concepts/extend-kubernetes/api-extension/custom-resources/)

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

Приведенная выше `AzureIngressProhibitedTarget` команда создает объект. Это делает AGIC (версия 0.8.0 и более поздно) осведомленным о существовании конфигурации Gateway приложения и `prod.contoso.com` явно поручает ему избегать изменения любой конфигурации, связанной с этим хост-именем.


### <a name="enable-with-new-agic-installation"></a>Включить новую установку AGIC
Ограничить AGIC (версия 0.8.0 и более далее) подмножеством `helm-config.yaml` конфигурации gateway приложения изменить шаблон.
Под `appgw:` разделом, `shared` добавить ключ `true`и установить его на .

```yaml
appgw:
    subscriptionId: <subscriptionId>    # existing field
    resourceGroup: <resourceGroupName>  # existing field
    name: <applicationGatewayName>      # existing field
    shared: true                        # <<<<< Add this field to enable shared Application Gateway >>>>>
```

Применить изменения руля:
  1. Убедитесь, что `AzureIngressProhibitedTarget` CRD установлен с:
      ```bash
      kubectl apply -f https://raw.githubusercontent.com/Azure/application-gateway-kubernetes-ingress/ae695ef9bd05c8b708cedf6ff545595d0b7022dc/crds/AzureIngressProhibitedTarget.yaml
      ```
  2. Обновление шлем:
      ```bash
      helm upgrade \
          --recreate-pods \
          -f helm-config.yaml \
          ingress-azure application-gateway-kubernetes-ingress/ingress-azure
      ```

В результате ваш AKS будет иметь `AzureIngressProhibitedTarget` `prohibit-all-targets`новый экземпляр называется:
```bash
kubectl get AzureIngressProhibitedTargets prohibit-all-targets -o yaml
```

Объект, `prohibit-all-targets`как следует из названия, запрещает AGIC изменять конфигурацию для *любого* узла и пути.
Установка helm `appgw.shared=true` с развернуть AGIC, но не будет вносить никаких изменений в приложение шлюз.


### <a name="broaden-permissions"></a>Расширение разрешений
С Helm `appgw.shared=true` с `prohibit-all-targets` и по умолчанию блокирует AGIC от применения любой конфигурации.

Расширить разрешения AGIC с:
1. Создайте `AzureIngressProhibitedTarget` новую с помощью конкретной настройки:
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

2. Только после того, как вы создали свой собственный пользовательский запрет, вы можете удалить по умолчанию, который является слишком широким:

    ```bash
    kubectl delete AzureIngressProhibitedTarget prohibit-all-targets
    ```

### <a name="enable-for-an-existing-agic-installation"></a>Включить для существующей установки AGIC
Предположим, что в нашем кластере уже есть работающий AKS, Application Gateway и настроен AGIC. У нас есть `prod.contosor.com` Ingress для и успешно обслуживая трафик для него от AKS. Мы хотим `staging.contoso.com` добавить к нашему существующему шлюзу приложений, но нам нужно разместить его на [VM.](https://azure.microsoft.com/services/virtual-machines/) Мы собираемся повторно использовать существующий шлюз приложения и вручную настроить `staging.contoso.com`слушателя и бэкэнд бассейны для . Но вручную настройки приложения gateway конфигурации (через [портал,](https://portal.azure.com) [ARM AIS](https://docs.microsoft.com/rest/api/resources/) или [Terraform](https://www.terraform.io/)) будет противоречить предположениям AGIC о полной собственности. Вскоре после того, как мы применяем изменения, AGIC перезаписывает или удаляет их.

Мы можем запретить AGIC вносить изменения в подмножество конфигурации.

1. Создание `AzureIngressProhibitedTarget` объекта:
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

2. Просмотр вновь созданного объекта:
    ```bash
    kubectl get AzureIngressProhibitedTargets
    ```

3. Изменение приложения шлюз конфигурации через портал - добавить слушателей, правила разгрома, бэкэнды и т.д. Новый объект,`manually-configured-staging-environment`который мы создали () запретит AGIC `staging.contoso.com`переписать конфигурацию application Gateway, связанную с .
