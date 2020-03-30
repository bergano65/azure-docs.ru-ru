---
title: Создание виртуальных узлов в Службе Azure Kubernetes (AKS) с помощью портала
description: Сведения о том, как с помощью портала Azure создать кластер Службы Azure Kubernetes (AKS), который использует виртуальные узлы для запуска групп pod.
services: container-service
ms.topic: conceptual
ms.date: 05/06/2019
ms.openlocfilehash: ea93ea4a68fad213fe5bd1dc61abcb2deaef2c9c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79473597"
---
# <a name="create-and-configure-an-azure-kubernetes-services-aks-cluster-to-use-virtual-nodes-in-the-azure-portal"></a>Создание и настройка кластера Службы Azure Kubernetes (AKS) для использования виртуальных узлов на портале Azure

Чтобы быстро развернуть рабочие нагрузки в кластере Службы Azure Kubernetes (AKS), вы можете использовать виртуальные узлы. Виртуальные узлы позволяют быстро подготовить группы pod и оплачивать только время их выполнения (посекундно). При выполнении масштабирования вам не придется ждать, пока средство автомасштабирования кластера Kubernetes развернет вычислительные узлы для виртуальных машин, на которых можно будет запустить дополнительные группы pod. Виртуальные узлы поддерживаются только с помощью стручков и узлов Linux.

В этой статье показано, как создать и настроить ресурсы виртуальной сети и кластер AKS с поддержкой виртуальных узлов.

## <a name="before-you-begin"></a>Перед началом

Виртуальные узлы обеспечивают сетевую связь между стручками, запускаемыми в Azure Container Instances (ACI) и кластером AKS. Для обеспечения этого взаимодействия создается подсеть виртуальной сети и назначаются делегированные разрешения. Виртуальные узлы работают только с кластерами AKS, созданными с помощью сетевого взаимодействия уровня *Расширенный*. По умолчанию кластеры AKS создаются с помощью сетевого взаимодействия уровня *Базовый*. В этой статье показано, как создать виртуальную сеть и подсети, а затем развернуть кластер AKS, использующий сетевое взаимодействие уровня "Расширенный".

Если вы не использовали ACI ранее, зарегистрируйте поставщик служб в подписке. Вы можете проверить состояние регистрации поставщика ACI с помощью команды [az provider list][az-provider-list], как показано в следующем примере.

```azurecli-interactive
az provider list --query "[?contains(namespace,'Microsoft.ContainerInstance')]" -o table
```

Поставщик *Microsoft.ContainerInstance* должен иметь состояние *Registered*, как показано в следующем примере выходных данных.

```output
Namespace                    RegistrationState
---------------------------  -------------------
Microsoft.ContainerInstance  Registered
```

Если поставщик имеет состояние *NotRegistered*, зарегистрируйте поставщик с помощью команды [az provider register][az-provider-register], как показано в следующем примере.

```azurecli-interactive
az provider register --namespace Microsoft.ContainerInstance
```

## <a name="regional-availability"></a>Доступность по регионам

Следующие регионы поддерживаются для развертывания виртуальных узлов:

* Австралия Восток (австралия)
* Центральная часть США (центральный)
* Восточная часть США (eastus).
* Восток США 2 (восток2)
* Япония Восток (Япония)
* Северная Европа (northeurope)
* юго-восточная Азия (юго-восточная Азия)
* Западный Центральный США (западныйцентральный)
* Западная Европа (westeurope).
* Западная часть США (westus).
* Западная часть США 2 (westus2).

## <a name="known-limitations"></a>Известные ограничения
Функциональность виртуальных узлов сильно зависит от набора функций ACI. Следующие сценарии пока не поддерживаются виртуальными узлами

* Использование принципаслужбы для вытягивания изображений ACR. [Обходной путь](https://github.com/virtual-kubelet/virtual-kubelet/blob/master/providers/azure/README.md#Private-registry) заключается в использовании [kubernetes секреты](https://kubernetes.io/docs/tasks/configure-pod-container/pull-image-private-registry/#create-a-secret-by-providing-credentials-on-the-command-line)
* [Виртуальные ограничения сети,](../container-instances/container-instances-vnet.md) включая вглядываение VNet, сетевые политики Kubernetes и исходящий трафик в Интернет с группами сетевой безопасности.
* Контейнеры Init
* [Хост псевдонимы](https://kubernetes.io/docs/concepts/services-networking/add-entries-to-pod-etc-hosts-with-host-aliases/)
* [Аргументы](../container-instances/container-instances-exec.md#restrictions) для Exec в ACI
* [DaemonSets](concepts-clusters-workloads.md#statefulsets-and-daemonsets) не будет развертывать стручки в виртуальный узел
* [Узлы Windows Server (в настоящее время в предварительном просмотре в AKS)](windows-container-cli.md) не поддерживаются наряду с виртуальными узлами. Можно использовать виртуальные узлы для планирования контейнеров Windows Server без необходимости использования узлов Windows Server в кластере AKS.

## <a name="sign-in-to-azure"></a>Вход в Azure

Войдите на портал Azure по адресу https://portal.azure.com.

## <a name="create-an-aks-cluster"></a>Создание кластера AKS

В верхнем левом углу портала Azure выберите **«Создайте ресурс** > **Kubernetes Service».**

На странице **Основные** настройте следующие параметры:

- *Сведения о проекте*. Выберите подписку Azure, а затем выберите или создайте группу ресурсов Azure, такую как *myResourceGroup*. Введите **Имя кластера Kubernetes**, например *myAKSCluster*.
- *Сведения о кластере*. Выберите регион, версию Kubernetes и префикс DNS-имени для кластера AKS.
- *PRIMARY NODE POOL*: Выберите размер VM для узлов AKS. Размер виртуальной машины **невозможно** изменить после развертывания кластера с AKS.
     - Выберите количество узлов для развертывания в кластере. Для задач в этой статье задайте для параметра **Число узлов** значение *1*. Количество узлов **можно** изменить после развертывания кластера.

Нажмите **далее: Масштабная шкала**.

На странице **Шкала** выберите *Включенные* под **виртуальными узлами.**

![Создание кластера AKS и включение виртуальных узлов](media/virtual-nodes-portal/enable-virtual-nodes.png)

По умолчанию создается субъект-служба Azure Active Directory. Этот субъект-служба используется для взаимодействия и интеграции с другими службами Azure.

Также для кластера настраивается расширенная поддержка сети. Для виртуальных узлов настраивается отдельная подсеть виртуальной сети Azure. Эта подсеть получает делегированные права для подключения ресурсов Azure из кластеров AKS. Если у вас еще нет делегированной подсети, портал Azure создаст и настроит виртуальную сеть Azure и подсеть для использования с виртуальными узлами.

Выберите **Review + create** (Просмотреть и создать). После завершения проверки щелкните **Создать**.

Для создания кластера AKS и его подготовки к использованию потребуется несколько минут.

## <a name="connect-to-the-cluster"></a>Подключение к кластеру

Azure Cloud Shell — это бесплатная интерактивная оболочка, с помощью которой можно выполнять действия, описанные в этой статье. Она включает предварительно установленные общие инструменты Azure и настроена для использования с вашей учетной записью. Управлять кластером Kubernetes можно при помощи [kubectl][kubectl], клиента командной строки Kubernetes. Клиент `kubectl` предварительно установлен в Azure Cloud Shell.

Чтобы открыть Cloud Shell, выберите **Попробовать** в правом верхнем углу блока кода. Вы также можете запустить Cloud Shell в [https://shell.azure.com/bash](https://shell.azure.com/bash)отдельной вкладке браузера, перейдя к . Выберите **Copy,** чтобы скопировать блоки кода, вставьте его в облачную оболочку и нажмите введите, чтобы запустить его.

Выполните команду [az aks get-credentials][az-aks-get-credentials]чтобы настроить подключение `kubectl` к кластеру Kubernetes. В следующем примере возвращаются учетные данные для имени кластера *myAKSCluster* в группе ресурсов *myResourceGroup*.

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

Чтобы проверить подключение к кластеру, используйте команду [kubectl get][kubectl-get] для получения списка узлов кластера.

```console
kubectl get nodes
```

В следующем примере выходных данных показано создание одного узла виртуальной машины и одного виртуального узла для Linux с именем *virtual-node-aci-linux*:

```output
NAME                           STATUS    ROLES     AGE       VERSION
virtual-node-aci-linux         Ready     agent     28m       v1.11.2
aks-agentpool-14693408-0       Ready     agent     32m       v1.11.2
```

## <a name="deploy-a-sample-app"></a>Развертывание примера приложения

Создайте в Azure Cloud Shell файл с именем `virtual-node.yaml` и скопируйте в него следующий код YAML. Чтобы назначить контейнер на узел, определите параметры [nodeSelector][node-selector] и [toleration][toleration]. Они включают выполнение группы pod в этом виртуальном узле и позволяют убедиться, что функция успешно включена.

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

```azurecli-interactive
kubectl apply -f virtual-node.yaml
```

Чтобы вывести список групп pod и назначенный узел, выполните команду [kubectl get pods][kubectl-get] с аргументом `-o wide`. Обратите внимание, что pod `virtual-node-helloworld` был назначен на узел `virtual-node-linux`.

```console
kubectl get pods -o wide
```

```output
NAME                                     READY     STATUS    RESTARTS   AGE       IP           NODE
virtual-node-helloworld-9b55975f-bnmfl   1/1       Running   0          4m        10.241.0.4   virtual-node-aci-linux
```

Группа pod получает внутренний IP-адрес из подсети виртуальной сети Azure, которая делегирована для использования с виртуальными узлами.

> [!NOTE]
> При использовании образов, хранящихся в Реестре контейнеров Azure, [настройте и используйте секрет Kubernetes][acr-aks-secrets]. В настоящее время ограничениевиртуальных виртуальных узлов заключается в том, что вы не можете использовать основную аутентификацию интегрированной службы Azure AD. Если вы не используете секрет, модули pod, запланированные на виртуальные узлы, не будут запускаться и сообщат об ошибке `HTTP response status code 400 error code "InaccessibleImage"`.

## <a name="test-the-virtual-node-pod"></a>Тестирование группы pod на виртуальном узле

Чтобы протестировать выполнение группы pod на виртуальном узле, откройте демонстрационную версию приложения в веб-клиенте. Так как группа pod получает внутренний IP-адрес, вы можете быстро проверить это подключение с другой группой pod в том же кластере AKS. Создайте тестовый системный модуль pod и свяжите с ним сеанс терминала.

```console
kubectl run -it --rm virtual-node-test --image=debian
```

Установите `curl` в модуле pod с помощью `apt-get`:

```console
apt-get update && apt-get install -y curl
```

Теперь получите доступ `curl`к адресу *http://10.241.0.4*вашего стручка с помощью, например. Укажите внутренний IP-адрес, который вы получили от предыдущей команды `kubectl get pods`.

```console
curl -L http://10.241.0.4
```

Отобразится демонстрационное приложение, как показано в следующем сокращенном примере выходных данных:

```output
<html>
<head>
  <title>Welcome to Azure Container Instances!</title>
</head>
[...]
```

Закройте сеанс подключения терминала к проверяемой группе pod, выполнив `exit`. Когда сеанс завершится, группа pod автоматически удаляется.

## <a name="next-steps"></a>Дальнейшие действия

В этой статье мы назначили выполнение группы pod в виртуальном узле и присвоили частный внутренний IP-адрес. В качестве альтернативы вы можете создать развертывание службы и направлять трафик в группу pod через подсистему балансировки нагрузки или контроллер входящего трафика. Дополнительные сведения см. в статье [Создание контроллера входящего трафика в Службе Azure Kubernetes (AKS)][aks-basic-ingress].

Виртуальные узлы — это один из компонентов, необходимых для масштабирования решения в AKS. Дополнительные сведения о таких решениях см. в следующих статьях:

- [Масштабирование приложений в Службе Azure Kubernetes (AKS)][aks-hpa]
- [Автомасштабирование кластера с помощью службы Azure Kubernetes (AKS)][aks-cluster-autoscaler]
- [Ознакомьтесь с автоскальзом для виртуальных узлов][virtual-node-autoscale]
- [Узнайте больше о библиотеке с открытым исходным кодом Virtual Kubelet][virtual-kubelet-repo]

<!-- LINKS - external -->
[kubectl]: https://kubernetes.io/docs/user-guide/kubectl/
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[node-selector]:https://kubernetes.io/docs/concepts/configuration/assign-pod-node/
[toleration]: https://kubernetes.io/docs/concepts/configuration/taint-and-toleration/
[azure-cni]: https://github.com/Azure/azure-container-networking/blob/master/docs/cni.md
[aks-github]: https://github.com/azure/aks/issues]
[virtual-node-autoscale]: https://github.com/Azure-Samples/virtual-node-autoscale
[virtual-kubelet-repo]: https://github.com/virtual-kubelet/virtual-kubelet
[acr-aks-secrets]: https://kubernetes.io/docs/tasks/configure-pod-container/pull-image-private-registry/

<!-- LINKS - internal -->
[aks-network]: ./networking-overview.md
[az-aks-get-credentials]: /cli/azure/aks?view=azure-cli-latest#az-aks-get-credentials
[aks-hpa]: tutorial-kubernetes-scale.md
[aks-cluster-autoscaler]: cluster-autoscaler.md
[aks-basic-ingress]: ingress-basic.md
[az-provider-list]: /cli/azure/provider#az-provider-list
[az-provider-register]: /cli/azure/provider?view=azure-cli-latest#az-provider-register
