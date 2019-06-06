---
title: Создание виртуальных узлов в Службе Azure Kubernetes (AKS) с помощью портала
description: Сведения о том, как с помощью портала Azure создать кластер Службы Azure Kubernetes (AKS), который использует виртуальные узлы для запуска групп pod.
services: container-service
author: iainfoulds
ms.topic: conceptual
ms.service: container-service
ms.date: 05/06/2019
ms.author: iainfou
ms.openlocfilehash: a82d9e6e1d5ffa9b97bb0c1a4272375d4a71863c
ms.sourcegitcommit: 7042ec27b18f69db9331b3bf3b9296a9cd0c0402
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/06/2019
ms.locfileid: "66742812"
---
# <a name="create-and-configure-an-azure-kubernetes-services-aks-cluster-to-use-virtual-nodes-in-the-azure-portal"></a>Создание и настройка кластера Службы Azure Kubernetes (AKS) для использования виртуальных узлов на портале Azure

Чтобы быстро развернуть рабочие нагрузки в кластере Службы Azure Kubernetes (AKS), вы можете использовать виртуальные узлы. Виртуальные узлы позволяют быстро подготовить группы pod и оплачивать только время их выполнения (посекундно). При выполнении масштабирования вам не придется ждать, пока средство автомасштабирования кластера Kubernetes развернет вычислительные узлы для виртуальных машин, на которых можно будет запустить дополнительные группы pod. Виртуальные узлы поддерживаются только с Linux модулей и узлами.

В этой статье показано, как создать и настроить ресурсы виртуальной сети и кластер AKS с поддержкой виртуальных узлов.

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

Если поставщик отображается в том, что *NotRegistered*, зарегистрируйте поставщик с помощью [az зарегистрировать поставщик] [az provider register], как показано в следующем примере:

```azurecli-interactive
az provider register --namespace Microsoft.ContainerInstance
```

## <a name="regional-availability"></a>Доступность по регионам

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
* [Узлы Windows Server (в настоящее время в предварительной версии в AKS)](windows-container-cli.md) вместе с виртуальными узлами не поддерживаются. Виртуальные узлы можно использовать для планирования контейнеры Windows Server без необходимости для узлов Windows Server в кластере AKS.

## <a name="sign-in-to-azure"></a>Вход в Azure

Войдите на портал Azure по адресу https://portal.azure.com.

## <a name="create-an-aks-cluster"></a>Создание кластера AKS

В верхнем левом углу портала Azure выберите **Создать ресурс** > **Служба Kubernetes**.

На странице **Основные** настройте следующие параметры:

- *Сведения о проекте*. Выберите подписку Azure, а затем выберите или создайте группу ресурсов Azure, например *myResourceGroup*. Введите **Имя кластера Kubernetes**, например *myAKSCluster*.
- *Сведения о кластере*. Выберите регион, версию Kubernetes и префикс DNS-имени для кластера AKS.
- *ОСНОВНОЙ УЗЕЛ ПУЛА*: Выберите размер виртуальной машины для узлов AKS. Размер виртуальной машины **невозможно** изменить после развертывания кластера с AKS.
     - Выберите количество узлов для развертывания в кластере. Для задач в этой статье задайте для параметра **Число узлов** значение *1*. Количество узлов **можно** изменить после развертывания кластера.

Щелкните **Далее: Масштаб**.

На **масштабирования** выберите *включено* под **виртуальных узлов**.

![Создание кластера AKS и включение виртуальных узлов](media/virtual-nodes-portal/enable-virtual-nodes.png)

По умолчанию создается субъект-служба Azure Active Directory. Этот субъект-служба используется для взаимодействия и интеграции с другими службами Azure.

Также для кластера настраивается расширенная поддержка сети. Для виртуальных узлов настраивается отдельная подсеть виртуальной сети Azure. Эта подсеть получает делегированные права для подключения ресурсов Azure из кластеров AKS. Если у вас еще нет делегированной подсети, портал Azure создаст и настроит виртуальную сеть Azure и подсеть для использования с виртуальными узлами.

Выберите **Review + create** (Просмотреть и создать). После завершения проверки щелкните **Создать**.

Для создания кластера AKS и его подготовки к использованию потребуется несколько минут.

## <a name="connect-to-the-cluster"></a>Подключение к кластеру

Azure Cloud Shell — это бесплатная интерактивная оболочка, с помощью которой можно выполнять действия, описанные в этой статье. Она включает предварительно установленные общие инструменты Azure и настроена для использования с вашей учетной записью. Управлять кластером Kubernetes можно при помощи [kubectl][kubectl], клиента командной строки Kubernetes. Клиент `kubectl` предварительно установлен в Azure Cloud Shell.

Чтобы открыть Cloud Shell, выберите **Попробовать** в правом верхнем углу блока кода. Cloud Shell можно также запустить в отдельной вкладке браузера, перейдя на страницу [https://shell.azure.com/bash](https://shell.azure.com/bash). Нажмите кнопку **Копировать**, чтобы скопировать блоки кода. Вставьте код в Cloud Shell и нажмите клавишу "ВВОД", чтобы выполнить его.

Выполните команду [az aks get-credentials][az-aks-get-credentials]чтобы настроить подключение `kubectl` к кластеру Kubernetes. В следующем примере возвращаются учетные данные для имени кластера *myAKSCluster* в группе ресурсов *myResourceGroup*.

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

Проверьте подключение к кластеру, выполнив команду [kubectl get][kubectl-get], чтобы просмотреть список узлов кластера.

```azurecli-interactive
kubectl get nodes
```

В следующем примере выходных данных показано создание одного узла виртуальной машины и одного виртуального узла для Linux с именем *virtual-node-aci-linux*:

```
$ kubectl get nodes

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

```
$ kubectl get pods -o wide

NAME                                     READY     STATUS    RESTARTS   AGE       IP           NODE
virtual-node-helloworld-9b55975f-bnmfl   1/1       Running   0          4m        10.241.0.4   virtual-node-aci-linux
```

Группа pod получает внутренний IP-адрес из подсети виртуальной сети Azure, которая делегирована для использования с виртуальными узлами.

> [!NOTE]
> При использовании образов, хранящихся в Реестре контейнеров Azure, [настройте и используйте секрет Kubernetes][acr-aks-secrets]. Текущее ограничение виртуальных узлов — что нельзя использовать интеграции Azure AD аутентификация субъекта-службы. Если вы не используете секрет, модули pod, запланированные на виртуальные узлы, не будут запускаться и сообщат об ошибке `HTTP response status code 400 error code "InaccessibleImage"`.

## <a name="test-the-virtual-node-pod"></a>Тестирование группы pod на виртуальном узле

Чтобы протестировать выполнение группы pod на виртуальном узле, откройте демонстрационную версию приложения в веб-клиенте. Так как группа pod получает внутренний IP-адрес, вы можете быстро проверить это подключение с другой группой pod в том же кластере AKS. Создайте тестовый системный модуль pod и свяжите с ним сеанс терминала.

```azurecli-interactive
kubectl run -it --rm virtual-node-test --image=debian
```

Установите `curl` в модуле pod с помощью `apt-get`:

```azurecli-interactive
apt-get update && apt-get install -y curl
```

Теперь откройте адрес этой группы pod с помощью `curl`, например, *http://10.241.0.4* . Укажите внутренний IP-адрес, который вы получили от предыдущей команды `kubectl get pods`.

```azurecli-interactive
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

Закройте сеанс подключения терминала к проверяемой группе pod, выполнив `exit`. Когда сеанс завершится, группа pod автоматически удаляется.

## <a name="next-steps"></a>Дальнейшие действия

В этой статье мы назначили выполнение группы pod в виртуальном узле и присвоили частный внутренний IP-адрес. В качестве альтернативы вы можете создать развертывание службы и направлять трафик в группу pod через подсистему балансировки нагрузки или контроллер входящего трафика. Дополнительные сведения см. в статье [Создание контроллера входящего трафика в Службе Azure Kubernetes (AKS)][aks-basic-ingress].

Виртуальные узлы — это один из компонентов, необходимых для масштабирования решения в AKS. Дополнительные сведения о таких решениях см. в следующих статьях:

- [Масштабирование приложений в Службе Azure Kubernetes (AKS)][aks-hpa]
- [Автомасштабирование кластера с помощью службы Azure Kubernetes (AKS)][aks-cluster-autoscaler]
- [Ознакомьтесь с примерами автомасштабирования отношении виртуальных узлов][virtual-node-autoscale]
- [Дополнительные сведения о библиотеке Virtual Kubelet открытым исходным кодом][virtual-kubelet-repo]

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

<!-- LINKS - internal -->
[aks-network]: ./networking-overview.md
[az-aks-get-credentials]: /cli/azure/aks?view=azure-cli-latest#az-aks-get-credentials
[aks-hpa]: tutorial-kubernetes-scale.md
[aks-cluster-autoscaler]: cluster-autoscaler.md
[aks-basic-ingress]: ingress-basic.md
[acr-aks-secrets]: ../container-registry/container-registry-auth-aks.md#access-with-kubernetes-secret
[az-provider-list]: /cli/azure/provider#az-provider-list