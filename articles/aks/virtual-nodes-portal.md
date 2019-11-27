---
title: Создание виртуальных узлов в Службе Azure Kubernetes (AKS) с помощью портала
description: Сведения о том, как с помощью портала Azure создать кластер Службы Azure Kubernetes (AKS), который использует виртуальные узлы для запуска групп pod.
services: container-service
author: mlearned
ms.topic: conceptual
ms.service: container-service
ms.date: 05/06/2019
ms.author: mlearned
ms.openlocfilehash: ab0aebf0b66ac01e19699795b14063df31cb9621
ms.sourcegitcommit: b4665f444dcafccd74415fb6cc3d3b65746a1a31
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/11/2019
ms.locfileid: "72263761"
---
# <a name="create-and-configure-an-azure-kubernetes-services-aks-cluster-to-use-virtual-nodes-in-the-azure-portal"></a>Создание и настройка кластера Службы Azure Kubernetes (AKS) для использования виртуальных узлов на портале Azure

Чтобы быстро развернуть рабочие нагрузки в кластере Службы Azure Kubernetes (AKS), вы можете использовать виртуальные узлы. Виртуальные узлы позволяют быстро подготовить группы pod и оплачивать только время их выполнения (посекундно). При выполнении масштабирования вам не придется ждать, пока средство автомасштабирования кластера Kubernetes развернет вычислительные узлы для виртуальных машин, на которых можно будет запустить дополнительные группы pod. Виртуальные узлы поддерживаются только с помощью модулей и узлов Linux.

В этой статье показано, как создать и настроить ресурсы виртуальной сети и кластер AKS с поддержкой виртуальных узлов.

## <a name="before-you-begin"></a>Перед началом работы

Виртуальные узлы обеспечивают взаимодействие контейнеров pod в сети, которые выполняются в кластерах ACI и AKS. Для обеспечения этого взаимодействия создается подсеть виртуальной сети и назначаются делегированные разрешения. Виртуальные узлы работают только с кластерами AKS, созданными с помощью сетевого взаимодействия уровня *Расширенный*. По умолчанию кластеры AKS создаются с помощью сетевого взаимодействия уровня *Базовый*. В этой статье показано, как создать виртуальную сеть и подсети, а затем развернуть кластер AKS, использующий сетевое взаимодействие уровня "Расширенный".

Если вы не использовали ACI ранее, зарегистрируйте поставщик служб в подписке. Состояние регистрации поставщика ACI можно проверить с помощью команды [AZ Provider List][az-provider-list] , как показано в следующем примере:

```azurecli-interactive
az provider list --query "[?contains(namespace,'Microsoft.ContainerInstance')]" -o table
```

Поставщик *Microsoft.ContainerInstance* должен иметь состояние *Registered*, как показано в следующем примере выходных данных.

```
Namespace                    RegistrationState
---------------------------  -------------------
Microsoft.ContainerInstance  Registered
```

Если поставщик отображается как *зарегистрировано*, зарегистрируйте поставщик с помощью [AZ Provider Register] [AZ-регистратор-Register], как показано в следующем примере:

```azurecli-interactive
az provider register --namespace Microsoft.ContainerInstance
```

## <a name="regional-availability"></a>Доступ по регионам

Для развертываний виртуальных узлов поддерживаются следующие регионы:

* Восточная Австралия (australiaeast)
* Центральная американская (centralus)
* Восточная часть США (eastus).
* Восточная часть США 2 (eastus2)
* Восточная Япония (жапанеаст)
* Северная Европа (northeurope)
* Юго-Восточная Азия (SoutheastAsia)
* Западная Центральная часть США (westcentralus)
* Западная Европа (westeurope).
* Западная часть США (westus).
* Западная часть США 2 (westus2).

## <a name="known-limitations"></a>Известные ограничения
Функциональность виртуальных узлов сильно зависит от набора функций ACI. Следующие сценарии пока не поддерживаются виртуальными узлами.

* Использование субъекта-службы для извлечения образов записи контроля доступа. [Обходной путь](https://github.com/virtual-kubelet/virtual-kubelet/blob/master/providers/azure/README.md#Private-registry) — использовать [секреты Kubernetes](https://kubernetes.io/docs/tasks/configure-pod-container/pull-image-private-registry/#create-a-secret-by-providing-credentials-on-the-command-line)
* [Ограничения виртуальной сети](../container-instances/container-instances-vnet.md) , включая пиринг виртуальных сетей, политики сети Kubernetes и исходящий трафик в Интернете с группами безопасности сети.
* Инициализировать контейнеры
* [Псевдонимы узлов](https://kubernetes.io/docs/concepts/services-networking/add-entries-to-pod-etc-hosts-with-host-aliases/)
* [Аргументы](../container-instances/container-instances-exec.md#restrictions) для Exec в ACI
* [Daemonset](concepts-clusters-workloads.md#statefulsets-and-daemonsets) не будет выполнять развертывание модулей Pod на виртуальном узле
* [Узлы Windows Server (в настоящее время в предварительной версии в AKS)](windows-container-cli.md) не поддерживаются вместе с виртуальными узлами. Виртуальные узлы можно использовать для планирования контейнеров Windows Server без необходимости использования узлов Windows Server в кластере AKS.

## <a name="sign-in-to-azure"></a>Вход в Azure

Войдите на портал Azure по адресу https://portal.azure.com.

## <a name="create-an-aks-cluster"></a>Создание кластера AKS

В верхнем левом углу портала Azure выберите **Создать ресурс** > **Служба Kubernetes**.

На странице **Основные** настройте следующие параметры:

- *Сведения о проекте*. Выберите подписку Azure, а затем выберите или создайте группу ресурсов Azure, такую как *myResourceGroup*. Введите **Имя кластера Kubernetes**, например *myAKSCluster*.
- *Сведения о кластере*. Выберите регион, версию Kubernetes и префикс DNS-имени для кластера AKS.
- *Пул основных узлов*: выберите размер виртуальной машины для узлов AKS. Размер виртуальной машины **невозможно** изменить после развертывания кластера с AKS.
     - Выберите количество узлов для развертывания в кластере. Для задач в этой статье задайте для параметра **Число узлов** значение *1*. Количество узлов **можно** изменить после развертывания кластера.

Нажмите кнопку **Далее: масштабировать**.

На странице **масштаб** выберите параметр *включено* в разделе **виртуальные узлы**.

![Создание кластера AKS и включение виртуальных узлов](media/virtual-nodes-portal/enable-virtual-nodes.png)

По умолчанию создается субъект-служба Azure Active Directory. Этот субъект-служба используется для взаимодействия и интеграции с другими службами Azure.

Также для кластера настраивается расширенная поддержка сети. Для виртуальных узлов настраивается отдельная подсеть виртуальной сети Azure. Эта подсеть получает делегированные права для подключения ресурсов Azure из кластеров AKS. Если у вас еще нет делегированной подсети, портал Azure создаст и настроит виртуальную сеть Azure и подсеть для использования с виртуальными узлами.

Выберите **Review + create** (Просмотреть и создать). После завершения проверки щелкните **Создать**.

Для создания кластера AKS и его подготовки к использованию потребуется несколько минут.

## <a name="connect-to-the-cluster"></a>Подключение к кластеру

Azure Cloud Shell — это бесплатная интерактивная оболочка, с помощью которой можно выполнять действия, описанные в этой статье. Она включает предварительно установленные общие инструменты Azure и настроена для использования с вашей учетной записью. Управлять кластером Kubernetes можно при помощи [kubectl][kubectl], клиента командной строки Kubernetes. Клиент `kubectl` предварительно установлен в Azure Cloud Shell.

Чтобы открыть Cloud Shell, выберите **Попробовать** в правом верхнем углу блока кода. Cloud Shell можно также запустить в отдельной вкладке браузера, перейдя на страницу [https://shell.azure.com/bash](https://shell.azure.com/bash). Нажмите кнопку **Копировать**, чтобы скопировать блок кода. Вставьте код в Cloud Shell и нажмите клавишу ВВОД, чтобы выполнить его.

Используйте команду [AZ AKS Get-Credential][az-aks-get-credentials] , чтобы настроить `kubectl` для подключения к кластеру Kubernetes. В следующем примере возвращаются учетные данные для имени кластера *myAKSCluster* в группе ресурсов *myResourceGroup*.

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

Создайте в Azure Cloud Shell файл с именем `virtual-node.yaml` и скопируйте в него следующий код YAML. Чтобы запланировать контейнер на узле, определяются [нодеселектор][node-selector] и [допуска][toleration] . Они включают выполнение группы pod в этом виртуальном узле и позволяют убедиться, что функция успешно включена.

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

Запустите приложение с помощью команды [kubectl Apply][kubectl-apply] .

```azurecli-interactive
kubectl apply -f virtual-node.yaml
```

Используйте команду [kubectl Get][kubectl-get] Pod с аргументом `-o wide` для вывода списка модулей Pod и запланированного узла. Обратите внимание, что pod `virtual-node-helloworld` был назначен на узел `virtual-node-linux`.

```
$ kubectl get pods -o wide

NAME                                     READY     STATUS    RESTARTS   AGE       IP           NODE
virtual-node-helloworld-9b55975f-bnmfl   1/1       Running   0          4m        10.241.0.4   virtual-node-aci-linux
```

Группа pod получает внутренний IP-адрес из подсети виртуальной сети Azure, которая делегирована для использования с виртуальными узлами.

> [!NOTE]
> Если вы используете образы, хранящиеся в реестре контейнеров Azure, [Настройте и используйте секрет Kubernetes][acr-aks-secrets]. Текущее ограничение виртуальных узлов заключается в том, что вы не можете использовать встроенную проверку подлинности субъекта-службы Azure AD. Если вы не используете секрет, модули pod, запланированные на виртуальные узлы, не будут запускаться и сообщат об ошибке `HTTP response status code 400 error code "InaccessibleImage"`.

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

Закройте сеанс подключения терминала к проверяемой группе pod, выполнив `exit`. Когда сеанс завершится, pod автоматически удаляется.

## <a name="next-steps"></a>Дополнительная информация

В этой статье мы назначили выполнение группы pod в виртуальном узле и присвоили частный внутренний IP-адрес. В качестве альтернативы вы можете создать развертывание службы и направлять трафик в группу pod через подсистему балансировки нагрузки или контроллер входящего трафика. Дополнительные сведения см. [в разделе Создание базового контроллера входящего трафика в AKS][aks-basic-ingress].

Виртуальные узлы — это один из компонентов, необходимых для масштабирования решения в AKS. Дополнительные сведения о таких решениях см. в следующих статьях.

- [Использование автомасштабирования Kubernetes горизонтального модуля Pod][aks-hpa]
- [Использование автомасштабирования кластера Kubernetes][aks-cluster-autoscaler]
- [Ознакомьтесь с примером автомасштабирования для виртуальных узлов][virtual-node-autoscale]
- [Подробнее о библиотеке виртуальных Kubelet с открытым исходным кодом][virtual-kubelet-repo]

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