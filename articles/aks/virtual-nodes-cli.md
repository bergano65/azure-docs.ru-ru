---
title: Создание виртуальных узлов в Службе Azure Kubernetes (AKS) с помощью Azure CLI
description: Сведения о том, как с помощью Azure CLI создать кластер Службы Azure Kubernetes (AKS), который использует виртуальные узлы для запуска pod.
services: container-service
author: iainfoulds
ms.topic: conceptual
ms.service: container-service
ms.date: 05/06/2019
ms.author: iainfou
ms.openlocfilehash: 7631a2d6aef2efedf30c0b9015913c89949d4c29
ms.sourcegitcommit: 8fc5f676285020379304e3869f01de0653e39466
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/09/2019
ms.locfileid: "65506960"
---
# <a name="create-and-configure-an-azure-kubernetes-services-aks-cluster-to-use-virtual-nodes-using-the-azure-cli"></a>Создание и настройка кластера Службы Azure Kubernetes (AKS) для использования виртуальных узлов с помощью Azure CLI

Чтобы быстро масштабировать рабочие нагрузки приложения в кластере Службы Azure Kubernetes (AKS), вы можете применить виртуальные узлы. Виртуальные узлы дают возможность быстро подготовить pod и оплачивать только время их выполнения (посекундно). Вам не придется ждать, пока средство автомасштабирования кластера Kubernetes развернет вычислительные узлы для виртуальных машин, на которых можно будет запустить дополнительные pod. Виртуальные узлы поддерживаются только с Linux модулей и узлами.

В этой статье показано, как создать и настроить ресурсы виртуальной сети и кластер AKS, а также включить виртуальные узлы.

## <a name="before-you-begin"></a>Перед началом работы

Виртуальные узлы обеспечивают взаимодействие контейнеров pod в сети, которые выполняются в кластерах ACI и AKS. Для обеспечения этого взаимодействия создается подсеть виртуальной сети и назначаются делегированные разрешения. Виртуальные узлы работают только с кластерами AKS, созданными с помощью сетевого взаимодействия уровня *Расширенный*. По умолчанию кластеры AKS создаются с помощью сетевого взаимодействия уровня *Базовый*. В этой статье показано, как создать виртуальную сеть и подсети, а затем развернуть кластер AKS, использующий сетевое взаимодействие уровня "Расширенный".

Если вы не использовали ACI ранее, зарегистрируйте поставщик служб в подписке. Вы можете проверить состояние регистрации поставщика ACI с помощью команды [az provider list][az-provider-list], как показано в следующем примере.

```azurecli-interactive
az provider list --query "[?contains(namespace,'Microsoft.ContainerInstance')]" -o table
```

Поставщик *Microsoft.ContainerInstance* должен иметь состояние *Registered*, как показано в следующем примере выходных данных.

```
Namespace                    RegistrationState
---------------------------  -------------------
Microsoft.ContainerInstance  Registered
```

Если поставщик имеет состояние *NotRegistered*, зарегистрируйте поставщик с помощью команды [az provider register][az-provider-register], как показано в следующем примере.

```azurecli-interactive
az provider register --namespace Microsoft.ContainerInstance
```

## <a name="regional-availability"></a>Доступность в регионах

Для развертывания на узле виртуального поддерживаются следующие регионы:

* Восточная Австралия (australiaeast)
* Центральная часть США (centralus)
* Восточная часть США (eastus).
* Восточная часть США 2 (eastus2)
* Восточная Япония (japaneast)
* Северная Европа (northeurope)
* Юго-Восточная Азия (southeastasia)
* Западно-Центральная часть США (westcentralus)
* Западная Европа (westeurope).
* Западная часть США (westus).
* Западная часть США 2 (westus2).

## <a name="known-limitations"></a>Известные ограничения
Виртуальные узлы функциональные возможности сильно зависит от набора функций в ACI. Приведенные ниже еще не поддерживается в виртуальных узлов

* С помощью субъекта-службы для получения образов ACR. [Инструкции по решению](https://github.com/virtual-kubelet/virtual-kubelet/blob/master/providers/azure/README.md#Private-registry) заключается в использовании [секреты Kubernetes](https://kubernetes.io/docs/tasks/configure-pod-container/pull-image-private-registry/#create-a-secret-by-providing-credentials-on-the-command-line)
* [Ограничения виртуальной сети](../container-instances/container-instances-vnet.md) включая пиринг виртуальной сети, политик сети Kubernetes и исходящий трафик в Интернет с помощью групп безопасности сети.
* Контейнеры init
* [Псевдонимы узлов](https://kubernetes.io/docs/concepts/services-networking/add-entries-to-pod-etc-hosts-with-host-aliases/)
* [Аргументы](../container-instances/container-instances-exec.md#restrictions) для exec в ACI
* [Наборы Daemonset](concepts-clusters-workloads.md#statefulsets-and-daemonsets) не будет выполнять развертывание модулей на виртуальный узел

## <a name="launch-azure-cloud-shell"></a>Запуск Azure Cloud Shell

Azure Cloud Shell — это бесплатная интерактивная оболочка, с помощью которой можно выполнять действия, описанные в этой статье. Она включает предварительно установленные общие инструменты Azure и настроена для использования с вашей учетной записью.

Чтобы открыть Cloud Shell, выберите **Попробовать** в правом верхнем углу блока кода. Cloud Shell можно также запустить в отдельной вкладке браузера, перейдя на страницу [https://shell.azure.com/bash](https://shell.azure.com/bash). Нажмите кнопку **Копировать**, чтобы скопировать блоки кода. Вставьте код в Cloud Shell и нажмите клавишу "ВВОД", чтобы выполнить его.

Если вы решили установить и использовать CLI локально, для выполнения инструкций из этой статьи вам потребуется Azure CLI 2.0.49 или более поздней версии. Чтобы узнать версию, выполните команду `az --version`. Если вам необходимо выполнить установку или обновление, см. статью [Установка Azure CLI 2.0]( /cli/azure/install-azure-cli).

## <a name="create-a-resource-group"></a>Создание группы ресурсов

Группа ресурсов Azure — это логическая группа, в которой выполняется развертывание и администрирование ресурсов Azure. Создайте группу ресурсов с помощью команды [az group create][az-group-create]. В следующем примере создается группа ресурсов с именем *myResourceGroup* в расположении *westus*.

```azurecli-interactive
az group create --name myResourceGroup --location westus
```

## <a name="create-a-virtual-network"></a>Создать виртуальную сеть

Создайте виртуальную сеть с помощью команды [az network vnet create][az-network-vnet-create]. В следующем примере создается виртуальная сеть с именем *myVnet* и префиксом адреса *10.0.0.0/8*, а также подсеть с именем *myAKSSubnet*: По умолчанию используется префикс адреса подсети *10.240.0.0/16*:

```azurecli-interactive
az network vnet create \
    --resource-group myResourceGroup \
    --name myVnet \
    --address-prefixes 10.0.0.0/8 \
    --subnet-name myAKSSubnet \
    --subnet-prefix 10.240.0.0/16
```

Теперь создайте дополнительную подсеть для виртуальных узлов с помощью команды [az network vnet subnet create][az-network-vnet-subnet-create]. В следующем примере создается подсеть с именем *myVirtualNodeSubnet* с префиксом адреса *10.241.0.0/16*.

```azurecli-interactive
az network vnet subnet create \
    --resource-group myResourceGroup \
    --vnet-name myVnet \
    --name myVirtualNodeSubnet \
    --address-prefixes 10.241.0.0/16
```

## <a name="create-a-service-principal"></a>Создание субъекта-службы

Для взаимодействия с API-интерфейсами Azure кластеру AKS требуется субъект-служба Azure Active Directory. Этот субъект-служба может быть автоматически создан с помощью интерфейса командной строки или портала Azure либо вы можете сами предварительно создать этот компонент и назначить дополнительные разрешения.

Создайте субъект-службу с помощью команды [az ad sp create-for-rbac][az-ad-sp-create-for-rbac]. Параметр `--skip-assignment` ограничивает назначение дополнительных разрешений.

```azurecli-interactive
az ad sp create-for-rbac --skip-assignment
```

Вы должны увидеть результат, аналогичный приведенному ниже.

```
{
  "appId": "bef76eb3-d743-4a97-9534-03e9388811fc",
  "displayName": "azure-cli-2018-11-21-18-42-00",
  "name": "http://azure-cli-2018-11-21-18-42-00",
  "password": "1d257915-8714-4ce7-a7fb-0e5a5411df7f",
  "tenant": "72f988bf-86f1-41af-91ab-2d7cd011db48"
}
```

Запишите значения параметров *appId* и *password*. Эти значения используются в приведенных далее действиях.

## <a name="assign-permissions-to-the-virtual-network"></a>Назначение разрешений для виртуальной сети

Чтобы разрешить кластеру использовать виртуальную сеть и управлять ею, необходимо предоставить субъекту-службе AKS необходимые права на использование ресурсов сети.

Сначала получите идентификатор ресурса виртуальной сети с помощью команды [az network vnet show][az-network-vnet-show]:

```azurecli-interactive
az network vnet show --resource-group myResourceGroup --name myVnet --query id -o tsv
```

Чтобы предоставить кластеру AKS соответствующие права доступа для использования виртуальной сети, создайте назначение ролей с помощью команды [az role assignment create][az-role-assignment-create]. Замените `<appId`> и `<vnetId>` на значения, собранных на предыдущих двух шагах.

```azurecli-interactive
az role assignment create --assignee <appId> --scope <vnetId> --role Contributor
```

## <a name="create-an-aks-cluster"></a>Создание кластера AKS

Разверните кластер AKS в подсеть AKS, созданную на предыдущем шаге. Получите идентификатор этой подсети с помощью команды [az network vnet subnet show][az-network-vnet-subnet-show]:

```azurecli-interactive
az network vnet subnet show --resource-group myResourceGroup --vnet-name myVnet --name myAKSSubnet --query id -o tsv
```

Используйте команду [az aks create][az-aks-create], чтобы создать кластер AKS. В следующем примере создается кластер *myAKSCluster* с одним узлом. Замените `<subnetId>` идентификатором, полученным на предыдущем шаге, а затем `<appId>` и `<password>` на 

```azurecli-interactive
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --node-count 1 \
    --network-plugin azure \
    --service-cidr 10.0.0.0/16 \
    --dns-service-ip 10.0.0.10 \
    --docker-bridge-address 172.17.0.1/16 \
    --vnet-subnet-id <subnetId> \
    --service-principal <appId> \
    --client-secret <password>
```

Через несколько минут выполнение команды завершается и отображаются сведения о кластере в формате JSON.

## <a name="enable-virtual-nodes-addon"></a>Включить дополнительный компонент виртуальных узлов

Чтобы включить виртуальные узлы, теперь используйте команду [az aks enable-addons][az-aks-enable-addons]. В следующем примере используется подсеть с именем *myVirtualNodeSubnet*, созданная на предыдущем шаге:

```azurecli-interactive
az aks enable-addons \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --addons virtual-node \
    --subnet-name myVirtualNodeSubnet
```

## <a name="connect-to-the-cluster"></a>Подключение к кластеру

Чтобы настроить подключение `kubectl` к кластеру Kubernetes, выполните команду [az aks get-credentials][az-aks-get-credentials]. На этом шаге скачиваются учетные данные и настраивается интерфейс командной строки Kubernetes для их использования.

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

Проверьте подключение к кластеру, выполнив команду [kubectl get][kubectl-get], чтобы просмотреть список узлов кластера.

```console
kubectl get nodes
```

В следующем примере выходных данных показано создание одного узла виртуальной машины и одного виртуального узла для Linux с именем *virtual-node-aci-linux*:

```
$ kubectl get nodes

NAME                          STATUS    ROLES     AGE       VERSION
virtual-node-aci-linux        Ready     agent     28m       v1.11.2
aks-agentpool-14693408-0      Ready     agent     32m       v1.11.2
```

## <a name="deploy-a-sample-app"></a>Развертывание примера приложения

Создайте файл `virtual-node.yaml` и скопируйте в него следующий код YAML. Чтобы назначить контейнер на узел, определите параметры [nodeSelector][node-selector] и [toleration][toleration].

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: aci-helloworld
spec:
  replicas: 1
  selector:
    matchLabels:
      app: aci-helloworld
  template:
    metadata:
      labels:
        app: aci-helloworld
    spec:
      containers:
      - name: aci-helloworld
        image: microsoft/aci-helloworld
        ports:
        - containerPort: 80
      nodeSelector:
        kubernetes.io/role: agent
        beta.kubernetes.io/os: linux
        type: virtual-kubelet
      tolerations:
      - key: virtual-kubelet.io/provider
        operator: Exists
      - key: azure.com/aci
        effect: NoSchedule
```

Запустите приложение, выполнив команду [kubectl apply][kubectl-apply].

```console
kubectl apply -f virtual-node.yaml
```

Чтобы вывести список групп pod и назначенный узел, выполните команду [kubectl get pods][kubectl-get] с аргументом `-o wide`. Обратите внимание, что pod `aci-helloworld` был назначен на узел `virtual-node-aci-linux`.

```
$ kubectl get pods -o wide

NAME                            READY     STATUS    RESTARTS   AGE       IP           NODE
aci-helloworld-9b55975f-bnmfl   1/1       Running   0          4m        10.241.0.4   virtual-node-aci-linux
```

Группа pod получает внутренний IP-адрес из подсети виртуальной сети Azure, которая делегирована для использования с виртуальными узлами.

> [!NOTE]
> При использовании образов, хранящихся в Реестре контейнеров Azure, [настройте и используйте секрет Kubernetes][acr-aks-secrets]. Текущее ограничение виртуальных узлов — что нельзя использовать интеграции Azure AD аутентификация субъекта-службы. Если вы не используете секрет, модули pod, запланированные на виртуальные узлы, не будут запускаться и сообщат об ошибке `HTTP response status code 400 error code "InaccessibleImage"`.

## <a name="test-the-virtual-node-pod"></a>Тестирование группы pod на виртуальном узле

Чтобы протестировать выполнение группы pod на виртуальном узле, откройте демонстрационную версию приложения в веб-клиенте. Так как группа pod получает внутренний IP-адрес, вы можете быстро проверить это подключение с другой группой pod в том же кластере AKS. Создайте тестовый системный модуль pod и свяжите с ним сеанс терминала.

```console
kubectl run -it --rm virtual-node-test --image=debian
```

Установите `curl` в модуле pod с помощью `apt-get`:

```console
apt-get update && apt-get install -y curl
```

Теперь откройте адрес этой группы pod с помощью `curl`, например, *http://10.241.0.4*. Укажите внутренний IP-адрес, который вы получили от предыдущей команды `kubectl get pods`.

```console
curl -L http://10.241.0.4
```

Отобразится демонстрационное приложение, как показано в следующем сокращенном примере выходных данных:

```
$ curl -L 10.241.0.4

<html>
<head>
  <title>Welcome to Azure Container Instances!</title>
</head>
[...]
```

Закройте сеанс подключения терминала к проверяемой группе pod, выполнив `exit`. Когда сеанс завершится, pod автоматически удаляется.

## <a name="remove-virtual-nodes"></a>Удаление виртуальных узлов

Если вы больше не хотите использовать виртуальные узлы, их можно отключить с помощью команды [az aks disable-addons][az aks disable-addons]. 

Во-первых удалите pod helloworld, на виртуальный узел:

```azurecli-interactive
kubectl delete -f virtual-node.yaml
```

Например, следующая команда отключает виртуальные узлы Linux:

```azurecli-interactive
az aks disable-addons --resource-group myResourceGroup --name myAKSCluster --addons virtual-node
```

Теперь удалите ресурсы виртуальной сети и группу ресурсов:

```azurecli-interactive
# Change the name of your resource group, cluster and network resources as needed
RES_GROUP=myResourceGroup
AKS_CLUSTER=myAKScluster
AKS_VNET=myVnet
AKS_SUBNET=myVirtualNodeSubnet

# Get AKS node resource group
NODE_RES_GROUP=$(az aks show --resource-group $RES_GROUP --name $AKS_CLUSTER --query nodeResourceGroup --output tsv)

# Get network profile ID
NETWORK_PROFILE_ID=$(az network profile list --resource-group $NODE_RES_GROUP --query [0].id --output tsv)

# Delete the network profile
az network profile delete --id $NETWORK_PROFILE_ID -y

# Get the service association link (SAL) ID
SAL_ID=$(az network vnet subnet show --resource-group $RES_GROUP --vnet-name $AKS_VNET --name $AKS_SUBNET --query id --output tsv)/providers/Microsoft.ContainerInstance/serviceAssociationLinks/default

# Delete the default SAL ID for the subnet
az resource delete --ids $SAL_ID --api-version 2018-07-01

# Delete the subnet delegation to Azure Container Instances
az network vnet subnet update --resource-group $RES_GROUP --vnet-name $AKS_VNET --name $AKS_SUBNET --remove delegations 0
```

## <a name="next-steps"></a>Дальнейшие действия

В этой статье мы назначили выполнение группы pod в виртуальном узле и присвоили частный внутренний IP-адрес. В качестве альтернативы вы можете создать развертывание службы и направлять трафик в группу pod через подсистему балансировки нагрузки или контроллер входящего трафика. Дополнительные сведения см. в статье [Создание контроллера входящего трафика в Службе Azure Kubernetes (AKS)][aks-basic-ingress].

Виртуальные узлы — это зачастую лишь один из компонентов, необходимых для масштабирования решения в AKS. Дополнительные сведения о таких решениях см. в следующих статьях.

- [Масштабирование приложений в Службе Azure Kubernetes (AKS)][aks-hpa]
- [Автомасштабирование кластера с помощью службы Azure Kubernetes (AKS)][aks-cluster-autoscaler]
- [Ознакомьтесь с примерами автомасштабирования отношении виртуальных узлов][virtual-node-autoscale]
- [Дополнительные сведения о библиотеке Virtual Kubelet открытым исходным кодом][virtual-kubelet-repo]

<!-- LINKS - external -->
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[node-selector]:https://kubernetes.io/docs/concepts/configuration/assign-pod-node/
[toleration]: https://kubernetes.io/docs/concepts/configuration/taint-and-toleration/
[aks-github]: https://github.com/azure/aks/issues
[virtual-node-autoscale]: https://github.com/Azure-Samples/virtual-node-autoscale
[virtual-kubelet-repo]: https://github.com/virtual-kubelet/virtual-kubelet

<!-- LINKS - internal -->
[azure-cli-install]: /cli/azure/install-azure-cli
[az-group-create]: /cli/azure/group#az-group-create
[az-network-vnet-create]: /cli/azure/network/vnet#az-network-vnet-create
[az-network-vnet-subnet-create]: /cli/azure/network/vnet/subnet#az-network-vnet-subnet-create
[az-ad-sp-create-for-rbac]: /cli/azure/ad/sp#az-ad-sp-create-for-rbac
[az-network-vnet-show]: /cli/azure/network/vnet#az-network-vnet-show
[az-role-assignment-create]: /cli/azure/role/assignment#az-role-assignment-create
[az-network-vnet-subnet-show]: /cli/azure/network/vnet/subnet#az-network-vnet-subnet-show
[az-aks-create]: /cli/azure/aks#az-aks-create
[az-aks-enable-addons]: /cli/azure/aks#az-aks-enable-addons
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-aks-get-credentials]: /cli/azure/aks#az-aks-get-credentials
[az aks disable-addons]: /cli/azure/aks#az-aks-disable-addons
[aks-hpa]: tutorial-kubernetes-scale.md
[aks-cluster-autoscaler]: autoscaler.md
[aks-basic-ingress]: ingress-basic.md
[az-provider-list]: /cli/azure/provider#az-provider-list
[az-provider-register]: /cli/azure/provider#az-provider-register
[acr-aks-secrets]: ../container-registry/container-registry-auth-aks.md#access-with-kubernetes-secret
