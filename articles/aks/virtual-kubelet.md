---
title: Запуск Virtual Kubelet в кластере службы Azure Kubernetes
description: Узнайте, как использовать Virtual Kubelet со службой Azure Kubernetes (AKS) для запуска контейнеров Linux и Windows в службе "Экземпляры контейнеров Azure".
services: container-service
author: mlearned
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 05/31/2019
ms.author: mlearned
ms.openlocfilehash: f18992be353d2d6cc739412d98ccd97d5e78d4c7
ms.sourcegitcommit: 0f54f1b067f588d50f787fbfac50854a3a64fff7
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/12/2019
ms.locfileid: "67613860"
---
# <a name="use-virtual-kubelet-with-azure-kubernetes-service-aks"></a>Использование Virtual Kubelet со службой Azure Kubernetes (AKS)

Экземпляры контейнеров Azure предоставляют размещенную среду для запуска контейнеров в Azure. При использовании экземпляров контейнеров Azure вам не требуется управлять базовой вычислительной инфраструктурой, это делает Azure. При запуске контейнеров в Экземплярах контейнеров Azure взимается плата за каждый второй запущенный контейнер.

При использовании поставщика Virtual Kubelet для Экземпляров контейнеров Azure вы можете назначить контейнеры Linux и Windows на экземпляр контейнера, как если бы это был стандартный узел Kubernetes. Эта конфигурация позволяет воспользоваться преимуществами Kubernetes и возможностью управления стоимостью и затратами экземпляров контейнеров.

> [!NOTE]
> Теперь в AKS есть встроенная поддержка планирования контейнеров в ACI (*виртуальные узлы*). Сейчас эти виртуальные узлы поддерживают экземпляры контейнеров Linux. Если вам нужно запланировать экземпляры контейнеров Windows, вы можете продолжить работу с Virtual Kubelet. В противном случае следует использовать виртуальные узлы и не выполнять вручную инструкции Virtual Kubelet, указанные в данной статье. Начать работу с виртуальными узлами можно с помощью [Azure CLI][virtual-nodes-cli] или [портал Azure][virtual-nodes-portal].
>
> Virtual Kubelet представляет собой экспериментальный открытый проект и должен использоваться таким образом. Сведения о том, как внести изменения в файл Virtual kubelet, см. в статье о [проекте Virtual Kubelet GitHub][vk-github].

## <a name="before-you-begin"></a>Перед началом работы

Для выполнения этих инструкций у вас должен быть кластер AKS. Если вам нужен кластер AKS, ознакомьтесь с кратким руководством по [службе Kubernetes Azure (AKS)][aks-quick-start].

Также требуется Azure CLI версии **2.0.65** или более поздней. Чтобы узнать версию, выполните команду `az --version`. Если вам необходимо выполнить установку или обновление, см. статью [Установка Azure CLI 2.0](/cli/azure/install-azure-cli).

Чтобы установить Virtual Kubelet, установите и настройте [Helm][aks-helm] в кластере AKS. При необходимости убедитесь, что ваш кассовый ящик [настроен для использования с KUBERNETES RBAC](#for-rbac-enabled-clusters).

### <a name="register-container-instances-feature-provider"></a>Регистрация поставщика функций для экземпляров контейнеров

Если вы ранее не использовали службу "экземпляр контейнера Azure" (ACI), зарегистрируйте поставщик службы в своей подписке. Состояние регистрации поставщика ACI можно проверить с помощью команды [AZ Provider List][az-provider-list] , как показано в следующем примере:

```azurecli-interactive
az provider list --query "[?contains(namespace,'Microsoft.ContainerInstance')]" -o table
```

Поставщик *Microsoft.ContainerInstance* должен иметь состояние *Registered*, как показано в следующем примере выходных данных.

```console
Namespace                    RegistrationState
---------------------------  -------------------
Microsoft.ContainerInstance  Registered
```

Если поставщик показан как *зарегистрировано*, зарегистрируйте поставщик с помощью команды [AZ Provider Register][az-provider-register] , как показано в следующем примере:

```azurecli-interactive
az provider register --namespace Microsoft.ContainerInstance
```

### <a name="for-rbac-enabled-clusters"></a>Кластеры с поддержкой RBAC

Если используется кластер AKS с поддержкой RBAC, необходимо создать учетную запись службы и привязку роли для использования с Tiller. Дополнительные сведения см. в статье [Helm управления доступом на основе ролей][helm-rbac]. Чтобы создать учетную запись службы и привязку роли, создайте файл с именем *rbac-virtual-kubelet.yaml* и вставьте следующее определение.

```yaml
apiVersion: v1
kind: ServiceAccount
metadata:
  name: tiller
  namespace: kube-system
---
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRoleBinding
metadata:
  name: tiller
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: ClusterRole
  name: cluster-admin
subjects:
  - kind: ServiceAccount
    name: tiller
    namespace: kube-system
```

Примените учетную запись службы и привязку с помощью [kubectl Apply][kubectl-apply] и укажите файл *RBAC-Virtual-kubelet. YAML* , как показано в следующем примере:

```console
$ kubectl apply -f rbac-virtual-kubelet.yaml

clusterrolebinding.rbac.authorization.k8s.io/tiller created
```

Настройка Helm для использования учетной записи службы Tiller.

```console
helm init --service-account tiller
```

Теперь можно продолжить установку Virtual Kubelet в кластере AKS.

## <a name="installation"></a>Установка

Чтобы установить Virtual Kubelet, выполните команду [AZ AKS Install-Connector][aks-install-connector] . В следующем примере разворачиваются соединители как для Linux, так и для Windows.

```azurecli-interactive
az aks install-connector \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --connector-name virtual-kubelet \
    --os-type Both
```

Эти аргументы доступны для команды [AZ AKS Install-Connector][aks-install-connector] .

| Аргумент: | Описание | Обязательное значение |
|---|---|:---:|
| `--connector-name` | Имя соединителя ACI.| Да |
| `--name` `-n` | Имя управляемого кластера. | Да |
| `--resource-group` `-g` | Имя группы ресурсов. | Да |
| `--os-type` | Тип операционной системы экземпляров контейнера. Допустимые значения: Оба, Linux, Windows. По умолчанию: Linux. | Нет |
| `--aci-resource-group` | Группа ресурсов, в которой будут созданы группы контейнеров ACI. | Нет |
| `--location` `-l` | Расположение для создания групп контейнеров ACI. | Нет |
| `--service-principal` | Субъект-служба, используемый для проверки подлинности в API-интерфейсах Azure. | Нет |
| `--client-secret` | Секрет, связанный с субъектом-службой. | Нет |
| `--chart-url` | URL-адрес диаграммы Helm для установки соединителя ACI. | Нет |
| `--image-tag` | Тег образа контейнера Virtual Kubelet. | Нет |

## <a name="validate-virtual-kubelet"></a>Проверка Virtual Kubelet

Чтобы проверить, установлена виртуальная Kubelet, возвращайте список узлов Kubernetes с помощью команды [kubectl Get Nodes][kubectl-get] :

```console
$ kubectl get nodes

NAME                                             STATUS   ROLES   AGE   VERSION
aks-nodepool1-56577038-0                         Ready    agent   11m   v1.12.8
virtual-kubelet-virtual-kubelet-linux-eastus     Ready    agent   39s   v1.13.1-vk-v0.9.0-1-g7b92d1ee-dev
virtual-kubelet-virtual-kubelet-windows-eastus   Ready    agent   37s   v1.13.1-vk-v0.9.0-1-g7b92d1ee-dev
```

## <a name="run-linux-container"></a>Запуск контейнера Linux

Создайте файл `virtual-kubelet-linux.yaml` и скопируйте в него следующий код YAML. Обратите внимание, что для планирования контейнера на узле используются [nodeSelector][node-selector] и [toleration][toleration].

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
        beta.kubernetes.io/os: linux
        kubernetes.io/role: agent
        type: virtual-kubelet
      tolerations:
      - key: virtual-kubelet.io/provider
        operator: Equal
        value: azure
        effect: NoSchedule
```

Запустите приложение с помощью команды [kubectl Create][kubectl-create] .

```console
kubectl create -f virtual-kubelet-linux.yaml
```

Используйте команду [kubectl Get][kubectl-get] Pod с `-o wide` аргументом для вывода списка модулей Pod с запланированным узлом. Обратите внимание, что pod `aci-helloworld` был назначен на узел `virtual-kubelet-virtual-kubelet-linux`.

```console
$ kubectl get pods -o wide

NAME                              READY   STATUS    RESTARTS   AGE     IP               NODE
aci-helloworld-7b9ffbf946-rx87g   1/1     Running   0          22s     52.224.147.210   virtual-kubelet-virtual-kubelet-linux-eastus
```

## <a name="run-windows-container"></a>Запуск контейнера Windows

Создайте файл `virtual-kubelet-windows.yaml` и скопируйте в него следующий код YAML. Обратите внимание, что для планирования контейнера на узле используются [nodeSelector][node-selector] и [toleration][toleration].

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nanoserver-iis
spec:
  replicas: 1
  selector:
    matchLabels:
      app: nanoserver-iis
  template:
    metadata:
      labels:
        app: nanoserver-iis
    spec:
      containers:
      - name: nanoserver-iis
        image: microsoft/iis:nanoserver
        ports:
        - containerPort: 80
      nodeSelector:
        beta.kubernetes.io/os: windows
        kubernetes.io/role: agent
        type: virtual-kubelet
      tolerations:
      - key: virtual-kubelet.io/provider
        operator: Equal
        value: azure
        effect: NoSchedule
```

Запустите приложение с помощью команды [kubectl Create][kubectl-create] .

```console
kubectl create -f virtual-kubelet-windows.yaml
```

Используйте команду [kubectl Get][kubectl-get] Pod с `-o wide` аргументом для вывода списка модулей Pod с запланированным узлом. Обратите внимание, что pod `nanoserver-iis` был назначен на узел `virtual-kubelet-virtual-kubelet-windows`.

```console
$ kubectl get pods -o wide

NAME                              READY   STATUS    RESTARTS   AGE     IP               NODE
nanoserver-iis-5d999b87d7-6h8s9   1/1     Running   0          47s     52.224.143.39    virtual-kubelet-virtual-kubelet-windows-eastus
```

## <a name="remove-virtual-kubelet"></a>Удаление Virtual Kubelet

Чтобы удалить виртуальную Kubelet, используйте команду [AZ AKS Remove-Connector][aks-remove-connector] . Замените значения аргументов на имя соединителя, кластера AKS и группы ресурсов кластера AKS.

```azurecli-interactive
az aks remove-connector \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --connector-name virtual-kubelet \
    --os-type Both
```

> [!NOTE]
> Если возникают ошибки при удалении соединителей обоих ОС или вы хотите удалить только соединитель Windows или Linux, тип операционной системы можно указать вручную. Добавьте параметр `--os-type` к предыдущей команде `az aks remove-connector` и укажите ОС: `Windows` или `Linux`.

## <a name="next-steps"></a>Следующие шаги

Сведения о возможных проблемах с виртуальным Kubelet см. в [статье известные проблемы и способы их решения][vk-troubleshooting]. Чтобы сообщить о проблемах с виртуальным Kubelet, [откройте проблему GitHub][vk-issues].

Дополнительные сведения о виртуальных Kubelet см. в статье о [проекте Virtual Kubelet GitHub][vk-github].

<!-- LINKS - internal -->
[aks-quick-start]: ./kubernetes-walkthrough.md
[aks-remove-connector]: /cli/azure/aks#az-aks-remove-connector
[az-container-list]: /cli/azure/aks#az-aks-list
[aks-install-connector]: /cli/azure/aks#az-aks-install-connector
[virtual-nodes-cli]: virtual-nodes-cli.md
[virtual-nodes-portal]: virtual-nodes-portal.md
[aks-helm]: kubernetes-helm.md
[az-provider-list]: /cli/azure/provider#az-provider-list
[az-provider-register]: /cli/azure/provider#az-provider-register

<!-- LINKS - external -->
[kubectl-create]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#create
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[node-selector]:https://kubernetes.io/docs/concepts/configuration/assign-pod-node/
[toleration]: https://kubernetes.io/docs/concepts/configuration/taint-and-toleration/
[vk-github]: https://github.com/virtual-kubelet/virtual-kubelet
[helm-rbac]: https://docs.helm.sh/using_helm/#role-based-access-control
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[vk-troubleshooting]: https://github.com/virtual-kubelet/virtual-kubelet#known-quirks-and-workarounds
[vk-issues]: https://github.com/virtual-kubelet/virtual-kubelet/issues
